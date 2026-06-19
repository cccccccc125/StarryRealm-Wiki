# 雙幣別與轉帳

EconomyCore 同時管理兩種貨幣：**銅錢**（橋接 Vault）與**元寶**（自建）。

## 銅錢 vs 元寶

| 項目 | 🪙 銅錢 (coin) | 💎 元寶 (gem) |
|------|---------------|---------------|
| 後端 | 橋接 Vault（EssentialsX / CMI） | 自建 MySQL `gem_balances` |
| 精度 | 可小數（沿用 Vault）；帳本 HALF_UP 取 2 位 | **整數**，含小數會被拒絕 |
| 典型用途 | 一般流通貨幣 | 高價值 / 課金貨幣 |

> **元寶為整數**：`/gem pay` 或 API 傳入含小數的數量會被**拒絕**（不會無條件捨去或四捨五入）。

## 銅錢指令

| 指令 | 別名 | 權限（預設） | 說明 |
|------|------|------|------|
| `/coin balance [玩家]` | `/balance`、`/bal` | 無 | 查詢銅錢餘額（省略玩家＝自己） |
| `/coin pay <玩家> <金額>` | — | `economy.coin.pay`（true） | 轉帳銅錢給其他玩家 |

範例：

```
/coin balance              # 查自己
/coin balance Steve        # 查 Steve
/coin pay Steve 1500       # 轉 1500 銅錢給 Steve
```

## 元寶指令

| 指令 | 權限（預設） | 說明 |
|------|------|------|
| `/gem balance [玩家]` | 無 | 查詢元寶餘額 |
| `/gem pay <玩家> <數量>` | `economy.gem.pay`（true） | 轉帳元寶（必須為整數） |

範例：

```
/gem balance
/gem pay Steve 50          # 轉 50 元寶
/gem pay Steve 5.5         # ✗ 被拒絕：元寶必須為整數
```

## 轉帳的原子性與安全

- **元寶**：於 DB 交易內 `SELECT … FOR UPDATE` + 樂觀鎖 CAS，CAS 失敗最多重試 3 次；轉帳依雙方 UUID 排序鎖定資料列以避免死結。
- **銅錢**：先確認餘額充足，再 `withdraw + deposit`；若 `deposit` 失敗，會補償退回發起方，避免銅錢憑空消失。
- 無法轉帳給自己；金額必須大於 0；對方須曾上線過。
- 每筆轉帳都會寫入[金流日誌](/economycore/ledger)。
