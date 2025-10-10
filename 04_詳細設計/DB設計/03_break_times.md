# BreakTimes テーブル詳細設計

## ドキュメント目的
- `break_times`テーブルのスキーマ・制約を明記し、就業規則の休憩時間管理で統一して利用する。
- 休憩時間ロジックを変更する場合は本書と就業規則関連ドキュメントを更新する。

## 1. 基本情報
| 項目 | 内容 | 備考 |
|---|---|---|
| テーブル物理名 | break_times |  |
| テーブル論理名 | 休憩時間設定 | 就業規則1件につき複数行 |
| ドメイン/コンテキスト | EmploymentRegulations |  |
| 主キー | id（BIGINT UNSIGNED AUTO_INCREMENT） |  |

## 2. カラム定義
| No | カラム名 | 論理名 | 型 | PK | Not Null | デフォルト | 説明/業務ルール | 備考 |
|---|---|---|---|---|---|---|---|---|
| 1 | id | 休憩時間ID | BIGINT UNSIGNED | ○ | ○ | AUTO INCREMENT | システム採番。 |  |
| 2 | employment_regulation_id | 就業規則ID | BIGINT UNSIGNED |  | ○ |  | `employment_regulations.id`参照。 | ON DELETE CASCADE |
| 3 | break_start_hour | 開始時（時間） | TINYINT UNSIGNED |  | ○ |  | 0–23。休憩開始時間（時）。 | CHECK相当をアプリで担保 |
| 4 | break_start_minute | 開始時（分） | TINYINT UNSIGNED |  | ○ |  | 0–59。 |  |
| 5 | break_end_hour | 終了時（時間） | TINYINT UNSIGNED |  | ○ |  | 0–23。 |  |
| 6 | break_end_minute | 終了時（分） | TINYINT UNSIGNED |  | ○ |  | 0–59。 |  |
| 7 | display_order | 表示順 | TINYINT UNSIGNED |  | ○ | 1 | 同一就業規則内での表示順。 | 初期値1 |
| 8 | created_at | 作成日時 | TIMESTAMP(0) |  |  | CURRENT_TIMESTAMP | Laravel標準。 |  |
| 9 | updated_at | 更新日時 | TIMESTAMP(0) |  |  | CURRENT_TIMESTAMP | Laravel標準。 | on update CURRENT_TIMESTAMP |

## 3. インデックス・キー設計
| 種別 | 名称 | 対象カラム | ユニーク | 用途/目的 | 備考 |
|---|---|---|---|---|---|
| 主キー | PRIMARY | id | ○ | レコード一意性 |  |
| 外部キー | break_times_employment_regulation_id_foreign | employment_regulation_id | ○ | 親就業規則削除時に連鎖削除 |  |
| セカンダリ | idx_employment_regulation_id | employment_regulation_id | × | 親IDによる取得 |  |
| セカンダリ | idx_employment_regulation_display_order | employment_regulation_id, display_order | × | 表示順付き取得 |  |

## 4. 制約・リレーション
| 種別 | 名称 | 内容 | 備考 |
|---|---|---|---|
| 外部キー | break_times_employment_regulation_id_foreign | `employment_regulations.id`参照、ON DELETE CASCADE/ON UPDATE CASCADE |  |
| リレーション | EmploymentRegulation→BreakTimes | 1:N、休憩時間スロットを複数保持 |  |
| 入力制約 | 時刻値バリデーション | 0–23,0–59の範囲チェックはアプリ/DB制約で実施 |  |

## 改定履歴
| 改定日 | 版数 | 変更概要 | 担当 |
|---|---|---|---|
| 2025-09-19 | v0.1 | 新規作成 |  |
| 2025-10-10 | v1 | フェーズ0.5のリリース版 | Codex AI |