# TaskManagers テーブル詳細設計

## ドキュメント目的
- `task_managers`テーブルの構造・制約を明文化し、タスクの担当者割り当てを管理する。
- 担当者ロジック変更時は本書と業務運用ルールを更新する。

## 1. 基本情報
| 項目 | 内容 | 備考 |
|---|---|---|
| テーブル物理名 | task_managers |  |
| テーブル論理名 | タスク担当者 |  |
| ドメイン/コンテキスト | Tasks |  |
| 主キー | id（BIGINT UNSIGNED AUTO_INCREMENT） |  |

## 2. カラム定義
| No | カラム名 | 論理名 | 型 | PK | Not Null | デフォルト | 説明/業務ルール | 備考 |
|---|---|---|---|---|---|---|---|---|
| 1 | id | 担当ID | BIGINT UNSIGNED | ○ | ○ | AUTO INCREMENT | システム採番。 |  |
| 2 | task_id | タスクID | BIGINT UNSIGNED |  | ○ |  | `tasks.id`参照。 | ON DELETE CASCADE |
| 3 | status | 担当区分 | TINYINT |  | ○ |  | 1=主担当、2=サブ担当。 |  |
| 4 | manager_id | 担当者ID | BIGINT UNSIGNED |  | ○ |  | `operators.id`参照。 | ON DELETE CASCADE |
| 5 | created_at | 登録日時 | TIMESTAMP(0) |  |  | CURRENT_TIMESTAMP | 割り当て日時。 |  |
| 6 | updated_at | 更新日時 | TIMESTAMP(0) |  |  | CURRENT_TIMESTAMP | Laravel標準。 | on update CURRENT_TIMESTAMP |
| 7 | deleted_at | 論理削除日時 | TIMESTAMP(0) |  |  |  | `softDeletes()`。 |  |

## 3. インデックス・キー設計
| 種別 | 名称 | 対象カラム | ユニーク | 用途/目的 | 備考 |
|---|---|---|---|---|---|
| 主キー | PRIMARY | id | ○ | レコード一意性 |  |
| 外部キー | task_managers_task_id_foreign | task_id | ○ | タスク削除時連鎖削除 |  |
| 外部キー | task_managers_manager_id_foreign | manager_id | ○ | オペレーター削除時連鎖削除 |  |
| セカンダリ | task_managers_task_id_index | task_id | × | タスク別担当者取得 |  |
| セカンダリ | task_managers_manager_id_index | manager_id | × | オペレーター別担当一覧 |  |
| セカンダリ | task_managers_status_index | status | × | 主/サブ担当での抽出 |  |
| 複合 | task_managers_task_id_status_index | task_id, status | × | 役割別フィルタ |  |

## 4. 制約・リレーション
| 種別 | 名称 | 内容 | 備考 |
|---|---|---|---|
| 外部キー | task_id/manager_id FK | `tasks.id`・`operators.id`参照、ON DELETE CASCADE |  |
| リレーション | Task→TaskManagers | 1:N、担当者割当 |  |
| 運用ルール | ソフトデリート | `deleted_at`で割当解除。再割当時は新規レコード。 |  |
| 業務ルール | status値 | 1=主担当, 2=サブ担当。追加値が必要な場合は要合意。 |  |

## 改定履歴
| 改定日 | 版数 | 変更概要 | 担当 |
|---|---|---|---|
| 2025-09-19 | v0.1 | 新規作成 |  |
| 2025-10-10 | v1 | フェーズ0.5のリリース版 | Codex AI |