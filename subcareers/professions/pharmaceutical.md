# ⚗️ 製藥 (Pharmaceutical)

## 概述

計時煉製系統（取代舊版 QTE 煉金）：在製藥爐選擇已學會的配方與數量，點「開始製藥」即扣除背包材料與金幣，等待 `brew-seconds × 數量` 後回爐領取。

- 煉製期間**可離線、可重啟伺服器**；一座製藥爐同時只能有一個進行中的工作。
- 對應 MMOCore 職業 `pharmaceutical`（`profession-id` 可調）。
- 單次最多煉 `max-batch`（預設 10）組。

## 製藥爐與爐階

管理員以 `/pharma wand` 取得選取工具框選範圍後 `/pharma create <爐階> <名稱>` 建立。爐階限制可煉的配方：

| 爐階 | 名稱 | 說明 |
|------|------|------|
| bottom | 下品爐 | 煉基礎毒藥/藥粉 |
| middle | 中品爐 | 煉中級藥湯 |
| top | 上品爐 | 煉頂級丹藥 |

## 配方（`pharmaceutical/pharmaceutical.yml`）

```yaml
recipes:
  grand_elixir:
    display-name: "大還丹"
    min-tier: "top"        # 最低爐階需求
    cost: 200              # 每份煉製費用（金幣）
    brew-seconds: 600      # 單組煉製秒數
    exp-reward: 500        # 單組基礎 EXP
    unlock: {item: true}   # 需藥方書；{level: N} = 製藥等級達 N 自動學會
    ingredients:
      - "mmoitems/MATERIAL/TOP_GINSENG;2"   # 材料;數量
```

| 欄位 | 說明 |
|------|------|
| `min-tier` | 最低爐階需求（bottom / middle / top） |
| `cost` | 每份煉製費用（金幣，開始時 × 數量一次扣清，**取消不退**；缺省 0 = 免費） |
| `brew-seconds` | 單組煉製秒數（總時間 = 秒數 × 組數） |
| `exp-reward` | 單組基礎 EXP（依品質倍率與里程碑加成） |
| `unlock` | `{level: N}` 達製藥等級自動學會；`{item: true}` 需藥方書 |
| `ingredients` | 材料列表 `"item_format;數量"` |

> **從舊版升級**：已部署的 `pharmaceutical.yml` **不會**自動長出 `cost` 欄位——缺欄位的配方一律免費、介面也不顯示費用行。請手動為每個配方補上 `cost` 後執行 `/pharma reload`。金幣費用需要 Vault ＋ 經濟插件，未安裝時自動視為免費。

## 流程與費用

1. 在製藥爐開啟介面，左側選配方、右側調數量。費用標示在左側配方清單（每份單價）、右側成品詳情（單次費用）與「開始製藥」按鈕（隨數量連動的總費用）上，免費配方不顯示費用行；材料格另顯示持有量。
2. 點「開始製藥」：一次扣除全部材料與 `cost × 數量` 金幣。餘額或材料不足會被擋下，什麼都不扣。
3. 等待期間回爐可查看進度或取消——**取消退材料、不退金幣**。
4. 完成後由發起者回爐領取。

## 品質

領取時**逐組擲品質**，權重依爐階（`quality-weights`）：

| 爐階 | 粗劣 | 普通 | 優良 | 完美 |
|:--:|:--:|:--:|:--:|:--:|
| bottom | 15 | 60 | 22 | 3 |
| middle | 8 | 55 | 30 | 7 |
| top | 3 | 45 | 38 | 14 |

| 品質 | 產量 | EXP 倍率 |
|------|------|:--:|
| 完美 | 基礎 +1（50% 再 +1） | ×1.5 |
| 優良 | 基礎（50% +1） | ×1.2 |
| 普通 | 基礎 | ×1.0 |
| 粗劣 | 基礎 | ×0.6 |

## 圖鑑與里程碑

學會的配方記入製藥圖鑑；每收錄 10 方達成里程碑：製藥經驗永久 +1%/級，且品質權重每級把 1 點從「粗劣」移到「完美」。

## 指令

| 指令 | 說明 |
|------|------|
| `/pharma wand` | 取得選取工具（框選製藥爐範圍） |
| `/pharma create <bottom/middle/top> <名稱>` | 建立製藥爐 |
| `/pharma delete <名稱>` | 刪除製藥爐（煉製中無法刪除） |
| `/pharma learn <玩家> <配方>` | 讓玩家學習配方 |
| `/pharma forget <玩家> <配方>` | 讓玩家遺忘配方 |
| `/pharma giverecipe <玩家> <配方> [數量]` | 發放藥方書 |
| `/pharma unlocktier <玩家> <bottom/middle/top>` | 設定玩家解鎖的爐階 |
| `/pharma checktier <玩家>` | 查詢玩家當前解鎖的爐階 |
| `/pharma list` | 列出所有製藥爐與配方 |
| `/pharma reload` | 重新載入設定 |
