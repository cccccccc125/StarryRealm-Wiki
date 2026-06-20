# 分類與條目

圖鑑條目以**大類 → 子分類 → 條目**三層結構組織。分類樹定義在 `categories.yml`，條目來源則分為自動掃描與手動兩種。

## 分類樹

以下為 `categories.yml` 中定義的 5 大類及其子分類：

### mobs — 自定義生物

| 子分類 ID | 顯示名稱 | 圖示 |
|-----------|----------|------|
| `mythicmobs` | MythicMobs | `ZOMBIE_HEAD` |

### professions — 副職業產物

| 子分類 ID | 顯示名稱 | 圖示 |
|-----------|----------|------|
| `mining` | 採礦 | `IRON_PICKAXE` |
| `woodcutting` | 伐木 | `IRON_AXE` |
| `alchemy` | 鍊藥 | `BREWING_STAND` |
| `enchanting` | 附魔 | `ENCHANTING_TABLE` |
| `smithing` | 鍛造 | `ANVIL` |
| `farming` | 耕作 | `WHEAT` |
| `fishing` | 釣魚 | `FISHING_ROD` |

### appearance — 外觀

| 子分類 ID | 顯示名稱 | 圖示 |
|-----------|----------|------|
| `mounts` | 坐騎 | `SADDLE` |
| `pets` | 寵物 | `BONE` |
| `costumes` | 時裝 | `LEATHER_CHESTPLATE` |

### multiplayer — 多人玩法特殊道具

| 子分類 ID | 顯示名稱 | 圖示 |
|-----------|----------|------|
| `guild` | 公會 | `BANNER` |
| `friends` | 結義（好友） | `NAME_TAG` |
| `romance` | 俠緣（情侶） | `POPPY` |
| `mentor` | 師徒 | `BOOK` |

### items — 物品

| 子分類 ID | 顯示名稱 | 圖示 |
|-----------|----------|------|
| `weapons` | 武器 | `DIAMOND_SWORD` |
| `armor` | 防具 | `DIAMOND_CHESTPLATE` |
| `accessories` | 飾品 | `AMETHYST_SHARD` |
| `medicine` | 藥品 | `POTION` |
| `tools` | 道具 | `COMPASS` |
| `food` | 料理 | `COOKED_BEEF` |

## categories.yml 結構

每個大類包含 `name`（顯示名稱，支援 `&` 色碼）、`icon`（Material 名稱）、`slot`（GUI 格位）、`order`（排序）以及 `subcategories` 子節點。子分類的結構相同。

```yaml
categories:
  mobs:
    name: '&c自定義生物'
    icon: ZOMBIE_HEAD
    slot: 10
    order: 0
    subcategories:
      mythicmobs:
        name: '&cMythicMobs'
        icon: ZOMBIE_HEAD
        slot: 13
        order: 0
```

### 新增分類或子分類

1. 在 `categories.yml` 的 `categories` 下新增大類節點，設定 `name`、`icon`、`slot`、`order`。
2. 在該大類的 `subcategories` 下新增子分類節點。
3. 執行 `/compendium reload` 或重啟伺服器使其生效。

## 條目來源

### 自動掃描

插件內建兩個掃描器，會在啟動時或執行 `/compendium sync` 時讀取其他插件的 YAML 檔案，自動產生圖鑑條目。

> **設計決策**：掃描器**直接讀取對方插件的 YAML 檔案，不呼叫其 Java API**。這是刻意的設計——藉此避開 MMOItems / MythicLib 版本不合時的 `NoSuchMethodError` 連鎖崩潰。

掃描結果寫入 `generated/` 目錄：

| 檔案 | 來源 |
|------|------|
| `generated/auto-mythicmobs.yml` | MythicMobs 自定義生物掃描結果 |
| `generated/auto-mmoitems.yml` | MMOItems 物品掃描結果 |

#### MythicMobs 掃描設定

`config.yml` 中的 `auto-sync.mythicmobs` 區段控制 MythicMobs 掃描行為：

| 鍵 | 預設值 | 說明 |
|----|--------|------|
| `enabled` | `true` | 是否啟用 MythicMobs 掃描 |
| `folder` | `MythicMobs/mobs` | 掃描目標資料夾（相對於 `plugins/`） |
| `generated-file` | `generated/auto-mythicmobs.yml` | 掃描結果輸出路徑 |
| `default-category` | `mobs.mythicmobs` | 預設分類路徑（`大類.子分類`） |
| `ignored-files` | `ExampleMobs.yml`、`VanillaMobs.yml` | 忽略的檔案清單 |
| `category-overrides` | `{}` | 個別生物 ID 的分類覆寫（格式：`生物ID: 大類.子分類`） |
| `type-icons` | 見下表 | 依 MythicMobs 的 `Type` 欄位對應 GUI 圖示 |

預設 `type-icons` 對應：

| Type | 圖示 |
|------|------|
| `ZOMBIE` | `ZOMBIE_HEAD` |
| `SKELETON` | `SKELETON_SKULL` |
| `CREEPER` | `CREEPER_HEAD` |
| `ARMOR_STAND` | `ARMOR_STAND` |

#### MMOItems 掃描設定與類型對應

`config.yml` 中的 `auto-sync.mmoitems` 區段控制 MMOItems 掃描行為：

| 鍵 | 預設值 | 說明 |
|----|--------|------|
| `enabled` | `true` | 是否啟用 MMOItems 掃描 |
| `folder` | `MMOItems/item` | 掃描目標資料夾（相對於 `plugins/`） |
| `generated-file` | `generated/auto-mmoitems.yml` | 掃描結果輸出路徑 |

`type-map` 定義 MMOItems 類型（type）到圖鑑分類的對應。格式為 `MMOItems類型: 大類.子分類`，可自由覆寫。

**武器類** (`items.weapons`)：

| MMOItems Type |
|---------------|
| `SWORD`、`DAGGER`、`SPEAR`、`HAMMER`、`GAUNTLET`、`WHIP`、`STAFF`、`BOW`、`CROSSBOW`、`MUSKET` |
| `AXE`、`GREATAXE`、`GREATSWORD`、`GREATHAMMER`、`GREATSTAFF`、`LONG_SWORD`、`KATANA`、`HALBERD`、`LANCE`、`THRUSTING_SWORD` |

**防具類** (`items.armor`)：

| MMOItems Type |
|---------------|
| `ARMOR`、`SHIELD` |

**飾品類** (`items.accessories`)：

| MMOItems Type |
|---------------|
| `TOME`、`TALISMAN`、`ORNAMENT`、`ACCESSORY`、`CATALYST`、`OFF_CATALYST`、`MAIN_CATALYST` |

**藥品類** (`items.medicine`)：

| MMOItems Type |
|---------------|
| `CONSUMABLE` |

**料理類** (`items.food`)：

| MMOItems Type |
|---------------|
| `FOOD` |

**道具類** (`items.tools`)：

| MMOItems Type |
|---------------|
| `MATERIAL`、`MISCELLANEOUS`、`TOOL`、`BLOCK`、`GEM_STONE`、`FUNNY` |

**副職業類**：

| MMOItems Type | 圖鑑分類 |
|---------------|----------|
| `MINING` | `professions.mining` |
| `WOODCUTTING` | `professions.woodcutting` |
| `ALCHEMY` | `professions.alchemy` |
| `ENCHANTING` | `professions.enchanting` |
| `SMITHING` | `professions.smithing` |
| `FARMING` | `professions.farming` |
| `FISHING`、`ROD`、`HOOK`、`BAIT`、`LINE` | `professions.fishing` |

**外觀類**：

| MMOItems Type | 圖鑑分類 |
|---------------|----------|
| `MOUNT` | `appearance.mounts` |
| `PET` | `appearance.pets` |
| `SKIN`、`COSTUME` | `appearance.costumes` |

**多人玩法類**：

| MMOItems Type | 圖鑑分類 |
|---------------|----------|
| `GUILD` | `multiplayer.guild` |
| `FRIEND` | `multiplayer.friends` |
| `PARTNER`、`COUPLE` | `multiplayer.romance` |
| `MENTOR` | `multiplayer.mentor` |

若 `type-map` 中未列出某個 MMOItems 類型，掃描器會使用內建的預設對應規則（與上表一致）。你可以在 `type-map` 中新增或覆寫任何對應，例如：

```yaml
auto-sync:
  mmoitems:
    type-map:
      MY_CUSTOM_TYPE: items.weapons
```

### 手動條目

並非所有條目都能自動掃描。部分條目的來源異質——例如公會/結義走 MMOCore、時裝走材質包、俠緣/師徒走未來插件——這些條目需在 `entries/manual.yml` 中手動定義。

手動條目的解鎖透過管理指令 `/compendium unlock` 或外部插件呼叫 `forceUnlock` 來觸發。

詳細指令說明見 [指令](/jiuyucompendium/commands)。

## 相關章節

- [解鎖機制](/jiuyucompendium/discovery) — 條目如何被解鎖
- [設定檔](/jiuyucompendium/config) — `config.yml` 完整設定
- [指令](/jiuyucompendium/commands) — `/compendium sync` 等管理指令
