# 結算與退款

競拍到期由背景任務**定時掃描結算**；一口價則即時成交（見[一口價](/economycore/auction/buyout)）。

## 結算排程

- 背景任務每隔 `settlement.scan-interval-seconds`（預設 **5 秒**）掃描一次。
- 每輪最多處理 `settlement.batch-size`（預設 **50**）筆，以 `LIMIT` 控量避免卡頓。
- 掃描條件：`status=ACTIVE` 且 `expires_at <= 現在`。

## 結算流程

每筆到期掛單會被**原子認領**（`ACTIVE → SETTLING`），確保重複掃描 / 多節點不會重複結算。接著：

### 無人出價
→ 物品退回**賣家信箱**（`LISTING_EXPIRED`）→ 掛單標記 `EXPIRED`（流標）。

### 有人出價（由高到低 cascade）
1. 取各出價者的最高額，由高到低嘗試。
2. 記下 `winner_id`（冪等），向該得標者收款並轉給賣家（`CurrencyAPI.transfer`，幣別銅錢，類型 `PLAYER_TRADE`）。
3. 若得標者**銅錢不足** → 對其[記點](/economycore/auction/penalties)（達門檻則禁拍）→ 換**次高**出價者，回到步驟 2。
4. 收款成功 → 物品進得標者信箱（`AUCTION_WON`）→ 掛單標記 `SOLD`、記 `settled_at`，通知雙方。
5. 全員都付不出 → 物品退回賣家信箱 → 掛單標記 `EXPIRED`（流標）。

> **先金流、後發物**：轉帳成功才算成交，接著才投遞物品；若投遞失敗會反向補償退款並記錄，維持 `SETTLING` 待復原，不會吞錢或重複收款。

## 掛單狀態

| 狀態 | 意義 |
|------|------|
| `ACTIVE` | 進行中 |
| `SETTLING` | 結算中（中間態） |
| `SOLD` | 已成交 |
| `EXPIRED` | 到期未成交 / 流標 |
| `CANCELLED` | 已下架 |

## 下架（取消）

`/ah cancel <掛單ID>` 可下架自己的掛單，物品退回信箱（`LISTING_RETURNED`）。限制：

- 僅**賣家本人**、掛單為 `ACTIVE`。
- 競拍**已有人出價時不可下架**（避免毀約）。

| 結果 | 意義 |
|------|------|
| `SUCCESS` | 已下架，物品退回信箱 |
| `NOT_FOUND` | 找不到掛單 |
| `NOT_OWNER` | 不是你的掛單 |
| `NOT_ACTIVE` | 已結束或狀態改變，無法下架 |
| `HAS_BIDS` | 已有人出價的競拍不可下架 |
| `DB_ERROR` | 系統錯誤 |

## 崩潰復原

`SETTLING` 為中間態。重啟後掃描器會另撈「卡在 `SETTLING` 過久」的掛單；物品投遞靠唯一鍵冪等，金流則先查帳本是否已有該筆成交再決定是否補扣，避免重複收款。極少數「轉帳成功但投遞前崩潰」的情形由管理員以 `/ecoadmin` 介入（`SETTLING` 狀態 + 帳本提供完整鑑識）。

## 手續費

v1 **停用**手續費（上架費、成交抽成皆為 0），資料表預留 `fee_paid` 欄位。見[拍賣行設定](/economycore/auction/config)。
