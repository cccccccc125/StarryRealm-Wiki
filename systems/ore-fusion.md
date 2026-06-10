# 🪨 礦物融合

## 概述

礦物融合系統允許玩家依設定配方，將多個礦物素材合成為更高級的材料。

## 開啟方式

- `/fusion` — 開啟礦物融合介面（快捷指令）
- `/subcareers fusion` — 同上

## 介面說明

### 配方列表（54 格）

顯示所有可用的融合配方。每個圖示格顯示：
- 所需輸入材料與數量
- 合成後輸出材料

點擊任意配方後進入確認介面。

### 確認介面（27 格）

| 格位 | 說明 |
|------|------|
| 材料槽 | 放入輸入材料（直接點擊放入，或 Shift 點擊背包中的材料） |
| 輸出預覽 | 顯示合成後的產物 |
| 確認按鈕 | 執行融合 |
| 返回按鈕 | 回到配方列表 |

按下確認時優先扣除材料槽內的材料；若槽內數量不足，會自動從背包補扣差額（槽內材料同樣計入成本）。輸出材料放入背包，背包已滿則掉落腳邊。關閉介面時，材料槽內未消耗的材料會自動退還。

## 設定檔 (`mining/mining.yml`)

```yaml
ore-fusion:
  IRON_INGOT_MEDIUM:
    input: "MATERIAL/IRON_ORE"
    input-amount: 3
    output: "MATERIAL/IRON_INGOT_MEDIUM"
    output-amount: 1
  GOLD_INGOT_MEDIUM:
    input: "MATERIAL/GOLD_ORE"
    input-amount: 3
    output: "MATERIAL/GOLD_INGOT_MEDIUM"
    output-amount: 1
  DIAMOND_MEDIUM:
    input: "MATERIAL/DIAMOND_ORE"
    input-amount: 3
    output: "MATERIAL/DIAMOND_MEDIUM"
    output-amount: 1
```

| 欄位 | 說明 |
|------|------|
| `input` | 輸入材料（`MATERIAL/<MMOItems ID>` 格式） |
| `input-amount` | 需要的數量 |
| `output` | 輸出材料（`MATERIAL/<MMOItems ID>` 格式） |
| `output-amount` | 輸出數量 |

配方 ID（最外層 key）僅作識別用，顯示名稱從 MMOItems 物品定義讀取。
