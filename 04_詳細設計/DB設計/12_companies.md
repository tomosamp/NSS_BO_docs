# Companies テーブル詳細設計

## ドキュメント目的
- `companies`テーブルの構造・制約・運用ルールを明文化し、企業マスタの一貫した管理を行う。
- 項目追加・変更時は本書と関連ドメイン設計を更新する。

## 1. 基本情報
| 項目 | 内容 | 備考 |
|---|---|---|
| テーブル物理名 | companies |  |
| テーブル論理名 | 企業マスタ |  |
| ドメイン/コンテキスト | Company |  |
| 主キー | id（BIGINT UNSIGNED AUTO_INCREMENT） |  |

## 2. カラム定義
| No | カラム名 | 論理名 | 型 | PK | Not Null | デフォルト | 説明/業務ルール | 備考 |
|---|---|---|---|---|---|---|---|---|
| 1 | id | 企業ID | BIGINT UNSIGNED | ○ | ○ | AUTO INCREMENT | システム採番。 |  |
| 2 | company_name | 企業名 | VARCHAR(100) |  | ○ |  | 正式法人名。 |  |
| 3 | representative_last_name | 代表者姓 | VARCHAR(100) |  | ○ |  | 法人代表の姓。 |  |
| 4 | representative_first_name | 代表者名 | VARCHAR(100) |  | ○ |  | 法人代表の名。 |  |
| 5 | representative_last_name_kana | 代表者姓カナ | VARCHAR(100) |  | ○ |  | 全角カナ。 |  |
| 6 | representative_first_name_kana | 代表者名カナ | VARCHAR(100) |  | ○ |  | 全角カナ。 |  |
| 7 | email | 代表メール | VARCHAR(100) |  | ○ |  | 代表問い合わせ窓口。ユニーク制約なし。 | 必要なら別途制約検討 |
| 8 | employee_count | 従業員数 | INT |  | ○ |  | 最新の従業員数。 |  |
| 9 | postal_code | 郵便番号 | VARCHAR(7) |  | ○ |  | ハイフン無し7桁。 |  |
|10 | prefecture_id | 都道府県ID | BIGINT UNSIGNED |  | ○ |  | `prefectures.id`参照。 |  |
|11 | city | 市区町村 | VARCHAR(100) |  | ○ |  | 市区町村名。 |  |
|12 | address_line_1 | 市区町村以降 | VARCHAR(100) |  | ○ |  | 以降の住所。 |  |
|13 | created_at | 作成日時 | TIMESTAMP(0) |  |  | CURRENT_TIMESTAMP | 登録日時。 |  |
|14 | updated_at | 更新日時 | TIMESTAMP(0) |  |  | CURRENT_TIMESTAMP | 更新日時。 | on update CURRENT_TIMESTAMP |
|15 | deleted_at | 論理削除日時 | TIMESTAMP(0) |  |  |  | `softDeletes()`による論理削除。 |  |

## 3. インデックス・キー設計
| 種別 | 名称 | 対象カラム | ユニーク | 用途/目的 | 備考 |
|---|---|---|---|---|---|
| 主キー | PRIMARY | id | ○ | レコード一意性 |  |
| 外部キー | companies_prefecture_id_foreign | prefecture_id | ○ | 都道府県削除時に連鎖削除 |  |

## 4. 制約・リレーション
| 種別 | 名称 | 内容 | 備考 |
|---|---|---|---|
| 外部キー | companies_prefecture_id_foreign | `prefectures.id`参照、ON DELETE CASCADE |  |
| リレーション | Company→Customers/Operators等 | 1:N。顧客・案件と紐付く基本マスタ |  |
| 運用ルール | 論理削除 | `deleted_at`で非表示。完全削除は業務要件協議。 |  |

## 改定履歴
| 改定日 | 版数 | 変更概要 | 担当 |
|---|---|---|---|
| 2025-09-19 | v0.1 | 新規作成 |  |
| 2025-10-10 | v1 | フェーズ0.5のリリース版 | Codex AI |
