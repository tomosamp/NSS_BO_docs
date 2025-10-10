# EmploymentRegulations テーブル詳細設計

## ドキュメント目的
- `employment_regulations`テーブルの詳細仕様を明文化し、就業規則入力・文書生成機能の共通基盤とする。
- カラム追加・削除時は本書、画面設計、帳票仕様を同時に更新する。

## 1. 基本情報
| 項目 | 内容 | 備考 |
|---|---|---|
| テーブル物理名 | employment_regulations |  |
| テーブル論理名 | 就業規則 | 企業ごとの労務設定 |
| ドメイン/コンテキスト | EmploymentRegulations |  |
| 主キー | id（BIGINT UNSIGNED AUTO_INCREMENT） |  |
| ユニークキー | idx_company_id（company_id） | 1企業1レコード |

## 2. カラム定義
| No | カラム名 | 論理名 | 型 | PK | Not Null | デフォルト | 説明/業務ルール | 備考 |
|---|---|---|---|---|---|---|---|---|
| 1 | id | 就業規則ID | BIGINT UNSIGNED | ○ | ○ | AUTO INCREMENT | システム採番。 |  |
| 2 | company_id | 企業ID | BIGINT UNSIGNED |  | ○ |  | `companies.id`参照。 | ON DELETE CASCADE |
| 3 | regular_employee_salary_system | 正社員給与体系 | ENUM('monthly','daily','hourly') |  |  |  | 正社員の基本給与形態。 | null=未設定 |
| 4 | part_time_salary_system | パート給与体系 | ENUM('monthly','daily','hourly') |  |  |  | パート・アルバイトの給与形態。 |  |
| 5 | fixed_term_contract_period | 有期契約期間（月） | INT UNSIGNED |  |  |  | 有期契約社員の契約期間。1–60を推奨。 | CHECK存在 |
| 6 | fixed_term_employee_salary_system | 有期契約社員給与体系 | ENUM('monthly','daily','hourly') |  |  |  | 有期契約社員の給与形態。 |  |
| 7 | part_time_contract_period | パート契約期間（月） | INT UNSIGNED |  |  |  | パートタイマー契約期間。1–60推奨。 |  |
| 8 | has_permanent_employee | 無期雇用社員有無 | BOOLEAN |  |  |  | 無期雇用社員を採用しているか。 |  |
| 9 | permanent_employee_salary_system | 無期雇用社員給与体系 | ENUM('monthly','daily','hourly') |  |  |  | 無期雇用社員の給与形態。 |  |
|10 | has_part_time_regular_employee | 短時間正社員有無 | BOOLEAN |  |  |  | 短時間正社員制度の有無。 |  |
|11 | part_time_regular_employee_salary_system | 短時間正社員給与体系 | ENUM('monthly','daily','hourly') |  |  |  | 短時間正社員の給与形態。 |  |
|12 | has_contract_employee | 嘱託社員有無 | BOOLEAN |  |  |  | 嘱託社員制度の有無。 |  |
|13 | probation_period | 試用期間（月） | INT UNSIGNED |  |  |  | 1–12推奨。 | CHECK有 |
|14 | weekly_working_hours | 週間労働時間 | INT UNSIGNED |  |  |  | 所定週労働時間。1–60推奨。 |  |
|15 | daily_working_hours | 1日労働時間 | INT UNSIGNED |  |  |  | 所定1日労働時間。1–24推奨。 |  |
|16 | start_time_hour | 始業時（時） | TINYINT UNSIGNED |  |  |  | 0–23。 | CHECK有 |
|17 | start_time_minute | 始業時（分） | TINYINT UNSIGNED |  |  |  | 0–59。 | CHECK有 |
|18 | end_time_hour | 終業時（時） | TINYINT UNSIGNED |  |  |  | 0–23。 | CHECK有 |
|19 | end_time_minute | 終業時（分） | TINYINT UNSIGNED |  |  |  | 0–59。 | CHECK有 |
|20 | total_break_minutes | 休憩合計（分） | SMALLINT UNSIGNED |  |  |  | 休憩総時間。 |  |
|21 | holidays | 休日 | VARCHAR(255) |  |  |  | 法定休日・所定休日の説明。 |  |
|22 | has_one_month_variable_working_hours | 1か月変形労働制 | BOOLEAN |  |  |  | 導入有無。 |  |
|23 | one_month_variable_start_day | 1か月変形起算日 | TINYINT UNSIGNED |  |  |  | 1–31。 | CHECK有 |
|24 | has_one_year_variable_working_hours | 1年変形労働制 | BOOLEAN |  |  |  | 導入有無。 |  |
|25 | one_year_variable_start_month | 1年変形起算月 | TINYINT UNSIGNED |  |  |  | 1–12。 | CHECK有 |
|26 | one_year_variable_start_day | 1年変形起算日 | TINYINT UNSIGNED |  |  |  | 1–31。 | CHECK有 |
|27 | has_half_day_paid_leave | 半日有給可否 | BOOLEAN |  |  |  | 半日単位取得可否。 |  |
|28 | half_day_morning_start_hour | 午前有給開始時 | TINYINT UNSIGNED |  |  |  | 0–23。 | CHECK有 |
|29 | half_day_morning_start_minute | 午前有給開始分 | TINYINT UNSIGNED |  |  |  | 0–59。 |  |
|30 | half_day_morning_end_hour | 午前有給終了時 | TINYINT UNSIGNED |  |  |  | 0–23。 |  |
|31 | half_day_morning_end_minute | 午前有給終了分 | TINYINT UNSIGNED |  |  |  | 0–59。 |  |
|32 | half_day_afternoon_start_hour | 午後有給開始時 | TINYINT UNSIGNED |  |  |  | 0–23。 |  |
|33 | half_day_afternoon_start_minute | 午後有給開始分 | TINYINT UNSIGNED |  |  |  | 0–59。 |  |
|34 | half_day_afternoon_end_hour | 午後有給終了時 | TINYINT UNSIGNED |  |  |  | 0–23。 |  |
|35 | half_day_afternoon_end_minute | 午後有給終了分 | TINYINT UNSIGNED |  |  |  | 0–59。 |  |
|36 | has_hourly_paid_leave | 時間単位有給可否 | BOOLEAN |  |  |  | 時間単位付与の有無。 |  |
|37 | congratulatory_leave_marriage | 慶弔休暇（本人結婚） | TINYINT UNSIGNED |  |  |  | 日数。1–20推奨。 | CHECK有 |
|38 | congratulatory_leave_birth | 慶弔休暇（出産） | TINYINT UNSIGNED |  |  |  | 日数。1–20推奨。 |  |
|39 | bereavement_leave_immediate_family | 忌引（近親） | TINYINT UNSIGNED |  |  |  | 日数。1–20推奨。 |  |
|40 | bereavement_leave_extended_family | 忌引（親族） | TINYINT UNSIGNED |  |  |  | 日数。1–20推奨。 |  |
|41 | has_retirement_money | 退職金制度有無 | BOOLEAN |  |  |  | 退職金の支給有無。 |  |
|42 | retirement_money_years | 退職金要件年数 | TINYINT UNSIGNED |  |  |  | 1–100。 | CHECK有 |
|43 | has_mandatory_retirement | 定年制度有無 | BOOLEAN |  |  |  | 定年の有無。 |  |
|44 | mandatory_retirement_age | 定年年齢 | TINYINT UNSIGNED |  |  |  | 50–100。 | CHECK有 |
|45 | has_reemployment | 再雇用制度有無 | BOOLEAN |  |  |  | 定年後再雇用の有無。 |  |
|46 | reemployment_age | 再雇用年齢 | TINYINT UNSIGNED |  |  |  | 60–85。 | CHECK: chk_reemployment_age |
|47 | has_mutual_aid_retirement_allowance | 中退共加入有無 | BOOLEAN |  |  |  | 中退共制度の採用有無。 |  |
|48 | mutual_aid_employee_contribution | 中退共従業員掛金 | INT UNSIGNED |  |  |  | 円単位。0以上。 | CHECK有 |
|49 | has_commute_allowance | 通勤手当有無 | BOOLEAN |  |  |  | 通勤手当支給の有無。 |  |
|50 | commute_allowance_limit | 通勤手当上限額 | INT UNSIGNED |  |  |  | 円単位。0以上。 | CHECK有 |
|51 | has_fixed_overtime_allowance | 固定残業手当有無 | BOOLEAN |  |  |  | 固定残業手当の採用有無。 | 旧has_other_allowance |
|52 | other_allowances | その他手当詳細 | TEXT |  |  |  | 手当内容の自由記述。 |  |
|53 | salary_calculation_start_date | 賃金計算起算日 | VARCHAR(100) |  |  |  | 例: 毎月1日。 |  |
|54 | salary_closing_date | 賃金締め日 | VARCHAR(100) |  |  |  | 例: 毎月末日。 |  |
|55 | salary_payment_date | 賃金支払日 | VARCHAR(100) |  |  |  | 例: 翌月25日。 |  |
|56 | salary_increase | 昇給 | ENUM('yes','no','irregular') |  |  |  | 昇給有無。 |  |
|57 | has_bonus | 賞与有無 | BOOLEAN |  |  |  | 賞与制度の有無。 |  |
|58 | has_summer_bonus | 夏季賞与有無 | BOOLEAN |  |  |  | 夏季賞与支給か。 |  |
|59 | bonus_summer_start_month | 夏季賞与開始月 | TINYINT UNSIGNED |  |  |  | 1–12。 | CHECK有 |
|60 | bonus_summer_start_day | 夏季賞与開始日 | TINYINT UNSIGNED |  |  |  | 1–31。 | CHECK有 |
|61 | bonus_summer_end_month | 夏季賞与終了月 | TINYINT UNSIGNED |  |  |  | 1–12。 | CHECK有 |
|62 | bonus_summer_end_day | 夏季賞与終了日 | TINYINT UNSIGNED |  |  |  | 1–31。 | CHECK有 |
|63 | has_winter_bonus | 冬季賞与有無 | BOOLEAN |  |  |  | 冬季賞与支給か。 |  |
|64 | bonus_winter_start_month | 冬季賞与開始月 | TINYINT UNSIGNED |  |  |  | 1–12。 | CHECK有 |
|65 | bonus_winter_start_day | 冬季賞与開始日 | TINYINT UNSIGNED |  |  |  | 1–31。 | CHECK有 |
|66 | bonus_winter_end_month | 冬季賞与終了月 | TINYINT UNSIGNED |  |  |  | 1–12。 | CHECK有 |
|67 | bonus_winter_end_day | 冬季賞与終了日 | TINYINT UNSIGNED |  |  |  | 1–31。 | CHECK有 |
|68 | registration_status | 登録ステータス | ENUM('draft','completed') |  | ○ | 'draft' | 入力完了状態の管理。 |  |
|69 | created_at | 作成日時 | TIMESTAMP(0) |  |  | CURRENT_TIMESTAMP | Laravel標準。 |  |
|70 | updated_at | 更新日時 | TIMESTAMP(0) |  |  | CURRENT_TIMESTAMP | Laravel標準。 | on update CURRENT_TIMESTAMP |

## 3. インデックス・キー設計
| 種別 | 名称 | 対象カラム | ユニーク | 用途/目的 | 備考 |
|---|---|---|---|---|---|
| 主キー | PRIMARY | id | ○ | レコード一意性 |  |
| ユニーク | idx_company_id | company_id | ○ | 企業ごとに1レコードを保証 |  |
| 外部キー | employment_regulations_company_id_foreign | company_id | ○ | 企業削除時の連鎖削除 |  |

## 4. 制約・リレーション
| 種別 | 名称 | 内容 | 備考 |
|---|---|---|---|
| 外部キー | employment_regulations_company_id_foreign | `companies.id`参照、ON DELETE CASCADE/ON UPDATE CASCADE |  |
| CHECK | chk_start_time_hour 等 | 時刻系（start/end/half_day）を0–23、分を0–59に制限 | MySQLで追加済 |
| CHECK | chk_fixed_term_contract_period 等 | 契約/休暇日数を範囲制限（1–60、1–20など） | 詳細はマイグレーション参照 |
| CHECK | chk_reemployment_age | `reemployment_age`を60–85に制限 | 2025-09-19追加 |
| リレーション | Company→EmploymentRegulations | 1:1。企業の就業規則本体 |  |
| リレーション | EmploymentRegulations→BreakTimes | 1:N。休憩時間は別テーブルで管理 |  |
| 運用ルール | 草案管理 | `registration_status='draft'`は未完。`completed`で文書生成対象。 |  |

## 改定履歴
| 改定日 | 版数 | 変更概要 | 担当 |
|---|---|---|---|
| 2025-09-19 | v0.1 | 新規作成 |  |
| 2025-10-10 | v1 | フェーズ0.5のリリース版 | Codex AI |