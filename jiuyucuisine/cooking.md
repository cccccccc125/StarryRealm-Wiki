# 烹飪流程與熟練度

## 開啟烹飪站

玩家**右鍵點擊「釜」方塊**即可開啟烹飪 GUI。相關設定位於 `config.yml` 的 `cauldron` 區段：

| 鍵 | 預設值 | 說明 |
|----|--------|------|
| `cauldron.block` | `CAULDRON` | 觸發烹飪 GUI 的方塊類型 |
| `cauldron.accept-any-cauldron` | `true` | 是否也接受 `WATER_CAULDRON`、`LAVA_CAULDRON` 等變體 |
| `cauldron.require-empty-hand` | `false` | 設為 `true` 時必須空手右鍵才會開啟（避免與打水等操作衝突） |

```yaml
cauldron:
  block: CAULDRON
  accept-any-cauldron: true
  require-empty-hand: false
```

## 烹飪 GUI

開啟後會出現一個容器介面，玩家可將食材放入**食材格**，再點擊**烹飪按鈕**開始烹飪。

| 鍵 | 預設值 | 說明 |
|----|--------|------|
| `gui.title` | `&8玖域釜` | GUI 標題（支援 `&` 色碼） |
| `gui.input-slots` | `9` | 食材格數量（格子編號 0 到 N-1） |
| `gui.cook-slot` | `26` | 「開始烹飪」按鈕所在的格子編號 |
| `gui.cook-button-material` | `BLAZE_POWDER` | 烹飪按鈕的顯示材質 |
| `gui.cook-button-name` | `&6▶ 開始烹飪` | 烹飪按鈕的顯示名稱 |

```yaml
gui:
  title: '&8玖域釜'
  input-slots: 9
  cook-slot: 26
  cook-button-material: BLAZE_POWDER
  cook-button-name: '&6▶ 開始烹飪'
```

- 食材格以外且非烹飪按鈕的格子會以灰色玻璃板填充，不可放入物品。
- **關閉介面時自動退還食材**：玩家關閉 GUI 時，食材格中剩餘的物品會歸還至玩家背包；背包已滿則掉落在腳邊。

## 食譜比對

點擊烹飪按鈕後，系統會將鍋中所有食材**歸納**為「食材身分 + 數量」的集合，接著進行**精確集合比對**：

1. 每個食材以 **MMOItems Type + ID** 識別（讀取物品 NBT 上的 `MMOITEMS_ITEM_TYPE` / `MMOITEMS_ITEM_ID`）。若非 MMOItems 物品，則以原版 Material 名稱識別。
2. 歸納後的集合（食材種類與數量）必須**與某一食譜完全一致**——種類和數量都不能多、也不能少。
3. 系統會依序掃描所有料理的所有食譜，第一個完全匹配的食譜即為命中。
4. **沒有命中任何食譜** → 視為失敗，直接產出「失敗的料理」。

食譜的定義方式詳見 [食譜設定](/jiuyucuisine/recipes)。

## 熟練度與結果判定

JiuyuCuisine **沒有等級系統**，僅有**熟練度**——每道料理各自累計製作次數。熟練度會影響成功率與大成功機率。

### 成功率

```
成功率 = base-success-rate + 熟練度 × success-per-proficiency
```

上限受 `max-success-rate` 約束。

| 鍵 | 預設值 | 說明 |
|----|--------|------|
| `cooking.base-success-rate` | `0.70` | 熟練度 0 時的基礎成功率（70%） |
| `cooking.success-per-proficiency` | `0.01` | 每 1 點熟練度增加的成功率（+1%） |
| `cooking.max-success-rate` | `0.98` | 成功率上限（98%） |

### 大成功（暴擊）

```
大成功率 = crit-base-rate + 熟練度 × crit-per-proficiency
```

大成功時品質 **+1**。

| 鍵 | 預設值 | 說明 |
|----|--------|------|
| `cooking.crit-base-rate` | `0.05` | 大成功基礎機率（5%） |
| `cooking.crit-per-proficiency` | `0.004` | 每 1 點熟練度增加的大成功機率（+0.4%） |

### 品質計算

成功烹飪時，品質的計算方式為：

```
品質 = 1 + 食譜 quality-mod + (大成功 ? 1 : 0)
```

品質最低為 1。部分食譜可透過 `quality-mod` 修正品質（例如替代食譜可能設定 `quality-mod: -1`）。

### 結果分支

| 結果 | 條件 | 產出 | 熟練度 |
|------|------|------|--------|
| **失敗（未命中食譜）** | 食材組合不符合任何食譜 | 「失敗的料理」 | 不累積 |
| **失敗（未通過成功率）** | 命中食譜但未通過成功率判定 | 「失敗的料理」 | +1 |
| **成功** | 通過成功率判定 | 料理成品（對應品質） | +1 |
| **大成功** | 成功後再通過大成功率判定 | 料理成品（品質再 +1） | +1 |

### 失敗的料理

失敗時產出的物品由 `cooking.fail-dish` 控制：

| 鍵 | 預設值 | 說明 |
|----|--------|------|
| `cooking.fail-dish.mmoitem-type` | `''`（空） | MMOItems 類型；留空則使用原版物品 |
| `cooking.fail-dish.mmoitem-id` | `''`（空） | MMOItems ID；留空則使用原版物品 |

兩者皆為空時，失敗產出為原版 `POISONOUS_POTATO`（顯示名稱「失敗的料理」）。

## 品質與 Buff 時長

料理食用時會觸發 **MMOBuffs** 狀態效果。狀態的實際時長取決於品質星級：

```
實際時長 = buff 基礎時長 × quality-duration-multiplier[品質]
```

| 鍵 | 預設值 | 說明 |
|----|--------|------|
| `cooking.quality-duration-multiplier.1` | `0.8` | 品質 1 星的時長倍率 |
| `cooking.quality-duration-multiplier.2` | `1.0` | 品質 2 星的時長倍率 |
| `cooking.quality-duration-multiplier.3` | `1.3` | 品質 3 星的時長倍率 |

例如一道料理的 `base-duration` 為 300 秒、品質 3 星，則實際時長為 300 × 1.3 = 390 秒。

## 音效

烹飪結果會播放對應音效：

| 鍵 | 預設值 | 說明 |
|----|--------|------|
| `cooking.sound-success` | `ENTITY_PLAYER_BURP` | 成功時音效 |
| `cooking.sound-crit` | `ENTITY_PLAYER_LEVELUP` | 大成功時音效 |
| `cooking.sound-fail` | `BLOCK_FIRE_EXTINGUISH` | 失敗時音效 |

## 成品 PDC 標記

每份成功烹飪的料理成品上會寫入兩個 **PersistentDataContainer（PDC）** 標記：

| 鍵 | 類型 | 說明 |
|----|------|------|
| `dish` | `STRING` | 料理 ID（如 `hong_shao_rou`） |
| `quality` | `INTEGER` | 品質星級（整數，最低 1） |

`ConsumeListener` 會在玩家食用時讀取這兩個標記，查詢對應的 buff 效果與時長倍率，再透過 MMOBuffs 套用狀態。

## 首次製作解鎖圖鑑

玩家首次成功製作某道料理時，插件會透過 `CompendiumBridge` 通知 JiuyuCompendium 解鎖對應的圖鑑條目（`items.food` 分類）。此功能由 `compendium.enabled` 控制。

詳見 [外掛連動](/jiuyucuisine/integration)。

## 相關章節

- [食譜設定](/jiuyucuisine/recipes) — `recipes.yml` 食譜定義格式
- [設定檔](/jiuyucuisine/config) — `config.yml` 完整設定
- [指令](/jiuyucuisine/commands) — `/cuisine` 指令
