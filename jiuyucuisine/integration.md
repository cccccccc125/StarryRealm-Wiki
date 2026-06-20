# 外掛連動

JiuyuCuisine 透過三個**反射橋接**（reflection bridge）與外部插件互動，避免編譯期硬綁特定版本的 jar。所有橋接類位於 `com.jiuyu.cuisine.bridge` 套件。

## MMOItemsRef — MMOItems 物品橋接

`MMOItemsRef` 負責兩件事：**讀取**物品上的 MMOItems 身分（食材辨識）與**產生** MMOItems 物品（料理產出）。

### 食材辨識（read）

透過反射取得 MythicLib 的 `NBTItem`，呼叫：

1. `NBTItem.get(ItemStack)` — 取得該物品的 NBT 包裝物件。
2. `NBTItem.getString("MMOITEMS_ITEM_TYPE")` / `NBTItem.getString("MMOITEMS_ITEM_ID")` — 讀取 MMOItems 寫入的 Type 與 ID。

回傳 `Optional<String[]>` 包含 `[type, id]`；非 MMOItems 物品或反射失敗時回傳 `empty`。

初始化時透過 `Class.forName("io.lumine.mythic.lib.api.item.NBTItem")` 載入 NBTItem 類別並快取 `get` 與 `getString` 兩個 Method 物件。若 MythicLib 不存在，`nbtReady` 設為 `false`，食材辨識功能停用。

### 物品產生（build）

透過反射取得 MMOItems 主類的靜態實例，呼叫：

1. `MMOItems.plugin`（靜態欄位）— 取得插件實例。
2. `MMOItems.getTypes()` — 取得 `TypeManager`。
3. `TypeManager.get(String)` — 依名稱取得 `Type` 物件。
4. `MMOItems.getItem(Type, String)` — 依 Type + ID 產生物品。

回傳 `Optional<ItemStack>`；Type 不存在、物品為 AIR 或反射失敗時回傳 `empty`。呼叫端收到 `empty` 時會 fallback 為原版 `COOKED_BEEF`。

初始化時透過 `Class.forName("net.Indyuce.mmoitems.MMOItems")` 載入 MMOItems 類別，並快取 `plugin` 欄位、`getTypes` 方法、`Type` 類別與 `getItem` 方法。若綁定失敗，`buildReady` 設為 `false`，啟動日誌會印出 `MMOItems產出=false`。

### 偵測方法

`MMOItemsRef.isMMOItemsPresent()` 以 `Bukkit.getPluginManager().getPlugin("MMOItems")` 判斷 MMOItems 是否在線。`nbtReady()` 與 `buildReady()` 分別回報讀取與產生功能是否可用。

## MMOBuffsBridge — MMOBuffs 狀態橋接

`MMOBuffsBridge` 負責在玩家食用料理時套用 MMOBuffs 狀態效果。採用**控制台指令**方式（最不綁版本），不直接呼叫 MMOBuffs Java API。

### 運作方式

1. 建構時檢查 `Bukkit.getPluginManager().getPlugin("MMOBuffs")` 是否存在；若 config 啟用但插件不在線，記錄 warning。
2. `apply(Player, effect, durationSeconds, stacks)` 被呼叫時，從 config 讀取指令模板 `mmobuffs.apply-command`，替換四個佔位符：

| 佔位符 | 替換內容 |
|--------|----------|
| `{player}` | 玩家名稱 |
| `{effect}` | MMOBuffs 效果 ID（對應 `recipes.yml` 中每道料理的 `buff.effect`） |
| `{duration}` | 實際時長秒數（= 基礎時長 × 品質倍率） |
| `{stacks}` | 疊加層數（最小 1） |

3. 組合後透過 `Bukkit.dispatchCommand(Bukkit.getConsoleSender(), cmd)` 以控制台身分執行。

### 預設指令模板

```yaml
mmobuffs:
  apply-command: 'mmobuffs effect add {player} {effect} {duration} {stacks}'
```

`active()` 方法回傳 `true` 僅當 config 啟用且 MMOBuffs 插件在線。

## CompendiumBridge — JiuyuCompendium 圖鑑橋接

`CompendiumBridge` 在玩家**首次成功製作**某道料理時，通知 JiuyuCompendium 解鎖對應的圖鑑條目。

### 運作方式

1. 建構時檢查 `Bukkit.getPluginManager().getPlugin("JiuyuCompendium")` 是否存在。
2. `discover(Player, dishId)` 被呼叫時，組合控制台指令：
   ```
   compendium unlock <玩家名稱> <keyPrefix>:<dishId>
   ```
   其中 `keyPrefix` 取自 config 的 `compendium.discover-key-prefix`（預設 `cuisine`），若為空則自動回退為 `cuisine`。
3. 透過 `Bukkit.dispatchCommand(Bukkit.getConsoleSender(), cmd)` 執行。

### 相關設定

```yaml
compendium:
  enabled: true
  discover-key-prefix: 'cuisine'
```

`active()` 方法回傳 `true` 僅當 config 啟用且 JiuyuCompendium 插件在線。此功能可由 `compendium.enabled` 關閉。

## 待驗證事項

以下三點尚未經過實機驗證，部署後須在遊戲內逐一確認：

> **1. MMOItemsRef 反射簽名 vs MMOItems 6.10**
>
> 目前 `initBuild()` 假設 `MMOItems.plugin` 靜態欄位、`getTypes().get(name)` 與 `getItem(Type, String)` 的簽名。**需對 MMOItems 6.10 實際 API 驗證**。若簽名不匹配，反射綁定會失敗，`buildReady` 為 `false`，啟動日誌會印出 `MMOItems產出=false`，所有料理產出將 fallback 為原版 `COOKED_BEEF`。修正點在 `MMOItemsRef.initBuild()`。

> **2. MMOBuffs 套用指令簽名 vs MMOBuffs 1.2.3**
>
> config 預設的 `mmobuffs.apply-command` 為 `mmobuffs effect add {player} {effect} {duration} {stacks}`。此指令簽名**需對 MMOBuffs 1.2.3 實際指令校正**——到時在遊戲內測一條 `/mmobuffs ...` 並利用 tab 補全即可確認正確格式。若不符，修改 config 中的模板即可。

> **3. Compendium 解鎖 key 對應**
>
> `CompendiumBridge` 送出的 key 格式為 `cuisine:<dishId>`。但料理成品是 MMOItems consumable，玩家拿到時 JiuyuCompendium 的物品監聽通常**已自動收錄**（key 形如 `mmoitem:CONSUMABLE.<ID>`）。若 `cuisine:<dishId>` 在 JiuyuCompendium 沒有對應的 entry，此呼叫會無效。解法：在 JiuyuCompendium 的 `entries/manual.yml` 建立對應條目，或改送 mmoitem key。可由 config `compendium.enabled` 關閉此橋接。

## 相關章節

- [設定檔](/jiuyucuisine/config) — `mmobuffs`、`compendium`、`cooking.fail-dish` 等設定
- [烹飪流程與熟練度](/jiuyucuisine/cooking) — 品質倍率如何影響 buff 時長
- [食譜設定](/jiuyucuisine/recipes) — 每道料理的 `buff` 欄位定義
- [JiuyuCompendium 事件與 API](/jiuyucompendium/api) — 圖鑑系統的 API 與事件
