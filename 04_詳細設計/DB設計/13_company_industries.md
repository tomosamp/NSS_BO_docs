# CompanyIndustries テーブル詳細設計

## ドキュメント目的
- `company_industries`テーブルの構造・制約を明記し、企業と業種の関連付けを管理する。
- 業種マスタ変更時は本書と業務ルールを更新する。

## 1. 基本情報
| 項目 | 内容 | 備考 |
|---|---|---|
| テーブル物理名 | company_industries |  |
| テーブル論理名 | 企業-業種関連 | 多対多中間テーブル |
| ドメイン/コンテキスト | Company |  |
| 主キー | id（BIGINT UNSIGNED AUTO_INCREMENT） |  |

## 2. カラム定義
| No | カラム名 | 論理名 | 型 | PK | Not Null | デフォルト | 説明/業務ルール | 備考 |
|---|---|---|---|---|---|---|---|---|
| 1 | id | 連番ID | BIGINT UNSIGNED | ○ | ○ | AUTO INCREMENT | 技術的PK。 |  |
| 2 | company_id | 企業ID | BIGINT UNSIGNED |  | ○ |  | `companies.id`参照。 | ON DELETE CASCADE |
| 3 | industry_id | 業種ID | BIGINT UNSIGNED |  | ○ |  | `industries.id`参照。 | ON DELETE CASCADE |
| 4 | created_at | 登録日時 | TIMESTAMP(0) |  |  | CURRENT_TIMESTAMP | Laravel標準。 |  |
| 5 | updated_at | 更新日時 | TIMESTAMP(0) |  |  | CURRENT_TIMESTAMP | Laravel標準。 |  |
| 6 | deleted_at | 論理削除日時 | TIMESTAMP(0) |  |  |  | `softDeletes()`。 |  |

## 3. インデックス・キー設計
| 種別 | 名称 | 対象カラム | ユニーク | 用途/目的 | 備考 |
|---|---|---|---|---|---|
| 主キー | PRIMARY | id | ○ | レコード一意性 |  |
| 外部キー | company_industries_company_id_foreign | company_id | ○ | 企業削除時に連鎖削除 |  |
| 外部キー | company_industries_industry_id_foreign | industry_id | ○ | 業種削除時に連鎖削除 |  |
| （必要に応じ） | - | company_id, industry_id | × | 組合せ重複はアプリ側で防止 | ユニーク制約が必要なら別途検討 |

## 4. 制約・リレーション
| 種別 | 名称 | 内容 | 備考 |
|---|---|---|---|
| 外部キー | company_industries_company_id_foreign | `companies.id`参照、ON DELETE CASCADE |  |
| 外部キー | company_industries_industry_id_foreign | `industries.id`参照、ON DELETE CASCADE |  |
| リレーション | Company↔Industry | 多対多中間。重複防止はビジネスロジックで制御。 |  |

## 改定履歴
| 改定日 | 版数 | 変更概要 | 担当 |
|---|---|---|---|
| 2025-09-19 | v0.1 | 新規作成 |  |
| 2025-10-10 | v1 | フェーズ0.5のリリース版 | Codex AI |