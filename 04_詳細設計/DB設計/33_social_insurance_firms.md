# SocialInsuranceFirms テーブル詳細設計

## ドキュメント目的
- `social_insurance_firms`テーブルの構造・制約を明文化し、連携する社労士法人情報を管理する。
- 項目追加や料金体系変更時は本書と連携フローを更新する。

## 1. 基本情報
| 項目 | 内容 | 備考 |
|---|---|---|
| テーブル物理名 | social_insurance_firms |  |
| テーブル論理名 | 社労士法人 |  |
| ドメイン/コンテキスト | ExternalPartners |  |
| 主キー | id（BIGINT UNSIGNED AUTO_INCREMENT） |  |

## 2. カラム定義
| No | カラム名 | 論理名 | 型 | PK | Not Null | デフォルト | 説明/業務ルール | 備考 |
|---|---|---|---|---|---|---|---|---|
| 1 | id | 法人ID | BIGINT UNSIGNED | ○ | ○ | AUTO INCREMENT | システム採番。 |  |
| 2 | name | 法人名 | VARCHAR(255) |  | ○ |  | 表示名。 | インデックスあり |
| 3 | description | 説明 | TEXT |  |  |  | 紹介文。 |  |
| 4 | base_fee | 基本料金 | DECIMAL(10,2) |  |  |  | 円単位。税込/税別は別途明記。 |  |
| 5 | success_fee_rate | 成功報酬率(%) | DECIMAL(5,2) |  |  |  | パーセンテージ。 |  |
| 6 | website_url | 公式サイトURL | VARCHAR(500) |  |  |  | Webサイトリンク。 |  |
| 7 | terms_url | 申込約款URL | VARCHAR(500) |  |  |  | 約款参照先。 |  |
| 8 | contact_email | 連絡先メール | VARCHAR(255) |  |  |  | 問い合わせメール。 |  |
| 9 | contact_phone | 連絡先電話番号 | VARCHAR(50) |  |  |  | 代表電話。 |  |
|10 | is_active | 有効フラグ | BOOLEAN |  | ○ | true | 連携中 = true。 | インデックスあり |
|11 | created_at | 作成日時 | TIMESTAMP(0) |  |  | CURRENT_TIMESTAMP | 登録日時。 |  |
|12 | updated_at | 更新日時 | TIMESTAMP(0) |  |  | CURRENT_TIMESTAMP | Laravel標準。 | on update CURRENT_TIMESTAMP |

## 3. インデックス・キー設計
| 種別 | 名称 | 対象カラム | ユニーク | 用途/目的 | 備考 |
|---|---|---|---|---|---|
| 主キー | PRIMARY | id | ○ | レコード一意性 |  |
| セカンダリ | social_insurance_firms_name_index | name | × | 名称検索 |  |
| セカンダリ | social_insurance_firms_is_active_index | is_active | × | 稼働中法人の抽出 |  |

## 4. 制約・リレーション
| 種別 | 名称 | 内容 | 備考 |
|---|---|---|---|
| リレーション | SocialInsuranceFirm→ApplicationRequests | 1:N。助成金紹介依頼と連携 |  |
| 運用ルール | is_active | falseの場合はマッチング対象外。 |  |

## 改定履歴
| 改定日 | 版数 | 変更概要 | 担当 |
|---|---|---|---|
| 2025-09-19 | v0.1 | 新規作成 |  |
| 2025-10-10 | v1 | フェーズ0.5のリリース版 | Codex AI |