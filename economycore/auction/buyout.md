# 一口價

**一口價（BUYOUT）**掛單標示固定價格，任何人付款即可**立即**購得。

## 如何購買

- **GUI**：在掛單詳情頁點「立即購買」。
- **指令**：`/ah buy <掛單ID>`。

```
/ah buy 3f9c2a1b-...
```

## 成交流程

1. 系統原子認領該掛單（`ACTIVE → SETTLING`），避免同時被兩人買到。
2. 向買家收取一口價的**銅錢**，直接轉入賣家帳戶。
3. 物品投遞到**買家信箱**，掛單標記為 `SOLD`。
4. 買家以 `/ah mailbox` → `/ah claim` 領取物品。

> 一口價即時成交、不需等結算掃描；款項與物品流向與競拍得標相同（錢直接入帳、物品進信箱）。

## 購買結果

| 結果 | 意義 |
|------|------|
| `SUCCESS` | 購買成功，物品已送到信箱 |
| `NOT_FOUND` | 找不到該掛單 |
| `NOT_AVAILABLE` | 已售出或不可購買 |
| `SELF_PURCHASE` | 不能購買自己的掛單 |
| `INSUFFICIENT_FUNDS` | 銅錢不足 |
| `ECONOMY_UNAVAILABLE` | 銅錢系統目前不可用（Vault 未掛載） |
| `DB_ERROR` | 系統錯誤 |

領取物品見[信箱](/economycore/auction/mailbox)。
