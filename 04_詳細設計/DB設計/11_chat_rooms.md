# ChatRooms テーブル詳細設計

## ドキュメント目的
- `chat_rooms`テーブルの構造・制約を明文化し、チャットルーム管理の基盤とする。
- ルーム仕様変更時は本書と関連設計資料を更新する。

## 1. 基本情報
| 項目 | 内容 | 備考 |
|---|---|---|
| テーブル物理名 | chat_rooms |  |
| テーブル論理名 | チャットルーム |  |
| ドメイン/コンテキスト | Chat |  |
| 主キー | id（BIGINT UNSIGNED AUTO_INCREMENT） |  |

## 2. カラム定義
| No | カラム名 | 論理名 | 型 | PK | Not Null | デフォルト | 説明/業務ルール | 備考 |
|---|---|---|---|---|---|---|---|---|
| 1 | id | ルームID | BIGINT UNSIGNED | ○ | ○ | AUTO INCREMENT | システム採番。 |  |
| 2 | company_id | 企業ID | BIGINT UNSIGNED |  | ○ |  | `companies.id`参照。 | ON DELETE CASCADE |
| 3 | name | ルーム名 | VARCHAR(255) |  | ○ |  | 表示名。 |  |
| 4 | created_at | 作成日時 | TIMESTAMP(0) |  | ○ | CURRENT_TIMESTAMP | ルーム作成日時。 |  |
| 5 | updated_at | 更新日時 | TIMESTAMP(0) |  | ○ | CURRENT_TIMESTAMP | 最終更新日時。 | on update CURRENT_TIMESTAMP |
| 6 | deleted_at | 論理削除日時 | TIMESTAMP(0) |  |  |  | `softDeletes()`による退避。 |  |

## 3. インデックス・キー設計
| 種別 | 名称 | 対象カラム | ユニーク | 用途/目的 | 備考 |
|---|---|---|---|---|---|
| 主キー | PRIMARY | id | ○ | レコード一意性 |  |
| 外部キー | chat_rooms_company_id_foreign | company_id | ○ | 企業削除時の連鎖削除 |  |
| セカンダリ | chat_rooms_company_id_index | company_id | × | 企業別ルーム一覧 |  |
| セカンダリ | chat_rooms_created_at_index | created_at | × | 作成日時ソート |  |
| セカンダリ | chat_rooms_deleted_at_index | deleted_at | × | 論理削除フィルタ |  |

## 4. 制約・リレーション
| 種別 | 名称 | 内容 | 備考 |
|---|---|---|---|
| 外部キー | chat_rooms_company_id_foreign | `companies.id`参照、ON DELETE CASCADE |  |
| リレーション | Company→ChatRooms | 1:N、企業別ルーム管理 |  |
| リレーション | ChatRoom→Participants/Messages | 1:N、参加者・メッセージ管理 |  |
| 運用 | 論理削除 | `deleted_at`利用。実削除は別途ジョブで対応。 |  |

## 改定履歴
| 改定日 | 版数 | 変更概要 | 担当 |
|---|---|---|---|
| 2025-09-19 | v0.1 | 新規作成 |  |
| 2025-10-10 | v1 | フェーズ0.5のリリース版 | Codex AI |