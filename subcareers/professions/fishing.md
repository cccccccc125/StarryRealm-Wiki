# 🎣 釣魚 (Fishing)

## 概述

完整的釣魚系統，包含釣竿客製化、魚種收集、魚袋儲存、魚商人販售、以及分解桶。

## 設定檔結構

| 檔案 | 用途 |
|------|------|
| `fishing/settings.yml` | 主設定 — 經驗倍率、成就條件、釣竿力量、訊息 |
| `fishing/tiers.yml` | Tier 門檻與 EXP 獎勵 |
| `fishing/loot.yml` | 魚種掉落表（含條件） |
| `fishing/regions.yml` | 釣魚區域定義 |

## 釣魚力量（Fishing Power）

釣竿的力量值決定能釣到什麼 Tier 的魚。力量值來源：

1. 釣竿本身的 PDC `fishing_power`（透過釣竿編輯器設定）
2. 釣竿的 MMOItems 屬性 `MMOITEMS_FISHING_POWER`
3. 基礎值 = 10

### Tier 門檻 (`fishing/tiers.yml`)

```yaml
tiers:
  1:
    min-power: 0
    exp-reward: 5.0
  2:
    min-power: 100
    exp-reward: 12.0
  3:
    min-power: 300
    exp-reward: 25.0
  4:
    min-power: 600
    exp-reward: 50.0
```

## 魚種設定 (`fishing/loot.yml`)

每種魚可設定以下條件：

```yaml
loots:
  COMMON_CARP:
    display-name: "&f普通鯉魚"
    base-weight: 1.5        # 基礎重量 (kg)
    base-length: 25.0       # 基礎長度 (cm)
    base-price: 10.0        # 基礎售價
    tier: 1                 # 魚的 Tier
    weight: 50              # 權重（越高越容易抽到）
    conditions:
      weather: "clear"      # clear / storm
      solar-term: "立春"    # 需 SolarTermCore
      weight-multiplier: 1.5
      yield-bonus-chance: 20.0
    decompose:
      item: minecraft/BONE_MEAL
      amount: 3
```

### 條件系統

| 條件 | 說明 |
|------|------|
| `weather` | 限定晴天 (`clear`) 或暴風雨 (`storm`) |
| `solar-term` | 限定特定節氣（需 SolarTermCore） |
| `weight-multiplier` | 條件滿足時重量倍率 |
| `yield-bonus-chance` | 條件滿足時機率雙倍捕獲 |

## 釣竿編輯器

指令 `/rodeditor` 開啟 27 格 GUI：

| 欄位 | 功能 |
|------|------|
| Slot 11 | 釣竿本體 |
| Slot 13 | 釣線（提供額外釣魚力量） |
| Slot 15 | 釣鉤（提供額外釣魚力量） |

釣線和釣鉤的 PDC `fishing_power` 或 MMOItems 屬性 `MMOITEMS_FISHING_POWER` 會被讀取並加到釣竿總力量上。關閉 GUI 時自動計算：

```
totalPower = 10 + linePower + hookPower
```

結果寫入釣竿 PDC `fishing_power`。

## 🎽 釣魚套裝（Set Bonus）

穿戴成套的釣魚裝備可獲得**釣魚力量加成**，件數越多加成越高。

### 套裝件偵測

系統掃描下列欄位來計算玩家身上的套裝件數：

- **原生盔甲** — 頭、胸、腿、鞋（4 格）
- **MMOInventory 飾品** — 項鍊（`necklace`）、戒指（`ring`）

> MMOInventory 為軟依賴（`softdepend`）。未安裝時自動略過飾品欄，只計算原生盔甲。

凡帶有 `subcareers:fishing_set_piece` PDC 字串標籤（非空值）的裝備即計入。此標籤由外部賦予（例如 MMOItems 物品的自訂 NBT），插件本身不提供標記指令。

### 加成門檻 (`fishing/settings.yml`)

```yaml
set-bonus:
  2: 0.10   # 穿 2 件 → 釣魚力量 +10%
  4: 0.25   # 穿 4 件 → 釣魚力量 +25%
  6: 0.50   # 穿滿 6 件 → 釣魚力量 +50%
```

- 取「件數 ≥ 門檻」中最高的一檔加成
- 不足 2 件 → 無加成
- 最多 6 件（4 盔甲 + 項鍊 + 戒指）

### 套用方式

套裝倍率以**乘法**套用在玩家當下的總釣魚力量（釣竿 + 副手釣餌）之上：

```
finalPower = 總釣魚力量 × (1 + setMultiplier)
```

因此套裝會等比放大整體釣魚力量，進而提升可釣到的魚種 Tier。

## 魚袋（Fishbag）

27 格虛擬儲存空間，自動將釣到的魚存入。指令 `/fishbag`。

- 只接受有 `fish_loot_id` PDC 標籤的魚
- 禁止 shift-click 和非魚物品
- 關閉時自動儲存（非同步 MySQL/YAML）
- 魚袋滿時發送警告訊息

## 魚商人（Fish Merchant）

使用 MythicMobs NPC（需設定內部名稱為 `settings.yml > merchant.npc-name`）。互動時開啟 54 格販售 GUI。

### 販售計算

```
price = weight × basePrice × amount
```

### GUI 功能

| 功能 | 說明 |
|------|------|
| 分頁瀏覽 | 每頁 27 格 |
| 過濾模式 | All / Normal / Rare+ |
| Select All | 全選 |
| Deselect All | 全不選 |
| Recycle 模式 | 以 `recycleRate` 折價販售 |
| Income 顯示 | 即時顯示預估收入 |

## 分解桶（Decompose Barrel）

拿著魚右鍵點擊堆肥桶（Composter），消耗 1 條魚，獲得對應分解產物。從 `loot.yml` 的 `decompose` 設定讀取產物。

## 伺服器紀錄

每個魚種追蹤：
- `max_weight` — 伺服器最重紀錄
- `max_length` — 伺服器最長紀錄
- `holder_name` / `holder_uuid` — 紀錄保持人

破紀錄時全服廣播。

## 成就系統

追蹤 `total_caught` 計數器，達到設定門檻時觸發成就：

```yaml
achievements:
  counts: [1, 10, 100, 2000, 10000]
```

## 經驗公式

```
exp = tierExp × (1 + 0.01 × milestone) × (1 + anglerBoost)
```

anglerBoost = angler 附魔等級 × 0.05

## 相關刻印

| 刻印 | 效果 |
|------|------|
| `angler` (釣運 Lv1-3) | 5/10/15% 經驗加成 |
