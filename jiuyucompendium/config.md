# 設定檔（config.yml）

本頁說明 `plugins/JiuyuCompendium/config.yml` 的所有設定區段。修改後可用 `/compendium reload` 熱重載，不需重啟伺服器。

## 一般設定

| 鍵 | 預設值 | 說明 |
|----|--------|------|
| `language` | `zh_TW` | 插件語系 |
| `debug` | `false` | 啟用除錯日誌 |
| `autosave-ticks` | `6000` | 自動存檔間隔（tick；6000 tick = 5 分鐘） |

```yaml
language: zh_TW
debug: false
autosave-ticks: 6000
```

## storage — 儲存後端

支援三種儲存後端：`YAML`（預設）、`SQLITE`、`MYSQL`。JDBC 驅動以反射載入，缺驅動時自動回退為 YAML。

| 鍵 | 預設值 | 說明 |
|----|--------|------|
| `storage.type` | `YAML` | 儲存後端類型（`YAML` / `SQLITE` / `MYSQL`） |
| `storage.table-prefix` | `jiuyu_compendium_` | JDBC 模式下的資料表前綴 |
| `storage.sqlite.file` | `compendium.db` | SQLite 資料庫檔名（相對於插件資料夾） |
| `storage.mysql.host` | `localhost` | MySQL 主機 |
| `storage.mysql.port` | `3306` | MySQL 連接埠 |
| `storage.mysql.database` | `minecraft` | MySQL 資料庫名稱 |
| `storage.mysql.username` | `root` | MySQL 帳號 |
| `storage.mysql.password` | `''` | MySQL 密碼 |
| `storage.mysql.jdbc-url` | `''` | 自訂 JDBC URL（留空則由上方欄位組合） |

```yaml
storage:
  type: YAML
  table-prefix: jiuyu_compendium_
  sqlite:
    file: compendium.db
  mysql:
    host: localhost
    port: 3306
    database: minecraft
    username: root
    password: ''
    jdbc-url: ''
```

## discovery — 解鎖行為

控制圖鑑條目的自動解鎖行為，詳見 [解鎖機制](/jiuyucompendium/discovery)。

| 鍵 | 預設值 | 說明 |
|----|--------|------|
| `discovery.notify` | `true` | 解鎖時是否在聊天欄顯示通知 |
| `discovery.sound` | `ENTITY_EXPERIENCE_ORB_PICKUP` | 解鎖音效（設為空字串可停用） |
| `discovery.scan-inventory-on-join` | `true` | 玩家登入時掃描背包 |
| `discovery.scan-inventory-on-click` | `true` | 玩家點擊背包時掃描物品 |
| `discovery.track-vanilla-mobs` | `true` | 追蹤原生生物擊殺 |

```yaml
discovery:
  notify: true
  sound: ENTITY_EXPERIENCE_ORB_PICKUP
  scan-inventory-on-join: true
  scan-inventory-on-click: true
  track-vanilla-mobs: true
```

## gui — 介面標題

| 鍵 | 預設值 | 說明 |
|----|--------|------|
| `gui.title-main` | `&8玖域圖鑑` | 主頁面標題（支援 `&` 色碼） |
| `gui.title-category` | `&8%category%` | 大類頁面標題（`%category%` 為大類名稱） |
| `gui.title-subcategory` | `&8%category% / %subcategory%` | 子分類頁面標題 |

```yaml
gui:
  title-main: '&8玖域圖鑑'
  title-category: '&8%category%'
  title-subcategory: '&8%category% / %subcategory%'
```

## messages — 訊息

| 鍵 | 預設值 | 說明 |
|----|--------|------|
| `messages.discovered` | `&b[圖鑑] &f已解鎖 &e%entry%` | 解鎖條目時顯示的訊息（`%entry%` 為條目名稱，支援 `&` 色碼） |

```yaml
messages:
  discovered: '&b[圖鑑] &f已解鎖 &e%entry%'
```

## achievement-bridge — 成就橋接

控制與成就插件（JuiyuAchievement / JiuyuCodex）的連動。解鎖圖鑑條目時，可將 discover key 鏡像送往成就插件，詳見 [事件與 API](/jiuyucompendium/api)。

| 鍵 | 預設值 | 說明 |
|----|--------|------|
| `achievement-bridge.enabled` | `true` | 是否啟用成就橋接 |
| `achievement-bridge.plugin-names` | `JuiyuAchievement`、`JiuyuCodex` | 嘗試連接的成就插件名稱清單（依序尋找） |
| `achievement-bridge.mirror-discover-key` | `true` | 將原始 discover key 送往成就插件（如 `mythic:boss_id`、`mmoitem:sword.id`） |
| `achievement-bridge.mirror-compendium-key` | `false` | 額外送出 `compendium:<entryId>` 格式的 key（僅在成就插件的設定有使用此格式時才需啟用） |

```yaml
achievement-bridge:
  enabled: true
  plugin-names:
    - JuiyuAchievement
    - JiuyuCodex
  mirror-discover-key: true
  mirror-compendium-key: false
```

## auto-sync — 自動掃描同步

控制啟動時與 `/compendium sync` 執行時的自動掃描行為。掃描結果寫入 `generated/` 目錄。

| 鍵 | 預設值 | 說明 |
|----|--------|------|
| `auto-sync.write-generated-files` | `true` | 是否將掃描結果寫入檔案 |

### MythicMobs 掃描

| 鍵 | 預設值 | 說明 |
|----|--------|------|
| `auto-sync.mythicmobs.enabled` | `true` | 啟用 MythicMobs 掃描 |
| `auto-sync.mythicmobs.folder` | `MythicMobs/mobs` | 掃描目標資料夾（相對於 `plugins/`） |
| `auto-sync.mythicmobs.generated-file` | `generated/auto-mythicmobs.yml` | 掃描結果輸出路徑 |
| `auto-sync.mythicmobs.default-category` | `mobs.mythicmobs` | 預設分類路徑（`大類.子分類`） |
| `auto-sync.mythicmobs.ignored-files` | `ExampleMobs.yml`、`VanillaMobs.yml` | 忽略的檔案 |
| `auto-sync.mythicmobs.category-overrides` | `{}` | 個別生物 ID 的分類覆寫 |
| `auto-sync.mythicmobs.type-icons` | 見下表 | 依 MythicMobs 的 Type 對應 GUI 圖示 |

預設 `type-icons`：

| Type | 圖示 |
|------|------|
| `ZOMBIE` | `ZOMBIE_HEAD` |
| `SKELETON` | `SKELETON_SKULL` |
| `CREEPER` | `CREEPER_HEAD` |
| `ARMOR_STAND` | `ARMOR_STAND` |

### MMOItems 掃描

| 鍵 | 預設值 | 說明 |
|----|--------|------|
| `auto-sync.mmoitems.enabled` | `true` | 啟用 MMOItems 掃描 |
| `auto-sync.mmoitems.folder` | `MMOItems/item` | 掃描目標資料夾（相對於 `plugins/`） |
| `auto-sync.mmoitems.generated-file` | `generated/auto-mmoitems.yml` | 掃描結果輸出路徑 |

`type-map` 定義 MMOItems 類型到圖鑑分類的對應，格式為 `MMOItems類型: 大類.子分類`。完整的預設對應表請參閱 [分類與條目 — MMOItems 掃描設定與類型對應](/jiuyucompendium/categories)。

## category-rewards / subcategory-rewards — 分類獎勵

定義玩家集滿某個大類或子分類全部條目時觸發的獎勵。預設為空（`{}`），需自行設定。

| 鍵 | 預設值 | 說明 |
|----|--------|------|
| `category-rewards` | `{}` | 大類全解鎖獎勵 |
| `subcategory-rewards` | `{}` | 子分類全解鎖獎勵 |

```yaml
category-rewards: {}
subcategory-rewards: {}
```

## 熱重載

修改 `config.yml` 後，執行 `/compendium reload` 即可重載所有設定與圖鑑資料，無需重啟伺服器。

## 相關章節

- [分類與條目](/jiuyucompendium/categories) — 分類樹與 type-map 完整對應表
- [解鎖機制](/jiuyucompendium/discovery) — discovery 區段詳細說明
- [事件與 API](/jiuyucompendium/api) — 成就橋接與 PAPI 整合
