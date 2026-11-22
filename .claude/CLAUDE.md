# go_api_template

## プロジェクト概要

Go で Web API サーバーを作るためのテンプレートリポジトリ。
新しい API プロジェクトを始めるときの土台として、レイヤー構成とディレクトリ規約をあらかじめ用意しておくことを目的とする。

## 現状

- 骨組みのみ（各ディレクトリは `.gitkeep` だけ）。`go.mod` やエントリーポイントはまだ無い。
- 以下は未決定。決まり次第このファイルに追記する。
  - TODO: Go のバージョン / モジュールパス
  - TODO: HTTP ルーター（標準 `net/http` / chi / echo など）
  - TODO: DB と DB アクセス手段（database/sql, sqlc, GORM など）
  - TODO: 設定の読み込み方法（環境変数, `.env` など）
  - TODO: CI（`.github/workflows/` は空）

## ディレクトリ構成

```
internal/
├── config/      # 設定の読み込み・保持
├── server/      # HTTP サーバーの起動、ルーティング登録、DI の組み立て
├── middleware/  # HTTP ミドルウェア（ロギング、認証、リカバリなど）
├── handler/     # HTTP の入出力（リクエストのパース、バリデーション、レスポンス生成）
├── service/     # ビジネスロジック
├── repository/  # 永続化層へのアクセス（DB, 外部ストレージなど）
└── model/       # ドメインモデル（レイヤー間で共有する型）
```

## レイヤーの依存方向

```
server → middleware, handler → service → repository
                                  ↘          ↙
                                    model
```

- 依存は上から下への一方向のみ。逆方向・横方向の依存は禁止。
- `model` はどのレイヤーからも参照してよいが、`model` 自身は他の `internal` パッケージに依存しない。
- 詳細なルールは `.claude/rules/` を参照。

## 開発コマンド

TODO: `go.mod` 作成後に追記（ビルド、テスト、lint、起動方法）。

## コーディング規約

`.claude/rules/` 配下に記載。
