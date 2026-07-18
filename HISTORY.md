# 7bay 方針変更履歴

このリポジトリで使う **設計・運用方針** の変更履歴。コードやデータの履歴（git log）とは別物。
新しい方針を導入したらここに追記する。最新を一番上に書く。

---

## 2026-07-19 — ハウス号数を北から通し採番（01–63（2桁表記））に改定・全棟の緯度経度を確定

### 決定の核
- 農場主が航空写真上に全63棟の棟軸ラインを作図（作図ツールで緯度経度化、精度±2m）。
- 号数を**北から南への通し番号 01–63（2桁表記） に振り直し**（ユーザー決定）。未設置の予定地3箇所
  （08=工場前南端、23・24=高野エリア）にも今後の増設を見据えて番号を確保。旧004欠番は廃止。
- 旧号数（2026-07-06版）との対応表は `site-map/house-coordinates.md` に保持。
- 垣内の上/中/下の境界は棟間ギャップ（約10m・約12m）が台帳の8+6+8と一致し確定。

### 作成・更新したファイル
- `site-map/house-coordinates.md`（全63棟の中心・両端座標＋旧号数対応）
- `site-map/house-map.geojson`（GISデータ、WGS84）
- `site-map/house-map-coordinates.jpg`（号数入り全域マップ）
- `site-map/house-map.md`（住所マスター表を新号数に更新）

### 残課題
- おぞれ右/左の号数境界、各エリアの主な作物・電源・電波（従来からの未記入項目）。

---

## 2026-07-04 — 作業指示自動化システム（Hermes×IoT×LINE）の計画とスキャフォールド

### 決定の核
- 身内のハウスを対象に、IoT環境データ＋管理者のラフ指示を **Hermes Agent** が統合し、
  現場パート向け作業指示書へ変換して **LINE** 配信するプロトタイプを企画。
- ゴールは機能検証プロトタイプ（多農園展開・製品化は後）。中核はHermes、配信はLINE、と方針決定（ユーザー選択）。
- 調査（4エージェント＋検証）で裏取り: Hermesは実在OSSでLINE公式サポート・Windowsネイティブ動作可・vision対応。
  ただし本機は**Hermesランタイム未インストール**（`~/.hermes/skills/`のみ）。温湿度は安価センサー＋クラウドAPIで自動化可、
  土壌pH常時センシングは鬼門ゆえ手入力で代替。総合農業SaaS（サグリ/みどりクラウド/KSAS）は一気通貫に噛み合わず見送り。

### 作成したファイル
- `work-instruction-system/`（README, current_status.md, docs/instruction-format.md, docs/setup-guide.md, scripts/README.md）
- `.gitignore`（.env等の秘密保護）
- 全体計画: `~/.claude/plans/ai-hermes-agent-iot-ph-hermes-agent-sla-federated-eclipse.md`

### 現状と次アクション
- Claude Code の直接担当（ドキュメント・仕様・スキャフォールド）は完了。
- 次は**手元操作が必要**: Hermesインストール→モデル設定（vision可）→Phase 0のループ検証。手順は `work-instruction-system/docs/setup-guide.md`。

---

## 2026-07-04 — リポジトリ新規作成

### 決定の核
- 飛騨高山エリアの農園情報を収集し、AI でデータ管理・経営管理を行うリポジトリとして `7bay` を新規作成。
- 既存の [APE](../Projects/APE)（農業×AI起業プロダクト）とは**別の独立プロジェクト**と位置づけ。
- 現時点では具体的な農園データは手元になし → まず**データ構造・スキーマの設計**から着手する。
- アプリ化（Web/DBなど）は要件が固まってから判断。今は文書・データ構造のみ。

### 作成したファイル
- `README.md`, `HISTORY.md`, `CLAUDE.md`
- `farms/README.md`, `farms/_schema.md`, `farms/_template.md`
- `org/README.md`

### 今後の運用
- 農園データが集まり次第 `farms/` にファイルを追加していく
- 「会社組織として運用する体制」の具体像が固まったら `org/` に書き足す
- 実装（アプリ化・自動収集スクリプト等）が必要になったらオーケストレーターモード（Codex/Ollama 委譲）に移行する
