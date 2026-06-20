# 指令

主指令 `/compendium`（別名 `/comp`、`/jc`、`/tujian`）。

## 一般指令

| 語法 | 範例 | 權限 | 行為 |
|------|------|------|------|
| `/compendium` | `/jc` | `compendium.use` | 開啟圖鑑 GUI（僅玩家可執行） |
| `/compendium help` | `/comp help` | 無 | 顯示指令說明 |

## 管理指令

以下子指令皆需 `compendium.admin` 權限。

| 語法 | 範例 | 權限 | 行為 |
|------|------|------|------|
| `/compendium reload` | `/jc reload` | `compendium.admin` | 重新載入設定與圖鑑 |
| `/compendium sync` | `/jc sync` | `compendium.admin` | 掃描 MythicMobs／MMOItems 並寫入 `generated/` 後重新載入 |
| `/compendium unlock <玩家> <entryId\|discoverKey>` | `/jc unlock Steve mobs.zombie_king` | `compendium.admin` | 手動解鎖指定玩家的條目 |
| `/compendium lock <玩家> <entryId\|discoverKey>` | `/jc lock Steve mobs.zombie_king` | `compendium.admin` | 手動鎖定指定玩家的條目 |
| `/compendium reset <玩家>` | `/jc reset Steve` | `compendium.admin` | 重置指定玩家的全部圖鑑資料 |
| `/compendium stats [玩家]` | `/jc stats Steve` | `compendium.admin` | 查看進度統計（省略玩家則查看自己） |
| `/compendium save` | `/jc save` | `compendium.admin` | 立即將玩家資料存檔 |

## 補充

- 提供 Tab 補全：一般玩家可補全 `help`；持有 `compendium.admin` 的玩家可補全所有子指令，`unlock`／`lock`／`reset`／`stats` 會補全線上玩家名稱，`unlock`／`lock` 還會補全所有條目 ID。
- 無參數執行時，若執行者不是玩家（如控制台），會收到錯誤訊息。

權限節點詳見 [權限](/jiuyucompendium/permissions)。
