# AI-employee

![GitHub Repo stars](https://img.shields.io/github/stars/hisashikato-eng/AI-employee?style=flat-square)
![GitHub forks](https://img.shields.io/github/forks/hisashikato-eng/AI-employee?style=flat-square)
![GitHub last commit](https://img.shields.io/github/last-commit/hisashikato-eng/AI-employee?style=flat-square)
![License](https://img.shields.io/github/license/hisashikato-eng/AI-employee?style=flat-square)

## 概要

AI-employee は、AI を活用した「AIエージェント社員」システムです。Claude / Codex / Cursor などの AI ツールを組み合わせ、ソフトウェア開発タスクの自動化・効率化を実現するためのフレームワークを提供します。

## 特徴

- 複数の AI ツールを連携し、開発ワークフローを統合
- 要件整理から実装・レビューまでを段階的に支援
- 人間と AI エージェントの協調を前提とした運用設計
- プロジェクトに応じて拡張しやすい構成

## アーキテクチャ

本プロジェクトは、以下の役割分担を想定した構成です。

- **オーケストレーション層**: タスク分解、エージェント間の調整、進行管理
- **エージェント実行層**: Claude / Codex / Cursor などを使った実処理
- **開発連携層**: GitHub 連携、コード生成、レビュー、ドキュメント更新

## セットアップ方法

1. リポジトリをクローンします。

```bash
git clone https://github.com/hisashikato-eng/AI-employee.git
cd AI-employee
```

2. 必要な設定ファイル（APIキーや環境変数）を用意します。

```bash
cp .env.example .env
# .env を編集して各種キーを設定
```

3. 依存関係をインストールします（使用する環境に合わせて実行）。

```bash
# 例: Node.js の場合
npm install
```

## 使い方

1. 自動化したい開発タスクを定義します。
2. 使用する AI ツール（Claude / Codex / Cursor など）を選択・設定します。
3. タスク実行後、生成物（コード・ドキュメント・レビュー結果）を確認します。
4. 必要に応じて人間が修正し、再実行して精度を高めます。

## ディレクトリ構成

```text
AI-employee/
├── README.md
├── src/            # コアロジック
├── agents/         # AIエージェント定義
├── workflows/      # タスク実行フロー
├── docs/           # 設計・運用ドキュメント
└── scripts/        # 補助スクリプト
```

※ 実際の構成は今後の実装に応じて変更される可能性があります。

## AI によるスライド生成 — 既知の注意事項

詳細は [`docs/slide-notes.md`](docs/slide-notes.md) を参照してください。

| # | 注意点 | 正しい対応 |
|---|--------|-----------|
| 1 | Google API 認証済みの場合は `.pptx` ではなく Google Slides API で直接作成する | `google-api-python-client` を使用 |
| 2 | pptxgenjs でカスタムレイアウトを定義するとアスペクト比が崩れる | `LAYOUT_16x9` 組み込み値を使用（10 × 5.625 インチ） |

## ライセンス

このプロジェクトは [MIT License](LICENSE) のもとで公開されています。
