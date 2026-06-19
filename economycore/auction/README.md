# 拍賣行總覽

EconomyCore 的拍賣行模組讓玩家上架物品、競拍或一口價交易，以**銅錢（Vault）**結算。可完全透過 GUI 操作，指令則作為備援。

## 名詞

| 名詞 | 說明 |
|------|------|
| 上架（Listing） | 賣家把物品放上拍賣行，分競拍 / 一口價兩種模式 |
| 競拍（AUCTION） | 玩家出價競爭，到期由最高有效出價者得標 |
| 一口價（BUYOUT） | 標示固定價，任何人付款即可立即購得 |
| 出價（Bid） | 對競拍掛單出價（**出價不凍結資金**，結算才收款） |
| 信箱（Mailbox） | 離線也能領取的物品收件匣（只裝物品，錢直接入帳） |
| 結算（Settlement） | 定時掃描到期競拍並完成成交 / 流標 |
| 賴帳懲罰（Strike） | 得標卻付不出款 → 記點，達門檻短期禁拍 |

## 兩種上架模式

| | 🔨 競拍 AUCTION | 💵 一口價 BUYOUT |
|---|---|---|
| 價格 | 起標價 + 最小加價幅度 | 固定一口價 |
| 成交 | 到期由最高出價者得標（定時結算） | 任何人付款即時成交 |
| 出價 | 可多次出價競爭 | 不適用 |
| v1 限制 | 不附直購價 | — |

## 交易流程

```
賣家 /ah sell 或 GUI「＋ 上架」
        │  物品離開背包，掛單 status=ACTIVE
        ▼
   買家瀏覽（/ah 或 /ah list）
        │
        ├─ 一口價：/ah buy 或詳情「立即購買」→ 付銅錢 → 即時成交
        │
        └─ 競拍：/ah bid 或詳情「出價」(鐵砧輸入) → 出價競爭
                 │  到期
                 ▼
            定時結算：最高出價者付款 → 成交（賴帳則順延次高）
        ▼
   物品進「得標者信箱」，款項直接入「賣家帳戶」
   雙方用 /ah mailbox / /ah claim 領取物品
```

## 重要設計

- **結算才收款，出價不凍結**：競拍出價當下不扣錢，到期結算時才向得標者收款。
- **信箱只裝物品**：賣出的銅錢透過 Vault 直接入帳（離線也有效，Essentials 會保留），信箱只負責退回 / 得標的物品。
- **MMOItems 品質整合**：有 MMOItems 時依物品 tier 分類與排序；無則退化為依材質概略分類。

## 子頁

[💾 指令](/economycore/auction/commands) ·
[🏷️ 上架](/economycore/auction/selling) ·
[🔨 競標](/economycore/auction/bidding) ·
[💵 一口價](/economycore/auction/buyout) ·
[📮 信箱](/economycore/auction/mailbox) ·
[⚖️ 結算與退款](/economycore/auction/settlement) ·
[⛔ 賴帳懲罰](/economycore/auction/penalties) ·
[🖥️ GUI 介面](/economycore/auction/gui) ·
[🗒️ 設定](/economycore/auction/config) ·
[🗄️ 資料表](/economycore/auction/database)
