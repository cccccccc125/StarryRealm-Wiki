# 指令

主指令 `/cuisine`（別名 `/cook`、`/liaoli`、`/fu`）。

## 一般指令

| 語法 | 範例 | 權限 | 行為 |
|------|------|------|------|
| `/cuisine` | `/cook` | `cuisine.use` | 開啟釜烹飪 GUI（僅玩家可執行） |
| `/cuisine open` | `/fu open` | `cuisine.use` | 同上，明確開啟釜 GUI |
| `/cuisine stats [玩家]` | `/cook stats Steve` | 無 | 查看自己或指定玩家的熟練度與料理進度 |

## 管理指令

以下子指令皆需 `cuisine.admin` 權限。

| 語法 | 範例 | 權限 | 行為 |
|------|------|------|------|
| `/cuisine reload` | `/cook reload` | `cuisine.admin` | 重新載入設定與食譜 |
| `/cuisine give <玩家> <dishId> [品質]` | `/cook give Steve ramen 2` | `cuisine.admin` | 給予指定玩家一份料理；品質預設為 1 |

## 補充

- 提供 Tab 補全：一般玩家可補全 `open`、`stats`；持有 `cuisine.admin` 的玩家可額外補全 `reload`、`give`。`give` 與 `stats` 會補全線上玩家名稱，`give` 還會補全所有料理 ID。
- 無參數執行時，若執行者不是玩家（如控制台），會收到錯誤訊息。
- 輸入無效子指令時，會顯示指令說明。

權限節點詳見 [權限](/jiuyucuisine/permissions)。
