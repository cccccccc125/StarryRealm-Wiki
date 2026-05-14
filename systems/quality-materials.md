# 🪵 品質素材

## 概述

伐木與採礦時，有機率產出帶有品質標記的特殊素材。品質素材可用於鍛造，提升最終產出物品的數值倍率。

## 品質等級

| 等級 | 說明 |
|------|------|
| 中品 | 輕微提升 |
| 上品 | 中等提升 |
| 特品 | 高等提升 |
| 極品 | 最高等級提升 |

品質以 PDC 標記在物品上（Key: `quality_material`，String 類型），並會在物品 Lore 顯示。

## 產出機率

### 木材類

| 品質 | 機率 |
|------|:--:|
| 中品 | 15% |
| 上品 | 8% |
| 特品 | 3% |
| 極品 | 由 `quality-materials.yml` 設定 |

### 礦石類

| 品質 | 機率 |
|------|:--:|
| 中品 | 12% |
| 上品 | 6% |
| 特品 | 2% |
| 極品 | 由 `quality-materials.yml` 設定 |

## 設定檔 (`quality-materials.yml`)

```yaml
quality-materials:
  enabled: true
  wood:
    chances:
      中品: 0.15
      上品: 0.08
      特品: 0.03
      極品: 0.01
    items:
      OAK_LOG:
        中品: "MATERIAL/OAK_WOOD_MEDIUM"
        上品: "MATERIAL/OAK_WOOD_HIGH"
        特品: "MATERIAL/OAK_WOOD_SPECIAL"
        極品: "MATERIAL/OAK_WOOD_ULTIMATE"
      # 支援：OAK_LOG, SPRUCE_LOG, BIRCH_LOG, JUNGLE_LOG,
      #       ACACIA_LOG, DARK_OAK_LOG, CHERRY_LOG, MANGROVE_LOG
  ore:
    chances:
      中品: 0.12
      上品: 0.06
      特品: 0.02
      極品: 0.005
    items:
      COAL_ORE:
        中品: "MATERIAL/COAL_ORE_MEDIUM"
        # ...
```

## 鍛造聯動

品質素材作為鍛造原料時，會依品質等級給予數值倍率加成，詳見[鍛造系統](../professions/forging.md#品質素材聯動)。
