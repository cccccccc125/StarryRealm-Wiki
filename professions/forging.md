# 🔨 鍛造 (Forging)

## 概述

三階段鍛造系統，模擬真實鍛造流程：**加熱 → 敲打 → 淬火**。每個階段都會影響最終物品的品質評分與星級。

## 核心概念

### 鍛造站

使用高爐（Blast Furnace）作為鍛造站。管理員需綁定：

```
/forge bind     # 看向高爐並點擊綁定
/forge unbind   # 解除綁定
```

綁定後該高爐無法被破壞。

### 鍛造錘

搭配 MMOItems 的特定物品作為鍛造錘。在 `forging.yml` 中設定：

```yaml
hammer:
  mmoitem-type: TOOL
  mmoitem-id: FORGE_HAMMER
```

### 鍛造配方

```yaml
recipes:
  iron_blade:
    display-name: "&f鐵劍胚"
    inputMaterial: IRON_INGOT
    inputAmount: 4
    outputMmoType: SWORD
    outputMmoId: IRON_BLADE
    cycles: 2
    strikesPerCycle: 5

  steel_sword:
    display-name: "&7鋼劍"
    inputMaterial: IRON_INGOT
    inputAmount: 6
    outputMmoType: SWORD
    outputMmoId: STEEL_SWORD
    cycles: 3
    strikesPerCycle: 6
```

| 欄位 | 說明 |
|------|------|
| `id` | 配方識別碼 |
| `inputMaterial` | 原料方塊類型（Material enum） |
| `inputAmount` | 原料數量 |
| `outputMmoType` | MMOItems 輸出類型 |
| `outputMmoId` | MMOItems 輸出 ID |
| `cycles` | 加熱循環數 |
| `strikesPerCycle` | 每循環需要敲打的次數 |

## 三階段流程

### 第一階段：加熱 (HEATING)

玩家將原料放在高爐上後開始加熱。

```yaml
heat-per-tick: 24    # 每 tick 上升的溫度（2 tick 執行一次 = 12/tick）
max-temp: 1100       # 最高溫度，超過 = 過熱失敗
cooling-rate: 2      # 冷卻速度（敲打階段用）
quench-cooling-rate: 8  # 淬火階段冷卻速度
```

- 溫度每 2 tick 上升 24 度
- 溫度達 700-900 度為最佳取出時機
- 超過 1100 度 = 材料燒毀
- 玩家右鍵點擊高爐取出材料進入下一階段

### 第二階段：敲打 (HAMMERING)

玩家將材料放在鐵砧上，使用鍛造錘敲打。

```yaml
min-strike-temp: 400    # 最低可敲打溫度
optimal-min-temp: 600   # 最佳敲打溫度底線
```

- 每敲打一次溫度下降 `cooling-rate`
- 溫度 < `min-strike-temp` = 太冷無法敲打，需重新加熱
- 每循環需要 `strikesPerCycle` 次有效敲打
- 完成所有循環後進入下一階段

### 第三階段：淬火 (QUENCHING)

玩家拿著炙熱的材料右鍵點擊裝水的鍋釜。

```yaml
optimal-quench-min: 350
optimal-quench-max: 550
```

- 溫度在 350-550 為最佳淬火區間
- 淬火後溫度急速下降

## 品質評分

### 各階段評分

| 階段 | 條件 | 分數 |
|------|------|:--:|
| **加熱** | 取出溫度 700-900 | 100 |
| | 取出溫度 500-700 | 75 |
| | 取出溫度 900-1100 | 60 |
| | 取出溫度 <500 | 30 |
| | 取出溫度 >1100（過熱） | 10 |
| **敲打** | 溫度 >90% optimalMin | 100 |
| | 溫度 >70% optimalMin | 75 |
| | 溫度 >1.5× minStrikeTemp | 50 |
| | 低於上述 | 25 |
| **淬火** | 350-550 | 100 |
| | 550-700 或 250-350 | 65 |
| | >700 | 35 |
| | <250 | 20 |

### 最終分數

```
finalScore = heatingScore × 0.35 + hammeringScore × 0.40 + quenchScore × 0.25
```

### 星級轉換

| 分數 | 星級 |
|:--:|:--:|
| ≥85 | ⭐⭐⭐⭐⭐ |
| ≥70 | ⭐⭐⭐⭐ |
| ≥50 | ⭐⭐⭐ |
| ≥30 | ⭐⭐ |
| <30 | ⭐ |

## 產出

1. 優先嘗試用 MMOItems 生成（`outputMmoType` + `outputMmoId`）
2. 若失敗則嘗試 `Material.matchMaterial(outputMmoId)`
3. 附加品質 Lore 和 PDC（`forge_quality`, `forge_score`, `forge_quality_multiplier`）
4. 3 星以上有機率附加隨機屬性加成

### 品質素材聯動

使用[品質素材](../systems/quality-materials.md)作為鍛造原料時，輸出物品會附加數值倍率加成：

| 材料品質 | 數值倍率 |
|:--:|:--:|
| 無（普通） | 100% + 評分貢獻（最高 +40%） |
| 中品 | 110% + 評分貢獻（最高 +30%） |
| 上品 | 120% + 評分貢獻（最高 +20%） |
| 特品 | 130% + 評分貢獻（最高 +10%） |
| 極品 | 固定 140% |

> 評分貢獻 = `(鍛造評分 / 100) × 上限`

**PDC 儲存：**

| Key | 類型 | 說明 |
|-----|------|------|
| `forge_quality` | Integer | 星級（1-5） |
| `forge_score` | Double | 最終評分 |
| `forge_quality_multiplier` | Double | 品質倍率（供外部系統讀取）|

### 加成屬性池

```yaml
bonus-stats:
  pool:
    - attack_damage
    - critical_chance
    - attack_speed
    - max_health
    - armor
```

## 鍛造狀態 GUI

打 `/subcareers` → 點選鍛造 → 查看各裝備類型配方（武器/防具/飾品）。
