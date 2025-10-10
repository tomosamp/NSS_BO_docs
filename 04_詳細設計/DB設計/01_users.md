# Users テーブル詳細設計

## ドキュメント目的
- `users`テーブルのスキーマ・制約・運用ルールを共有し、アプリケーション/バッチ間の整合性を担保する。
- テーブル構造を変更する場合は本書とテーブル一覧を同時に更新する。

## 1. 基本情報
| 項目 | 内容 | 備考 |
|---|---|---|
| テーブル物理名 | users |  |
| テーブル論理名 | 利用ユーザー |  |
| ドメイン/コンテキスト | Users（会員管理） |  |
| 主キー | id（BIGINT UNSIGNED AUTO_INCREMENT） |  |

## 2. カラム定義
| No | カラム名 | 論理名 | 型 | PK | Not Null | デフォルト | 説明/業務ルール | 備考 |
|---|---|---|---|---|---|---|---|---|
| 1 | id | 会員ID | BIGINT UNSIGNED | ○ | ○ | AUTO INCREMENT | システム採番。 |  |
| 2 | company_id | 企業ID | BIGINT UNSIGNED |  | ○ |  | `companies.id`へのFK。所属企業必須。 |  |
| 3 | email | メールアドレス | VARCHAR(100) |  | ○ |  | 一意制約あり。ログインIDとして利用。 | 小文字正規化を前提 |
| 4 | password | パスワードハッシュ | VARCHAR(255) |  |  |  | nullの場合は未設定状態として扱う。 |  |
| 5 | last_name | 姓 | VARCHAR(50) |  | ○ |  | 氏名（漢字）。 |  |
| 6 | first_name | 名 | VARCHAR(50) |  | ○ |  | 氏名（漢字）。 |  |
| 7 | last_name_kana | セイ | VARCHAR(50) |  | ○ |  | 全角カタカナ想定。 |  |
| 8 | first_name_kana | メイ | VARCHAR(50) |  | ○ |  | 全角カタカナ想定。 |  |
| 9 | created_at | 作成日時 | TIMESTAMP(0) |  | ○ | CURRENT_TIMESTAMP | Laravel標準タイムスタンプ。 |  |
| 10 | updated_at | 更新日時 | TIMESTAMP(0) |  | ○ | CURRENT_TIMESTAMP | 更新トリガーで自動更新。 | on update 現地DB依存 |
| 11 | deleted_at | 論理削除日時 | TIMESTAMP(0) |  |  |  | `softDeletes()`。論理削除管理。 |  |

## 3. インデックス・キー設計
| 種別 | 名称 | 対象カラム | ユニーク | 用途/目的 | 備考 |
|---|---|---|---|---|---|
| 主キー | PRIMARY | id | ○ | レコード一意性 |  |
| ユニーク | users_email_unique | email | ○ | ログイン時照合高速化、重複防止 | Laravel自動命名 |
| 外部キー | users_company_id_foreign | company_id | ○ | 企業削除時にカスケード | InnoDB FK |

## 4. 制約・リレーション
| 種別 | 名称 | 内容 | 備考 |
|---|---|---|---|
| 外部キー | users_company_id_foreign | `companies.id`へ参照、ON DELETE CASCADE |  |
| リレーション | Company-User | 企業 : ユーザー = 1 : N | アプリケーション側Eloquent想定 |
| リレーション | User-Task/Labor等 | 他テーブルからのFKあり（application_requests等） | 詳細は各テーブル参照 |

## 改定履歴
| 改定日 | 版数 | 変更概要 | 担当 |
|---|---|---|---|
| 2025-09-19 | v0.1 | 初版（テンプレ差し替え） |  |
| 2025-10-10 | v1 | フェーズ0.5のリリース版 | Codex AI |
