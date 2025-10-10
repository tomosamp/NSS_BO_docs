# Prefectures テーブル詳細設計

## ドキュメント目的
- `prefectures`テーブルの構造・運用ルールを定義し、都道府県マスタを統一管理する。
- マスタ更新時は本書とデータ更新手順を同時に見直す。

## 1. 基本情報
| 項目 | 内容 | 備考 |
|---|---|---|
| テーブル物理名 | prefectures |  |
| テーブル論理名 | 都道府県マスタ |  |
| ドメイン/コンテキスト | Master |  |
| 主キー | id（BIGINT UNSIGNED AUTO_INCREMENT） |  |

## 2. カラム定義
| No | カラム名 | 論理名 | 型 | PK | Not Null | デフォルト | 説明/業務ルール | 備考 |
|---|---|---|---|---|---|---|---|---|
| 1 | id | 都道府県ID | BIGINT UNSIGNED | ○ | ○ | AUTO INCREMENT | システム採番。 |  |
| 2 | name | 都道府県名 | VARCHAR(100) |  | ○ |  | 表示名。 |  |
| 3 | created_at | 作成日時 | TIMESTAMP(0) |  | ○ | CURRENT_TIMESTAMP | Laravel標準。 |  |
| 4 | updated_at | 更新日時 | TIMESTAMP(0) |  | ○ | CURRENT_TIMESTAMP | Laravel標準。 | on update CURRENT_TIMESTAMP |

## 3. インデックス・キー設計
| 種別 | 名称 | 対象カラム | ユニーク | 用途/目的 | 備考 |
|---|---|---|---|---|---|
| 主キー | PRIMARY | id | ○ | レコード一意性 |  |

## 4. 制約・リレーション
| 種別 | 名称 | 内容 | 備考 |
|---|---|---|---|
| リレーション | Prefecture→Companies/Municipalities | 1:N。住所情報の親マスタ |  |
| 運用ルール | マスタ管理 | データは固定。必要な場合のみ追加・修正。 |  |

## 改定履歴
| 改定日 | 版数 | 変更概要 | 担当 |
|---|---|---|---|
| 2025-09-19 | v0.1 | 新規作成 |  |
| 2025-10-10 | v1 | フェーズ0.5のリリース版 | Codex AI |