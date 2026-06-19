# 管理指令

`/ecoadmin` 提供發放、扣除、退款、重載與說明。全部需要權限 `economy.admin`（預設 op）。

## 指令一覽

| 指令 | 說明 |
|------|------|
| `/ecoadmin give <玩家> <coin\|gem> <數量>` | 發放（系統 → 玩家） |
| `/ecoadmin take <玩家> <coin\|gem> <數量>` | 扣除（玩家 → 系統） |
| `/ecoadmin refund <交易ID>` | 退款指定交易 |
| `/ecoadmin reload` | 熱重載 `config.yml` |
| `/ecoadmin help`（別名 `?`） | 顯示完整指令說明 |

範例：

```
/ecoadmin give Steve gem 100      # 發放 100 元寶
/ecoadmin take Steve coin 500     # 扣除 500 銅錢
/ecoadmin refund 8f3a...          # 退款交易
/ecoadmin reload
```

## 退款語意

`/ecoadmin refund <交易ID>` 會：

1. 讀取原交易，於相同雙方間**反向移動相同金額**。
2. 寫入一筆 `tx_type=REFUND`、`related_tx_id=原交易`、`status=SUCCESS` 的新紀錄。
3. 將**原交易標記為 `REFUNDED`**。

若原交易非 `SUCCESS`（已退款或本身失敗）則拒絕，達成冪等——同一筆不會被退款兩次。

## 管理員稽核

由**遊戲內管理員**執行 `give / take / refund` 時，其名稱會寫入 `transaction_ledger.operator` 欄位，並顯示於 `/ledger` 懸停明細與 Discord 管理告警，可直接以 SQL 查「哪位管理員做了哪筆操作」。

> 主控台 / 後台操作記為 `NULL`（不追蹤特定操作者）。既有資料表會於啟動時自動補上 `operator` 欄位（相容 MySQL / MariaDB）。

## 熱重載

`/ecoadmin reload` 會重新載入 `config.yml`，包含 Discord webhook、告警門檻、高頻參數、外部經濟監聽開關與訊息字串，**即時生效**，無需重啟。
