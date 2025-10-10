# FundingFiles テーブル詳細設計

## ドキュメント目的
- `funding_files`テーブルの構造・制約を定義し、助成金関連ファイル（募集要項等）の管理を一元化する。
- ファイル仕様変更時は本書とアップロード仕様を更新する。

## 1. 基本情報
| 項目 | 内容 | 備考 |
|---|---|---|
| テーブル物理名 | funding_files |  |
| テーブル論理名 | 助成金ファイル |  |
| ドメイン/コンテキスト | Fundings |  |
| 主キー | id（BIGINT UNSIGNED AUTO_INCREMENT） |  |

## 2. カラム定義
| No | カラム名 | 論理名 | 型 | PK | Not Null | デフォルト | 説明/業務ルール | 備考 |
|---|---|---|---|---|---|---|---|---|
| 1 | id | ファイルID | BIGINT UNSIGNED | ○ | ○ | AUTO INCREMENT | システム採番。 |  |
| 2 | funding_id | 助成金ID | BIGINT UNSIGNED |  | ○ |  | `fundings.id`参照。 | ON DELETE CASCADE |
| 3 | file_name | 保存ファイル名 | VARCHAR(255) |  | ○ |  | ストレージ管理名。 |  |
| 4 | original_name | 元ファイル名 | VARCHAR(255) |  | ○ |  | ユーザーアップロード名。 |  |
| 5 | file_path | ファイルパス | VARCHAR(500) |  | ○ |  | ストレージ上のパス。 |  |
| 6 | file_type | 種別 | VARCHAR(50) |  |  |  | 書類種別（PDFなど）。 |  |
| 7 | mime_type | MIMEタイプ | VARCHAR(100) |  |  |  | Content-Type。 |  |
| 8 | file_size | ファイルサイズ | BIGINT |  |  |  | バイト単位。 |  |
| 9 | category | カテゴリ | VARCHAR(100) |  |  |  | 募集要項/申請書等。 |  |
|10 | description | 説明 | TEXT |  |  |  | ファイル説明文。 |  |
|11 | sort_order | 表示順 | INT |  | ○ | 0 | 一覧表示順。小さいほど先頭。 |  |
|12 | is_public | 公開フラグ | BOOLEAN |  | ○ | true | 一般公開可否。 |  |
|13 | created_at | 作成日時 | TIMESTAMP(0) |  | ○ | CURRENT_TIMESTAMP | Laravel標準。 |  |
|14 | updated_at | 更新日時 | TIMESTAMP(0) |  | ○ | CURRENT_TIMESTAMP | Laravel標準。 | on update CURRENT_TIMESTAMP |
|15 | deleted_at | 論理削除日時 | TIMESTAMP(0) |  |  |  | `softDeletes()`。 |  |

## 3. インデックス・キー設計
| 種別 | 名称 | 対象カラム | ユニーク | 用途/目的 | 備考 |
|---|---|---|---|---|---|
| 主キー | PRIMARY | id | ○ | レコード一意性 |  |
| 外部キー | funding_files_funding_id_foreign | funding_id | ○ | 助成金削除時の連鎖削除 |  |
| セカンダリ | funding_files_funding_id_index | funding_id | × | 助成金別取得 |  |
| セカンダリ | funding_files_file_type_index | file_type | × | 種別検索 |  |
| セカンダリ | funding_files_category_index | category | × | カテゴリ別抽出 |  |
| セカンダリ | funding_files_is_public_index | is_public | × | 公開/非公開フィルタ |  |
| セカンダリ | funding_files_sort_order_index | sort_order | × | 表示順ソート |  |
| セカンダリ | funding_files_created_at_index | created_at | × | 登録日時検索 |  |
| セカンダリ | funding_files_deleted_at_index | deleted_at | × | 論理削除フィルタ |  |
| 複合 | funding_files_funding_id_category_index | funding_id, category | × | 助成金×カテゴリの絞込 |  |
| 複合 | funding_files_funding_id_sort_order_index | funding_id, sort_order | × | 表示順取得 |  |
| 複合 | funding_files_funding_id_is_public_index | funding_id, is_public | × | 公開状態別取得 |  |

## 4. 制約・リレーション
| 種別 | 名称 | 内容 | 備考 |
|---|---|---|---|
| 外部キー | funding_files_funding_id_foreign | `fundings.id`参照、ON DELETE CASCADE |  |
| リレーション | Funding→FundingFiles | 1:N、助成金ごとの添付管理 |  |
| 運用ルール | is_public | falseの場合は一般一覧非表示。管理画面のみ閲覧。 |  |

## 改定履歴
| 改定日 | 版数 | 変更概要 | 担当 |
|---|---|---|---|
| 2025-09-19 | v0.1 | 新規作成 |  |
| 2025-10-10 | v1 | フェーズ0.5のリリース版 | Codex AI |