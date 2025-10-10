# LaborDocumentEdits テーブル詳細設計

## ドキュメント目的
- `labor_document_edits`テーブルの構造・制約を明文化し、労務文書の編集履歴を追跡する。
- 履歴仕様変更時は本書と文書編集フロー資料を更新する。

## 1. 基本情報
| 項目 | 内容 | 備考 |
|---|---|---|
| テーブル物理名 | labor_document_edits |  |
| テーブル論理名 | 労務文書編集履歴 |  |
| ドメイン/コンテキスト | LaborDocuments |  |
| 主キー | id（BIGINT UNSIGNED AUTO_INCREMENT） |  |

## 2. カラム定義
| No | カラム名 | 論理名 | 型 | PK | Not Null | デフォルト | 説明/業務ルール | 備考 |
|---|---|---|---|---|---|---|---|---|
| 1 | id | 編集履歴ID | BIGINT UNSIGNED | ○ | ○ | AUTO INCREMENT | システム採番。 |  |
| 2 | original_document_id | 元文書ID | BIGINT UNSIGNED |  | ○ |  | `labor_documents.id`参照。 | ON DELETE CASCADE |
| 3 | edited_document_id | 編集後文書ID | BIGINT UNSIGNED |  | ○ |  | `labor_documents.id`参照。 | ON DELETE CASCADE |
| 4 | edited_by | 編集者ユーザーID | BIGINT UNSIGNED |  | ○ |  | `users.id`参照。 | ON DELETE CASCADE |
| 5 | created_at | 編集日時 | TIMESTAMP(0) |  | ○ | CURRENT_TIMESTAMP | 編集日時。 |  |
| 6 | updated_at | 更新日時 | TIMESTAMP(0) |  | ○ | CURRENT_TIMESTAMP | Laravel標準。 | on update CURRENT_TIMESTAMP |

## 3. インデックス・キー設計
| 種別 | 名称 | 対象カラム | ユニーク | 用途/目的 | 備考 |
|---|---|---|---|---|---|
| 主キー | PRIMARY | id | ○ | レコード一意性 |  |
| 外部キー | labor_document_edits_original_document_id_foreign | original_document_id | ○ | 元文書削除時連鎖削除 |  |
| 外部キー | labor_document_edits_edited_document_id_foreign | edited_document_id | ○ | 編集後文書削除時連鎖削除 |  |
| 外部キー | labor_document_edits_edited_by_foreign | edited_by | ○ | ユーザー削除時連鎖削除 |  |
| セカンダリ | labor_document_edits_original_document_id_index | original_document_id | × | 元文書別履歴取得 |  |
| セカンダリ | labor_document_edits_edited_document_id_index | edited_document_id | × | 新文書から逆引き |  |
| セカンダリ | labor_document_edits_edited_by_index | edited_by | × | 編集者別履歴 |  |
| セカンダリ | labor_document_edits_created_at_index | created_at | × | 時系列抽出 |  |

## 4. 制約・リレーション
| 種別 | 名称 | 内容 | 備考 |
|---|---|---|---|
| 外部キー | original/edited_document FK | `labor_documents.id`参照、ON DELETE CASCADE |  |
| 外部キー | edited_by FK | `users.id`参照、ON DELETE CASCADE |  |
| リレーション | LaborDocument→LaborDocumentEdits | 1:N、編集履歴連鎖 |  |
| 運用ルール | 履歴整合性 | 編集後文書にも`labor_documents`の実体が存在すること。 |  |

## 改定履歴
| 改定日 | 版数 | 変更概要 | 担当 |
|---|---|---|---|
| 2025-09-19 | v0.1 | 新規作成 |  |
| 2025-10-10 | v1 | フェーズ0.5のリリース版 | Codex AI |