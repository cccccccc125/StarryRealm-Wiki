# 安裝

## 環境需求

| 項目 | 需求 |
|------|------|
| 伺服器 | Paper 1.21.1（`api-version: 1.21`） |
| Java | 21 |

## 軟依賴

以下插件皆為**軟依賴（softdepend）**——缺少仍可載入，對應功能自動停用：

| 插件 | 用途 |
|------|------|
| MythicMobs | 自動掃描自定義生物並產生圖鑑條目 |
| MMOItems | 自動掃描物品並產生圖鑑條目 |
| MythicLib | MMOItems 底層依賴 |
| MMOCore | 公會、結義等多人玩法類別來源 |
| PlaceholderAPI | 提供 PAPI 變數（id `jiuyucompendium`） |
| JuiyuAchievement | 解鎖時鏡像 discover key 至成就插件 |
| JiuyuCodex | JuiyuAchievement 的內部名稱（向下相容） |

## 安裝步驟

1. 將 `JiuyuCompendium` jar 放入 `plugins/`。
2. **重啟伺服器**（**勿使用 `/reload`**）。
3. 首次啟動後自動產生以下檔案：
   - `config.yml` — 主設定檔
   - `categories.yml` — 圖鑑分類樹
   - `entries/manual.yml` — 手動條目
4. 執行期間，插件會在 `generated/` 目錄寫入自動掃描結果：
   - `auto-mythicmobs.yml`
   - `auto-mmoitems.yml`

## 儲存後端

插件支援三種儲存後端，透過 `config.yml` 的 `storage.type` 切換：

| 後端 | 說明 |
|------|------|
| **YAML**（預設） | 將玩家進度存於 `playerdata/` 資料夾 |
| **SQLite** | 本地資料庫檔案 |
| **MySQL** | 遠端資料庫 |

JDBC 驅動以反射載入；若缺少驅動，插件會**自動回退至 YAML**。

詳細設定說明見 [設定檔](/jiuyucompendium/config)。
