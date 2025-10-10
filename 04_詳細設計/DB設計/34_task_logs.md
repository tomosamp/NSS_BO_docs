# TaskLogs テーブル詳細設計

## ドキュメント目的
- `task_logs`テーブルの構造・制約を定義し、タスクの履歴コメントを一元管理する。
- ログ仕様変更時は本書と業務フロー資料を更新する。

## 1. 基本情報
| 項目 | 内容 | 備考 |
|---|---|---|
| テーブル物理名 | task_logs |  |
| テーブル論理名 | タスクログ |  |
| ドメイン/コンテキスト | Tasks |  |
| 主キー | id（BIGINT UNSIGNED AUTO_INCREMENT） |  |

## 2. カラム定義
| No | カラム名 | 論理名 | 型 | PK | Not Null | デフォルト | 説明/業務ルール | 備考 |
|---|---|---|---|---|---|---|---|---|
| 1 | id | ログID | BIGINT UNSIGNED | ○ | ○ | AUTO INCREMENT | システム採番。 |  |
| 2 | task_id | タスクID | BIGINT UNSIGNED |  | ○ |  | `tasks.id`参照。 | ON DELETE CASCADE |
| 3 | operator_id | 記録者ID | BIGINT UNSIGNED |  | ○ |  | `operators.id`参照。 | ON DELETE CASCADE |
| 4 | content | ログ内容 | TEXT |  | ○ |  | コメント・進捗内容。 |  |
| 5 | created_at | 登録日時 | TIMESTAMP(0) |  | ○ | CURRENT_TIMESTAMP | 投稿時刻。 |  |
| 6 | updated_at | 更新日時 | TIMESTAMP(0) |  | ○ | CURRENT_TIMESTAMP | Laravel標準。 | on update CURRENT_TIMESTAMP |
| 7 | deleted_at | 論理削除日時 | TIMESTAMP(0) |  |  |  | `softDeletes()`。 |  |

## 3. インデックス・キー設計
| 種別 | 名称 | 対象カラム | ユニーク | 用途/目的 | 備考 |
|---|---|---|---|---|---|
| 主キー | PRIMARY | id | ○ | レコード一意性 |  |
| 外部キー | task_logs_task_id_foreign | task_id | ○ | タスク削除時連鎖削除 |  |
| 外部キー | task_logs_operator_id_foreign | operator_id | ○ | オペレーター削除時連鎖削除 |  |
| セカンダリ | task_logs_task_id_index | task_id | × | タスク別ログ表示 |  |
| セカンダリ | task_logs_operator_id_index | operator_id | × | 記録者別抽出 |  |
| セカンダリ | task_logs_created_at_index | created_at | × | 時系列取得 |  |

## 4. 制約・リレーション
| 種別 | 名称 | 内容 | 備考 |
|---|---|---|---|
| 外部キー | task_id/ operator_id FK | `tasks.id`・`operators.id`参照、ON DELETE CASCADE |  |
| リレーション | Task→TaskLogs | 1:N、対応履歴 |  |
| 運用ルール | 論理削除 | `deleted_at`で非表示扱い。監査上は履歴保持。 |  |

## 改定履歴
| 改定日 | 版数 | 変更概要 | 担当 |
|---|---|---|---|
| 2025-09-19 | v0.1 | 新規作成 |  |
| 2025-10-10 | v1 | フェーズ0.5のリリース版 | Codex AI |