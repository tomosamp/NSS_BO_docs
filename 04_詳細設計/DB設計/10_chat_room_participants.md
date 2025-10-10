# ChatRoomParticipants テーブル詳細設計

## ドキュメント目的
- `chat_room_participants`テーブルの構造・制約を明文化し、チャットルーム参加者管理を統一する。
- 参加者ロール仕様の変更時には本書を更新する。

## 1. 基本情報
| 項目 | 内容 | 備考 |
|---|---|---|
| テーブル物理名 | chat_room_participants |  |
| テーブル論理名 | チャット参加者 |  |
| ドメイン/コンテキスト | Chat |  |
| 主キー | id（BIGINT UNSIGNED AUTO_INCREMENT） |  |

## 2. カラム定義
| No | カラム名 | 論理名 | 型 | PK | Not Null | デフォルト | 説明/業務ルール | 備考 |
|---|---|---|---|---|---|---|---|---|
| 1 | id | 参加者ID | BIGINT UNSIGNED | ○ | ○ | AUTO INCREMENT | システム採番。 |  |
| 2 | chat_room_id | チャットルームID | BIGINT UNSIGNED |  | ○ |  | `chat_rooms.id`参照。 | ON DELETE CASCADE |
| 3 | participant_id | 参加主体ID | BIGINT UNSIGNED |  | ○ |  | 顧客/運営者等のID。 |  |
| 4 | participant_type | 参加者種別 | VARCHAR(50) |  | ○ |  | 例: `users`, `operators`。 |  |
| 5 | role | 参加ロール | TINYINT UNSIGNED |  | ○ | 1 | 1=顧客、2=運営等。 | デフォルト顧客 |
| 6 | created_at | 参加日時 | TIMESTAMP(0) |  | ○ | CURRENT_TIMESTAMP | Laravel標準。 |  |
| 7 | updated_at | 更新日時 | TIMESTAMP(0) |  | ○ | CURRENT_TIMESTAMP | Laravel標準。 | on update CURRENT_TIMESTAMP |
| 8 | deleted_at | 退室日時 | TIMESTAMP(0) |  |  |  | 論理削除で退室扱い。 |  |

## 3. インデックス・キー設計
| 種別 | 名称 | 対象カラム | ユニーク | 用途/目的 | 備考 |
|---|---|---|---|---|---|
| 主キー | PRIMARY | id | ○ | レコード一意性 |  |
| 外部キー | chat_room_participants_chat_room_id_foreign | chat_room_id | ○ | ルーム削除時の連鎖削除 |  |
| ユニーク | uk_chat_room_participants | chat_room_id, participant_id, participant_type | ○ | 参加者の重複登録防止 |  |
| セカンダリ | crp_participant_idx | participant_id, participant_type | × | 参加者単位の検索 |  |
| セカンダリ | crp_room_id_idx | chat_room_id | × | ルーム内参加者取得 |  |
| セカンダリ | crp_role_idx | role | × | ロール別抽出 |  |

## 4. 制約・リレーション
| 種別 | 名称 | 内容 | 備考 |
|---|---|---|---|
| 外部キー | chat_room_participants_chat_room_id_foreign | `chat_rooms.id`参照、ON DELETE CASCADE |  |
| リレーション | ChatRoom→Participants | 1:N、ルーム参加者管理 |  |
| 業務ルール | role値 | 1=顧客、2=運営、追加時はマスタ管理。 |  |
| 運用 | 論理削除 | `deleted_at`で退室管理。復帰時は再挿入。 |  |

## 改定履歴
| 改定日 | 版数 | 変更概要 | 担当 |
|---|---|---|---|
| 2025-09-19 | v0.1 | 新規作成 |  |
| 2025-10-10 | v1 | フェーズ0.5のリリース版 | Codex AI |
