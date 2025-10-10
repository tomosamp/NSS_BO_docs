# CompanyOperators テーブル詳細設計

## ドキュメント目的
- `company_operators`テーブルの構造・制約を明文化し、企業に紐づく運営者アサインを管理する。
- アサインロジック変更時は本書を更新する。

## 1. 基本情報
| 項目 | 内容 | 備考 |
|---|---|---|
| テーブル物理名 | company_operators |  |
| テーブル論理名 | 企業-運営者アサイン |  |
| ドメイン/コンテキスト | Company |  |
| 主キー | id（BIGINT UNSIGNED AUTO_INCREMENT） |  |

## 2. カラム定義
| No | カラム名 | 論理名 | 型 | PK | Not Null | デフォルト | 説明/業務ルール | 備考 |
|---|---|---|---|---|---|---|---|---|
| 1 | id | アサインID | BIGINT UNSIGNED | ○ | ○ | AUTO INCREMENT | システム採番。 |  |
| 2 | company_id | 企業ID | BIGINT UNSIGNED |  | ○ |  | `companies.id`参照。 | ON DELETE CASCADE |
| 3 | status | アサインステータス | VARCHAR(100) |  | ○ |  | 担当状況。 | 値一覧は運用定義 |
| 4 | operator_id | 運営者ID | BIGINT UNSIGNED |  | ○ |  | `operators.id`参照。 | ON DELETE CASCADE |
| 5 | created_at | 作成日時 | TIMESTAMP(0) |  |  | CURRENT_TIMESTAMP | 登録時刻。 |  |
| 6 | updated_at | 更新日時 | TIMESTAMP(0) |  |  | CURRENT_TIMESTAMP | 更新時刻。 | on update CURRENT_TIMESTAMP |
| 7 | deleted_at | 論理削除日時 | TIMESTAMP(0) |  |  |  | `softDeletes()`。 |  |

## 3. インデックス・キー設計
| 種別 | 名称 | 対象カラム | ユニーク | 用途/目的 | 備考 |
|---|---|---|---|---|---|
| 主キー | PRIMARY | id | ○ | レコード一意性 |  |
| 外部キー | company_operators_company_id_foreign | company_id | ○ | 企業削除時の連鎖削除 |  |
| 外部キー | company_operators_operator_id_foreign | operator_id | ○ | 運営者削除時の連鎖削除 |  |
| セカンダリ | - | company_id, operator_id | × | 重複チェック用途 | ユニーク制約要否は業務検討 |

## 4. 制約・リレーション
| 種別 | 名称 | 内容 | 備考 |
|---|---|---|---|
| 外部キー | company_operators_company_id_foreign | `companies.id`参照、ON DELETE CASCADE |  |
| 外部キー | company_operators_operator_id_foreign | `operators.id`参照、ON DELETE CASCADE |  |
| リレーション | Company↔Operator | 多対多（アサイン情報）。論理削除で解除管理。 |  |
| 業務ルール | status値管理 | 列挙値は運用部門管理（例: 稼働中/停止中）。 |  |

## 改定履歴
| 改定日 | 版数 | 変更概要 | 担当 |
|---|---|---|---|
| 2025-09-19 | v0.1 | 新規作成 |  |
| 2025-10-10 | v1 | フェーズ0.5のリリース版 | Codex AI |