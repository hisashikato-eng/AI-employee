# スライド生成 — 注意事項まとめ

AI によるスライド自動生成を行う際に発生した問題と、その正しい対応を記録します。

---

## 注意1: スライドの出力先は Google Slides API を優先する

### 問題

Google Slides API の認証情報（`credentials.json`）が用意されているにもかかわらず、
`pptxgenjs` を使って `.pptx` ファイルを生成してしまった。

### 原因

スライドルールのテンプレート（Cynthialy）が `pptxgenjs` ベースで書かれていたため、
そのままコードを流用した。

### 正しい対応

- Google API の認証情報がある場合は **Google Slides API（Python）で直接作成する**
- `.pptx` 生成は、Google 認証が不要・オフライン要件がある場合のみ使用する

### Google Slides API の基本構成

```python
from googleapiclient.discovery import build
from google.oauth2.credentials import Credentials

creds = Credentials.from_authorized_user_file('token.json', SCOPES)
service = build('slides', 'v1', credentials=creds)

# プレゼン作成
presentation = service.presentations().create(body={
    'title': 'タイトル',
    'pageSize': {
        'width':  {'magnitude': 9144000, 'unit': 'EMU'},
        'height': {'magnitude': 5143500, 'unit': 'EMU'},
    }
}).execute()
```

---

## 注意2: 16:9 アスペクト比の正しい寸法

### 問題

`pptxgenjs` でカスタムレイアウトを定義する際に以下のように書いてしまった。

```javascript
// ❌ 間違い — これは 4:3 (10 ÷ 7.5 = 1.333...)
pres.defineLayout({ name: 'LAYOUT_16x9', width: 10, height: 7.5 });
```

### 正しい値

| 形式 | 幅 | 高さ | 比率 |
|------|----|------|------|
| インチ（pptxgenjs） | 10 | **5.625** | 16:9 ✅ |
| EMU（Google Slides API） | 9,144,000 | **5,143,500** | 16:9 ✅ |
| インチ（pptxgenjs） | 10 | 7.5 | 4:3 ❌ |

### 正しいコード

```javascript
// ✅ 正しい — pptxgenjs 組み込みの LAYOUT_16x9 をそのまま使う
pres.layout = 'LAYOUT_16x9'; // width=10, height=5.625 が自動設定される

// カスタム定義が必要な場合は高さを 5.625 にする
pres.defineLayout({ name: 'MY_16x9', width: 10, height: 5.625 });
```

```python
# ✅ 正しい — Google Slides API（単位: EMU）
page_size = {
    'width':  {'magnitude': 9144000, 'unit': 'EMU'},  # 10インチ
    'height': {'magnitude': 5143500, 'unit': 'EMU'},  # 5.625インチ
}
```

### 計算式

```
1インチ = 914,400 EMU
16:9 の高さ = 幅 × (9 ÷ 16) = 10 × 0.5625 = 5.625インチ = 5,143,500 EMU
```

---

## チェックリスト（スライド生成前に確認）

- [ ] Google API 認証情報（`credentials.json` / `token.json`）はあるか？
  - ある → **Google Slides API（Python）を使う**
  - ない → `pptxgenjs` または `python-pptx` を使う
- [ ] スライドサイズは 16:9 か？
  - pptxgenjs: `height: 5.625` または組み込み `LAYOUT_16x9` を使う
  - Google Slides API: `height: 5143500 EMU`
- [ ] スライドルールのテンプレートが `pptxgenjs` ベースでも、出力先に合わせてAPIを切り替える
