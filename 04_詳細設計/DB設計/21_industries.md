# Industries テーブル詳細設計

## ドキュメント目的
- `industries`テーブルの構造・制約を定義し、業種マスタの一貫した管理を行う。
- 業種体系変更時は本書と関連マスタ設計を更新する。

## 1. 基本情報
| 項目 | 内容 | 備考 |
|---|---|---|
| テーブル物理名 | industries |  |
| テーブル論理名 | 業種マスタ |  |
| ドメイン/コンテキスト | Master |  |
| 主キー | id（BIGINT UNSIGNED AUTO_INCREMENT） |  |

## 2. カラム定義
| No | カラム名 | 論理名 | 型 | PK | Not Null | デフォルト | 説明/業務ルール | 備考 |
|---|---|---|---|---|---|---|---|---|
| 1 | id | 業種ID | BIGINT UNSIGNED | ○ | ○ | AUTO INCREMENT | システム採番。 |  |
| 2 | industry_name | 業種名 | VARCHAR(100) |  | ○ |  | 表示業種名。 |  |
| 3 | created_at | 作成日時 | TIMESTAMP(0) |  |  | CURRENT_TIMESTAMP | Laravel標準。 |  |
| 4 | updated_at | 更新日時 | TIMESTAMP(0) |  |  | CURRENT_TIMESTAMP | Laravel標準。 | on update CURRENT_TIMESTAMP |
| 5 | deleted_at | 論理削除日時 | TIMESTAMP(0) |  |  |  | `softDeletes()`。 |  |

## 3. インデックス・キー設計
| 種別 | 名称 | 対象カラム | ユニーク | 用途/目的 | 備考 |
|---|---|---|---|---|---|
| 主キー | PRIMARY | id | ○ | レコード一意性 |  |

## 4. 制約・リレーション
| 種別 | 名称 | 内容 | 備考 |
|---|---|---|---|
| リレーション | Industry→CompanyIndustries | 1:N。企業との関連は中間テーブル管理 |  |
| 運用ルール | 表示順 | 別途マスタ設定。必要に応じてソート用カラム追加検討。 |  |

## 改定履歴
| 改定日 | 版数 | 変更概要 | 担当 |
|---|---|---|---|
| 2025-09-19 | v0.1 | 新規作成 |  |
| 2025-10-10 | v1 | フェーズ0.5のリリース版 | Codex AI |