# 安裝

## 環境需求

| 項目 | 需求 |
|------|------|
| 伺服器 | Paper 1.21（`api-version: 1.21`） |
| 選用 | PlaceholderAPI（提供 `%current_term%`，soft-depend） |

## 步驟

1. 將 `SolarTermCore` jar 放入 `plugins/`。
2. 啟動一次以產生 `config.yml`。
3. 編輯 `config.yml` 的 `affected-worlds`，填入要由本插件接管天氣的世界名稱（見[設定](/solartermcore/config)）。
4. 若要使用佔位符，安裝 **PlaceholderAPI**。
5. `/term reload` 重新載入設定。

> 節氣進度存於插件資料夾的 `data.yml`（`current-index`、`last-date`），由插件自動維護，一般無需手動編輯。
