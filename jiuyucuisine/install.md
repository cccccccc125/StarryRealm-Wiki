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
| MMOItems | 食材辨識（Type+ID）與料理成品產出 |
| MythicLib | MMOItems 底層依賴 |
| MMOCore | 副職業體系整合 |
| MMOBuffs | 食用料理時套用狀態效果 |
| PlaceholderAPI | 提供 PAPI 變數 |
| JiuyuCompendium | 首次製作時解鎖圖鑑條目（`items.food`） |

## 安裝步驟

1. 將 `JiuyuCuisine` jar 放入 `plugins/`。
2. **重啟伺服器**（**勿使用 `/reload`**）。
3. 首次啟動後自動產生以下檔案：
   - `config.yml` — 主設定檔（釜方塊、GUI、熟練度、品質倍率、訊息等）
   - `recipes.yml` — 食譜定義檔

## 初始化順序

插件啟動時的載入順序對功能正確性有影響：

```
settings → recipeBook → storage / bridges → cooking（最後）
```

`CookingService` 建構時會讀取 `recipeBook` 與 `settings`，因此必須在它們之後初始化。正常安裝下此順序由插件自動處理，無需手動介入。

## 儲存後端

目前插件**僅支援 YAML 儲存**——玩家的熟練度與解鎖資料存於 `playerdata/` 資料夾。

`config.yml` 中的 `storage.type` 雖列有 `SQLITE` 與 `MYSQL` 選項，但這兩種後端**尚未實作**（規劃中）。目前請維持預設的 `YAML`。

詳細設定說明見 [設定檔](/jiuyucuisine/config)。
