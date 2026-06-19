# EconomyCore

Paper 1.21.1 **雙幣別經濟核心**插件，外加完整的**拍賣行**模組。

- **銅錢**：橋接 Vault Economy（與 EssentialsX / CMI 完全相容），不自建餘額表。
- **元寶**：自建貨幣，獨立 MySQL 資料表，整數制。

內建完整金流日誌（稽核帳本）、原子化交易處理、退款機制、告警與 Discord 通知，以及拍賣行（上架 / 競標 / 一口價 / 信箱 / 結算）。

## 銅錢 vs 元寶

| 項目 | 🪙 銅錢 (coin) | 💎 元寶 (gem) |
|------|---------------|---------------|
| 後端 | 橋接 Vault（EssentialsX / CMI 提供） | 自建 MySQL `gem_balances` |
| 精度 | 沿用 Vault 的小數；帳本以 HALF_UP 取 2 位 | **整數**；含小數的數量會被拒絕 |
| 相依 | 需 Vault + 經濟提供者 | 無（只需資料庫） |
| 原子性 | 確認餘額 → withdraw + deposit；失敗補償退回 | DB 交易 + `SELECT … FOR UPDATE` + 樂觀鎖 CAS |

## 模組組成

| 模組 | 說明 |
|------|------|
| 經濟核心 | 銅錢 / 元寶餘額查詢與轉帳 |
| 金流日誌 | 每筆異動寫入稽核帳本，可查詢、翻頁、退款 |
| 告警 / Discord | 大額、高頻、失敗、管理四類事件通知 |
| 拍賣行 | 玩家間物品交易（一口價 / 競標 + 信箱領取） |

## 環境需求

- **Paper 1.21.1**、**Java 21**（執行環境必需）。
- **MySQL / MariaDB**（元寶與金流日誌儲存）。
- **Vault** + 一個經濟提供者（EssentialsX / CMI）——僅銅錢功能需要；未安裝時插件仍可啟動，只停用銅錢。
- **MMOItems**（選用，拍賣行物品品質排序用）。

## 文件導覽

| 章節 | 說明 |
|------|------|
| [🔌 安裝與設定](/economycore/install) | 建置、安裝、資料庫與相依 |
| [🪙 雙幣別與轉帳](/economycore/currencies) | `/coin`、`/gem` 指令 |
| [📒 金流日誌](/economycore/ledger) | `/ledger` 查詢與稽核 |
| [🛠️ 管理指令](/economycore/admin) | `/ecoadmin` 發放 / 扣除 / 退款 |
| [🚨 告警與 Discord](/economycore/alerts) | 告警與 webhook 通知 |
| [🗒️ 設定檔](/economycore/config) | `config.yml` 全欄位 |
| [🧑‍⚖️ 權限](/economycore/permissions) | 權限節點 |
| [🔧 開發者 API](/economycore/api) | `CurrencyAPI` |
| [📦 拍賣行](/economycore/auction/) | 上架 / 競標 / 一口價 / 信箱 |
