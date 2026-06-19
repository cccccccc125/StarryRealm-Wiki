# 玖域自製插件 Wiki 多插件總站 — 實作計畫

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** 把 `SubCareers-Wiki` 從單插件 docsify 站改造成「玖域自製插件（StarryRealm）」多插件總站，遷移既有 SubCareers 內容，並補上 EconomyCore（含拍賣行）、SolarTermCore、TwoFactorAuth 的完整文件。

**Architecture:** docsify 純前端（CDN，零建置）。以插件為一級分區：每個插件一個資料夾、各自 `_sidebar.md`；根層放 `_navbar.md`（跨插件切換）、`README.md`（總站首頁）。`loadSidebar` 自動取最近側邊欄，`alias` 讓深層 fallback 到根 navbar。

**Tech Stack:** docsify@4（vue 主題 + search 外掛）、Markdown、git。本機路徑 `C:\Users\user\OneDrive\Desktop\subcareers-wiki`，分支 `docs/multi-plugin-wiki`。

**對應設計：** `docs/specs/2026-06-19-starryrealm-plugins-wiki-design.md`

---

## 驗證方式（取代單元測試）

本專案無單元測試框架，每個「驗證」步驟採下列其一：

- **連結/檔案存在檢查**（自動）：對 `.md` 內的站內連結，確認目標 `.md` 檔存在。在 repo 根目錄執行：
  ```powershell
  $root = (Get-Location).Path
  Get-ChildItem -Recurse -Filter *.md |
    Where-Object { $_.FullName -notmatch '\\docs\\(specs|plans)\\|\\.git\\' } |
    ForEach-Object {
      $file = $_.FullName; $dir = $_.DirectoryName
      foreach ($m in [regex]::Matches((Get-Content $file -Raw), '\]\((/?[^)#]+\.md)')) {
        $link = $m.Groups[1].Value
        $target = if ($link.StartsWith('/')) { Join-Path $root $link.TrimStart('/') } else { Join-Path $dir $link }
        if (-not (Test-Path $target)) { Write-Output ("DEAD: {0}  ->  {1}" -f $file.Replace($root+'\',''), $link) }
      }
    }
  ```
  輸出空白＝無死連結。docsify 連結常省略 `.md` 副檔名（如 `/economycore/install`），這類在 sidebar/navbar 中很常見、此腳本只查帶 `.md` 的連結；省略副檔名的連結改於本機預覽時點擊驗證。
- **本機預覽**（人工目視）：`npx docsify-cli serve .`（或 `python -m http.server`）後開 `http://localhost:3000`，確認：頁面渲染、左側顯示**該插件自己的** sidebar、頂部 navbar 可切換插件、搜尋可命中。docsify 需經 http 載入 `.md`，**不可**用 `file://` 直接開 `index.html`。

每個 Task 完成後 commit；commit 訊息結尾加：
`Co-Authored-By: Claude Opus 4.8 <noreply@anthropic.com>`

---

## 檔案結構總覽

```
（根）index.html  _navbar.md  README.md  _sidebar.md  .nojekyll
economycore/    _sidebar.md README.md install.md currencies.md ledger.md admin.md alerts.md config.md permissions.md api.md
economycore/auction/  README.md commands.md selling.md bidding.md buyout.md mailbox.md settlement.md penalties.md gui.md config.md database.md
subcareers/     _sidebar.md README.md install.md general/* professions/* systems/* api/*   （由既有檔遷移）
solartermcore/  _sidebar.md README.md install.md commands.md placeholders.md config.md events.md
twofactorauth/  _sidebar.md README.md install.md setup.md commands.md permissions.md config.md events.md
docs/specs/, docs/plans/   （設計與計畫，不在站台導覽中）
```

**權威資料來源（撰寫內容時據此，勿臆測）：**
- EconomyCore：`C:\Users\user\OneDrive\Desktop\Economy\` 的 `README.md`、`src\main\resources\{plugin.yml,config.yml}`、`src\main\java\com\economycore\**`（拍賣行在 `...\auction\**`）。
- SubCareers：既有 wiki 檔（內容不變，僅遷移）。
- SolarTermCore：`C:\Users\user\OneDrive\Desktop\SolarTermCore\SolarTermCore\src\main\java\me\cc\solarTermCore\*.java` 與 `...\resources\{plugin.yml,config.yml}`。
- TwoFactorAuth：`C:\Users\user\OneDrive\Desktop\TwoFactorAuth\src\main\java\com\twofactorauth\**` 與 `...\resources\{plugin.yml,config.yml}`。

---

# Phase 1 — 多插件骨架

### Task 1: 更新 index.html 為多插件設定

**Files:**
- Modify: `index.html`

- [ ] **Step 1: 改寫 `window.$docsify` 設定與標題**

```html
<!DOCTYPE html>
<html lang="zh-TW">
<head>
  <meta charset="UTF-8">
  <title>玖域自製插件 Wiki</title>
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <meta name="description" content="玖域（StarryRealm）自製 Minecraft 插件文件總站：EconomyCore、SubCareers、SolarTermCore、TwoFactorAuth">
  <link rel="stylesheet" href="//cdn.jsdelivr.net/npm/docsify@4/lib/themes/vue.css">
  <style>
    :root { --theme-color: #42b983; }
  </style>
</head>
<body>
  <div id="app"></div>
  <script>
    window.$docsify = {
      name: '玖域自製插件',
      repo: 'https://github.com/cccccccc125/SubCareers-Wiki',
      loadSidebar: true,
      loadNavbar: true,
      subMaxLevel: 2,
      auto2top: true,
      alias: {
        '/.*/_navbar.md': '/_navbar.md'
      },
      search: {
        placeholder: '搜尋文件...',
        noData: '找不到結果',
        depth: 6,
        paths: 'auto'
      }
    }
  </script>
  <script src="//cdn.jsdelivr.net/npm/docsify@4"></script>
  <script src="//cdn.jsdelivr.net/npm/docsify/lib/plugins/search.min.js"></script>
</body>
</html>
```

- [ ] **Step 2: 驗證**

執行本機預覽（`npx docsify-cli serve .`），確認站台可載入、標題為「玖域自製插件」。（此時 sidebar/navbar 內容尚未建立，下一個 Task 補。）

- [ ] **Step 3: Commit**

```bash
git add index.html
git commit -m "feat(wiki): index.html 改為多插件設定（navbar + 巢狀 sidebar + 全站搜尋）"
```

---

### Task 2: 建立根 `_navbar.md`（跨插件切換）

**Files:**
- Create: `_navbar.md`

- [ ] **Step 1: 寫入頂部導覽**

```md
* [🏠 首頁](/)
* 插件
  * [💰 EconomyCore — 雙幣別經濟 + 拍賣行](/economycore/)
  * [⛏️ SubCareers — 副職業系統](/subcareers/)
  * [🌤️ SolarTermCore — 二十四節氣](/solartermcore/)
  * [🔐 TwoFactorAuth — OP 二階段驗證](/twofactorauth/)
```

- [ ] **Step 2: 驗證**：預覽時頂部出現「插件」下拉，四個項目可點（目標頁稍後建立，先確認 navbar 出現即可）。

- [ ] **Step 3: Commit**

```bash
git add _navbar.md
git commit -m "feat(wiki): 新增跨插件頂部導覽 _navbar.md"
```

---

### Task 3: 改寫根 `README.md` 為總站首頁

**Files:**
- Modify: `README.md`

- [ ] **Step 1: 寫入總站首頁**（品牌介紹 + 四插件卡片表格）

```md
# 玖域自製插件 Wiki

玖域（StarryRealm）團隊自製的 Minecraft（Paper 1.21.1）插件文件總站。選擇下方插件查看安裝、指令、設定與使用方法。

## 插件一覽

| 插件 | 說明 | 文件 | 狀態 |
|------|------|------|------|
| 💰 **EconomyCore** | 雙幣別經濟核心（銅錢橋接 Vault / 元寶自建），含金流日誌、管理工具與**拍賣行**模組 | [進入](/economycore/) | ✅ |
| ⛏️ **SubCareers** | RPG 副職業系統：8 種副職業 + 收集圖鑑、強化、空島等系統 | [進入](/subcareers/) | ✅ |
| 🌤️ **SolarTermCore** | 二十四節氣循環系統，含節氣天氣與 PlaceholderAPI 佔位符 | [進入](/solartermcore/) | ✅ |
| 🔐 **TwoFactorAuth** | OP 帳號 TOTP 二階段驗證，加入時凍結並以驗證碼解鎖 | [進入](/twofactorauth/) | ✅ |

## 關於玖域

- GitHub：cccccccc125 / StarryRealm
- 所有插件皆為 Paper 1.21.1、Java 21 開發。
```

- [ ] **Step 2: 驗證**：預覽首頁顯示四插件表格，連結指向各插件首頁。

- [ ] **Step 3: Commit**

```bash
git add README.md
git commit -m "feat(wiki): 首頁改為玖域自製插件總站入口"
```

---

### Task 4: 建立根 `_sidebar.md`（首頁側邊欄）

**Files:**
- Create: `_sidebar.md`

- [ ] **Step 1: 寫入根側邊欄**（總站層級的導覽；各插件進入後會切到自己的 sidebar）

```md
* [🏠 首頁](/)
* **插件**
* [💰 EconomyCore](/economycore/)
* [⛏️ SubCareers](/subcareers/)
* [🌤️ SolarTermCore](/solartermcore/)
* [🔐 TwoFactorAuth](/twofactorauth/)
```

- [ ] **Step 2: 驗證**：首頁左側顯示此清單。

- [ ] **Step 3: Commit**

```bash
git add _sidebar.md
git commit -m "feat(wiki): 新增根層 _sidebar.md"
```

---

# Phase 2 — SubCareers 內容遷移（內容不變，僅移動 + 修連結）

> 既有檔（root）：`install.md`、`general/{commands,permissions,placeholders,config}.md`、`professions/{mining,logging,farming,fishing,herb,alchemy,forging,enchanting}.md`、`systems/{island,collections,database,quality-materials,enhancement,ore-fusion}.md`、`api/{maven,api,events}.md`、`README.md`（SubCareers 介紹）、`_sidebar.md`。

### Task 5: 以 `git mv` 將既有內容移入 `subcareers/`

**Files:**
- Move: 既有內容檔 → `subcareers/...`（**保留** root 的 `index.html`、`.nojekyll`、`.gitignore`、`docs/`，以及 Phase 1 新建的 `_navbar.md`/`README.md`/`_sidebar.md`）

> 重要：Phase 1 的 Task 3、Task 4 已分別把 root `README.md`、`_sidebar.md` 換成總站版本，**原本的 SubCareers 版本只存在於分支 `docs/fishing-set-bonus`**。本 Task 從該分支取回原版內容寫入 `subcareers/`。root 的總站 `README.md` / `_sidebar.md` 不動。

- [ ] **Step 1: 建立資料夾並移動資料夾型內容（保留 git 歷史）**

```bash
mkdir -p subcareers
git mv install.md subcareers/install.md
git mv general subcareers/general
git mv professions subcareers/professions
git mv systems subcareers/systems
git mv api subcareers/api
```

- [ ] **Step 2: 從原分支取回 SubCareers 的 README 內容**

```bash
git show docs/fishing-set-bonus:README.md > subcareers/README.md
git add subcareers/README.md
```

（`subcareers/_sidebar.md` 於 Task 6 直接以新內容建立，不需從舊檔搬移。）

- [ ] **Step 3: 驗證**：`subcareers/` 下含 install.md、general/、professions/、systems/、api/、README.md。root 不再有這些散落檔，但仍保有總站 `README.md`、`_sidebar.md`、`_navbar.md`、`index.html`。

- [ ] **Step 4: Commit**

```bash
git add -A
git commit -m "refactor(wiki): SubCareers 內容遷移至 subcareers/ 子區（保留歷史）"
```

---

### Task 6: 建立 `subcareers/_sidebar.md`

**Files:**
- Create: `subcareers/_sidebar.md`

> 內容對應改造前的 SubCareers sidebar（可參照 `git show docs/fishing-set-bonus:_sidebar.md` 核對章節），連結全部改成 `/subcareers/...` 絕對路徑：

- [ ] **Step 1: 寫入 `subcareers/_sidebar.md`**

```md
* [⬅️ 回總站](/)
* [🏠 SubCareers 首頁](/subcareers/)
* [🔌 安裝指南](/subcareers/install)

---

* **一般**
* [💾 指令](/subcareers/general/commands)
* [🧑‍⚖️ 權限](/subcareers/general/permissions)
* [🏷️ 佔位符](/subcareers/general/placeholders)
* [🗒️ 設定](/subcareers/general/config)

---

* **副職業**
* [⛏️ 採礦](/subcareers/professions/mining)
* [🪓 伐木](/subcareers/professions/logging)
* [🌾 耕作](/subcareers/professions/farming)
* [🎣 釣魚](/subcareers/professions/fishing)
* [🌿 草藥](/subcareers/professions/herb)
* [⚗️ 煉金](/subcareers/professions/alchemy)
* [🔨 鍛造](/subcareers/professions/forging)
* [✨ 刻印](/subcareers/professions/enchanting)

---

* **系統**
* [🏝️ 空島](/subcareers/systems/island)
* [📊 收集圖鑑](/subcareers/systems/collections)
* [🗄️ 資料儲存](/subcareers/systems/database)
* [🪵 品質素材](/subcareers/systems/quality-materials)
* [⭐ 強化系統](/subcareers/systems/enhancement)
* [🪨 礦物融合](/subcareers/systems/ore-fusion)

---

* **API**
* [📦 Maven](/subcareers/api/maven)
* [🔧 Plugin API](/subcareers/api/api)
* [📮 事件](/subcareers/api/events)
```

- [ ] **Step 2: 驗證**：進入 `/subcareers/` 任一頁，左側顯示此 SubCareers 專屬 sidebar；頂端「回總站」可回首頁。

- [ ] **Step 3: Commit**

```bash
git add subcareers/_sidebar.md
git commit -m "feat(wiki): 新增 subcareers/_sidebar.md（絕對路徑）"
```

---

### Task 7: 修正 SubCareers 內文與 README 的站內連結

**Files:**
- Modify: `subcareers/README.md`、`subcareers/**/*.md`（凡含指向 `install.md`、`general/`、`professions/`、`systems/`、`api/` 的相對連結者）

- [ ] **Step 1: 找出所有需改的連結**

```powershell
Select-String -Path subcareers\*.md, subcareers\**\*.md -Pattern '\]\((?!/|https?:|#)([^)]+\.md)' -AllMatches
```

- [ ] **Step 2: 將相對連結改為站內絕對路徑**

規則：`](install.md)` → `](/subcareers/install.md)`；`](professions/mining.md)` → `](/subcareers/professions/mining.md)`；`](../systems/x.md)` → `](/subcareers/systems/x.md)`。`subcareers/README.md` 的快速導覽表格全部加 `/subcareers/` 前綴。

- [ ] **Step 3: 驗證**：執行「連結/檔案存在檢查」片段，`subcareers/` 範圍內**無 DEAD**；預覽點擊 README 各連結與 sidebar 皆正確跳轉。

- [ ] **Step 4: Commit**

```bash
git add subcareers
git commit -m "fix(wiki): SubCareers 內部連結改為 /subcareers/ 絕對路徑"
```

---

# Phase 3 — EconomyCore 核心文件

> 每個內容 Task 的步驟模式：**(1)** 讀權威來源 →**(2)** 依「必含章節」撰寫頁面 →**(3)** 視需要更新 `economycore/_sidebar.md` →**(4)** 連結檢查 + 預覽 →**(5)** commit。內容以使用方法為導向：指令一律給「語法 / 範例 / 權限 / 行為」。

### Task 8: 建立 `economycore/_sidebar.md` 與 `economycore/README.md`

**Files:**
- Create: `economycore/_sidebar.md`、`economycore/README.md`
- 來源：`Economy\README.md`（第 1–60、114–146 行的概述與架構）、`plugin.yml`

- [ ] **Step 1: `economycore/_sidebar.md`**

```md
* [⬅️ 回總站](/)
* [🏠 EconomyCore 首頁](/economycore/)
* [🔌 安裝與設定](/economycore/install)

---

* **經濟核心**
* [🪙 雙幣別與轉帳](/economycore/currencies)
* [📒 金流日誌](/economycore/ledger)
* [🛠️ 管理指令](/economycore/admin)
* [🚨 告警與 Discord](/economycore/alerts)
* [🗒️ 設定檔](/economycore/config)
* [🧑‍⚖️ 權限](/economycore/permissions)
* [🔧 開發者 API](/economycore/api)

---

* **拍賣行**
* [📦 總覽](/economycore/auction/)
* [💾 指令](/economycore/auction/commands)
* [🏷️ 上架](/economycore/auction/selling)
* [🔨 競標](/economycore/auction/bidding)
* [💵 一口價](/economycore/auction/buyout)
* [📮 信箱](/economycore/auction/mailbox)
* [⚖️ 結算與退款](/economycore/auction/settlement)
* [⛔ 取消懲罰](/economycore/auction/penalties)
* [🖥️ GUI 介面](/economycore/auction/gui)
* [🗒️ 設定](/economycore/auction/config)
* [🗄️ 資料表](/economycore/auction/database)
```

- [ ] **Step 2: `economycore/README.md`（總覽）** — 必含章節：
  - 一句話定位（雙幣別經濟核心 + 拍賣行）。
  - **銅錢 vs 元寶** 對照（銅錢＝橋接 Vault、可小數；元寶＝自建 MySQL、整數）。
  - 模組組成：經濟核心、金流日誌、告警/Discord、拍賣行。
  - 環境需求摘要（Paper 1.21.1 / Java 21 / MySQL；Vault 為銅錢必需、可選）。
  - 導覽表格（連到本區各頁，路徑用 `/economycore/...`）。

- [ ] **Step 3: 驗證**：進入 `/economycore/` 顯示總覽 + 專屬 sidebar（含「拍賣行」分組）。連結檢查無 DEAD（拍賣行頁稍後建立，先允許這些連結為待建——或在 Phase 4 完成後再跑全站檢查）。

- [ ] **Step 4: Commit**

```bash
git add economycore/_sidebar.md economycore/README.md
git commit -m "feat(wiki): EconomyCore 總覽頁與側邊欄"
```

---

### Task 9: `economycore/install.md`

**Files:** Create `economycore/install.md`。來源：`Economy\README.md` 第 64–95 行、`config.yml`。

- [ ] **Step 1: 撰寫** — 必含：建置（`mvn clean package`、JDK 21、產物 jar）、安裝（放 plugins/）、MySQL 建庫與自動建表（提 `sql/` 腳本）、`config.yml` 的 `database.*` 連線設定、Vault + 經濟提供者（EssentialsX/CMI）安裝。relocate 說明（HikariCP/MySQL 不衝突）。
- [ ] **Step 2: 驗證**：連結檢查無 DEAD；預覽渲染正常。
- [ ] **Step 3: Commit** — `git commit -m "docs(economycore): 安裝與設定"`

---

### Task 10: `economycore/currencies.md`

**Files:** Create。來源：`README.md` 第 99–120 行、`CoinCommand.java`、`GemCommand.java`、`plugin.yml`。

- [ ] **Step 1: 撰寫** — 必含：
  - 銅錢/元寶定位與精度規則（元寶整數，含小數會被**拒絕**；銅錢沿用 Vault 小數、帳本 HALF_UP 2 位）。
  - 指令表（語法/範例/權限/說明）：`/coin balance [玩家]`（別名 `/balance`,`/bal`）、`/coin pay <玩家> <金額>`（`economy.coin.pay`）、`/gem balance [玩家]`、`/gem pay <玩家> <數量>`（`economy.gem.pay`）。
  - 原子性簡述（元寶 FOR UPDATE + CAS；銅錢 withdraw→deposit 補償退回）。
- [ ] **Step 2: 驗證** → **Step 3: Commit** `docs(economycore): 雙幣別與轉帳指令`

---

### Task 11: `economycore/ledger.md`

**Files:** Create。來源：`README.md` 第 105、121、126 行、`LedgerCommand.java`、`LedgerService.java`、`model\Transaction*`。

- [ ] **Step 1: 撰寫** — 必含：金流日誌用途（稽核帳本）、`/ledger <玩家> [起] [迄] [頁]`（別名 `/txlog`，權限 `economy.ledger.view`）語法與範例、查詢上限（預設近 7 天、上限 90 天、每頁 10 筆、可點翻頁、懸停明細）、方向性帳本概念（系統/玩家、前後餘額快照）、交易狀態（SUCCESS/FAILED/REFUNDED）。
- [ ] **Step 2: 驗證** → **Step 3: Commit** `docs(economycore): 金流日誌與 /ledger`

---

### Task 12: `economycore/admin.md`

**Files:** Create。來源：`README.md` 第 106–110、122、124 行、`EcoAdminCommand.java`。

- [ ] **Step 1: 撰寫** — 必含指令表：`/ecoadmin give|take <玩家> <coin|gem> <數量>`、`/ecoadmin refund <交易ID>`、`/ecoadmin reload`、`/ecoadmin help`（別名 `?`），權限 `economy.admin`。退款語意（反向移動、原交易標記 REFUNDED、冪等、非 SUCCESS 拒絕）。管理員稽核（operator 欄位寫入、`/ledger` 與 Discord 顯示）。
- [ ] **Step 2: 驗證** → **Step 3: Commit** `docs(economycore): 管理指令與退款`

---

### Task 13: `economycore/alerts.md`

**Files:** Create。來源：`README.md` 第 123、129–145 行、`alert\*`、`util\Discord*`。

- [ ] **Step 1: 撰寫** — 必含：四類告警（大額/高頻/失敗/管理）、console 大額門檻（`alert-threshold.coin/.gem`）、高頻滑動時間窗（預設 60 秒 ≥10 筆，僅計玩家主動交易、冷卻節流）、Discord webhook 啟用設定（`discord.enabled/webhook-url/mention`、`velocity.*`，`/ecoadmin reload` 生效）、外部經濟監聽（`monitor-external-economy`、CMI `CMIUserBalanceChangeEvent` 反射、`source=EXTERNAL`）。
- [ ] **Step 2: 驗證** → **Step 3: Commit** `docs(economycore): 告警與 Discord 通知`

---

### Task 14: `economycore/config.md`

**Files:** Create。來源：`config.yml` 全檔 + `README.md` 第 84–93 行設定表。

- [ ] **Step 1: 撰寫** — 必含：`config.yml` 各區段逐欄說明表（database.*、alert-threshold、log-player-ip 預設 false、server-node-name、monitor-external-economy、discord.*、velocity.*、messages.*）。標註預設值與熱重載（`/ecoadmin reload`）。附最小可用範例。
- [ ] **Step 2: 驗證** → **Step 3: Commit** `docs(economycore): config.yml 設定說明`

---

### Task 15: `economycore/permissions.md`

**Files:** Create。來源：`plugin.yml` 第 29–48 行。

- [ ] **Step 1: 撰寫** — 權限節點表（節點 / 預設 / 說明）：`economy.admin`(op)、`economy.coin.pay`(true)、`economy.gem.pay`(true)、`economy.ledger.view`(op)、`auction.use`(true)、`auction.sell`(true)。
- [ ] **Step 2: 驗證** → **Step 3: Commit** `docs(economycore): 權限節點`

---

### Task 16: `economycore/api.md`

**Files:** Create。來源：`README.md` 第 149–170 行、`api\CurrencyAPI.java`、`model\*`。

- [ ] **Step 1: 撰寫** — 必含：取得服務（`Bukkit.getServicesManager().getRegistration(CurrencyAPI.class)`）、`getBalance`/`deposit` 範例（含 `CurrencyType`、`TransactionType`、`TransactionSource`）、**非同步呼叫提醒**（內部含 DB/Vault、需自行排程非同步並切回主執行緒）。
- [ ] **Step 2: 驗證** → **Step 3: Commit** `docs(economycore): 開發者 API`

---

# Phase 4 — EconomyCore 拍賣行模組

> 來源：`Economy\src\main\java\com\economycore\auction\**`（`command\AuctionCommand`、`service\{ListingService,ListingValidator,BidService,BidValidator,SettlementService,MailboxService,PenaltyPolicy,PriceParser}`、`settlement\SettlementTask`、`dao\{ListingDao,BidDao,MailboxDao,PenaltyDao}`、`gui\*`、`item\*`、`model\*`、`AuctionConfig`）、`plugin.yml`（`ah` 指令、`auction.*` 權限）、`Economy\docs\superpowers\specs\2026-06-15-auction-house-design.md` 與 `2026-06-16-auction-full-gui-anvil-design.md`。撰寫前先讀 `AuctionConfig.java` 與 `AuctionCommand.java` 確認實際子指令與設定鍵。

### Task 17: `economycore/auction/README.md`（總覽）+ `commands.md`

**Files:** Create both。

- [ ] **Step 1: README.md** — 必含：拍賣行定位、名詞（上架 listing、競標 bid、一口價 buyout、信箱 mailbox、結算 settlement、懲罰 penalty）、整體流程（賣家上架 → 買家競標/一口價 → 結算 → 雙方至信箱領取）、兩種上架模式（一口價 / 競標，對應 `ListingMode`）。
- [ ] **Step 2: commands.md** — `/ah`（別名 `/auction`）子指令表，逐一給語法/範例/權限：`sell`、`list`、`buy`、`bid`、`cancel`、`mailbox`、`claim`（以 `AuctionCommand.java` 實際分派為準）。權限 `auction.use`(true)、`auction.sell`(true)。
- [ ] **Step 3: 驗證**（連結 + 預覽） → **Step 4: Commit** `docs(auction): 總覽與指令`

---

### Task 18: `economycore/auction/selling.md`

**Files:** Create。來源：`ListingService`、`ListingValidator`、`PriceParser`、`gui\*`（賣出流程 + 鐵砧價格輸入）、設計文件。

- [ ] **Step 1: 撰寫** — 必含：手持物品上架流程、選一口價或競標、價格輸入（AnvilGUI 鐵砧輸入、`PriceParser` 正整數規則）、上架限制（驗證項：物品非空、價格正、件數/上限等以 `ListingValidator` 為準）、貨幣別（銅錢/元寶）。
- [ ] **Step 2: 驗證** → **Step 3: Commit** `docs(auction): 上架流程`

---

### Task 19: `economycore/auction/bidding.md` + `buyout.md`

**Files:** Create both。來源：`BidService`、`BidValidator`、`model\{Bid,BidResult,TopBidder}`；`buyout` 來源 `ListingService`/`model\BuyoutResult`。

- [ ] **Step 1: bidding.md** — 競標規則：最低加價、出價凍結/退還前一最高出價者、結標判定、`BidValidator` 的拒絕原因。
- [ ] **Step 2: buyout.md** — 一口價購買流程、即時成交、款項與物品流向（賣家得款入信箱、買家得物入信箱）。
- [ ] **Step 3: 驗證** → **Step 4: Commit** `docs(auction): 競標與一口價`

---

### Task 20: `economycore/auction/mailbox.md` + `settlement.md` + `penalties.md`

**Files:** Create three。來源：`MailboxService`/`MailboxDao`/`model\{MailboxEntry,MailboxReason}`；`SettlementService`/`settlement\SettlementTask`；`PenaltyPolicy`/`PenaltyDao`/`AuctionConfig`。

- [ ] **Step 1: mailbox.md** — 信箱用途（領取得標物、售出款、退回物品/退款）、`/ah mailbox`、`/ah claim`、`MailboxReason` 種類、領取流程。
- [ ] **Step 2: settlement.md** — 結算排程（`SettlementTask` 週期）、過期處理、退款、手續費/稅（以 `AuctionConfig` 實際鍵為準）。
- [ ] **Step 3: penalties.md** — 取消上架的懲罰政策（`PenaltyPolicy`：費用/冷卻/次數，以原始碼為準）。
- [ ] **Step 4: 驗證** → **Step 5: Commit** `docs(auction): 信箱、結算與懲罰`

---

### Task 21: `economycore/auction/gui.md`

**Files:** Create。來源：`gui\{GuiManager,GuiType,AuctionHolder}`、`item\{ItemCategorizer,QualityResolver}`、`model\{ItemCategory,SortOrder,ListingQuery}`、`2026-06-16-auction-full-gui-anvil-design.md`、`Economy\screenshot\`（若有截圖可引用）。

- [ ] **Step 1: 撰寫** — 必含：各 GUI 畫面（主頁/分類瀏覽/物品詳情/信箱）操作步驟、分類（`ItemCategory`）、排序（`SortOrder`）、品質（`QualityResolver`）、詳情頁的競標/一口價按鈕與鐵砧輸入。若 `Economy\screenshot\` 有對應圖檔，複製到 `economycore/auction/img/` 並嵌入。
- [ ] **Step 2: 驗證** → **Step 3: Commit** `docs(auction): GUI 介面`

---

### Task 22: `economycore/auction/config.md` + `database.md`

**Files:** Create both。來源：`AuctionConfig.java`、`Economy\config.yml`（拍賣行區段）、`Economy\sql\*` 與 `dao\*`（資料表結構）。

- [ ] **Step 1: config.md** — 拍賣行設定鍵逐欄說明（手續費、結算週期、上架上限、懲罰參數等，以 `AuctionConfig` 讀取的鍵為準）。
- [ ] **Step 2: database.md** — 資料表 listings / bids / mailbox / penalties 的欄位與用途（以 `dao\*` 的 SQL 與 `sql\` 腳本為準）。
- [ ] **Step 3: 驗證**：執行全站「連結/檔案存在檢查」，**整個 economycore/ 範圍無 DEAD**。
- [ ] **Step 4: Commit** `docs(auction): 設定與資料表`

---

# Phase 5 — SolarTermCore

> 來源：`SolarTermCore\SolarTermCore\src\main\java\me\cc\solarTermCore\*.java`（`SolarTermManager`、`SolarTermCommand`、`SolarTermPlaceholder`、`SolarTermTask`、`WeatherManager`、`WeatherTask`、`SolarTermChangeEvent`、`SystemListener`）、`resources\{plugin.yml,config.yml}`。撰寫前先讀 `SolarTermManager`（24 節氣定義/循環）、`WeatherManager`（天氣對應）、`config.yml`。

### Task 23: `solartermcore/` 骨架（_sidebar + README + install）

**Files:** Create `solartermcore/_sidebar.md`、`README.md`、`install.md`。

- [ ] **Step 1: `_sidebar.md`**

```md
* [⬅️ 回總站](/)
* [🏠 SolarTermCore 首頁](/solartermcore/)
* [🔌 安裝](/solartermcore/install)
* [💾 指令](/solartermcore/commands)
* [🌦️ 節氣與天氣](/solartermcore/weather)
* [🏷️ 佔位符](/solartermcore/placeholders)
* [🗒️ 設定](/solartermcore/config)
* [📮 事件 API](/solartermcore/events)
```

- [ ] **Step 2: `README.md`** — 必含：24 節氣循環概念、節氣如何隨遊戲時間推進（`SolarTermTask`）、與天氣連動（`WeatherManager`）、PlaceholderAPI 支援、導覽表。
- [ ] **Step 3: `install.md`** — 環境（Paper 1.21.1、PlaceholderAPI 為 softdepend）、安裝、首次啟動產生 config。
- [ ] **Step 4: 驗證** → **Step 5: Commit** `docs(solartermcore): 骨架、總覽與安裝`

---

### Task 24: SolarTermCore 內容頁（commands / weather / placeholders / config / events）

**Files:** Create `solartermcore/{commands,weather,placeholders,config,events}.md`。

- [ ] **Step 1: commands.md** — `/term next`、`/term set <節氣>`、`/term reload`（語法/範例/權限 `solarterm.admin` op）；列出 24 節氣可用名稱（取自 `SolarTermManager`）。
- [ ] **Step 2: weather.md** — 各節氣對應天氣/效果（以 `WeatherManager`/`WeatherTask` 為準）。
- [ ] **Step 3: placeholders.md** — `SolarTermPlaceholder` 提供的佔位符（如目前節氣、下一節氣、進度等，以原始碼為準）。
- [ ] **Step 4: config.md** — `config.yml` 逐項（節氣週期、訊息、天氣開關等）。
- [ ] **Step 5: events.md** — `SolarTermChangeEvent`（欄位、觸發時機、監聽範例）。
- [ ] **Step 6: 驗證**（solartermcore/ 範圍無 DEAD） → **Step 7: Commit** `docs(solartermcore): 指令、天氣、佔位符、設定、事件`

---

# Phase 6 — TwoFactorAuth

> 來源：`TwoFactorAuth\src\main\java\com\twofactorauth\**`（`command\*`、`service\{VerificationService,SecureInputManager}`、`listener\{PlayerJoinListener,AsyncChatListener,PlayerMoveListener,SecurityGuardListener,PlayerOPListener,PlayerQuitListener}`、`task\FreezeTask`、`util\{TOTPGenerator,QRCodeGenerator,MapQRRenderer,VerificationManager}`、`data\{TwoFAData,TwoFAUser,EncryptionUtil}`、`event\TwoFAVerificationEvent`、`config\TwoFactorAuthConfig`）、`resources\{plugin.yml,config.yml}`。撰寫前先讀 `PlayerJoinListener`、`VerificationService`、`TwoFAEnableSubcommand`、`config.yml`。

### Task 25: `twofactorauth/` 骨架（_sidebar + README + install）

**Files:** Create `twofactorauth/_sidebar.md`、`README.md`、`install.md`。

- [ ] **Step 1: `_sidebar.md`**

```md
* [⬅️ 回總站](/)
* [🏠 TwoFactorAuth 首頁](/twofactorauth/)
* [🔌 安裝](/twofactorauth/install)
* [🔑 綁定與驗證流程](/twofactorauth/setup)
* [💾 指令](/twofactorauth/commands)
* [🧑‍⚖️ 權限](/twofactorauth/permissions)
* [🗒️ 設定](/twofactorauth/config)
* [📮 事件 API](/twofactorauth/events)
```

- [ ] **Step 2: `README.md`** — 必含：安全模型（保護 OP 帳號、TOTP 標準、相容 Google/Microsoft Authenticator）、運作概觀（OP 加入時被凍結 → 出示密鑰/地圖 QR → 聊天輸入 6 位碼 → 解鎖；密鑰加密儲存 `EncryptionUtil`）。
- [ ] **Step 3: `install.md`** — 環境（Paper 1.21.1、`load: POSTWORLD`）、安裝、首次啟動 config。
- [ ] **Step 4: 驗證** → **Step 5: Commit** `docs(2fa): 骨架、總覽與安裝`

---

### Task 26: TwoFactorAuth 內容頁（setup / commands / permissions / config / events）

**Files:** Create `twofactorauth/{setup,commands,permissions,config,events}.md`。

- [ ] **Step 1: setup.md** — 綁定流程逐步：`/2fa enable` 產生密鑰與 QR（`QRCodeGenerator`/`MapQRRenderer` 地圖顯示）、用 App 掃描、加入時 `FreezeTask` 凍結與 `AsyncChatListener` 輸入驗證碼、`SecureInputManager` 安全輸入；`/2fa disable` 取消。
- [ ] **Step 2: commands.md** — `/2fa enable|disable|status`（別名 `twofa`、`2factor`）語法/範例/權限。
- [ ] **Step 3: permissions.md** — `twofactorauth.2fa.enable`(op)、`.disable`(op)、`.reset`(false) 表。
- [ ] **Step 4: config.md** — `config.yml` 逐項（凍結、訊息、加密、是否強制 OP 等，以 `TwoFactorAuthConfig` 為準）。
- [ ] **Step 5: events.md** — `TwoFAVerificationEvent`（觸發時機、欄位、監聽範例）。
- [ ] **Step 6: 驗證**（twofactorauth/ 範圍無 DEAD） → **Step 7: Commit** `docs(2fa): 綁定流程、指令、權限、設定、事件`

---

# Phase 7 — 收尾與全站驗證

### Task 27: 全站死連結掃描與導覽/搜尋驗證

**Files:** 視掃描結果修正任何檔。

- [ ] **Step 1: 全站連結檢查**：在 repo 根執行「連結/檔案存在檢查」片段（排除 `docs/specs`、`docs/plans`），確認**全站無 DEAD**。修正任何遺漏。
- [ ] **Step 2: 本機預覽全面驗證**：`npx docsify-cli serve .`，逐項確認：
  - 首頁四插件卡片皆可進入。
  - 進入每個插件後，左側為**該插件自己的** sidebar、頂部 navbar 可切換、「回總站」可回首頁。
  - 搜尋輸入關鍵字（如「拍賣」「節氣」「2fa」「強化」）可跨插件命中。
- [ ] **Step 3: Commit**（如有修正）`fix(wiki): 死連結與導覽修正`

---

### Task 28: 首頁與 navbar 定稿、合併準備

**Files:** `README.md`、`_navbar.md`（如需微調狀態/說明）。

- [ ] **Step 1:** 校對首頁四插件說明與狀態徽章與實際內容一致。
- [ ] **Step 2:** 確認 `_navbar.md` 四插件連結正確。
- [ ] **Step 3: Commit** `docs(wiki): 首頁與導覽定稿`
- [ ] **Step 4:** 回報使用者：是否推送 `docs/multi-plugin-wiki`、開 PR 併入 `master`，以及是否將 repo 改名為 `StarryRealm-Wiki`（見設計 §8，需使用者決定，**勿自行執行**）。

---

## 完成定義（對照設計 §9）

- 首頁為總站，四插件可進入。
- 每個插件有自己的 sidebar，頂部可切換，搜尋跨全站。
- SubCareers 原內容於 `/subcareers/...` 完整可用、無死連結。
- EconomyCore（含拍賣行）每指令皆有語法/範例/權限,拍賣行流程可照做。
- SolarTermCore、TwoFactorAuth 章節完整。
- 純前端、無建置、`.nojekyll` 保留。
