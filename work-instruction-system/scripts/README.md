# scripts/ — センサーポーリング等

Phase 2 で追加する。SwitchBot Cloud API（HMAC-SHA256署名、`GET /v1.1/devices/{id}/status`）
または Nature Remo API（OAuth Bearer）を叩き、取得値を `../current_status.md` に整形出力する薄いスクリプトを置く。

**実装は [orchestrator-mode](../../../../.claude/rules/orchestrator-mode.md) に従い Codex に委譲**して生成する。
現状は空（プレースホルダ）。

APIキー等の秘密は環境変数で渡し、コードにハードコードしない（[../docs/setup-guide.md](../docs/setup-guide.md) 参照）。
