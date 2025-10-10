# Operators テーブル詳細設計

## ドキュメント目的
- `operators`テーブルの構造・制約を定義し、運営者アカウントの管理方針を統一する。
- 項目変更時は本書と認証・権限仕様を更新する。

## 1. 基本情報
| 項目 | 内容 | 備考 |
|---|---|---|
| テーブル物理名 | operators |  |
| テーブル論理名 | 運営者 |  |
| ドメイン/コンテキスト | Operator |  |
| 主キー | id（BIGINT UNSIGNED AUTO_INCREMENT） |  |

## 2. カラム定義
| No | カラム名 | 論理名 | 型 | PK | Not Null | デフォルト | 説明/業務ルール | 備考 |
|---|---|---|---|---|---|---|---|---|
| 1 | id | 運営者ID | BIGINT UNSIGNED | ○ | ○ | AUTO INCREMENT | システム採番。 |  |
| 2 | email | メールアドレス | VARCHAR(100) |  | ○ |  | 一意制約あり。ログインID。 |  |
| 3 | password | パスワードハッシュ | VARCHAR(255) |  |  |  | 2025-07-07以降NULL可（未設定対応）。 |  |
| 4 | last_name | 姓 | VARCHAR(50) |  |  |  | 氏名（漢字）。NULL許容。 |  |
| 5 | first_name | 名 | VARCHAR(50) |  |  |  | 氏名（漢字）。NULL許容。 |  |
| 6 | last_name_kana | セイ | VARCHAR(50) |  |  |  | 全角カナ。 |  |
| 7 | first_name_kana | メイ | VARCHAR(50) |  |  |  | 全角カナ。 |  |
| 8 | role | 権限 | TINYINT |  | ○ |  | 権限区分（1:管理者等）。 | 値定義は別表 |
| 9 | created_at | 作成日時 | TIMESTAMP(0) |  | ○ | CURRENT_TIMESTAMP | アカウント作成日時。 |  |
|10 | updated_at | 更新日時 | TIMESTAMP(0) |  | ○ | CURRENT_TIMESTAMP | Laravel標準。 | on update CURRENT_TIMESTAMP |
|11 | deleted_at | 論理削除日時 | TIMESTAMP(0) |  |  |  | `deleted_at`で退職/停止管理。 |  |

## 3. インデックス・キー設計
| 種別 | 名称 | 対象カラム | ユニーク | 用途/目的 | 備考 |
|---|---|---|---|---|---|
| 主キー | PRIMARY | id | ○ | レコード一意性 |  |
| ユニーク | operators_email_unique | email | ○ | ログインIDの一意性 |  |
| セカンダリ | operators_email_index | email | × | メール検索 | ユニークと兼用 |

## 4. 制約・リレーション
| 種別 | 名称 | 内容 | 備考 |
|---|---|---|---|
| リレーション | Operator→CompanyOperators/TaskManagers等 | 1:N。アサイン・ログと関連 |  |
| 運用ルール | パスワードNULL | 招待済み未設定状態を意味。初回ログインで設定必須。 |  |
| 運用ルール | 論理削除 | `deleted_at`で利用停止。再開時は復活。 |  |

## 改定履歴
| 改定日 | 版数 | 変更概要 | 担当 |
|---|---|---|---|
| 2025-09-19 | v0.1 | 新規作成 |  |
| 2025-10-10 | v1 | フェーズ0.5のリリース版 | Codex AI |