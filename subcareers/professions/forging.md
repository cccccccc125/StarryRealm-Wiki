# 🔨 鍛造 (Forging)

## 概述

計時煉製系統：右鍵已綁定的鍛爐（高爐）→ 選武器 → 選材料 → 扣料開始鍛造 → 等待完成後回爐領取。

- 成品品質由「該配方對所選材料自定的品質%」決定，同一把武器用越稀有的材料品質越高。
- 鍛造期間**可離線、可重啟伺服器**；一座鍛爐同時只能有一個進行中的工作。
- 對應 MMOCore 職業 `forging`（`profession-id` 可調），鍛造給職業經驗。

## 鍛爐

管理員看向高爐執行：

```
/forge bind     # 將面對的高爐登記為鍛爐
/forge unbind   # 取消登記（鍛造中無法解除）
```

## 配方（`forging/forging.yml`）

```yaml
recipes:
  desert_blade:
    display-name: "沙漠金刀"
    output-mmoitem-type: SWORD
    output-mmoitem-id: DESERTSWORD
    amount: 4              # 消耗所選材料的數量
    forge-seconds: 180     # 鍛造耗時（秒）
    cost: 300              # 每次鍛造扣除的金幣
    unlock: {level: 0}     # 達到鍛造等級自動學會；{item: true} = 需鍛造圖紙
    materials:
      "minecraft/IRON_INGOT": {quality: 100}
      "mmoitems/MATERIAL/FLAWLESS_DIAMOND":
        quality: 130
        stats: {critical_chance: 1}   # 選此材料附加的固定詞條
```

| 欄位 | 說明 |
|------|------|
| `display-name` | GUI/訊息顯示名（缺省用成品顯示名） |
| `amount` | 消耗所選材料的數量 |
| `forge-seconds` | 鍛造耗時（秒，缺省 120） |
| `cost` | 每次鍛造扣除的金幣（開始時與材料一起扣，**取消不退**；缺省 0 = 免費） |
| `exp-reward` | 固定 EXP（缺省 → 依品質星級公式 `experience.per-quality-star`） |
| `unlock` | `{level: N}` 達鍛造等級自動學會；`{item: true}` 需鍛造圖紙 |
| `materials` | 材料 key → `{quality: 品質%, stats: {詞條id: 數值}}` |

> **從舊版升級**：已部署的 `forging.yml` **不會**自動長出 `cost` 欄位——缺欄位的配方一律免費、介面也不顯示費用行。請手動為每個配方補上 `cost` 後執行 `/forge reload`。金幣費用需要 Vault ＋ 經濟插件，未安裝時自動視為免費。

## 流程與費用

1. 右鍵鍛爐開啟介面，左側選武器、右側選材料。費用標示在左側武器清單、右側成品詳情與「開始鍛造」按鈕上（免費配方不顯示費用行）；材料格另顯示品質%、固定詞條與持有量。
2. 點「開始鍛造」：一次扣除材料與金幣。餘額不足會被擋下，材料不動。
3. 等待 `forge-seconds`；期間回爐可查看進度或取消——**取消退材料、不退金幣**。
4. 完成後由發起者回爐領取成品。

## 品質與星級

- 品質% 依所選材料而定；星級 = `round((品質 - 100%) / 10%) + 1`，夾在 1~5 星。
- 品質倍率會乘進成品數值：`最終數值 = 原始值 × 製作品質 × 強化倍率 × 升星倍率 × (1 + 精煉)`（見 `forging/stat-scaling.yml`）。
- 高品質（達 `bonus-stats.min-quality`）另有機率附加隨機詞條，品質越高擲越多次。
- 經驗：配方有 `exp-reward` 用固定值；否則依星級在 `experience.per-quality-star` 的 min~max 之間給。

## 圖鑑與里程碑

學會的配方記入鍛造圖鑑；每收錄 10 項達成里程碑，鍛造經驗永久 +1%/級。

## 指令

| 指令 | 說明 |
|------|------|
| `/forge bind` | 將面對的高爐登記為鍛爐 |
| `/forge unbind` | 取消高爐的鍛爐登記（鍛造中無法解除） |
| `/forge learn <玩家> <配方>` | 讓玩家學習配方 |
| `/forge forget <玩家> <配方>` | 讓玩家遺忘配方 |
| `/forge giveblueprint <玩家> <配方> [數量]` | 發放鍛造圖紙 |
| `/forge list` | 列出配方與進行中的鍛造 |
| `/forge reload` | 重新載入鍛造配方 |
