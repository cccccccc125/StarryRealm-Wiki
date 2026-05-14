# 🌾 耕作 (Farming)

## 概述

耕作系統取代原版農作物收穫。支援連鎖收穫、自動補種、鋤頭耐久保護。

## 設定檔 (`farming/farming.yml`)

```yaml
experience:
  WHEAT: 1.5
  CARROTS: 2.0
  POTATOES: 2.0
  BEETROOTS: 2.5
  NETHER_WART: 5.0
  MELON: 0.5
  PUMPKIN: 0.5

rarity:
  WHEAT: 0.0
  CARROTS: 0.0
  POTATOES: 0.0
  BEETROOTS: 0.1
  NETHER_WART: 0.3
  MELON: 0.1
  PUMPKIN: 0.1

drops:
  WHEAT:
    minecraft/WHEAT:
      chance: 100.0
      amount: "1-3"
    minecraft/WHEAT_SEEDS:
      chance: 100.0
      amount: "1-2"
  CARROTS:
    minecraft/CARROT:
      chance: 100.0
      amount: "1-4"
```

## 核心機制

### 連鎖收穫（chain_gathering 附魔）

BFS 連接所有相鄰的相同成熟作物（最多 64 塊），一次收穫。掉落物 = 單塊掉落 × 連鎖數量。

### 自動補種（replant 附魔）

破壞作物後排程 2 tick 後自動重新種植。**對西瓜和南瓜無效**（因為它們的莖不會被破壞）。

### 鋤頭耐久保護（green_thumb 附魔）

| 等級 | 機率 |
|------|------|
| Lv1 | 5% |
| Lv2 | 10% |
| Lv3 | 15% |

觸發時鋤頭不消耗耐久。

### 經驗公式

```
exp = baseExp × (1 + 0.1 × profLevel) × count × (1 + rarity) × (1 + cultivatorBoost) + (1 + 0.01 × milestone)
```

| 參數 | 說明 |
|------|------|
| `baseExp` | 作物基礎經驗 |
| `profLevel` | 玩家耕作職業等級（MMOCore） |
| `count` | 收穫的作物數量 |
| `rarity` | 該作物的稀有度加成（從 rarity 設定讀取） |
| `milestone` | 圖鑑里程碑等級（以加法加成疊加） |
| `cultivatorBoost` | cultivator 附魔等級 × 0.05 |

## 相關刻印

| 刻印 | 效果 |
|------|------|
| `chain_gathering` (連鎖採集) | 連鎖收穫相鄰作物 |
| `replant` (補種) | 自動補種（西瓜/南瓜除外）|
| `double_harvest` (雙倍採集 Lv1-3) | 3/5/7% 機率雙倍掉落 |
| `green_thumb` (綠手指 Lv1-3) | 5/10/15% 機率不消耗鋤頭耐久 |
| `cultivator` (精耕 Lv1-3) | 5/10/15% 經驗加成 |

## 農產品圖鑑

打 `/subcareers` → 點選耕作 → 點選圖鑑，可查看已解鎖的作物種類。best_record 紀錄該作物的總收穫數量。
