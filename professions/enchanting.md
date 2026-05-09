# ✨ 刻印 (Enchanting)

## 概述

11 種自訂刻印，透過捲軸 + 鐵砧應用至工具上。使用 PDC（PersistentDataContainer）儲存刻印資料，不依賴原版附魔系統。

## 設定檔 (`enchanting/enchanting.yml`)

### 刻印定義格式

```yaml
enchants:
  vein_sense:
    name: "&b礦脈感知"
    description: "&7增加採礦經驗 {level}%"
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
| `description` | Lore 描述（`{level}` 自動替換為等級） |
| `lore-color` | Lore 顏色碼 |
| `scroll-material` | 捲軸物品類型（預設 ENCHANTED_BOOK） |
| `scroll-custom-model-data` | 捲軸 CustomModelData |
| `max-level` | 最高強化等級 |
| `applicable-to` | 適用的工具材質列表 |
| `anvil-cost` | 鐵砧花費等級 |
| `chance-per-level` | 各等級的效果數值 |

## 刻印列表（11 種）

| ID | 名稱 | 最高等級 | 效果 | 適用工具 |
|------|------|:--:|------|------|
| `chain_gathering` | 連鎖採集 | 1 | 連鎖破壞相鄰同類方塊 | 斧、鎬、鋤 |
| `replant` | 補種 | 1 | 自動補種作物 | 鋤 |
| `double_harvest` | 雙倍採集 | 3 | 3/5/7% 雙倍掉落 | 斧、鎬、鋤 |
| `green_thumb` | 綠手指 | 3 | 5/10/15% 節省鋤頭耐久 | 鋤 |
| `cultivator` | 精耕 | 3 | 5/10/15% 耕作經驗加成 | 鋤 |
| `vein_sense` | 礦脈感知 | 3 | 5/10/15% 採礦經驗加成 | 鎬 |
| `forester` | 伐木專精 | 3 | 5/10/15% 伐木經驗加成 | 斧 |
| `angler` | 釣運 | 3 | 5/10/15% 釣魚經驗加成 | 釣竿 |
| `herbalist` | 藥感 | 3 | 5/10/15% 草藥經驗加成 | 任意手持 |
| `lifesteal` | 吸血 | 3 | 0.5/1.0/1.5% 最大生命恢復 | 劍 |
| `auto_collect` | 自動拾取 | 1 | 採集時掉落物直接進入背包 | 斧、鎬、鋤 |

## 使用方式

1. 管理員發放捲軸：`/subcareers enchant give <player> <刻印ID> <等級>`
2. 玩家將工具放鐵砧左槽、捲軸放右槽
3. 滿足以下條件即可刻印：
   - 工具類型在 `applicable-to` 清單中
   - 工具上沒有同等或更高級的同種刻印
4. 消耗設定的經驗等級（`anvil-cost`）

## PDC 儲存結構

### 捲軸
- Key: `enchant_scroll_id` — 刻印 ID（String）
- Key: `enchant_scroll_level` — 刻印等級（Integer）

### 已刻印工具
- Key: `enchant_<id>` — 刻印等級（Integer）

使用 `PersistentDataContainer` API，不會與原版附魔衝突。

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

打 `/subcareers` → 點選刻印 → 點選刻印列表，查看所有刻印及其效果。
