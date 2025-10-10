# NotificationCompanies テーブル詳細設計

## ドキュメント目的
- `notification_companies`テーブルの構造・制約を明文化し、企業別のお知らせ配信状況を管理する。
- 配信ロジック変更時は本書と通知機能仕様を更新する。

## 1. 基本情報
| 項目 | 内容 | 備考 |
|---|---|---|
| テーブル物理名 | notification_companies |  |
| テーブル論理名 | お知らせ配信先企業 |  |
| ドメイン/コンテキスト | Notifications |  |
| 主キー | id（BIGINT UNSIGNED AUTO_INCREMENT） |  |

## 2. カラム定義
| No | カラム名 | 論理名 | 型 | PK | Not Null | デフォルト | 説明/業務ルール | 備考 |
|---|---|---|---|---|---|---|---|---|
| 1 | id | 配信ID | BIGINT UNSIGNED | ○ | ○ | AUTO INCREMENT | システム採番。 |  |
| 2 | notification_id | お知らせID | BIGINT UNSIGNED |  | ○ |  | `notifications.id`参照。 | ON DELETE CASCADE |
| 3 | company_id | 企業ID | BIGINT UNSIGNED |  | ○ |  | `companies.id`参照。 | ON DELETE CASCADE |
| 4 | read_time | 既読日時 | TIMESTAMP(0) |  |  |  | 既読時刻。未読はNULL。 |  |
| 5 | read_user_id | 既読顧客ID | BIGINT UNSIGNED |  |  |  | 既読操作をした顧客（`users.id`）。 | ON DELETE SET NULL |
| 6 | created_at | 作成日時 | TIMESTAMP(0) |  |  | CURRENT_TIMESTAMP | 配信登録日時。 |  |
| 7 | updated_at | 更新日時 | TIMESTAMP(0) |  |  | CURRENT_TIMESTAMP | Laravel標準。 | on update CURRENT_TIMESTAMP |
| 8 | deleted_at | 論理削除日時 | TIMESTAMP(0) |  |  |  | `softDeletes()`。 |  |

## 3. インデックス・キー設計
| 種別 | 名称 | 対象カラム | ユニーク | 用途/目的 | 備考 |
|---|---|---|---|---|---|
| 主キー | PRIMARY | id | ○ | レコード一意性 |  |
| ユニーク | uk_notification_companies_notification_company | notification_id, company_id | ○ | 同一お知らせの重複配信防止 |  |
| 外部キー | notification_companies_notification_id_foreign | notification_id | ○ | お知らせ削除時の連鎖削除 |  |
| 外部キー | notification_companies_company_id_foreign | company_id | ○ | 企業削除時の連鎖削除 |  |
| 外部キー | notification_companies_read_user_id_foreign | read_user_id | × | 顧客削除時にNULL化 |  |
| セカンダリ | idx_notification_companies_notification_id | notification_id | × | お知らせ単位の配信先取得 |  |
| セカンダリ | idx_notification_companies_company_id | company_id | × | 企業別通知履歴 |  |
| セカンダリ | idx_notification_companies_read_time | read_time | × | 既読管理・集計 |  |

## 4. 制約・リレーション
| 種別 | 名称 | 内容 | 備考 |
|---|---|---|---|
| 外部キー | notification_id FK | `notifications.id`参照、ON DELETE CASCADE |  |
| 外部キー | company_id FK | `companies.id`参照、ON DELETE CASCADE |  |
| 外部キー | read_user_id FK | `users.id`参照（顧客）、ON DELETE SET NULL |  |
| リレーション | Notification→NotificationCompanies | 1:N、配信先単位管理 |  |
| リレーション | Company→NotificationCompanies | 1:N、企業別通知履歴 |  |
| 運用ルール | 既読判定 | `read_time`と`read_user_id`の双方で既読者・時刻を管理。 |  |

## 改定履歴
| 改定日 | 版数 | 変更概要 | 担当 |
|---|---|---|---|
| 2025-09-19 | v0.1 | 新規作成 |  |
| 2025-10-10 | v1 | フェーズ0.5のリリース版 | Codex AI |
