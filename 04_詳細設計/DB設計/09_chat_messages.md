# ChatMessages テーブル詳細設計

## ドキュメント目的
- `chat_messages`テーブルの構造・制約を整理し、チャットメッセージ管理の共通基盤とする。
- 仕様変更時はチャット機能の設計資料と併せて更新する。

## 1. 基本情報
| 項目 | 内容 | 備考 |
|---|---|---|
| テーブル物理名 | chat_messages |  |
| テーブル論理名 | チャットメッセージ |  |
| ドメイン/コンテキスト | Chat |  |
| 主キー | id（BIGINT UNSIGNED AUTO_INCREMENT） |  |

## 2. カラム定義
| No | カラム名 | 論理名 | 型 | PK | Not Null | デフォルト | 説明/業務ルール | 備考 |
|---|---|---|---|---|---|---|---|---|
| 1 | id | メッセージID | BIGINT UNSIGNED | ○ | ○ | AUTO INCREMENT | システム採番。 |  |
| 2 | chat_room_id | チャットルームID | BIGINT UNSIGNED |  | ○ |  | `chat_rooms.id`参照。 | ON DELETE CASCADE |
| 3 | sender_id | 送信者ID | BIGINT UNSIGNED |  | ○ |  | 送信主体のID。 | 多態 |
| 4 | sender_type | 送信者種別 | VARCHAR(50) |  | ○ |  | 例: `users`, `operators`。 |  |
| 5 | content | メッセージ本文 | TEXT |  |  |  | リッチテキスト。添付のみの場合null可。 |  |
| 6 | created_at | 作成日時 | TIMESTAMP(0) |  |  | CURRENT_TIMESTAMP | 送信日時。 |  |
| 7 | updated_at | 更新日時 | TIMESTAMP(0) |  |  | CURRENT_TIMESTAMP | 編集時に更新。 | on update CURRENT_TIMESTAMP |
| 8 | deleted_at | 論理削除日時 | TIMESTAMP(0) |  |  |  | `softDeletes()`による削除フラグ。 |  |

## 3. インデックス・キー設計
| 種別 | 名称 | 対象カラム | ユニーク | 用途/目的 | 備考 |
|---|---|---|---|---|---|
| 主キー | PRIMARY | id | ○ | レコード一意性 |  |
| 外部キー | chat_messages_chat_room_id_foreign | chat_room_id | ○ | ルーム削除時の連鎖削除 |  |
| セカンダリ | chat_messages_chat_room_id_created_at_index | chat_room_id, created_at | × | 時系列メッセージ取得 |  |
| セカンダリ | chat_messages_sender_id_sender_type_index | sender_id, sender_type | × | 送信者別抽出/監査 |  |
| フルテキスト | chat_messages_content_fulltext | content | × | 本文検索 | MySQL InnoDB FT |

## 4. 制約・リレーション
| 種別 | 名称 | 内容 | 備考 |
|---|---|---|---|
| 外部キー | chat_messages_chat_room_id_foreign | `chat_rooms.id`参照、ON DELETE CASCADE |  |
| リレーション | ChatRoom→ChatMessages | 1:N、チャット履歴 |  |
| リレーション | ChatMessage→ChatMessageFiles/Reads/Mentions | 1:N。詳細は各テーブル参照 |  |
| 運用ルール | 論理削除 | `deleted_at`でメッセージ非表示。完全削除は別途。 |  |

## 改定履歴
| 改定日 | 版数 | 変更概要 | 担当 |
|---|---|---|---|
| 2025-09-19 | v0.1 | 新規作成 |  |
| 2025-10-10 | v1 | フェーズ0.5のリリース版 | Codex AI |