# ApplicationRequests テーブル詳細設計

## ドキュメント目的
- `application_requests`テーブルのスキーマと制約を明文化し、社労士紹介フローで一貫した利用を行う。
- 構造変更時は本書とテーブル一覧・関連業務ドキュメントを同時に更新する。

## 1. 基本情報
| 項目 | 内容 | 備考 |
|---|---|---|
| テーブル物理名 | application_requests |  |
| テーブル論理名 | 社労士紹介依頼 | 助成金申請依頼の履歴 |
| ドメイン/コンテキスト | ApplicationRequests（助成金紹介） |  |
| 主キー | id（BIGINT UNSIGNED AUTO_INCREMENT） |  |

## 2. カラム定義
| No | カラム名 | 論理名 | 型 | PK | Not Null | デフォルト | 説明/業務ルール | 備考 |
|---|---|---|---|---|---|---|---|---|
| 1 | id | 依頼ID | BIGINT UNSIGNED | ○ | ○ | AUTO INCREMENT | システム採番。 |  |
| 2 | user_id | 依頼ユーザーID | BIGINT UNSIGNED |  | ○ |  | `users.id`参照。依頼を行った会員。 | ON DELETE CASCADE |
| 3 | company_id | 企業ID | BIGINT UNSIGNED |  | ○ |  | `companies.id`参照。ユーザー所属企業。 | ON DELETE CASCADE |
| 4 | funding_id | 助成金ID | BIGINT UNSIGNED |  | ○ |  | `fundings.id`参照。対象助成金。 | ON DELETE CASCADE |
| 5 | firm_id | 社労士法人ID | BIGINT UNSIGNED |  | ○ |  | `social_insurance_firms.id`参照。担当社労士法人。 | ON DELETE CASCADE |
| 6 | request_date | 依頼日時 | TIMESTAMP(0) |  | ○ | CURRENT_TIMESTAMP | 依頼受付日時。タイムゾーンはDB依存。 |  |
| 7 | created_at | 作成日時 | TIMESTAMP(0) |  | ○ | CURRENT_TIMESTAMP | Laravel標準。 |  |
| 8 | updated_at | 更新日時 | TIMESTAMP(0) |  | ○ | CURRENT_TIMESTAMP | Laravel標準。 | on update CURRENT_TIMESTAMP |

## 3. インデックス・キー設計
| 種別 | 名称 | 対象カラム | ユニーク | 用途/目的 | 備考 |
|---|---|---|---|---|---|
| 主キー | PRIMARY | id | ○ | レコード一意性 |  |
| 外部キー | application_requests_user_id_foreign | user_id | ○ | 会員削除時の連鎖削除 |  |
| 外部キー | application_requests_company_id_foreign | company_id | ○ | 企業削除時の連鎖削除 |  |
| 外部キー | application_requests_funding_id_foreign | funding_id | ○ | 助成金削除時の連鎖削除 |  |
| 外部キー | application_requests_firm_id_foreign | firm_id | ○ | 社労士法人削除時の連鎖削除 |  |
| セカンダリ | application_requests_user_id_index | user_id | × | ユーザー別検索 | Laravel自動命名 |
| セカンダリ | application_requests_company_id_index | company_id | × | 企業別抽出 |  |
| セカンダリ | application_requests_funding_id_index | funding_id | × | 助成金別抽出 |  |
| セカンダリ | application_requests_firm_id_index | firm_id | × | 社労士法人別抽出 |  |
| セカンダリ | application_requests_request_date_index | request_date | × | 期間検索 |  |

## 4. 制約・リレーション
| 種別 | 名称 | 内容 | 備考 |
|---|---|---|---|
| 外部キー | application_requests_user_id_foreign | `users.id`参照、ON DELETE CASCADE |  |
| 外部キー | application_requests_company_id_foreign | `companies.id`参照、ON DELETE CASCADE |  |
| 外部キー | application_requests_funding_id_foreign | `fundings.id`参照、ON DELETE CASCADE |  |
| 外部キー | application_requests_firm_id_foreign | `social_insurance_firms.id`参照、ON DELETE CASCADE |  |
| リレーション | User→ApplicationRequest | 1:N、依頼元ユーザー単位に履歴保持 |  |
| リレーション | Company→ApplicationRequest | 1:N、企業での依頼集計に利用 |  |
| リレーション | Funding→ApplicationRequest | 1:N、助成金別の紹介状況管理 |  |
| リレーション | SocialInsuranceFirm→ApplicationRequest | 1:N、社労士法人対応状況管理 |  |

## 改定履歴
| 改定日 | 版数 | 変更概要 | 担当 |
|---|---|---|---|
| 2025-09-19 | v0.1 | 新規作成 |  |
| 2025-10-10 | v1 | フェーズ0.5のリリース版 | Codex AI |