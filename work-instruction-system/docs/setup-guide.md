# セットアップ手順書（Hermes / LINE / トンネル）

手元操作（インストール・APIキー・LINE設定）が必要な部分の手順。
Claude Code は代わりに実行しない（リモートコード実行・秘密情報のため）。この手順に沿って人間が進める。

> 各コマンドは実行後に結果を確認する。「動いたはず」で先に進まない（`hermes doctor` 等で必ず検証）。
> ✅=調査で裏取り済みの事実 / ⚠=未確認・要現地確認。

---

## 現状（このPCの実測）

- `~/.hermes/` は `skills/`（90スキル束）のみ存在。
- `config.yaml` / `.env` / `state.db` は**未生成**。
- `AppData\Local\hermes` 本体ディレクトリは**不在**（PATHエントリだけ孤立）。
- → **Hermesランタイムは未インストール。まずインストールから。**

---

## Phase 0-1: Hermes インストール

✅ Windowsネイティブで動作（WSL/Docker不要）。インストーラがPython3.11をuvで自動配置。

```powershell
# PowerShell（管理者不要のはず。要確認）
iex (irm https://hermes-agent.nousresearch.com/install.ps1)
```

- ⚠ このコマンドはリモートスクリプトを実行する。実行前に中身を一読推奨（URLをブラウザで開いて確認）。
- 完了後、新しいターミナルを開いて動作確認:

```powershell
hermes --version
hermes doctor      # 診断。ここで環境の不足を洗い出す
```

- ⚠ もし `hermes` が見つからない場合、PATHの孤立エントリ（`AppData\Local\hermes\...`）とインストーラの実配置先が食い違っている可能性。`hermes doctor` の出力と実ディレクトリを突き合わせる。

---

## Phase 0-2: LLMモデル設定（vision対応必須）

✅ 画像を扱うため**マルチモーダル対応モデル**を選ぶ（Claude / GPT / Gemini / OpenRouter）。

```powershell
hermes setup model     # 対話でプロバイダ・モデルを選択
hermes model           # 選択状態の確認
hermes auth            # APIキー/OAuth登録
```

- 設定ファイル: `~/.hermes/config.yaml`（`model.default` / `model.provider` / `model.base_url` 等）。
- 秘密情報: `~/.hermes/.env`（`ANTHROPIC_API_KEY` 等）。**このファイルは Claude Code に読ませない・gitに入れない**。
- ⚠ ローカルOllamaを補助に使う場合はカスタムエンドポイント方式（`model.base_url`）。ただしOllamaはvision非対応なので中核モデルには使わない。

**この時点で Phase 0 の検証が可能**:
`current_status.md` を手書きで埋め、Hermesに「[instruction-format.md](instruction-format.md) の書式で本日の作業指示書を作れ」と指示 → 出力品質を採点。

---

## Phase 1-1: LINE公式アカウント（Messaging API）

✅ LINE Notifyは2025-03-31終了。push配信は Messaging API（公式アカウント）が必須。

1. [LINE Developers Console](https://developers.line.biz/) で Provider 作成。
2. **Messaging API チャネル**を作成（＝LINE公式アカウントが1つできる）。
3. 取得する値:
   - `LINE_CHANNEL_ACCESS_TOKEN`（長期トークン。チャネル設定で発行）
   - `LINE_CHANNEL_SECRET`（Webhook署名検証用）
4. 応答設定: Webhookを「利用する」、あいさつ/自動応答は必要に応じてオフ。

## Phase 1-2: HTTPSトンネル

✅ Hermes LINEアダプタは既定ポート `8646` で `/line/webhook` を待ち受け。家庭PCなので公開HTTPSが必要。

- 推奨: **Cloudflare Tunnel**（無料・固定ドメイン可・安定）。代替: ngrok（無料枠はURL可変）。
- トンネルで `https://<your-domain>/line/webhook` → `localhost:8646/line/webhook` を公開。
- LINE Developers Console の Webhook URL にこの公開URLを設定し「検証」。

## Phase 1-3: Hermes LINEゲートウェイ有効化

```powershell
hermes setup gateway   # LINE を選択して有効化
```

`~/.hermes/.env` に設定する値（✅ 調査で確認したenv名）:

```
LINE_CHANNEL_ACCESS_TOKEN=...
LINE_CHANNEL_SECRET=...
LINE_PUBLIC_URL=https://<your-domain>   # メディア送信に必要
LINE_ALLOWED_USERS=...                   # 管理者・パートのLINE userId
LINE_HOME_CHANNEL=...                    # 定時配信の宛先（グループ等）
```

- ✅ 送信は無料のreply token（受信後約60秒）を優先し、期限切れは課金対象のPush APIへ自動フォールバック。
- ⚠ LINE公式アカウントのpushには無料枠の月間上限あり。配信頻度・宛先数を要確認。

**Phase 1 検証**: 管理者がLINEでラフ指示 → 指示書生成 → LINEグループへ配信 → パートの完了報告受信、の一周をログで確認。

---

## Phase 2 以降（別途）

- センサー（SwitchBot＋Hub2 / Nature Remo 3）設置 → ポーリングscriptは Codex に委譲して `scripts/` に生成。
- 土壌pHはポータブル計で測定しLINE/フォームで手入力 → `current_status.md` にマージ。
- 毎朝の定時生成は Hermes スケジュール（`LINE_HOME_CHANNEL` 宛）。

---

## セキュリティ留意

- ⚠ Hermesは端末操作権限を持つ自律エージェント。`config.yaml` の `security` / `terminal.backend` を確認し、検証中はスコープを絞る。
- `.env`（APIキー・LINEトークン）は絶対にコミットしない。`work-instruction-system/` 配下に秘密を置かない（Hermesの秘密は `~/.hermes/.env` に集約）。
