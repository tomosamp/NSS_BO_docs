# LaborDocuments テーブル詳細設計

## ドキュメント目的
- `labor_documents`テーブルの構造・運用ルールを定義し、就業規則等の自動生成文書を統一管理する。
- 出力仕様変更時は本書と帳票設計・ファイル保管仕様を更新する。

## 1. 基本情報
| 項目 | 内容 | 備考 |
|---|---|---|
| テーブル物理名 | labor_documents |  |
| テーブル論理名 | 労務文書 | 自動生成ファイル |
| ドメイン/コンテキスト | LaborDocuments |  |
| 主キー | id（BIGINT UNSIGNED AUTO_INCREMENT） |  |

## 2. カラム定義
| No | カラム名 | 論理名 | 型 | PK | Not Null | デフォルト | 説明/業務ルール | 備考 |
|---|---|---|---|---|---|---|---|---|
| 1 | id | 文書ID | BIGINT UNSIGNED | ○ | ○ | AUTO INCREMENT | システム採番。 |  |
| 2 | company_id | 企業ID | BIGINT UNSIGNED |  | ○ |  | `companies.id`参照。 | ON DELETE CASCADE |
| 3 | employment_regulation_id | 就業規則ID | BIGINT UNSIGNED |  | ○ |  | `employment_regulations.id`参照。 | ON DELETE CASCADE |
| 4 | document_type | 文書種別 | ENUM('work_regulations','employment_contract','various_regulations') |  | ○ |  | 文書タイプ。 |  |
| 5 | template_code | テンプレコード | VARCHAR(50) |  | ○ |  | 使用テンプレ識別子。 |  |
| 6 | template_name | テンプレ名 | VARCHAR(100) |  | ○ |  | 表示名。 |  |
| 7 | file_name | 保存ファイル名 | VARCHAR(255) |  | ○ |  | ストレージ管理名。 |  |
| 8 | original_file_name | 元ファイル名 | VARCHAR(255) |  | ○ |  | ダウンロード用表示名。 |  |
| 9 | s3_url | S3 URL | VARCHAR(255) |  |  |  | S3署名付きURL等。 | 2025-07-11以降NULL可 |
|10 | s3_key | S3キー | VARCHAR(255) |  |  |  | S3オブジェクトキー。 | NULL許容 |
|11 | mime_type | MIMEタイプ | VARCHAR(255) |  | ○ | 'application/vnd.openxmlformats-officedocument.wordprocessingml.document' | 既定でWord。 |  |
|12 | file_size | ファイルサイズ | BIGINT |  |  |  | バイト単位。 |  |
|13 | generation_data | 生成データ | JSON |  |  |  | 生成時の入力値保持。 |  |
|14 | generated_at | 生成日時 | TIMESTAMP(0) |  |  |  | ファイル生成完了時刻。 |  |
|15 | created_by_type | 作成者種別 | VARCHAR(255) |  |  |  | `users`/`operators` 等。 |  |
|16 | created_by_id | 作成者ID | BIGINT UNSIGNED |  |  |  | 作成者のID。 |  |
|17 | created_at | 作成日時 | TIMESTAMP(0) |  |  | CURRENT_TIMESTAMP | Laravel標準。 |  |
|18 | updated_at | 更新日時 | TIMESTAMP(0) |  |  | CURRENT_TIMESTAMP | Laravel標準。 | on update CURRENT_TIMESTAMP |
|19 | deleted_at | 論理削除日時 | TIMESTAMP(0) |  |  |  | `softDeletes()`。 |  |

## 3. インデックス・キー設計
| 種別 | 名称 | 対象カラム | ユニーク | 用途/目的 | 備考 |
|---|---|---|---|---|---|
| 主キー | PRIMARY | id | ○ | レコード一意性 |  |
| 外部キー | labor_documents_company_id_foreign | company_id | ○ | 企業削除時の連鎖削除 |  |
| 外部キー | labor_documents_employment_regulation_id_foreign | employment_regulation_id | ○ | 規則削除時の連鎖削除 |  |
| セカンダリ | labor_documents_company_id_document_type_index | company_id, document_type | × | 企業×文書種別で抽出 |  |
| セカンダリ | labor_documents_employment_regulation_id_index | employment_regulation_id | × | 就業規則別取得 |  |
| セカンダリ | labor_documents_template_code_index | template_code | × | テンプレ別検索 |  |
| セカンダリ | labor_documents_created_by_type_created_by_id_index | created_by_type, created_by_id | × | 作成者履歴 |  |

## 4. 制約・リレーション
| 種別 | 名称 | 内容 | 備考 |
|---|---|---|---|
| 外部キー | company/employment_regulation FK | `companies`・`employment_regulations`参照、ON DELETE CASCADE |  |
| リレーション | LaborDocument→LaborDocumentEdits | 1:N（編集履歴） |  |
| リレーション | LaborDocument→LaborDocumentFiles(なし) | 添付は本テーブルで直接保持 |  |
| 運用ルール | ステータス | `status`カラムは2025-07-14に削除。状態管理は別テーブル/ジョブで対応。 |  |
| 運用ルール | S3連携 | `s3_key`が設定されている場合はS3保管、未設定時はローカル保管。 |  |

## 改定履歴
| 改定日 | 版数 | 変更概要 | 担当 |
|---|---|---|---|
| 2025-09-19 | v0.1 | 新規作成 |  |
| 2025-10-10 | v1 | フェーズ0.5のリリース版 | Codex AI |