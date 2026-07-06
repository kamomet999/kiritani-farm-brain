# 7bay — 飛騨高山 農園データ・経営管理リポジトリ

飛騨高山エリアに実在する農園の情報を幅広く収集し、AI を使ったデータ管理・経営管理を行うためのリポジトリ。
将来的に「会社組織として運用する体制」を見据え、農園データと組織設計の両方をここに蓄積していく。

> 意思決定の経緯は [HISTORY.md](HISTORY.md) を参照。
> Claude Code がこのフォルダで作業するときのルールは [CLAUDE.md](CLAUDE.md) を参照。

---

## 現在のフェーズ

**データ構造設計フェーズ**（実データ収集前）

- 具体的な農園名・連絡先・データはまだ手元にない
- まずは「どういう項目を集めるか」のスキーマとテンプレートを整備する
- アプリ化（Web/DBなど）は要件が固まってから判断する

## 既存プロジェクトとの関係

[APE (Agri-Profit Engine)](../Projects/APE) とは**別の独立プロジェクト**。
7bay は飛騨高山エリアの農園情報収集・経営管理に特化する。

---

## ディレクトリ構造

```
7bay/
├── README.md        ← このファイル（全体俯瞰）
├── HISTORY.md        ← 意思決定・方針変更の履歴
├── CLAUDE.md          ← Claude Code 向け作業ルール（自動ロード）
│
├── farms/             ← 農園ごとのデータ
│   ├── README.md      ← データ構造の説明
│   ├── _schema.md      ← 農園データが持つべき項目定義
│   └── _template.md    ← 新規農園追加用テンプレート
│
├── work-instruction-system/  ← ハウス作業指示 自動化システム（Hermes×IoT×LINE）
│   ├── README.md              ← システム俯瞰・フェーズ状況
│   ├── current_status.md       ← 環境サマリ（Hermesの入力）
│   ├── docs/                    ← 指示書式・セットアップ手順
│   └── scripts/                 ← センサーポーリング（Phase 2〜）
│
└── org/               ← 会社組織として運用するための組織設計
    └── README.md       ← 現状は空・TBD（要件が固まり次第埋める）
```

## 使い方

1. 農園情報が集まったら `farms/_template.md` をコピーし `farms/<farm-slug>.md` として保存
2. データ項目に迷ったら `farms/_schema.md` を確認・更新
3. 会社組織としての体制を検討し始めたら `org/README.md` に書き足す
4. ハウス作業指示の自動化を進めるときは `work-instruction-system/README.md` を参照
5. 方針変更・大きな意思決定は `HISTORY.md` に追記
