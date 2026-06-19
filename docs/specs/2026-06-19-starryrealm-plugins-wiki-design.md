# 玖域自製插件 Wiki — 多插件總站改造設計

- 日期：2026-06-19
- 狀態：設計（待實作）
- repo：`cccccccc125/SubCareers-Wiki`（本機 `Desktop/subcareers-wiki`），分支 `docs/multi-plugin-wiki`

## 1. 背景與目標

現有 wiki 是 docsify 架構，但**只服務 SubCareers 一個插件**：首頁、安裝、`general/`、`professions/`、`systems/`、`api/` 全部圍繞 SubCareers。

目標是把它改造成 **「玖域自製插件」（StarryRealm）總站**：

1. **先改資料結構** — 從「以功能分區（單插件）」改成「以插件分區（多插件 hub）」，每個插件自成一區、各有獨立側邊欄，首頁是總入口。
2. **再補完整內容** — 為四個插件各建完整文件章節，其中 **EconomyCore（含拍賣行模組）** 為本次重點，需寫到「詳細說明 + 使用方法」。

四個插件：

| 插件 | 來源 | 規模 | 主要指令 |
|---|---|---|---|
| **EconomyCore** | cccccccc125 | 大（雙幣別經濟核心 + 拍賣行） | `/coin` `/gem` `/ledger` `/ecoadmin` `/ah` |
| **SubCareers** | cccccccc125 | 大（8 副職業 + 多系統，wiki 已有完整內容） | 見既有文件 |
| **SolarTermCore** | StarryRealm | 小（24 節氣系統） | `/term [next\|set\|reload]` |
| **TwoFactorAuth** | StarryRealm | 中（OP TOTP 二階段驗證） | `/2fa <enable\|disable\|status>` |

## 2. 範圍與非目標

**範圍內**

- docsify 多插件骨架（navbar、巢狀 sidebar、首頁、全站搜尋）。
- SubCareers 既有頁面整批遷移到 `subcareers/`（移動 + 修連結，**不重寫內容**）。
- EconomyCore 完整文件（經濟核心 + 拍賣行）。
- SolarTermCore、TwoFactorAuth 完整文件（依其原始碼撰寫）。
- 品牌更名為「玖域自製插件」。

**非目標（YAGNI）**

- 不重寫 SubCareers 既有內容、不改其資訊正確性（僅搬移與修連結）。
- 不在 GitHub 改 repo 名稱（見 §8，僅提建議，待使用者決定）。
- 不引入新的文件框架（續用 docsify CDN，零建置）。
- 不做多語系。

## 3. 資訊架構（目標結構）

```
玖域自製插件 Wiki (docsify, 根目錄即站台)
├── index.html              # 站名「玖域自製插件」, 開啟 loadNavbar + 巢狀 loadSidebar + alias
├── _navbar.md              # 頂部導覽：首頁 + 各插件（下拉）
├── README.md               # 總站首頁：品牌介紹 + 四個插件卡片/表格
├── _sidebar.md             # 首頁（根層）側邊欄
├── _coverpage.md           # （選用）封面頁，可省略
│
├── economycore/            # ★ 本次重點
│   ├── _sidebar.md
│   ├── README.md           # 總覽（雙幣別概念、模組組成、特色）
│   ├── install.md          # 環境需求、安裝、MySQL、Vault/CMI 相依
│   ├── currencies.md       # 銅錢 vs 元寶；/coin、/gem 指令與用法
│   ├── ledger.md           # 金流日誌；/ledger 查詢、分頁、懸停明細
│   ├── admin.md            # /ecoadmin give/take/refund/reload/help；退款語意
│   ├── alerts.md           # 告警（大額/高頻/失敗/管理）+ Discord webhook + 外部經濟監聽（CMI）
│   ├── config.md           # config.yml 全欄位
│   ├── permissions.md      # 權限節點清單
│   ├── api.md              # CurrencyAPI（第三方開發者）+ Maven/服務取得
│   └── auction/            # 拍賣行模組
│       ├── README.md       # 拍賣行總覽（流程圖、名詞）
│       ├── commands.md     # /ah sell/list/buy/bid/cancel/mailbox/claim
│       ├── selling.md      # 上架流程：一口價/競標、GUI、鐵砧價格輸入
│       ├── bidding.md      # 競標規則、加價、結算判定
│       ├── buyout.md       # 一口價購買
│       ├── mailbox.md      # 信箱：領取得標/售出款/退回物品
│       ├── settlement.md   # 結算、過期處理、退款、手續費
│       ├── penalties.md    # 取消上架的懲罰政策（PenaltyPolicy）
│       ├── gui.md          # 各 GUI 介面說明（主頁/分類/詳情/信箱）
│       ├── config.md       # 拍賣行相關設定
│       └── database.md     # 資料表：listings / bids / mailbox / penalties
│
├── subcareers/             # 既有內容整批搬入（內容不變）
│   ├── _sidebar.md         # = 目前的 _sidebar.md（路徑改為 subcareers/...）
│   ├── README.md           # = 目前 README（SubCareers 介紹）
│   ├── install.md
│   ├── general/{commands,permissions,placeholders,config}.md
│   ├── professions/{mining,logging,farming,fishing,herb,alchemy,forging,enchanting}.md
│   ├── systems/{island,collections,database,quality-materials,enhancement,ore-fusion}.md
│   └── api/{maven,api,events}.md
│
├── solartermcore/          # 24 節氣
│   ├── _sidebar.md  README.md(總覽)  install.md
│   ├── commands.md         # /term next/set/reload
│   ├── placeholders.md     # PlaceholderAPI 佔位符（目前節氣等）
│   └── config.md           # 節氣設定/訊息
│
├── twofactorauth/          # OP TOTP 二階段驗證
│   ├── _sidebar.md  README.md(總覽)  install.md
│   ├── setup.md            # 玩家綁定流程（authenticator app、QR/密鑰）
│   ├── commands.md         # /2fa enable/disable/status
│   ├── permissions.md      # twofactorauth.2fa.enable/disable/reset
│   └── config.md
│
└── .nojekyll
```

## 4. docsify 技術設計

關鍵：讓「每個插件有自己的側邊欄」且「頂部可切換插件」。

`index.html` 的 `window.$docsify` 設定：

- `name: '玖域自製插件'`、`repo:` 指向 wiki repo。
- `loadSidebar: true` — docsify 自動載入**最近**的 `_sidebar.md`（沿路徑往上找）。於是 `economycore/` 下的頁面用 `economycore/_sidebar.md`，根層用根 `_sidebar.md`。
- `loadNavbar: true` — 載入根 `_navbar.md`，做為跨插件切換的頂部導覽（含下拉）。
- **巢狀側邊欄做法**：每個插件資料夾放自己的 `_sidebar.md`，docsify 會自動用最近的；沒有自己 sidebar 的深層路徑，靠 `alias` fallback 到上層，避免 404。`_navbar.md` 只放根層，深層路徑一律 fallback 到它：
  ```js
  alias: {
    '/.*/_navbar.md': '/_navbar.md',                 // 所有路徑共用根 navbar
    '/economycore/auction/(.*)': '/economycore/_sidebar.md',  // auction 子層沿用 economycore sidebar（若不另設）
  }
  ```
  （以 docsify 官方「Multiple sidebars / nested」做法為準；實作時若每個資料夾都自備 `_sidebar.md`，alias 只需保留 navbar fallback。）
- `subMaxLevel: 2`、`auto2top: true`。
- `search` — 續用 search 外掛，`paths: 'auto'`、`depth: 6`，全站皆可搜。
- 主題：續用 `vue.css`，主色維持綠（`--theme-color`，可微調為品牌色）。

`_navbar.md` 範例：

```md
* [🏠 首頁](/)
* 插件
  * [💰 EconomyCore](/economycore/)
  * [⛏️ SubCareers](/subcareers/)
  * [🌤️ SolarTermCore](/solartermcore/)
  * [🔐 TwoFactorAuth](/twofactorauth/)
```

## 5. 品牌

- 站名 / 首頁標題：**玖域自製插件**（英文 StarryRealm）。
- 首頁 `README.md`：簡短品牌介紹 + 四個插件卡片（名稱、一句話、進入連結、狀態）。
- `<title>` 與 `meta description` 同步更新。

## 6. 各插件內容大綱

### 6.1 EconomyCore（重點，含拍賣行）

來源以 repo `README.md`（已有完整架構說明）、`plugin.yml`、`config.yml` 與原始碼為準。

- **總覽**：雙幣別（銅錢橋接 Vault / 元寶自建 MySQL）、金流日誌、原子交易、退款、拍賣行模組。
- **使用方法導向**：每個指令給「語法 + 範例 + 權限 + 行為說明」表格。
- **拍賣行**：依 `auction/` 原始碼（`ListingService`、`BidService`、`SettlementService`、`MailboxService`、`PenaltyPolicy`、`GuiManager` 等）寫流程與規則；GUI 章節描述操作步驟（含鐵砧輸入價格）。
- **開發者 API**：`CurrencyAPI` 服務取得、`deposit/getBalance` 範例、非同步呼叫提醒。

### 6.2 SubCareers（遷移，內容不變）

- 既有 23 個內容檔整批移入 `subcareers/`，**只修內部連結與 sidebar 路徑**。
- 既有 `_sidebar.md` 改寫為 `subcareers/_sidebar.md`，連結加 `subcareers/` 前綴（或以絕對路徑 `/subcareers/...`）。
- 根層不再保留 SubCareers 專屬頁。

### 6.3 SolarTermCore

- 依原始碼補：24 節氣機制、`/term` 指令、PlaceholderAPI 佔位符、設定/訊息。

### 6.4 TwoFactorAuth

- 依原始碼補：OP TOTP 機制、玩家綁定流程（setup）、`/2fa` 指令、權限、設定。

## 7. SubCareers 遷移策略與連結修正

1. 以 `git mv` 將既有檔案移入 `subcareers/`（保留 git 歷史）。
2. **連結一律改用站內絕對路徑 `/subcareers/...`**（sidebar 與內文皆然），避免相對路徑因移動而錯位。全域掃描 `.md`，把指向 `general/`、`professions/`、`systems/`、`api/`、`install.md` 的連結改為 `/subcareers/...`。
3. 既有 `_sidebar.md` → `subcareers/_sidebar.md`，連結改 `/subcareers/...`。
4. 根層新建總站 `README.md`、`_sidebar.md`、`_navbar.md`。

## 8. repo 改名建議（待使用者決定）

repo 現名 `SubCareers-Wiki`，改造後語意對不上。建議改名為 `StarryRealm-Wiki` 或 `Plugins-Wiki`（GitHub 會自動建立舊名轉址）。**本設計預設不改名**，僅更新內容；改名與否、改成什麼，由使用者另行決定。

## 9. 成功標準

- 首頁為總站，可清楚看到四個插件並進入。
- 切到任一插件，頂部可切換、左側顯示**該插件自己的**側邊欄。
- 搜尋可跨全部插件命中。
- SubCareers 原有頁面內容與連結在新路徑下完整可用、無死連結。
- EconomyCore（含拍賣行）每個指令都有語法 + 範例 + 權限說明，拍賣行流程可被新手照著操作。
- docsify 仍為純前端、無建置步驟（CDN 載入），`.nojekyll` 保留。

## 10. 實作分期（高層，細節由實作計畫展開）

1. **骨架**：index.html / _navbar.md / 根 README + _sidebar；驗證巢狀 sidebar 與 navbar 切換可運作。
2. **SubCareers 遷移**：git mv + 修連結 + subcareers/_sidebar.md。
3. **EconomyCore 核心**：overview/install/currencies/ledger/admin/alerts/config/permissions/api。
4. **EconomyCore 拍賣行**：auction/* 全部頁面。
5. **SolarTermCore**：讀原始碼後補完整章節。
6. **TwoFactorAuth**：讀原始碼後補完整章節。
7. **收尾**：首頁卡片、死連結掃描、搜尋與導覽全面驗證。
