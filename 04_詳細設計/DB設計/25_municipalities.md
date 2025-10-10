# Municipalities テーブル詳細設計

## ドキュメント目的
- `municipalities`テーブルの構造・制約を明文化し、市区町村マスタの一貫した運用を行う。
- マスタ更新時は本書とデータ更新手順を併せて更新する。

## 1. 基本情報
| 項目 | 内容 | 備考 |
|---|---|---|
| テーブル物理名 | municipalities |  |
| テーブル論理名 | 市区町村マスタ |  |
| ドメイン/コンテキスト | Master |  |
| 主キー | id（BIGINT UNSIGNED AUTO_INCREMENT） |  |

## 2. カラム定義
| No | カラム名 | 論理名 | 型 | PK | Not Null | デフォルト | 説明/業務ルール | 備考 |
|---|---|---|---|---|---|---|---|---|
| 1 | id | 市区町村ID | BIGINT UNSIGNED | ○ | ○ | AUTO INCREMENT | システム採番。 |  |
| 2 | prefecture_id | 都道府県ID | BIGINT UNSIGNED |  | ○ |  | `prefectures.id`参照。 |  |
| 3 | name | 市区町村名 | VARCHAR(100) |  | ○ |  | 表示名。 |  |
| 4 | code | 市区町村コード | CHAR(6) |  |  |  | 全国地方公共団体コード等。 |  |
| 5 | kana | 市区町村名（カナ） | VARCHAR(100) |  |  |  | 読み仮名。 |  |
| 6 | created_at | 作成日時 | TIMESTAMP(0) |  |  | CURRENT_TIMESTAMP | Laravel標準。 |  |
| 7 | updated_at | 更新日時 | TIMESTAMP(0) |  |  | CURRENT_TIMESTAMP | Laravel標準。 | on update CURRENT_TIMESTAMP |

## 3. インデックス・キー設計
| 種別 | 名称 | 対象カラム | ユニーク | 用途/目的 | 備考 |
|---|---|---|---|---|---|
| 主キー | PRIMARY | id | ○ | レコード一意性 |  |
| 外部キー | municipalities_prefecture_id_foreign | prefecture_id | ○ | 都道府県マスタ参照 |  |
| セカンダリ | municipalities_prefecture_id_name_index | prefecture_id, name | × | 都道府県内での検索 |  |

## 4. 制約・リレーション
| 種別 | 名称 | 内容 | 備考 |
|---|---|---|---|
| 外部キー | municipalities_prefecture_id_foreign | `prefectures.id`参照。ON DELETE制約は未指定（デフォルトRESTRICT）。 |  |
| リレーション | Prefecture→Municipality | 1:N、住所マスタ用途 |  |
| 運用ルール | データ更新 | 総務省公開データ等を元にバッチ更新予定。 |  |

## 改定履歴
| 改定日 | 版数 | 変更概要 | 担当 |
|---|---|---|---|
| 2025-09-19 | v0.1 | 新規作成 |  |
| 2025-10-10 | v1 | フェーズ0.5のリリース版 | Codex AI |