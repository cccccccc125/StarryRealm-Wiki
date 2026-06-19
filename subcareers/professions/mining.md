# ⛏️ 採礦 (Mining)

## 概述

採礦系統使用自訂礦脈節點取代原版礦物生成。玩家破壞節點時觸發客製掉落、經驗計算、以及連鎖挖掘機制。

## 礦脈節點

節點由管理員手動放置。每個節點綁定特定 Tier，對應 `mining.yml` 中的設定。

### 節點管理

使用 `/subcareers mining setnode <tier>` 看向方塊來設定節點。支援的 Tier：

| Tier | 預設方塊 | 掉落物 |
|------|---------|--------|
| `coal_node` | COAL_ORE | 煤炭 (MMOItems) |
| `iron_node` | IRON_ORE | 鐵礦 (MMOItems) |
| `gold_node` | GOLD_ORE | 金礦 (MMOItems) |
| `diamond_node` | DIAMOND_ORE | 鑽石 (MMOItems) |
| `ancient_debris_node` | ANCIENT_DEBRIS | 遠古遺骸 (MMOItems) |

## 設定檔 (`mining/mining.yml`)

```yaml
# 幸運屬性每點增加的掉落率
fortune-bonus-per-point: 0.005

# 節氣經驗加成（需 SolarTermCore）
term-multipliers:
  "立春": 1.2
  "雨水": 1.1
  # ....

nodes:
  coal_node:
    exp: 5.0
    drops:
      minecraft/coal:
        chance: 80.0
        amount: "1-3"
      mmoitems/MATERIAL/COAL_ORE:
        chance: 20.0
        amount: "1-1"

  iron_node:
    exp: 12.0
    drops:
      mmoitems/MATERIAL/IRON_ORE:
        chance: 100.0
        amount: "1-2"

  gold_node:
    exp: 25.0
    drops:
      mmoitems/MATERIAL/GOLD_ORE:
        chance: 100.0
        amount: "1-1"

  diamond_node:
    exp: 50.0
    drops:
      mmoitems/MATERIAL/DIAMOND_ORE:
        chance: 100.0
        amount: "1-1"

  ancient_debris_node:
    exp: 100.0
    drops:
      mmoitems/MATERIAL/ANCIENT_DEBRIS_ORE:
        chance: 100.0
        amount: "1-1"
```

### drop 格式

- `minecraft/<MATERIAL>` — 原版物品，amount 用 `"min-max"` 格式
- `mmoitems/<TYPE>/<ID>` — MMOItems 物品

## 核心機制

### 連鎖挖掘（chain_gathering 附魔）

觸發後採用 BFS 廣度優先搜尋，連接相鄰的同類型礦石（最多 64 塊），一次性全部破壞。掉落物 = 單塊掉落 × 連鎖數量。

### 幸運屬性（Fortune）

每個玩家有 fortune（幸運）屬性，計算公式：

```
額外掉落率 = fortune × fortune-bonus-per-point (0.005)
```

即 fortune=10 時增加 5% 掉落率。

### 速度屬性（Speed）

速度屬性影響經驗加成倍率。

### 經驗公式

每個節點計算一次，最後統一套用礦脈感知：

```
nodeExp   = baseExp × (1 + 0.1 × profLevel) × termMultiplier × speedMultiplier × chainMultiplier
totalExp  = Σ(nodeExp) × (1 + veinBoost)
```

| 參數 | 說明 |
|------|------|
| `baseExp` | 節點 Tier 設定的基礎經驗 |
| `profLevel` | 玩家採礦職業等級（MMOCore） |
| `termMultiplier` | 當前節氣倍率（需 SolarTermCore） |
| `speedMultiplier` | 速度屬性倍率 |
| `chainMultiplier` | 連鎖挖掘時為 0.1（鼓勵單挖），否則為 1.0 |
| `veinBoost` | vein_sense 附魔等級 × 0.05（Lv1=5%, Lv2=10%, Lv3=15%）|

### 礦力系統（Mining Power）

礦力決定玩家是否能開採特定等級的礦脈節點。礦力不足時，會觸發採礦疲勞效果且無法掉落物品。

**礦力計算：**
```
totalPower = profLevel × level-power-per-level + Σ(裝備 PDC 加成)
```

- `level-power-per-level`（預設 2）：每職業等級提供的礦力
- 裝備 PDC key：`mining_power_bonus`（裝備上可附加此屬性）

**設定節點所需礦力：**

```yaml
mining-power:
  enabled: true
  level-power-per-level: 2
  insufficient-power:
    mining-fatigue-amplifier: 4
  node-required-power:
    coal_node: 0
    iron_node: 10
    gold_node: 20
    diamond_node: 40
    ancient_debris_node: 80
```

## 相關刻印

| 刻印 | 效果 |
|------|------|
| `chain_gathering` (連鎖採集) | 連鎖破壞相鄰礦石 |
| `double_harvest` (雙倍採集 Lv1-3) | 3/5/7% 機率雙倍掉落 |
| `vein_sense` (礦脈感知 Lv1-3) | 5/10/15% 經驗加成 |

## 節點重生

礦脈被挖掘後會被替換為 bedrock，經過設定的 `respawnTime`（秒）後自動恢復。伺服器關閉時會強制恢復所有已挖掘節點。

## 礦物融合

採礦所得的礦物素材可透過礦物融合系統合成為高級材料，詳見[礦物融合](/subcareers/systems/ore-fusion.md)。

使用 `/fusion` 開啟融合介面。
