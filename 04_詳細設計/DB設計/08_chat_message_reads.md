# ChatMessageReads テーブル詳細設計

## ドキュメント目的
- `chat_message_reads`テーブルの構造・制約を定義し、チャット既読管理の一貫性を確保する。
- 既読ロジック変更時は本書と関連仕様を更新する。

## 1. 基本情報
| 項目 | 内容 | 備考 |
|---|---|---|
| テーブル物理名 | chat_message_reads |  |
| テーブル論理名 | チャット既読 |  |
| ドメイン/コンテキスト | Chat |  |
| 主キー | id（BIGINT UNSIGNED AUTO_INCREMENT） |  |

## 2. カラム定義
| No | カラム名 | 論理名 | 型 | PK | Not Null | デフォルト | 説明/業務ルール | 備考 |
|---|---|---|---|---|---|---|---|---|
| 1 | id | 既読ID | BIGINT UNSIGNED | ○ | ○ | AUTO INCREMENT | システム採番。 |  |
| 2 | chat_message_id | メッセージID | BIGINT UNSIGNED |  | ○ |  | `chat_messages.id`参照。 | ON DELETE CASCADE |
| 3 | user_id | 参照者ID | BIGINT UNSIGNED |  | ○ |  | 既読ユーザーのID。 | polymorphic |
| 4 | user_type | 参照者種別 | VARCHAR(50) |  | ○ |  | ユーザー種別識別子。 |  |
| 5 | read_at | 既読日時 | TIMESTAMP(0) |  | ○ | CURRENT_TIMESTAMP | 既読記録時点。 |  |
| 6 | created_at | 作成日時 | TIMESTAMP(0) |  | ○ | CURRENT_TIMESTAMP | Laravel標準。 |  |
| 7 | updated_at | 更新日時 | TIMESTAMP(0) |  | ○ | CURRENT_TIMESTAMP | Laravel標準。 | on update CURRENT_TIMESTAMP |

## 3. インデックス・キー設計
| 種別 | 名称 | 対象カラム | ユニーク | 用途/目的 | 備考 |
|---|---|---|---|---|---|
| 主キー | PRIMARY | id | ○ | レコード一意性 |  |
| 外部キー | chat_message_reads_chat_message_id_foreign | chat_message_id | ○ | メッセージ削除時に連鎖削除 |  |
| ユニーク | uk_chat_message_reads_unique | chat_message_id, user_id, user_type | ○ | 同一メッセージでの二重既読防止 | start_position不要に調整済 |
| セカンダリ | cmr_user_read_idx | user_id, user_type, read_at | × | ユーザー別未読/既読確認 |  |
| セカンダリ | cmr_message_read_idx | chat_message_id, read_at | × | メッセージの既読一覧 |  |
| セカンダリ | cmr_read_at_idx | read_at | × | 時間帯別集計 |  |

## 4. 制約・リレーション
| 種別 | 名称 | 内容 | 備考 |
|---|---|---|---|
| 外部キー | chat_message_reads_chat_message_id_foreign | `chat_messages.id`参照、ON DELETE CASCADE |  |
| リレーション | ChatMessage→ChatMessageReads | 1:N、メッセージ毎の既読状況 |  |
| 業務ルール | 多態ユーザー | `user_type`でユーザー種別を識別。参照先の整合性はアプリ側で担保。 |  |

## 改定履歴
| 改定日 | 版数 | 変更概要 | 担当 |
|---|---|---|---|
| 2025-09-19 | v0.1 | 新規作成 |  |
| 2025-10-10 | v1 | フェーズ0.5のリリース版 | Codex AI |