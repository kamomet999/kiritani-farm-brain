# 02 データモデル

「農場の頭脳」が扱うデータの設計。実装技術は [03](03-architecture.md) で決めるが、モデルは技術非依存でここに定義。

## エンティティ

### Area（エリア）
| 項目 | 型 | 例 |
|---|---|---|
| id | string | "kakiuchi-kami" |
| name | string | "垣内上" |
| order | int | 北→南の並び順 |

### House（ハウス）
| 項目 | 型 | 例 |
|---|---|---|
| no | string(3) | "012"（001〜061、一意キー） |
| area_id | FK Area | |
| map_x, map_y | number | ダッシュボード全体図上の座標（イラスト図の配置） |
| status_note | string | 補足 |

### PlantingCycle（作・定植）
1ハウスに複数作があり得る前提（当面1でよい＝[06](06-decisions-open.md)）。
| 項目 | 型 | 例 |
|---|---|---|
| id | uuid | |
| house_no | FK House | |
| crop | string | トマト（品種） |
| planted_on | date | 定植日 |
| ended_on | date? | 撤去日 |

### Observation（観察・朝の知見）
| 項目 | 型 | 例 |
|---|---|---|
| id | uuid | |
| house_no | FK | |
| observed_on | datetime | |
| by | string | 農場長名 |
| text | string | 「A棟の下葉が黄変、水切れ気味」 |
| source | enum | field-walk / other |

### Measurement（測定）
| 項目 | 型 | 例 |
|---|---|---|
| id | uuid | |
| house_no | FK | |
| measured_on | datetime | |
| type | enum | soil_moisture / EC / pH / soil_temp / air_temp |
| value | number | |
| unit | string | %, mS/cm, pH, ℃ |
| method | enum | finger（感覚）/ device（機器）/ manual |
| device | string? | 機種名 |

### WorkTask（作業＝日誌兼指示）
状態で「予定（指示書）」と「完了（日誌）」を兼ねる。
| 項目 | 型 | 例 |
|---|---|---|
| id | uuid | |
| house_no | FK | |
| task_type | string | 誘引/摘芽/追肥/摘葉/収穫/防除/灌水… |
| status | enum | planned（予定）/ done（完了）/ skipped |
| planned_for | date? | 予定日 |
| done_on | datetime? | 実施日時 |
| by | string? | 実施者 |
| note | string | 一言・注意 |
| origin | enum | ai（AI提案）/ manual |

### Photo（生育写真）
| 項目 | 型 | 例 |
|---|---|---|
| id | uuid | |
| house_no | FK | |
| taken_on | datetime | |
| url | string | ストレージ上のパス |
| ai_analysis | json/text? | vision解析結果（状況把握＋次の手） |

### AIInstruction（AI作業指示）
| 項目 | 型 | 例 |
|---|---|---|
| id | uuid | |
| house_no | FK（or 全体） | |
| generated_on | datetime | |
| input_refs | json | 使った観察/測定/写真のid |
| text | string | 生成された明快な指示 |
| accepted | bool | 農場長が採用/修正したか |

### Tool（道具）
| 項目 | 型 | 例 |
|---|---|---|
| id | uuid | |
| name | string | 誘引ひも / 動噴 |
| category | string | 資材 / 機械 / 手道具 |
| qty | number? | |
| location | string | 倉庫 / 各ハウス |
| note | string | |

## 状態の導出（ダッシュボード表示用）

各ハウスの「色・アイコン・数値」は生データから**導出**する（保存しない）。

- **生育ステージ**：`今日 - planted_on = 経過日数` → ステージ目安（背景情報。工程は当てにならない前提なので"参考"表示）。
- **作業状況**：未実施の planned タスク有無・遅れ（planned_for 超過）→ 色（正常/要対応/遅れ）。
- **生育状況**：最新 Observation / Photo.ai_analysis / Measurement（土壌水分など）から要注意フラグ → アイコン。
- **測定の鮮度**：最終測定からの経過（古ければ「測定しよう」）。

## キー・関係

- House.no（001〜061）が全ての一意な"住所キー"。Observation/Measurement/WorkTask/Photo/AIInstruction は house_no で紐づく。
- Area 1—* House。House 1—* PlantingCycle。
- 時系列クエリ（ハウス別タイムライン）が主要アクセスパターン → house_no + 日時のインデックス。
