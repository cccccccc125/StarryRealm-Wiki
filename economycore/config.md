# 設定檔（config.yml）

所有訊息字串同時支援 **MiniMessage** 標籤（如 `<green>`）與傳統 `&` 色碼；訊息佔位符使用 `<name>` 形式（如 `<player>`、`<amount>`）。改完設定執行 `/ecoadmin reload` 即時生效。

## database — 資料庫連線

| 鍵 | 預設 | 說明 |
|----|------|------|
| `host` / `port` / `database` | `localhost` / `3306` / `economy` | 連線位址與資料庫名 |
| `username` / `password` | `root` / `""` | 帳密 |
| `pool-size` | `10` | HikariCP 連線池大小 |
| `connection-timeout` | `10000` | 取得連線逾時（毫秒） |

## alert-threshold — 大額門檻

| 鍵 | 預設 | 說明 |
|----|------|------|
| `coin` | `100000` | 銅錢成功交易超過此值即 console 警告 |
| `gem` | `1000` | 元寶同上 |

## 其他核心開關

| 鍵 | 預設 | 說明 |
|----|------|------|
| `log-player-ip` | `false` | 是否記錄發起玩家 IP（預設關閉，介面與告警皆不顯示） |
| `server-node-name` | `"node-1"` | 多節點環境的節點識別名稱 |
| `monitor-external-economy` | `true` | 監聽 CMI / EssentialsX 餘額變動，補記繞過 EconomyCore 的銅錢異動 |

## velocity — 高頻偵測

| 鍵 | 預設 | 說明 |
|----|------|------|
| `enabled` | `true` | 是否啟用 |
| `window-seconds` | `60` | 計數時間窗（秒） |
| `max-transactions` | `10` | 視窗內達此筆數即告警 |

## discord — Webhook 通知

| 鍵 | 預設 | 說明 |
|----|------|------|
| `enabled` | `false` | 是否啟用 |
| `webhook-url` | `""` | Discord webhook 網址 |
| `username` | `"EconomyCore"` | 顯示名稱 |
| `mention` | `""` | 提及字串（`<@&身分組ID>` 等） |
| `cooldown-seconds` | `30` | 同類告警對同一玩家的最短間隔 |
| `notify.large-amount` / `.high-frequency` / `.failed` / `.admin-and-refund` | `true` | 四類事件開關 |

詳見 [告警與 Discord](/economycore/alerts)。

## messages — 訊息字串

`messages.prefix` 為共用前綴；其餘為各情境訊息（通用、銅錢、元寶、金流查詢、管理、失敗原因）。可自由改寫，支援 MiniMessage 與 `&` 色碼，`<newline>` 為換行。

## auction — 拍賣行

拍賣行相關設定（加價幅度、賴帳禁拍、結算間隔、上架時長與上限、品質排序權重、手續費）獨立成一區，詳見 [拍賣行設定](/economycore/auction/config)。

## 最小可用範例

```yaml
database:
  host: localhost
  port: 3306
  database: economy
  username: economy
  password: "your-password"
```

其餘留預設即可啟動（銅錢功能另需安裝 Vault + 經濟提供者）。
