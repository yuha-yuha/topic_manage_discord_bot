---
paths:
  - "internal/**"
---

# レイヤー構成の規約

## 各レイヤーの責務

| パッケージ | 責務 | やってはいけないこと |
|---|---|---|
| `config` | 設定値の読み込みと型付きの構造体への変換 | 他の `internal` パッケージへの依存 |
| `server` | サーバー起動、ルーティング登録、各レイヤーの組み立て（DI） | ビジネスロジックを書く |
| `middleware` | 横断的関心事（ロギング、認証、リカバリ、リクエスト ID など） | `service` / `repository` を直接呼ぶ業務処理 |
| `handler` | リクエストのパース・入力バリデーション・レスポンス変換・HTTP ステータスの決定 | DB アクセス、ビジネスルールの判断 |
| `service` | ビジネスロジック、トランザクション境界 | `net/http` 型（`*http.Request` など）への依存 |
| `repository` | DB・外部ストレージへのアクセス | ビジネスルールの判断 |
| `model` | ドメインの型定義 | 他の `internal` パッケージへの依存 |

## 依存方向

- `server` → `middleware`, `handler` → `service` → `repository`、および全レイヤー → `model`。
- 逆方向・同レイヤー間の依存は作らない。
- `service` は `repository` の具体型ではなく、`service` 側で定義したインターフェースに依存する。

## データの受け渡し

- リクエスト / レスポンスの JSON 用構造体（DTO）は `handler` に置き、`model` に `json` タグ付きの HTTP 専用型を混ぜない。
- DB の行に対応する型が `model` と異なる場合は `repository` 内で変換する。

## エラー

- `service` / `repository` はドメインのエラー（例: `model.ErrNotFound`）を返し、HTTP ステータスへの変換は `handler` で行う。
- 内部エラーの詳細をレスポンスにそのまま出さない。
