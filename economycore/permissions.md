# 權限

| 權限節點 | 預設 | 說明 |
|----------|------|------|
| `economy.admin` | op | 管理指令 `/ecoadmin`（發放 / 扣除 / 退款 / 重載 / 說明） |
| `economy.coin.pay` | true | 銅錢轉帳 `/coin pay` |
| `economy.gem.pay` | true | 元寶轉帳 `/gem pay` |
| `economy.ledger.view` | op | 查詢交易紀錄 `/ledger` |
| `auction.use` | true | 使用拍賣行 `/ah` |
| `auction.sell` | true | 拍賣行上架 `/ah sell` |

> 餘額查詢（`/coin balance`、`/gem balance`）無需權限。

設定範例（LuckPerms）：

```
# 禁止一般玩家轉帳元寶
lp group default permission set economy.gem.pay false

# 給予商人群組查帳權
lp group merchant permission set economy.ledger.view true
```
