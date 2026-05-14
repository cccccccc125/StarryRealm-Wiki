# ⚗️ 煉金 (Alchemy)

## 概述

三段式煉藥爐系統，搭配 QTE（Quick Time Event）小遊戲。玩家需學習配方、解鎖階級、投入正確材料與燃料才能成功煉製。

## 煉藥爐架構

### 階級系統

| 階級 | 名稱 | 材料格數 | 說明 |
|------|------|:--:|------|
| 0 | Bottom | 3 | 初階，煉製基礎毒藥 |
| 1 | Middle | 6 | 中階，煉製中級藥水 |
| 2 | Top | 9 | 高階，煉製頂級丹藥 |

玩家初始階級為 0，需管理員使用 `/alchemy unlocktier` 解鎖更高階級。

## 設定檔 (`alchemy/alchemy.yml`)

### 預設灰燼

```yaml
default-ashes:
  type: minecraft
  item: GUNPOWDER
  amount: 1
  display-name: "&7失敗的灰燼"
```

配方不匹配時產出此物品。

### 煉藥爐區域

由管理員用 `/alchemy wand` + `/alchemy create` 建立，儲存在 `furnaces` 區塊。

### 配方格式

```yaml
recipes:
  mana_potion:
    display-name: "&b魔力藥水"
    min-tier: 1                    # 最低需要 middle (1)
    speed-level: 2                 # QTE 速度 1-4
    exp-reward: 50.0
    fuel:
      type: minecraft
      material: BLAZE_POWDER
      amount: 1
    ingredients:
      1:
        type: mmoitems
        mmoitem-type: MATERIAL
        mmoitem-id: MID_GINSENG
        amount: 2
      2:
        type: mmoitems
        mmoitem-type: MATERIAL
        mmoitem-id: MID_ANGELICA
        amount: 1
    result:
      type: mmoitems
      mmoitem-type: CONSUMABLE
      mmoitem-id: MANA_POTION
      amount: 1
```

### 材料格式

| 格式 | 範例 | 說明 |
|------|------|------|
| `minecraft/<MATERIAL>` | `minecraft/BLAZE_POWDER` | 原版物品 |
| `mmoitems/<TYPE>/<ID>` | `mmoitems/MATERIAL/GINSENG` | MMOItems 物品 |

## 煉藥爐 GUI（45 格）

| 區域 | 欄位 | 說明 |
|------|------|------|
| 材料區 | 0-8（依階級） | Bottom 3 格、Middle 6 格、Top 9 格 |
| 燃料槽 | 25 | 放入燃料物品 |
| 開始按鈕 | 23 | 點擊開始煉製 |
| 配方圖鑑 | 8 | 查看已學會的配方 |
| QTE 區域 | 36-44 (9 格) | 溫度計來回移動 |

## QTE 小遊戲

### 規則

- 溫度計（玻璃片）在 9 格中來回移動
- 速度由配方的 `speed-level` 決定（1=慢, 4=快）
- 點擊任意格子來「停止」溫度計

### 判定

| 位置 | 判定 | 顏色 |
|------|------|------|
| 3-5 | 成功 (Perfect) | 綠色 |
| 2, 6 | 中性 (Neutral) | 黃色 |
| 0-1, 7-8 | 失敗 (Fail) | 紅色 |

- **3 次成功** = 煉製成功，獲得產物與經驗
- **3 次失敗** = 煉製失敗，產出灰燼，不消耗材料（消耗燃料）
- **10 秒超時** = 自動失敗

### 首次成功

配方會自動解鎖（`learn`），可在配方圖鑑中查看。

## 配方圖鑑（54 格）

顯示所有已學會的配方及其材料組合。未知配方顯示灰色染料（CustomModelData: 999）。

## 學習與忘記

管理員可用指令管理玩家的配方知識：

```
/alchemy learn <player> <recipe>    # 強制學習配方
/alchemy forget <player> <recipe>   # 強制忘記配方（從 collection 中 lock）
```

## 指令

參見 [💾 指令](../general/commands.md) 的煉金部分。

## Tier 追蹤

玩家當前的煉藥爐階級儲存在 collection data 的 `_tier_` meta key 中。
