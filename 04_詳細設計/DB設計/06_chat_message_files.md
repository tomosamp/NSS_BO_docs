# ChatMessageFiles テーブル詳細設計

## ドキュメント目的
- `chat_message_files`テーブルの構造・制約を明確化し、チャット添付ファイル管理を統一する。
- ストレージ方式変更時は本書と関連ドキュメントを更新する。

## 1. 基本情報
| 項目 | 内容 | 備考 |
|---|---|---|
| テーブル物理名 | chat_message_files |  |
| テーブル論理名 | チャット添付ファイル |  |
| ドメイン/コンテキスト | Chat |  |
| 主キー | id（BIGINT UNSIGNED AUTO_INCREMENT） |  |

## 2. カラム定義
| No | カラム名 | 論理名 | 型 | PK | Not Null | デフォルト | 説明/業務ルール | 備考 |
|---|---|---|---|---|---|---|---|---|
| 1 | id | 添付ID | BIGINT UNSIGNED | ○ | ○ | AUTO INCREMENT | システム採番。 |  |
| 2 | chat_message_id | メッセージID | BIGINT UNSIGNED |  | ○ |  | `chat_messages.id`参照。 | ON DELETE CASCADE |
| 3 | file_name | 保存ファイル名 | VARCHAR(255) |  | ○ |  | ストレージ上のファイル名。 |  |
| 4 | original_name | 元ファイル名 | VARCHAR(255) |  | ○ |  | アップロード時の元ファイル名。 |  |
| 5 | file_path | 物理パス | VARCHAR(255) |  |  |  | 従来ローカル保存時のパス。S3移行でnull許容。 | 2025-07-29よりNULL可 |
| 6 | s3_key | S3キー | VARCHAR(500) |  |  |  | S3保管時のキー。 |  |
| 7 | access_token | ダウンロードトークン | VARCHAR(100) |  |  |  | 有効期限付きアクセス制御。 |  |
| 8 | expires_at | トークン失効日時 | TIMESTAMP(0) |  |  |  | `access_token`有効期限。 |  |
| 9 | file_type | 種別 | VARCHAR(50) |  |  |  | アプリ分類（画像/文書等）。 |  |
|10 | mime_type | MIMEタイプ | VARCHAR(100) |  | ○ |  | Content-Type。 |  |
|11 | file_size | ファイルサイズ | BIGINT |  | ○ |  | バイト単位。 |  |
|12 | created_at | 作成日時 | TIMESTAMP(0) |  | ○ | CURRENT_TIMESTAMP | Laravel標準。 |  |
|13 | updated_at | 更新日時 | TIMESTAMP(0) |  | ○ | CURRENT_TIMESTAMP | Laravel標準。 | on update CURRENT_TIMESTAMP |
|14 | deleted_at | 論理削除日時 | TIMESTAMP(0) |  |  |  | `softDeletes()`利用。 |  |

## 3. インデックス・キー設計
| 種別 | 名称 | 対象カラム | ユニーク | 用途/目的 | 備考 |
|---|---|---|---|---|---|
| 主キー | PRIMARY | id | ○ | レコード一意性 |  |
| 外部キー | chat_message_files_chat_message_id_foreign | chat_message_id | ○ | メッセージ削除時の連鎖削除 |  |
| セカンダリ | chat_message_files_chat_message_id_index | chat_message_id | × | メッセージ単位の取得 |  |
| セカンダリ | chat_message_files_file_type_index | file_type | × | 種別検索 |  |
| セカンダリ | chat_message_files_mime_type_index | mime_type | × | MIME別抽出 |  |
| セカンダリ | chat_message_files_file_size_index | file_size | × | サイズ順ソート |  |
| セカンダリ | chat_message_files_s3_key_index | s3_key | × | S3キー参照 |  |
| セカンダリ | chat_message_files_access_token_expires_at_index | access_token, expires_at | × | トークン検証高速化 |  |

## 4. 制約・リレーション
| 種別 | 名称 | 内容 | 備考 |
|---|---|---|---|
| 外部キー | chat_message_files_chat_message_id_foreign | `chat_messages.id`参照、ON DELETE CASCADE |  |
| リレーション | ChatMessage→ChatMessageFiles | 1:N、メッセージ毎の添付管理 |  |
| 運用ルール | ソフトデリート | `deleted_at`を用いた論理削除。バックアップ保持方針に沿う。 |  |

## 改定履歴
| 改定日 | 版数 | 変更概要 | 担当 |
|---|---|---|---|
| 2025-09-19 | v0.1 | 新規作成 |  |
| 2025-10-10 | v1 | フェーズ0.5のリリース版 | Codex AI |
