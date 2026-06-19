# 解鎖機制

JiuyuCompendium 採用**互動即解鎖**模型——玩家在遊戲中與內容互動就自動解鎖對應的圖鑑條目，無需完成任務或輸入指令。

## 自動解鎖途徑

### 擊殺 MythicMobs 自定義生物

由 `MythicMobListener` 監聽 `MythicMobDeathEvent`。當玩家擊殺 MythicMobs 自定義生物時，插件以該生物的內部名稱（`internalName`）比對圖鑑條目，立即解鎖。

- discover key 格式：`mythic:<生物內部名稱>`

### 取得 MMOItems 物品

由 `ItemDiscoveryListener` 監聽物品取得事件。觸發時機包含：

- **撿取物品** — 監聽 `EntityPickupItemEvent`，玩家撿起地上的 MMOItems 物品時觸發。
- **背包掃描** — 監聽 `InventoryClickEvent`，玩家點擊背包時掃描背包中所有物品（受 `scan-inventory-on-click` 控制）。另外，玩家登入時也會掃描一次背包（受 `scan-inventory-on-join` 控制）。

插件透過反射讀取物品的 NBT 標籤（`MMOITEMS_ITEM_TYPE` + `MMOITEMS_ITEM_ID`）來識別 MMOItems 物品。

- discover key 格式：`mmoitem:<類型>.<物品ID>`

### 擊殺原生生物

由 `VanillaMobListener` 監聽 `EntityDeathEvent`。當玩家擊殺原版 Minecraft 生物時，以該生物的 `EntityType` 名稱比對圖鑑條目。

- discover key 格式：`mob:<實體類型名稱>`
- 此功能受 `discovery.track-vanilla-mobs` 控制，預設啟用。

## 掃描器設計

插件的掃描器（`MythicMobsScanner`、`MMOItemsScanner`）負責在啟動或執行 `/compendium sync` 時讀取其他插件的資料並產生圖鑑條目。

> **關鍵設計決策**：掃描器**直接讀取對方插件的 YAML 檔案，不呼叫其 Java API**。這是刻意的設計——藉此避開 MMOItems / MythicLib 版本不合時的 `NoSuchMethodError` 連鎖崩潰。維護時切勿改成直接呼叫對方 API。

- `MythicMobsScanner` 讀取 `plugins/MythicMobs/mobs/` 下的 YAML 檔案，解析每個生物定義的 `Type`、`Display`、`Health` 等欄位。
- `MMOItemsScanner` 讀取 `plugins/MMOItems/item/` 下的 YAML 檔案，依子目錄名稱判斷物品類型，再透過 `type-map` 對應到圖鑑分類。

掃描結果寫入 `generated/` 目錄（`auto-mythicmobs.yml`、`auto-mmoitems.yml`），詳見 [分類與條目](/jiuyucompendium/categories)。

## 手動解鎖

部分條目來源異質（如公會/結義走 MMOCore、時裝走材質包），無法自動偵測。這些條目需透過以下方式解鎖：

- 管理指令 `/compendium unlock <玩家> <entryId|discoverKey>`
- 外部插件呼叫 `forceUnlock`

詳見 [指令](/jiuyucompendium/commands)。

## discovery 設定

`config.yml` 的 `discovery` 區段控制解鎖行為：

| 鍵 | 預設值 | 說明 |
|----|--------|------|
| `notify` | `true` | 解鎖時是否在聊天欄顯示通知訊息 |
| `sound` | `ENTITY_EXPERIENCE_ORB_PICKUP` | 解鎖時播放的音效（設為空字串可停用） |
| `scan-inventory-on-join` | `true` | 玩家登入時是否掃描背包中的 MMOItems 並解鎖對應條目 |
| `scan-inventory-on-click` | `true` | 玩家點擊背包時是否掃描所有物品並解鎖對應條目 |
| `track-vanilla-mobs` | `true` | 是否追蹤原生生物擊殺並解鎖對應條目 |

```yaml
discovery:
  notify: true
  sound: ENTITY_EXPERIENCE_ORB_PICKUP
  scan-inventory-on-join: true
  scan-inventory-on-click: true
  track-vanilla-mobs: true
```

## 相關章節

- [分類與條目](/jiuyucompendium/categories) — 分類樹與條目結構
- [設定檔](/jiuyucompendium/config) — `config.yml` 完整設定
- [事件與 API](/jiuyucompendium/api) — `CompendiumDiscoverEvent` 解鎖事件
