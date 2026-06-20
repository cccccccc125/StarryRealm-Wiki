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
| MythicMobs | MythicMobs 自定義生物擊殺觸發成就與圖鑑 |
| MMOCore | 主等級 / 副職業等級提升觸發成就 |
| MMOItems | MMOItems 物品取得觸發成就與圖鑑 |
| BeautyQuests | 任務階段 / 完成觸發成就 |
| MythicLib | MMOItems 底層依賴（NBTItem 偵測） |
| PlaceholderAPI | 提供 PAPI 變數（id `juiyu`） |

## 安裝步驟

1. 將 `JuiyuAchievement` jar 放入 `plugins/`。
2. **重啟伺服器**（**勿使用 `/reload`**）。
3. 首次啟動後自動產生以下設定檔與資料夾：

| 路徑 | 說明 |
|------|------|
| `config.yml` | 主設定檔（完成音效、記分板、聊天前綴等） |
| `achievements/` | 成就定義（多檔） |
| `codex/` | 圖鑑條目定義 |
| `titles.yml` | 稱號定義 |
| `shop.yml` | 點數商店 |

所有設定檔位於 `plugins/JuiyuAchievement/` 資料夾下。

## 玩家資料儲存

插件採用 **YAML per-player** 儲存方式，每位玩家的進度存於：

```
plugins/JuiyuAchievement/playerdata/<uuid>.yml
```

檔案內容包含：已完成成就、已領取獎勵、追蹤中的成就、每日/每週重置紀錄、玩家名稱（供排行榜使用）。
