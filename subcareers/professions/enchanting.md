# ✨ 刻印 (Enchanting)

## 概述

11 種自訂刻印，透過卷軸 + 鐵砧應用至工具上。使用 PDC（PersistentDataContainer）儲存刻印資料，不依賴原版附魔系統。

刻印視覺風格仿原版附魔（灰色 lore + 羅馬數字），玩家獲取需先取得原版附魔成就，並有玩家路徑與管理員/活動兩條進階。

## 設定檔 (`enchanting/enchanting.yml`)

### 刻印定義格式

```yaml
enchantments:
  vein_sense:
    name: "礦脈感知"
    description: "採礦經驗值加成"
    lore-color: "§b"
    scroll-material: ENCHANTED_BOOK
    scroll-custom-model-data: 0
    max-level: 3
    applicable-to:
      - WOODEN_PICKAXE
      - STONE_PICKAXE
      - IRON_PICKAXE
      - GOLDEN_PICKAXE
      - DIAMOND_PICKAXE
      - NETHERITE_PICKAXE
    anvil-cost: 5
    chance-per-level:
      - 5.0
      - 10.0
      - 15.0
```

| 欄位 | 說明 |
|------|------|
| `name` | 顯示名稱 |
| `description` | 圖鑑說明 |
| `lore-color` | 保留欄位（目前 lore 統一用原版灰色 §7） |
| `scroll-material` | 卷軸物品類型（預設 `ENCHANTED_BOOK`） |
| `scroll-custom-model-data` | 卷軸 CustomModelData |
| `max-level` | **玩家正常途徑可達上限**（圖鑑與 tab 補完範圍） |
| `applicable-to` | 適用的工具材質列表 |
| `anvil-cost` | 鐵砧花費等級 |
| `chance-per-level` | 1..N 等的數值，超過 N 等用最後兩級差線性外推 |

## 前置條件（重要）

施加刻印前，**工具上必須有 ≥ 3 個達到該附魔最高等級的原版附魔**。

範例：劍上同時有
- Sharpness V (鋒利5，max=5)
- Fire Aspect II (燃燒2，max=2)
- Unbreaking III (耐久3，max=3)

三個都是「該附魔自身的 max-level」→ 符合條件，鐵砧才會顯示刻印結果。

> 未滿足條件時，鐵砧結果欄會顯示為空（無錯誤訊息）。圖鑑書本 lore 已標注此前置條件。

## 等級設計

### 兩條獲取路徑

| 路徑 | 等級範圍 | 取得方式 |
|------|---------|---------|
| 玩家正常 | 1 ~ `max-level`（如 1-3） | 鐵砧施加 |
| 管理員 / 活動 | 任意正整數 | `/subcareers enchant give <玩家> <刻印ID> <等級>` |

`max-level` 僅作為「玩家正常上限」的顯示與 tab 補完範圍。實際無等級上限，管理員可發放任意等級的卷軸。

### 機率公式

設 yml `chance-per-level = [v₁, v₂, ..., v_N]`：

- Lv 1..N → 直接取 `v_lv`
- Lv > N → 線性外推：`v_N + (lv - N) × (v_N - v_{N-1})`
- 結果 `Math.max(0, …)` 防護負值

| 刻印 | yml 設定 | Lv1 | Lv3 | Lv5 | Lv100 |
|------|---------|-----|-----|-----|-------|
| `double_harvest` | [3, 5, 7] | 3% | 7% | 11% | 201% |
| `lifesteal` | [0.5, 1, 1.5] | 0.5% | 1.5% | 2.5% | 50% |
| `vein_sense` | [5, 10, 15] | 5% | 15% | 25% | 500% |

機率超過 100% 對下游：
- 機率類（雙倍掉落、綠手指等）：≥1.0 必觸發
- 經驗倍率類（採礦/伐木/耕作/釣魚/草藥）：直接放大經驗
- `lifesteal`：被「最大生命值上限」自動 cap

## 刻印列表（11 種）

| ID | 名稱 | 玩家上限 | Lv1 效果 | 適用工具 |
|------|------|:--:|------|------|
| `chain_gathering` | 連鎖採集 | 1 | 連鎖破壞相鄰同類方塊 | 斧、鎬、鋤 |
| `replant` | 補種 | 1 | 自動補種作物 | 鋤 |
| `double_harvest` | 雙倍採集 | 3 | 3% 雙倍掉落 | 斧、鎬、鋤 |
| `green_thumb` | 綠手指 | 3 | 5% 節省鋤頭耐久 | 鋤 |
| `cultivator` | 精耕 | 3 | 5% 耕作經驗加成 | 鋤 |
| `vein_sense` | 礦脈感知 | 3 | 5% 採礦經驗加成 | 鎬 |
| `forester` | 伐木專精 | 3 | 5% 伐木經驗加成 | 斧 |
| `angler` | 釣運 | 3 | 5% 釣魚經驗加成 | 釣竿 |
| `herbalist` | 藥感 | 3 | 5% 草藥經驗加成 | 任意手持 |
| `lifesteal` | 吸血 | 3 | 0.5% 最大生命恢復 | 劍 |
| `auto_collect` | 自動拾取 | 1 | 採集時直接進背包 | 斧、鎬、鋤 |

## 使用方式

1. 取得卷軸：
   - 管理員 `/subcareers enchant give <player> <刻印ID> [等級]`
   - 活動 / 商店 / 任務獎勵（依伺服器設定）
2. 將**工具**放鐵砧左槽、**卷軸**放鐵砧右槽
3. 系統檢查：
   - 工具類型在 `applicable-to` 中
   - 工具當前刻印等級 < 卷軸等級
   - 工具上有 ≥ 3 個原版滿級附魔（**前置條件**）
4. 全部通過 → 結果欄顯示刻印後的工具，消耗 `anvil-cost` 等級

## 顯示風格（仿原版附魔）

- 卷軸：物品名顯示原版「附魔之書」，lore 唯一一行 `§7<刻印名> <等級>`
- 工具刻印 lore：`§7<刻印名> <等級>`，跟 vanilla `Sharpness V` 視覺一致
- 等級顯示：
  - Lv 1-10 → 羅馬數字（I, II, III, IV, V, VI, VII, VIII, IX, X）
  - Lv 11+ → 阿拉伯數字

## PDC 儲存結構

### 卷軸
- Key: `enchant_scroll_id` — 刻印 ID（String）
- Key: `enchant_scroll_level` — 刻印等級（Integer）

### 已刻印工具
- Key: `enchant_<id>` — 刻印等級（Integer，舊資料 BYTE 亦相容）

使用 `PersistentDataContainer` API，不會與原版附魔衝突，可與「強化」「升星」等系統並存。

## 刻印交叉使用表

| 刻印 | 採礦 | 伐木 | 耕作 | 釣魚 | 草藥 | 戰鬥 |
|------|:--:|:--:|:--:|:--:|:--:|:--:|
| `chain_gathering` | ✅ | ✅ | ✅ | | | |
| `replant` | | | ✅ | | | |
| `double_harvest` | ✅ | ✅ | ✅ | | | |
| `green_thumb` | | | ✅ | | | |
| `cultivator` | | | ✅ | | | |
| `vein_sense` | ✅ | | | | | |
| `forester` | | ✅ | | | | |
| `angler` | | | | ✅ | | |
| `herbalist` | | | | | ✅ | |
| `lifesteal` | | | | | | ✅ |
| `auto_collect` | ✅ | ✅ | ✅ | | | |

## 刻印圖鑑

打 `/subcareers` → 點選刻印 → 點選刻印列表，查看所有刻印及其各等級效果。
