# NotificationFiles テーブル詳細設計

## ドキュメント目的
- `notification_files`テーブルの構造・制約を明文化し、お知らせに添付するファイルを管理する。
- 添付仕様変更時は本書とアップロード手順を更新する。

## 1. 基本情報
| 項目 | 内容 | 備考 |
|---|---|---|
| テーブル物理名 | notification_files |  |
| テーブル論理名 | お知らせ添付ファイル |  |
| ドメイン/コンテキスト | Notifications |  |
| 主キー | id（BIGINT UNSIGNED AUTO_INCREMENT） |  |

## 2. カラム定義
| No | カラム名 | 論理名 | 型 | PK | Not Null | デフォルト | 説明/業務ルール | 備考 |
|---|---|---|---|---|---|---|---|---|
| 1 | id | 添付ID | BIGINT UNSIGNED | ○ | ○ | AUTO INCREMENT | システム採番。 |  |
| 2 | notification_id | お知らせID | BIGINT UNSIGNED |  | ○ |  | `notifications.id`参照。 | ON DELETE CASCADE |
| 3 | name | ファイル名 | VARCHAR(100) |  | ○ |  | 表示名。 |  |
| 4 | url | ファイルURL | VARCHAR(255) |  | ○ |  | 外部/内部ストレージURL。 |  |
| 5 | created_at | 作成日時 | TIMESTAMP(0) |  | ○ | CURRENT_TIMESTAMP | 登録時刻。 |  |
| 6 | updated_at | 更新日時 | TIMESTAMP(0) |  | ○ | CURRENT_TIMESTAMP | Laravel標準。 | on update CURRENT_TIMESTAMP |
| 7 | deleted_at | 論理削除日時 | TIMESTAMP(0) |  |  |  | `softDeletes()`。 |  |

## 3. インデックス・キー設計
| 種別 | 名称 | 対象カラム | ユニーク | 用途/目的 | 備考 |
|---|---|---|---|---|---|
| 主キー | PRIMARY | id | ○ | レコード一意性 |  |
| 外部キー | notification_files_notification_id_foreign | notification_id | ○ | お知らせ削除時の連鎖削除 |  |
| セカンダリ | idx_notification_files_notification_id | notification_id | × | お知らせ別添付取得 |  |
| セカンダリ | idx_notification_files_name | name | × | ファイル名検索 |  |
| セカンダリ | idx_notification_files_url | url | × | URLでの逆引き |  |

## 4. 制約・リレーション
| 種別 | 名称 | 内容 | 備考 |
|---|---|---|---|
| 外部キー | notification_id FK | `notifications.id`参照、ON DELETE CASCADE |  |
| リレーション | Notification→NotificationFiles | 1:N、添付管理 |  |
| 運用ルール | URL制御 | URLは署名付きS3/外部リンクなど。期限管理はアプリ側。 |  |

## 改定履歴
| 改定日 | 版数 | 変更概要 | 担当 |
|---|---|---|---|
| 2025-09-19 | v0.1 | 新規作成 |  |
| 2025-10-10 | v1 | フェーズ0.5のリリース版 | Codex AI |