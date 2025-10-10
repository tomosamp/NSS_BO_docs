# ChatMessageMentions テーブル詳細設計

## ドキュメント目的
- `chat_message_mentions`テーブルの構造を明文化し、チャットのメンション機能を安定運用する。
- 仕様変更時はチャット関連ドキュメントと併せて更新する。

## 1. 基本情報
| 項目 | 内容 | 備考 |
|---|---|---|
| テーブル物理名 | chat_message_mentions |  |
| テーブル論理名 | チャットメンション |  |
| ドメイン/コンテキスト | Chat |  |
| 主キー | id（BIGINT UNSIGNED AUTO_INCREMENT） |  |

## 2. カラム定義
| No | カラム名 | 論理名 | 型 | PK | Not Null | デフォルト | 説明/業務ルール | 備考 |
|---|---|---|---|---|---|---|---|---|
| 1 | id | メンションID | BIGINT UNSIGNED | ○ | ○ | AUTO INCREMENT | システム採番。 |  |
| 2 | message_id | メッセージID | BIGINT UNSIGNED |  | ○ |  | `chat_messages.id`参照。 | ON DELETE CASCADE |
| 3 | mentioned_user_id | 参照ユーザーID | BIGINT UNSIGNED |  | ○ |  | メンション対象のID。 | polymorphic |
| 4 | mentioned_user_type | 参照ユーザー種別 | VARCHAR(50) |  | ○ |  | ユーザー種別（例: users/operators）。 |  |
| 5 | start_position | 開始位置 | INT |  | ○ |  | メンション開始文字位置（0基点）。 |  |
| 6 | end_position | 終了位置 | INT |  | ○ |  | メンション終了位置。 |  |
| 7 | mention_text | 表示テキスト | VARCHAR(255) |  | ○ |  | 表示用の@文字列等。 |  |
| 8 | created_at | 作成日時 | TIMESTAMP(0) |  | ○ | CURRENT_TIMESTAMP | Laravel標準。 |  |
| 9 | updated_at | 更新日時 | TIMESTAMP(0) |  | ○ | CURRENT_TIMESTAMP | Laravel標準。 | on update CURRENT_TIMESTAMP |

## 3. インデックス・キー設計
| 種別 | 名称 | 対象カラム | ユニーク | 用途/目的 | 備考 |
|---|---|---|---|---|---|
| 主キー | PRIMARY | id | ○ | レコード一意性 |  |
| 外部キー | chat_message_mentions_message_id_foreign | message_id | ○ | メッセージ削除時の連鎖削除 |  |
| セカンダリ | cmm_message_id_idx | message_id | × | メッセージ別メンション取得 |  |
| セカンダリ | cmm_user_type_idx | mentioned_user_id, mentioned_user_type | × | ユーザーへの通知抽出 |  |
| セカンダリ | cmm_created_at_idx | created_at | × | 作成日時順検索 |  |
| ユニーク | cmm_unique_mention | message_id, mentioned_user_id, mentioned_user_type, start_position | ○ | 同一メッセージ内の重複排除 |  |

## 4. 制約・リレーション
| 種別 | 名称 | 内容 | 備考 |
|---|---|---|---|
| 外部キー | chat_message_mentions_message_id_foreign | `chat_messages.id`参照、ON DELETE CASCADE |  |
| リレーション | ChatMessage→ChatMessageMentions | 1:N、メンション位置管理 |  |
| 業務ルール | start/end位置 | リッチテキスト中のUTF-8ベース位置を保持。編集時に再計算。 |  |

## 改定履歴
| 改定日 | 版数 | 変更概要 | 担当 |
|---|---|---|---|
| 2025-09-19 | v0.1 | 新規作成 |  |
| 2025-10-10 | v1 | フェーズ0.5のリリース版 | Codex AI |