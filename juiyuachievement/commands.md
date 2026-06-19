# 指令

主指令 `/codex`（別名 `/tujian`、`/cj`、`/achievement`）。

## 一般指令

| 語法 | 範例 | 權限 | 行為 |
|------|------|------|------|
| `/codex` | `/cj` | `codex.use` | 開啟主介面 GUI（圖鑑／成就／稱號分頁；僅玩家可執行） |
| `/codex help` | `/cj help` | 無 | 顯示指令說明 |

## 管理指令

以下子指令皆需 `codex.admin` 權限。

| 語法 | 範例 | 權限 | 行為 |
|------|------|------|------|
| `/codex reload` | `/cj reload` | `codex.admin` | 重新載入設定與所有成就／圖鑑內容 |
| `/codex give <玩家> <成就id>` | `/cj give Steve dragon_slayer` | `codex.admin` | 強制給予指定玩家一項成就（若成就 ID 不存在會提示錯誤） |
| `/codex points <玩家> <±數量>` | `/cj points Steve 50` | `codex.admin` | 調整指定玩家的成就點數（正數增加、負數扣除，最低為 0） |
| `/codex reset <玩家>` | `/cj reset Steve` | `codex.admin` | 清空指定玩家的全部成就資料 |
| `/codex export <玩家>` | `/cj export Steve` | `codex.admin` | 將指定玩家的資料匯出至 `backups/` 資料夾（檔名含玩家名稱與時間戳） |
| `/codex import <玩家> <備份檔名>` | `/cj import Steve Steve-1700000000000.yml` | `codex.admin` | 從 `backups/` 資料夾匯入備份檔，覆蓋指定玩家的資料 |

## 補充

- 無參數執行時，若執行者不是玩家（如控制台），會收到錯誤訊息。
- `give`、`points`、`reset`、`export`、`import` 皆要求目標玩家在線上。
- `points` 的數量為整數，可帶正負號（如 `-30` 扣除 30 點）。
- `export` 產生的備份檔名格式為 `<玩家名稱>-<時間戳>.yml`。

權限節點詳見 [權限](/juiyuachievement/permissions)。
