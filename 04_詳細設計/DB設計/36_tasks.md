# Tasks テーブル詳細設計

## ドキュメント目的
- `tasks`テーブルの構造・制約を明文化し、業務タスク管理機能の基盤とする。
- 項目変更時は本書とタスク運用ガイドを更新する。

## 1. 基本情報
| 項目 | 内容 | 備考 |
|---|---|---|
| テーブル物理名 | tasks |  |
| テーブル論理名 | タスク |  |
| ドメイン/コンテキスト | Tasks |  |
| 主キー | id（BIGINT UNSIGNED AUTO_INCREMENT） |  |

## 2. カラム定義
| No | カラム名 | 論理名 | 型 | PK | Not Null | デフォルト | 説明/業務ルール | 備考 |
|---|---|---|---|---|---|---|---|---|
| 1 | id | タスクID | BIGINT UNSIGNED | ○ | ○ | AUTO INCREMENT | システム採番。 |  |
| 2 | company_id | 企業ID | BIGINT UNSIGNED |  | ○ |  | `companies.id`参照。 | ON DELETE CASCADE |
| 3 | category | カテゴリ | TINYINT |  | ○ |  | タスク区分。定義は別表。 |  |
| 4 | name | タスク名 | VARCHAR(500) |  | ○ |  | 2025-08-06に500文字へ拡張。 |  |
| 5 | status | ステータス | TINYINT |  | ○ |  | 進行状態。値定義は別表。 |  |
| 6 | limit_date | 期限日 | DATE |  |  |  | 期限未設定時はNULL。 |  |
| 7 | taskable_id | 関連ID | BIGINT UNSIGNED |  | ○ |  | ポリモーフィック先のID。 |  |
| 8 | taskable_type | 関連タイプ | VARCHAR(255) |  | ○ |  | ポリモーフィック先クラス。 |  |
| 9 | created_at | 作成日時 | TIMESTAMP(0) |  |  | CURRENT_TIMESTAMP | 登録日時。 |  |
|10 | updated_at | 更新日時 | TIMESTAMP(0) |  |  | CURRENT_TIMESTAMP | Laravel標準。 | on update CURRENT_TIMESTAMP |
|11 | deleted_at | 論理削除日時 | TIMESTAMP(0) |  |  |  | `softDeletes()`。 |  |

## 3. インデックス・キー設計
| 種別 | 名称 | 対象カラム | ユニーク | 用途/目的 | 備考 |
|---|---|---|---|---|---|
| 主キー | PRIMARY | id | ○ | レコード一意性 |  |
| 外部キー | tasks_company_id_foreign | company_id | ○ | 企業削除時連鎖削除 |  |
| セカンダリ | tasks_company_id_index | company_id | × | 企業別タスク一覧 |  |
| セカンダリ | tasks_taskable_id_taskable_type_index | taskable_id, taskable_type | × | ポリモーフィック先単位の取得 |  |
| セカンダリ | tasks_category_index | category | × | カテゴリ別抽出 |  |
| セカンダリ | tasks_status_index | status | × | ステータス別抽出 |  |
| セカンダリ | tasks_limit_date_index | limit_date | × | 期限管理 |  |

## 4. 制約・リレーション
| 種別 | 名称 | 内容 | 備考 |
|---|---|---|---|
| 外部キー | tasks_company_id_foreign | `companies.id`参照、ON DELETE CASCADE |  |
| リレーション | Task→TaskManagers/TaskLogs | 1:N。担当者・履歴管理 |  |
| リレーション | Task→taskable | ポリモーフィック。targetクラスにより動的紐付け。 |  |
| 運用ルール | 論理削除 | `deleted_at`でアーカイブ。完全削除は別途。 |  |

## 改定履歴
| 改定日 | 版数 | 変更概要 | 担当 |
|---|---|---|---|
| 2025-09-19 | v0.1 | 新規作成 |  |
| 2025-10-10 | v1 | フェーズ0.5のリリース版 | Codex AI |