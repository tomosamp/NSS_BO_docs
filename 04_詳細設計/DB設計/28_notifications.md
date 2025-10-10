# Notifications テーブル詳細設計

## ドキュメント目的
- `notifications`テーブルの構造・運用ルールを定義し、管理画面から配信するお知らせ情報を統一管理する。
- 配信仕様変更時は本書と通知機能設計を更新する。

## 1. 基本情報
| 項目 | 内容 | 備考 |
|---|---|---|
| テーブル物理名 | notifications |  |
| テーブル論理名 | お知らせ |  |
| ドメイン/コンテキスト | Notifications |  |
| 主キー | id（BIGINT UNSIGNED AUTO_INCREMENT） |  |

## 2. カラム定義
| No | カラム名 | 論理名 | 型 | PK | Not Null | デフォルト | 説明/業務ルール | 備考 |
|---|---|---|---|---|---|---|---|---|
| 1 | id | お知らせID | BIGINT UNSIGNED | ○ | ○ | AUTO INCREMENT | システム採番。 |  |
| 2 | title | タイトル | VARCHAR(100) |  | ○ |  | 表示タイトル。 |  |
| 3 | content | 本文 | TEXT |  | ○ |  | HTML/Markdown等で保存。 |  |
| 4 | created_at | 作成日時 | TIMESTAMP(0) |  |  | CURRENT_TIMESTAMP | 公開登録日時。 |  |
| 5 | updated_at | 更新日時 | TIMESTAMP(0) |  |  | CURRENT_TIMESTAMP | 更新日時。 | on update CURRENT_TIMESTAMP |
| 6 | deleted_at | 論理削除日時 | TIMESTAMP(0) |  |  |  | `deleted_at`で非表示管理。 |  |

## 3. インデックス・キー設計
| 種別 | 名称 | 対象カラム | ユニーク | 用途/目的 | 備考 |
|---|---|---|---|---|---|
| 主キー | PRIMARY | id | ○ | レコード一意性 |  |

## 4. 制約・リレーション
| 種別 | 名称 | 内容 | 備考 |
|---|---|---|---|
| リレーション | Notification→NotificationFiles/Companies | 1:N。添付・配信先は各テーブル参照 |  |
| 運用ルール | 論理削除 | `deleted_at`で非公開扱い。履歴保持のため物理削除しない。 |  |

## 改定履歴
| 改定日 | 版数 | 変更概要 | 担当 |
|---|---|---|---|
| 2025-09-19 | v0.1 | 新規作成 |  |
| 2025-10-10 | v1 | フェーズ0.5のリリース版 | Codex AI |