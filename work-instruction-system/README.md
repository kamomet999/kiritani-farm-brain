# work-instruction-system — ハウス作業指示 自動化システム

飛騨高山の農園（まずは**身内のハウス**でプロトタイプ）向けに、
「IoT環境データ（温湿度・pH・カメラ画像）＋ 管理者のラフな指示」を **Hermes Agent** が統合し、
現場のパートさんが迷わず動ける**作業指示書**へ変換して **LINE** で配信する仕組み。

> 全体計画（背景・調査根拠・段階手順・リスク）は
> [計画書](../../../../.claude/plans/ai-hermes-agent-iot-ph-hermes-agent-sla-federated-eclipse.md) を参照。
> 親リポジトリの位置づけは [7bay/README.md](../README.md) を参照。

---

## 全体像

```
[SwitchBot/Nature Remo クラウドAPI] ──(ポーリングscript)──┐
[土壌pH: ポータブル計で手入力]        ─────────────────────┤
[定点カメラ: スマホ/webカメラの写真]  ─────────────────────┤
                                                          ▼
                                          current_status.md（環境サマリ＋画像パス）
                                                          ▼
[管理者] ──LINEでラフ指示──▶       Hermes Agent（Windowsネイティブ稼働）
                                   ・current_status.md を読取り
                                   ・管理者指示と統合 → 指示書生成
                                   ・skills=手続き的記憶を自己蓄積
                                                          ▼
                                   Hermes LINEアダプタ（Messaging API＋HTTPSトンネル）
                                                          ▼
[現場パート] ◀── LINE公式アカウントで「本日の作業指示書」受信・完了報告
```

## ディレクトリ構造

```
work-instruction-system/
├── README.md              ← このファイル
├── current_status.md       ← 環境サマリ（Hermesが毎回読む入力。手書き→将来自動更新）
├── docs/
│   ├── instruction-format.md  ← 作業指示書の目標書式（Hermesスキルの種）
│   └── setup-guide.md          ← Hermes/LINE/トンネルのセットアップ手順（手元操作用）
└── scripts/                ← センサーポーリングscript（Phase 2でCodex委譲生成。現状 空）
```

## 現在のフェーズ

**Phase 0 準備中** — ループ検証（ハード不要）。

| Phase | 内容 | 状態 |
|---|---|---|
| 0 | ループ検証（手書きstatus＋ラフ指示→指示書生成） | 準備中（このスキャフォールド） |
| 1 | LINE配信の確立 | 未着手 |
| 2 | 実センサー取込（SwitchBot/Nature Remo） | 未着手 |
| 3 | カメラ画像＋スキル育成 | 未着手 |

## 次にやること（手元操作が必要）

Hermes 本体は**まだこのPCに未インストール**（`~/.hermes/` は `skills/` のみ）。
[docs/setup-guide.md](docs/setup-guide.md) の手順に沿って、インストールとモデル設定を進める。
その後 [current_status.md](current_status.md) を手書きで埋め、
[docs/instruction-format.md](docs/instruction-format.md) の書式で指示書が生成できるか検証する。
