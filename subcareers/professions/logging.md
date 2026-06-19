# 🪓 伐木 (Logging)

## 概述

伐木系統取代原版砍樹機制。破壞原木時觸發客製掉落、樹木結構驗證、以及連鎖砍伐。

## 設定檔 (`logging/logging.yml`)

```yaml
# 連鎖砍伐經驗倍率（連鎖越多經驗衰減越多）
chain-exp-multiplier: 0.1

messages:
  exp-gained: "&a伐木經驗 +{exp}"

trees:
  OAK_LOG:
    exp: 3.0
    drops:
      minecraft/OAK_LOG:
        chance: 100.0
        amount: "1-1"
      minecraft/STICK:
        chance: 20.0
        amount: "1-3"
      minecraft/APPLE:
        chance: 5.0
        amount: "1-1"

  SPRUCE_LOG:
    exp: 3.5
    drops:
      minecraft/SPRUCE_LOG:
        chance: 100.0
        amount: "1-1"

  BIRCH_LOG:
    exp: 3.5
    drops:
      minecraft/BIRCH_LOG:
        chance: 100.0
        amount: "1-1"
```

### 支援的樹種

OAK_LOG、SPRUCE_LOG、BIRCH_LOG、JUNGLE_LOG、ACACIA_LOG、DARK_OAK_LOG、CHERRY_LOG、MANGROVE_LOG

## 核心機制

### 樹木結構驗證

砍伐時需滿足以下條件之一才算有效樹木：

1. **有樹葉相鄰** — 被破壞的原木旁有樹葉方塊
2. **垂直柱狀** — 高度 ≥2 且水平擴散 ≤1×1

不符合條件則無法觸發伐木機制，方塊不會被破壞。

### 連鎖砍伐（chain_gathering 附魔）

採用 BFS 連接所有相同類型的原木（最多 256 塊），整棵樹一次砍倒。掉落物 = 單塊掉落 × 連鎖數量。

### 經驗公式

```
exp = baseExp × (1 + 0.1 × profLevel) × count × chainMultiplier × (1 + foresterBoost)
```

| 參數 | 說明 |
|------|------|
| `baseExp` | 該樹種設定的基礎經驗 |
| `profLevel` | 玩家伐木職業等級（MMOCore） |
| `count` | 破壞的原木數量 |
| `chainMultiplier` | chain_gathering 連鎖 >1 塊時 = `chain-exp-multiplier`（預設 0.1），否則 1.0 |
| `foresterBoost` | forester 附魔等級 × 0.05 |

## 相關刻印

| 刻印 | 效果 |
|------|------|
| `chain_gathering` (連鎖採集) | 連鎖破壞整棵樹 |
| `double_harvest` (雙倍採集 Lv1-3) | 3/5/7% 機率雙倍掉落 |
| `forester` (伐木專精 Lv1-3) | 5/10/15% 經驗加成 |
