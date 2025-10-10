# Fundings テーブル詳細設計

## ドキュメント目的
- `fundings`テーブルの構造・制約を明文化し、助成金/補助金情報の一貫した管理を行う。
- 項目変更時はスクレイピング・入力UI・連携仕様と併せて更新する。

## 1. 基本情報
| 項目 | 内容 | 備考 |
|---|---|---|
| テーブル物理名 | fundings |  |
| テーブル論理名 | 助成金マスタ |  |
| ドメイン/コンテキスト | Fundings |  |
| 主キー | id（BIGINT UNSIGNED AUTO_INCREMENT） |  |
| ユニークキー | fundings_external_funding_id_unique（external_funding_id） | 外部ID一意 |

## 2. カラム定義
| No | カラム名 | 論理名 | 型 | PK | Not Null | デフォルト | 説明/業務ルール | 備考 |
|---|---|---|---|---|---|---|---|---|
| 1 | id | 助成金ID | BIGINT UNSIGNED | ○ | ○ | AUTO INCREMENT | システム採番。 |  |
| 2 | external_funding_id | 外部助成金ID | BIGINT UNSIGNED |  | ○ |  | 外部システムID。重複禁止。 | UNIQUE |
| 3 | name | 名称 | VARCHAR(255) |  | ○ |  | 助成金の正式名称。 |  |
| 4 | subtitle1 | サブタイトル1 | VARCHAR(255) |  |  |  | 任意の補足タイトル。 |  |
| 5 | subtitle2 | サブタイトル2 | VARCHAR(255) |  |  |  | 追加補足タイトル。 |  |
| 6 | field | 分野 | VARCHAR(255) |  |  |  | 対象分野。 |  |
| 7 | area | エリア | VARCHAR(255) |  |  |  | 対象地域。 |  |
| 8 | recruitment_period | 募集時期 | TEXT |  |  |  | 長文対応の募集期間説明。 |  |
| 9 | organization | 実施機関 | VARCHAR(255) |  |  |  | 主管部署/自治体。 |  |
|10 | purpose | 目的 | TEXT |  |  |  | 制度の目的。 |  |
|11 | target_audience | 対象者 | TEXT |  |  |  | 申請対象者の条件。 |  |
|12 | support_content | 支援内容 | TEXT |  |  |  | 補助率・補助額等。 |  |
|13 | target_period | 対象期間 | TEXT |  |  |  | 対象となる期間の説明。 |  |
|14 | contact_info | 問い合わせ先 | TEXT |  |  |  | 連絡先情報。 |  |
|15 | url | 公式URL | VARCHAR(500) |  |  |  | 公式サイトリンク。 |  |
|16 | type | 種別 | VARCHAR(100) |  |  |  | 助成金/補助金などの分類。 |  |
|17 | target | 対象 | VARCHAR(500) |  |  |  | 業種/規模など。 |  |
|18 | industry | 業種 | VARCHAR(500) |  |  |  | 対象業種。 |  |
|19 | support_scale | 支援規模 | VARCHAR(500) |  |  |  | 支援上限額等。 |  |
|20 | deadline_period | 締切時期 | VARCHAR(255) |  |  |  | 受付締切情報。 |  |
|21 | public_end_date | 公募終了日 | DATE |  |  |  | 公募終了予定日。 |  |
|22 | last_updated | 最終更新日 | DATE |  |  |  | データ更新日。 |  |
|23 | is_priority_display | 優先表示フラグ | BOOLEAN |  | ○ | false | トップ掲載等の優先表示。 | インデックス有 |
|24 | created_at | 作成日時 | TIMESTAMP(0) |  |  | CURRENT_TIMESTAMP | Laravel標準。 |  |
|25 | updated_at | 更新日時 | TIMESTAMP(0) |  |  | CURRENT_TIMESTAMP | Laravel標準。 | on update CURRENT_TIMESTAMP |
|26 | deleted_at | 論理削除日時 | TIMESTAMP(0) |  |  |  | `softDeletes()`。 |  |

## 3. インデックス・キー設計
| 種別 | 名称 | 対象カラム | ユニーク | 用途/目的 | 備考 |
|---|---|---|---|---|---|
| 主キー | PRIMARY | id | ○ | レコード一意性 |  |
| ユニーク | fundings_external_funding_id_unique | external_funding_id | ○ | 外部連携IDの一意性 |  |
| セカンダリ | fundings_name_index | name | × | 名称検索 |  |
| セカンダリ | fundings_field_index | field | × | 分野別検索 |  |
| セカンダリ | fundings_area_index | area | × | エリア別検索 |  |
| セカンダリ | fundings_organization_index | organization | × | 実施機関別 |  |
| セカンダリ | fundings_type_index | type | × | 種別フィルタ |  |
| セカンダリ | fundings_target_index | target | × | 対象条件検索 |  |
| セカンダリ | fundings_industry_index | industry | × | 業種フィルタ |  |
| セカンダリ | fundings_support_scale_index | support_scale | × | 支援規模検索 |  |
| セカンダリ | fundings_deadline_period_index | deadline_period | × | 締切時期検索 |  |
| セカンダリ | fundings_public_end_date_index | public_end_date | × | 公募終了日での絞り込み |  |
| セカンダリ | fundings_last_updated_index | last_updated | × | 更新日順表示 |  |
| セカンダリ | fundings_created_at_index | created_at | × | 登録日時ソート |  |
| セカンダリ | fundings_updated_at_index | updated_at | × | 更新日時ソート |  |
| セカンダリ | fundings_deleted_at_index | deleted_at | × | 論理削除フィルタ |  |
| セカンダリ | fundings_is_priority_display_index | is_priority_display | × | 優先表示抽出 |  |

## 4. 制約・リレーション
| 種別 | 名称 | 内容 | 備考 |
|---|---|---|---|
| ユニーク | fundings_external_funding_id_unique | `external_funding_id`は重複不可 | NULLは許容されない |
| リレーション | Funding→FundingFiles/ApplicationRequests | 1:N。詳細は各テーブル参照 |  |
| 運用ルール | ソフトデリート | `deleted_at`で非表示。完全削除は別途ジョブ。 |  |

## 改定履歴
| 改定日 | 版数 | 変更概要 | 担当 |
|---|---|---|---|
| 2025-09-19 | v0.1 | 新規作成 |  |
| 2025-10-10 | v1 | フェーズ0.5のリリース版 | Codex AI |