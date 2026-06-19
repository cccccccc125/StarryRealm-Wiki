# 資料表

拍賣行使用 4 張資料表，於插件啟動時由 `DatabaseManager` 自動建立（`CREATE TABLE IF NOT EXISTS`，`InnoDB` / `utf8mb4`）。慣例：UUID 為 `VARCHAR(36)`、金額 `DECIMAL(20,2)`、時間 `BIGINT`（Unix 毫秒）、`version` 為樂觀鎖。

## auction_listings — 掛單

```sql
id              BIGINT        AUTO_INCREMENT PRIMARY KEY,
listing_id      VARCHAR(36)   NOT NULL,            -- 對外唯一 ID
seller_id       VARCHAR(36)   NOT NULL,
seller_name     VARCHAR(16)   NOT NULL,            -- 顯示快取
item_data       TEXT          NOT NULL,            -- ItemStack → Base64
item_amount     INT           NOT NULL,
item_type       VARCHAR(32)   NOT NULL,            -- ItemCategory
item_quality    VARCHAR(32)   DEFAULT NULL,        -- MMOItems tier id；null=無
quality_weight  INT           NOT NULL DEFAULT 0,  -- tier 對應排序權重（品質排序用）
item_display    VARCHAR(128)  NOT NULL,            -- 去色碼顯示名（搜尋 / 排序）
listing_mode    ENUM('AUCTION','BUYOUT') NOT NULL,
start_price     DECIMAL(20,2) DEFAULT NULL,        -- 競拍起標價
buyout_price    DECIMAL(20,2) DEFAULT NULL,        -- 一口價
current_bid     DECIMAL(20,2) DEFAULT NULL,        -- 目前最高出價
current_bidder  VARCHAR(36)   DEFAULT NULL,
min_increment   DECIMAL(20,2) NOT NULL DEFAULT 1,
fee_paid        DECIMAL(20,2) NOT NULL DEFAULT 0,  -- v1=0，預留
created_at      BIGINT        NOT NULL,
expires_at      BIGINT        NOT NULL,
status          ENUM('ACTIVE','SETTLING','SOLD','EXPIRED','CANCELLED') NOT NULL,
winner_id       VARCHAR(36)   DEFAULT NULL,        -- 結算選定得標者（冪等）
settled_at      BIGINT        DEFAULT NULL,
version         INT           NOT NULL DEFAULT 0,
UNIQUE KEY uq_listing (listing_id),
INDEX idx_browse (status, item_type, item_quality),
INDEX idx_expiry (status, expires_at),
INDEX idx_seller (seller_id, status),
INDEX idx_bidder (current_bidder, status)
```

## auction_bids — 出價歷史

```sql
id          BIGINT        AUTO_INCREMENT PRIMARY KEY,
bid_id      VARCHAR(36)   NOT NULL,
listing_id  VARCHAR(36)   NOT NULL,        -- 邏輯關聯 auction_listings.listing_id
bidder_id   VARCHAR(36)   NOT NULL,
bidder_name VARCHAR(16)   NOT NULL,
amount      DECIMAL(20,2) NOT NULL,
created_at  BIGINT        NOT NULL,
UNIQUE KEY uq_bid (bid_id),
INDEX idx_listing_amount (listing_id, amount),
INDEX idx_bidder (bidder_id, created_at)
```

## player_mailbox — 信箱（只裝物品）

```sql
id             BIGINT      AUTO_INCREMENT PRIMARY KEY,
entry_id       VARCHAR(36) NOT NULL,
owner_id       VARCHAR(36) NOT NULL,
item_data      TEXT        NOT NULL,       -- Base64
item_amount    INT         NOT NULL,
reason         ENUM('AUCTION_WON','LISTING_EXPIRED','LISTING_RETURNED','BUYOUT') NOT NULL,
source_listing VARCHAR(36) DEFAULT NULL,
created_at     BIGINT      NOT NULL,
claimed        TINYINT(1)  NOT NULL DEFAULT 0,
claimed_at     BIGINT      DEFAULT NULL,
UNIQUE KEY uq_entry (entry_id),
UNIQUE KEY uq_won_listing (source_listing, reason),  -- 同單同原因冪等投遞
INDEX idx_owner (owner_id, claimed, created_at)
```

## auction_penalties — 賴帳記點 / 禁拍

```sql
player_id    VARCHAR(36) NOT NULL PRIMARY KEY,
strikes      INT         NOT NULL DEFAULT 0,
banned_until BIGINT      DEFAULT NULL,    -- null=未禁；>now=禁拍中
updated_at   BIGINT      NOT NULL
```

> 篩選 / 排序全在 SQL 完成（`WHERE status='ACTIVE' AND item_type=? AND item_quality=?` + `ORDER BY ... LIMIT`），不撈全表再於 Java 排序。對應的建表腳本也可在專案 `sql/` 目錄找到。
