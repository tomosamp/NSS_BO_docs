# CompanyLogs テーブル詳細設計

## ドキュメント目的
- `company_logs`テーブルの構造と運用ルールを定義し、企業に対する対応履歴を記録する。
- ログ仕様変更時は本書と業務フロー資料を更新する。

## 1. 基本情報
| 項目 | 内容 | 備考 |
|---|---|---|
| テーブル物理名 | company_logs |  |
| テーブル論理名 | 企業対応ログ |  |
| ドメイン/コンテキスト | Company |  |
| 主キー | id（BIGINT UNSIGNED AUTO_INCREMENT） |  |

## 2. カラム定義
| No | カラム名 | 論理名 | 型 | PK | Not Null | デフォルト | 説明/業務ルール | 備考 |
|---|---|---|---|---|---|---|---|---|
| 1 | id | ログID | BIGINT UNSIGNED | ○ | ○ | AUTO INCREMENT | システム採番。 |  |
| 2 | company_id | 企業ID | BIGINT UNSIGNED |  | ○ |  | 対応対象の企業。 | FK未設定（将来設定検討） |
| 3 | operator_id | オペレーターID | BIGINT UNSIGNED |  | ○ |  | 対応した運営者。 | FK未設定 |
| 4 | content | 対応内容 | TEXT |  | ○ |  | 実施内容の記録。 |  |
| 5 | created_at | 記録日時 | TIMESTAMP(0) |  |  | CURRENT_TIMESTAMP | 登録日時。 |  |
| 6 | updated_at | 更新日時 | TIMESTAMP(0) |  |  | CURRENT_TIMESTAMP | 編集時更新。 |  |
| 7 | deleted_at | 論理削除日時 | TIMESTAMP(0) |  |  |  | `softDeletes()`による論理削除。 |  |

## 3. インデックス・キー設計
| 種別 | 名称 | 対象カラム | ユニーク | 用途/目的 | 備考 |
|---|---|---|---|---|---|
| 主キー | PRIMARY | id | ○ | レコード一意性 |  |
| （追加検討） | company_logs_company_id_index | company_id | × | 企業別検索用途 | 必要に応じ作成 |
| （追加検討） | company_logs_operator_id_index | operator_id | × | オペレーター別抽出 | 必要に応じ作成 |

## 4. 制約・リレーション
| 種別 | 名称 | 内容 | 備考 |
|---|---|---|---|
| リレーション | Company→CompanyLogs | 1:N（論理的）。外部キー未設定のためアプリで整合性担保。 |  |
| リレーション | Operator→CompanyLogs | 1:N（論理的）。外部キー未設定。 |  |
| 運用ルール | ソフトデリート | `deleted_at`で非表示。完全削除は監査要件に従う。 |  |

## 改定履歴
| 改定日 | 版数 | 変更概要 | 担当 |
|---|---|---|---|
| 2025-09-19 | v0.1 | 新規作成 |  |
| 2025-10-10 | v1 | フェーズ0.5のリリース版 | Codex AI |