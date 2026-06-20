# 食譜設定

食譜定義在 `plugins/JiuyuCuisine/recipes.yml`。每道料理（dish）可擁有**多組食譜**（recipes），玩家使用任何一組湊齊食材都能做出同一道料理——這就是「**同料理多食譜 + 自由探索鍋**」的設計。

## 整體結構

```yaml
dishes:
  <dish_id>:
    display: '<顯示名稱>'
    result:
      mmoitem-type: <MMOItems 類型>
      mmoitem-id: <MMOItems ID>
      amount: <數量>
    buff:
      effect: <MMOBuffs 狀態 ID>
      base-duration: <基礎時長（秒）>
      stacks: <層數>
    recipes:
      <食譜代號>:
        ingredients:
          - '<TYPE> <ID> <數量>'
        quality-mod: <品質修正>   # 選填，預設 0
```

所有料理定義在頂層 `dishes` 節點下，以 `dish_id` 作為唯一識別鍵。

## 欄位說明

### dish 層級

| 欄位 | 必填 | 說明 |
|------|------|------|
| `display` | 是 | 料理顯示名稱（支援 `&` 色碼） |
| `result` | 是 | 烹飪成功時產出的物品（MMOItems） |
| `buff` | 是 | 食用時套用的 MMOBuffs 狀態效果 |
| `recipes` | 是 | 該料理的所有食譜做法 |

### result — 產出物品

| 欄位 | 說明 |
|------|------|
| `mmoitem-type` | MMOItems 物品類型（如 `CONSUMABLE`） |
| `mmoitem-id` | MMOItems 物品 ID（如 `HONG_SHAO_ROU`） |
| `amount` | 產出數量 |

產出物品需事先在 MMOItems 中建立。若找不到對應的 MMOItems 物品，插件會 fallback 為原版 `COOKED_BEEF` 作為替代。

### buff — 食用效果

| 欄位 | 說明 |
|------|------|
| `effect` | MMOBuffs `effects.yml` 中定義的狀態 ID |
| `base-duration` | 基礎時長（秒）；實際時長 = `base-duration` × 品質倍率（見 [品質與 Buff 時長](/jiuyucuisine/cooking#品質與-buff-時長)） |
| `stacks` | 套用的層數 |

### recipes — 食譜做法

每道料理的 `recipes` 下可定義多組食譜，每組以自訂的代號（如 `classic`、`honey`）作為鍵。

| 欄位 | 必填 | 說明 |
|------|------|------|
| `ingredients` | 是 | 食材清單（字串陣列） |
| `quality-mod` | 否 | 此食譜的品質修正值（整數，預設 0）；例如替代食譜可設為 `-1` 使品質略低 |

## 食材格式

每條食材以**空格分隔的字串**表示，有兩種格式：

### MMOItems 食材

```
<TYPE> <ID> <數量>
```

- `TYPE`：MMOItems 物品類型（如 `MATERIAL`、`CONSUMABLE`）
- `ID`：MMOItems 物品 ID（如 `PORK_RAW`、`WILD_HONEY`）
- `數量`：所需數量（整數，省略時預設 1）

範例：`MATERIAL PORK_RAW 2` 表示需要 2 個 MMOItems 類型 `MATERIAL`、ID 為 `PORK_RAW` 的物品。

### 原版食材

```
vanilla <MATERIAL> <數量>
```

- `vanilla`：固定關鍵字，表示使用原版 Minecraft 材質
- `MATERIAL`：原版材質名稱（如 `WATER_BUCKET`）
- `數量`：所需數量（整數，省略時預設 1）

範例：`vanilla WATER_BUCKET 1` 表示需要 1 個原版水桶。

## 食材辨識原理

插件以 **MMOItems Type + ID** 作為食材身分識別。具體做法是讀取物品 NBT 上的 `MMOITEMS_ITEM_TYPE` 和 `MMOITEMS_ITEM_ID` 欄位。

食材通常來自**副職業產物**（如採礦、耕作等副職業的 MMOItems 產出）。選用 Type+ID 而非自訂標籤是因為 MMOItems 沒有自由 tag 機制，直接使用其原生 NBT 最為可靠。

非 MMOItems 的原版物品則以 Material 名稱識別（對應 `vanilla` 格式）。

## 比對規則

烹飪時的食譜比對採**精確集合比對**：鍋中食材歸納後的種類與數量必須與某一食譜**完全一致**——不能多放、也不能少放。

詳細比對流程見 [烹飪流程與熟練度](/jiuyucuisine/cooking#食譜比對)。

## 完整範例

以下為 `recipes.yml` 中的兩道實際料理定義：

```yaml
dishes:

  hong_shao_rou:
    display: '&c紅燒肉'
    result:
      mmoitem-type: CONSUMABLE
      mmoitem-id: HONG_SHAO_ROU
      amount: 1
    buff:
      effect: feast_attack
      base-duration: 300
      stacks: 1
    recipes:
      # 正統做法
      classic:
        ingredients:
          - 'MATERIAL PORK_RAW 2'
          - 'MATERIAL SUGAR_LUMP 1'
          - 'MATERIAL SOY_SAUCE 1'
      # 替代做法（沒醬油時用蜂蜜，品質略低）
      honey:
        ingredients:
          - 'MATERIAL PORK_RAW 2'
          - 'CONSUMABLE WILD_HONEY 1'
        quality-mod: -1

  cao_yao_tang:
    display: '&a草藥湯'
    result:
      mmoitem-type: CONSUMABLE
      mmoitem-id: HERB_SOUP
      amount: 1
    buff:
      effect: regen_minor
      base-duration: 180
      stacks: 1
    recipes:
      basic:
        ingredients:
          - 'MATERIAL GINSENG 1'
          - 'MATERIAL HERB_LEAF 2'
          - 'vanilla WATER_BUCKET 1'
```

### 範例解析

**紅燒肉**（`hong_shao_rou`）：

- 成品為 MMOItems `CONSUMABLE` 類型的 `HONG_SHAO_ROU`，產出 1 個。
- 食用後套用 `feast_attack` 狀態，基礎時長 300 秒，1 層。
- 有兩組食譜做法：
  - `classic`（正統做法）：2 個 `MATERIAL PORK_RAW` + 1 個 `MATERIAL SUGAR_LUMP` + 1 個 `MATERIAL SOY_SAUCE`。
  - `honey`（替代做法）：2 個 `MATERIAL PORK_RAW` + 1 個 `CONSUMABLE WILD_HONEY`，但 `quality-mod: -1` 使品質降低 1 級。

**草藥湯**（`cao_yao_tang`）：

- 成品為 MMOItems `CONSUMABLE` 類型的 `HERB_SOUP`，產出 1 個。
- 食用後套用 `regen_minor` 狀態，基礎時長 180 秒，1 層。
- 單一食譜：1 個 `MATERIAL GINSENG` + 2 個 `MATERIAL HERB_LEAF` + 1 個原版 `WATER_BUCKET`。

## 新增食譜

1. 在 `recipes.yml` 的 `dishes` 下新增一個料理節點。
2. 設定 `display`、`result`（需在 MMOItems 中先建好對應物品）、`buff`（需在 MMOBuffs `effects.yml` 中先定義狀態）。
3. 在 `recipes` 下定義至少一組食譜，列出所有食材。
4. 執行 `/cuisine reload` 或重啟伺服器使食譜生效。

## 相關章節

- [烹飪流程與熟練度](/jiuyucuisine/cooking) — 比對流程、熟練度、成功率
- [設定檔](/jiuyucuisine/config) — `config.yml` 完整設定
- [外掛連動](/jiuyucuisine/integration) — MMOItems、MMOBuffs 整合
