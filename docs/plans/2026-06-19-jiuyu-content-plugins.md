# 玖域內容插件 Wiki 擴充（圖鑑/料理/成就）— 實作計畫

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** 在既有玖域 docsify 總站新增 JiuyuCompendium、JiuyuCuisine、JuiyuAchievement 三個完整文件區，並接入總站導覽。

**Architecture:** docsify 純前端（CDN，零建置）。每個插件一個資料夾、各自 `_sidebar.md`，連結用 `/<plugin>/...` 絕對路徑；根層 `_navbar.md`/`_sidebar.md`/`README.md`/`index.html` 收錄三插件。比照既有 `economycore/`、`twofactorauth/` 的頁面深度與風格。

**Tech Stack:** docsify@4（vue 主題 + search 外掛）、Markdown、git。本機路徑 `C:\Users\user\OneDrive\Desktop\subcareers-wiki`，分支 `docs/fishing-set-bonus`。

**對應設計：** `docs/specs/2026-06-19-jiuyu-content-plugins-design.md`

## Global Constraints

- 語言一律 **zh-TW**。
- 指令說明固定四欄：**語法 / 範例 / 權限 / 行為**。
- 站內連結用 `/<plugin>/<page>` 絕對路徑；`_sidebar.md`、README 導覽表可省略 `.md`（同既有慣例）。
- 內容**據權威來源撰寫，不臆測**。來源優先序：插件 `CLAUDE.md` → `plugin.yml` → `resources/config.yml`（及 `recipes.yml`/`categories.yml`/`achievements/`/`codex/`/`titles.yml`/`shop.yml`）→ `src/**`。來源根目錄：`C:\Users\user\OneDrive\Desktop\目前開發插件\程式碼\<plugin>\`。
- **未實作 / 待實機驗證**的功能據實標註（料理 SQLite/MySQL 未做、成就遊戲內編輯器未做、料理 MMOItemsRef/MMOBuffs 反射簽名與 Compendium 解鎖 key 待驗證），不得寫成已完成。
- 純前端、零建置、保留 `.nojekyll`。
- 工作分支 `docs/fishing-set-bonus`。每個 commit 訊息結尾加：
  `Co-Authored-By: Claude Opus 4.8 <noreply@anthropic.com>`

---

## 驗證方式（取代單元測試）

無單元測試框架。每個 Task 完成後執行下列檢查（皆於 repo 根 `C:\Users\user\OneDrive\Desktop\subcareers-wiki`）：

**檢查 A — sidebar/README 連結目標存在（自動）：** 解析所有 `_sidebar.md` 與本次新增頁面內的站內連結（含省略 `.md` 者），確認對應檔存在。

```powershell
$root = "C:\Users\user\OneDrive\Desktop\subcareers-wiki"
Get-ChildItem -Path $root -Recurse -Include _sidebar.md,*.md |
  Where-Object { $_.FullName -notmatch '\\docs\\(specs|plans)\\|\\\.git\\' } |
  ForEach-Object {
    $f = $_.FullName
    foreach ($m in [regex]::Matches((Get-Content $f -Raw), '\]\((/[^)#\s]+)\)')) {
      $link = $m.Groups[1].Value.TrimEnd('/')
      if ($link -eq '') { continue }                 # '/' = 站台首頁，跳過
      if ($link -match '^/?https?:') { continue }
      $rel = $link.TrimStart('/')
      $cands = @((Join-Path $root ($rel + '.md')),
                 (Join-Path $root (Join-Path $rel 'README.md')),
                 (Join-Path $root $rel))
      if (-not ($cands | Where-Object { Test-Path $_ })) {
        Write-Output ("MISSING: {0} -> {1}" -f $f.Replace($root+'\',''), $link)
      }
    }
  }
```
輸出空白＝通過。（撰寫某頁前其連結目標尚未建立時，該頁的 MISSING 可暫時忽略，於該插件 Phase 結束時必須為空。）

**檢查 B — 本機預覽（人工，選用，建議於 Phase 4 做一次）：** `npx docsify-cli serve .` 開 `http://localhost:3000`，確認三插件可從首頁/navbar 進入、各自 sidebar 正確、搜尋可命中（「料理」「圖鑑」「成就」「釜」）。docsify 需經 http，不可 `file://`。

---

# Phase 1 — JiuyuCompendium（收集圖鑑）

> 來源根：`程式碼\JiuyuCompendium\`。先讀 `CLAUDE.md`、`plugin.yml`、`resources\config.yml`、`resources\categories.yml`、`src\com\jiuyu\compendium\command\CompendiumCommand.java`（核對子指令）、`src\com\jiuyu\compendium\api\CompendiumDiscoverEvent.java`、`src\com\jiuyu\compendium\bridge\PapiHook.java`。

### Task 1: `jiuyucompendium/` 骨架（_sidebar + README + install）

**Files:**
- Create: `jiuyucompendium/_sidebar.md`
- Create: `jiuyucompendium/README.md`
- Create: `jiuyucompendium/install.md`

**Interfaces:**
- Produces: 頁面路徑 `/jiuyucompendium/`、`/jiuyucompendium/install`，以及 sidebar 引用的 `commands`/`categories`/`discovery`/`config`/`permissions`/`api`（後續 Task 建立）。

- [ ] **Step 1: 寫 `jiuyucompendium/_sidebar.md`（精確內容）**

```md
* [⬅️ 回總站](/)
* [🏠 JiuyuCompendium 首頁](/jiuyucompendium/)
* [🔌 安裝](/jiuyucompendium/install)

---

* **使用**
* [💾 指令](/jiuyucompendium/commands)
* [🗂️ 分類與條目](/jiuyucompendium/categories)
* [🔓 解鎖機制](/jiuyucompendium/discovery)

---

* **設定與整合**
* [🗒️ 設定檔](/jiuyucompendium/config)
* [🧑‍⚖️ 權限](/jiuyucompendium/permissions)
* [🔧 事件與 API](/jiuyucompendium/api)
```

- [ ] **Step 2: 寫 `jiuyucompendium/README.md`** — 必含章節：
  - 一句話定位：互動即解鎖的收集圖鑑（圖譜）。
  - 運作概念：擊殺 MythicMobs 自定義生物 / 取得 MMOItems 物品 → 自動解鎖；未解鎖在 GUI 顯示 `???`。
  - 規模：5 大類（mobs / professions / appearance / multiplayer / items）、實機載入 672 條目（來源 CLAUDE.md「載入驗證紀錄」）。
  - 與 JuiyuAchievement 連動一句話（解鎖時鏡像 discover key）。
  - 結尾「文件導覽」表格（連 install/commands/categories/discovery/config/permissions/api，路徑用 `/jiuyucompendium/...`），格式比照 `twofactorauth/README.md`。

- [ ] **Step 3: 寫 `jiuyucompendium/install.md`** — 必含：
  - 環境：Paper 1.21.1、Java 21；softdepend 清單（MythicMobs、MMOItems、MythicLib、MMOCore、PlaceholderAPI、JuiyuAchievement、JiuyuCodex），說明皆為軟依賴（缺少仍可載入，對應功能停用）。
  - 安裝步驟：放 `plugins/`、重啟（**勿 `/reload`**）、首次啟動產生 `config.yml`/`categories.yml`/`entries/manual.yml`、執行期寫 `generated/`。
  - 儲存後端：YAML（預設，playerdata）/ SQLite / MySQL，JDBC 反射載入、**缺驅動自動回退 YAML**（指向 config `storage.type`，詳見設定頁）。

- [ ] **Step 4: 驗證** — 執行檢查 A。預期：`jiuyucompendium/_sidebar.md` 對 commands/categories/discovery/config/permissions/api 的 MISSING 為「本 Phase 待建」可暫忽略；`/jiuyucompendium/`、`/jiuyucompendium/install` 不可 MISSING。

- [ ] **Step 5: Commit**

```bash
git add jiuyucompendium/_sidebar.md jiuyucompendium/README.md jiuyucompendium/install.md
git commit -m "docs(compendium): 骨架、總覽與安裝"
```

---

### Task 2: `jiuyucompendium/commands.md` + `permissions.md`

**Files:**
- Create: `jiuyucompendium/commands.md`
- Create: `jiuyucompendium/permissions.md`

- [ ] **Step 1: 寫 `commands.md`** — 以四欄（語法/範例/權限/行為）列出（核對 `CompendiumCommand.java` 實際分派）：
  - `/compendium`（別名 `comp`/`jc`/`tujian`）— 開圖鑑 GUI，權限 `compendium.use`。
  - `/compendium help` — 說明。
  - 管理（權限 `compendium.admin`）：`reload`（重載設定與圖鑑）、`sync`（掃描 MythicMobs/MMOItems 寫入 generated 後重載）、`unlock <玩家> <entryId|discoverKey>`、`lock <玩家> <entryId|discoverKey>`、`reset <玩家>`（重置該玩家資料）、`stats [玩家]`（進度統計）、`save`（立即存檔）。
- [ ] **Step 2: 寫 `permissions.md`** — 權限節點表（節點/預設/說明）：`compendium.use`(true，開啟圖鑑)、`compendium.admin`(op，reload/sync/unlock/lock/reset/stats/save)。
- [ ] **Step 3: 驗證** — 執行檢查 A，本兩頁無 MISSING。
- [ ] **Step 4: Commit** — `git add jiuyucompendium/commands.md jiuyucompendium/permissions.md && git commit -m "docs(compendium): 指令與權限"`

---

### Task 3: `jiuyucompendium/categories.md` + `discovery.md`

**Files:**
- Create: `jiuyucompendium/categories.md`
- Create: `jiuyucompendium/discovery.md`
- 來源：`resources\categories.yml`、`config.yml`（`auto-sync` 區）、`src\...\scan\*`、`src\...\listener\*`、CLAUDE.md「關鍵設計決策」。

- [ ] **Step 1: 寫 `categories.md`** — 必含：
  - 5 大類樹與各自子分類：`mobs`(自定義生物)、`professions`(採礦/伐木/鍊藥/附魔/鍛造/耕作/釣魚)、`appearance`(坐騎/寵物/時裝)、`multiplayer`(公會/結義/俠緣/師徒)、`items`(武器/防具/飾品/藥品/道具/料理)（以 `categories.yml` 實際節點為準）。
  - `categories.yml` 結構說明（如何新增分類/子分類）。
  - 自動對應：`config.yml` `auto-sync.mmoitems.type-map`（MMOItems type → `category.subcategory`）摘要表 + 可覆寫；`auto-sync.mythicmobs`（folder/ignored-files/type-icons/default-category）。
  - 手動條目 `entries/manual.yml`（異質來源：公會/結義走 MMOCore、時裝走材質包等，需手動條目 + 外部 unlock 橋接）。
  - `generated/` 自動掃描結果（`auto-mythicmobs.yml`、`auto-mmoitems.yml`）。
- [ ] **Step 2: 寫 `discovery.md`** — 必含：
  - 互動即解鎖（無需任務/指令）：擊殺 MythicMobs（`MythicMobListener`）、取得 MMOItems（`ItemDiscoveryListener`，撿取＋背包掃描）、原生生物（`VanillaMobListener`，受 `discovery.track-vanilla-mobs` 控制）。
  - 關鍵設計：scanner **直接讀對方 plugin 的 YAML 檔、不呼叫其 Java API**（刻意避開版本不合崩潰）。
  - `config.yml` `discovery.*`：`notify`、`sound`、`scan-inventory-on-join`、`scan-inventory-on-click`、`track-vanilla-mobs` 各自行為。
- [ ] **Step 3: 驗證** — 檢查 A 無 MISSING。
- [ ] **Step 4: Commit** — `git commit -m "docs(compendium): 分類與解鎖機制"`

---

### Task 4: `jiuyucompendium/config.md` + `api.md`

**Files:**
- Create: `jiuyucompendium/config.md`
- Create: `jiuyucompendium/api.md`
- 來源：`resources\config.yml` 全檔、`api\CompendiumDiscoverEvent.java`、`bridge\PapiHook.java`、`bridge\AchievementBridge`。

- [ ] **Step 1: 寫 `config.md`** — `config.yml` 逐區欄位表（鍵/預設/說明）：`language`、`debug`、`autosave-ticks`、`storage`(type/table-prefix/sqlite/mysql)、`discovery`、`gui`(title-main/title-category/title-subcategory)、`messages`、`achievement-bridge`(enabled/plugin-names/mirror-discover-key/mirror-compendium-key)、`auto-sync`(write-generated-files/mythicmobs/mmoitems/type-map)、`category-rewards`/`subcategory-rewards`。標註熱重載（`/compendium reload`）。
- [ ] **Step 2: 寫 `api.md`** — 必含：
  - `CompendiumDiscoverEvent`：以 `api\CompendiumDiscoverEvent.java` 實際欄位/是否可取消為準，給觸發時機 + 監聽範例。
  - PAPI：id `jiuyucompendium`（以 `PapiHook.java` 實際提供的佔位符為準）。
  - 成就橋接：`AchievementBridge` 反射呼叫成就插件 `content().discover(player, key)`，受 `achievement-bridge.*` 設定控制（鏡像 discover key / 可選 compendium key）。
- [ ] **Step 3: 驗證** — 執行檢查 A，**整個 `jiuyucompendium/` 範圍無 MISSING**。
- [ ] **Step 4: Commit** — `git commit -m "docs(compendium): 設定檔與事件/API"`

---

# Phase 2 — JiuyuCuisine（料理系統）

> 來源根：`程式碼\JiuyuCuisine\`。先讀 `CLAUDE.md`、`plugin.yml`、`resources\config.yml`、`resources\recipes.yml`、`src\com\jiuyu\cuisine\command\CuisineCommand.java`、`src\...\service\CookingService.java`、`src\...\bridge\*`。

### Task 5: `jiuyucuisine/` 骨架（_sidebar + README + install）

**Files:**
- Create: `jiuyucuisine/_sidebar.md`
- Create: `jiuyucuisine/README.md`
- Create: `jiuyucuisine/install.md`

**Interfaces:**
- Produces: `/jiuyucuisine/`、`/jiuyucuisine/install`，及 sidebar 引用的 `commands`/`cooking`/`recipes`/`config`/`permissions`/`integration`。

- [ ] **Step 1: 寫 `jiuyucuisine/_sidebar.md`（精確內容）**

```md
* [⬅️ 回總站](/)
* [🏠 JiuyuCuisine 首頁](/jiuyucuisine/)
* [🔌 安裝](/jiuyucuisine/install)

---

* **使用**
* [💾 指令](/jiuyucuisine/commands)
* [🍳 烹飪流程與熟練度](/jiuyucuisine/cooking)
* [📖 食譜設定](/jiuyucuisine/recipes)

---

* **設定與整合**
* [🗒️ 設定檔](/jiuyucuisine/config)
* [🧑‍⚖️ 權限](/jiuyucuisine/permissions)
* [🔌 外掛連動](/jiuyucuisine/integration)
```

- [ ] **Step 2: 寫 `README.md`** — 必含：定位（右鍵「釜」開烹飪 GUI，投入 MMOItems 副職業產物 → 比對食譜 → 產出料理；食用觸發 MMOBuffs）；核心設計（一料理多食譜 + 自由探索鍋；失敗不退還、產出「失敗的料理」；無等級、只有熟練度影響成功率與大成功；首次製作解鎖 JiuyuCompendium `items.food`）；文件導覽表。
- [ ] **Step 3: 寫 `install.md`** — 環境（Paper 1.21.1 / Java 21；softdepend：MMOItems、MythicLib、MMOCore、MMOBuffs、PlaceholderAPI、JiuyuCompendium）；安裝、重啟（勿 `/reload`）、首次啟動產生 `config.yml`/`recipes.yml`；**儲存目前僅 YAML**（SQLite/MySQL 尚未實作，據實標註）。
- [ ] **Step 4: 驗證** — 檢查 A，`/jiuyucuisine/`、`/jiuyucuisine/install` 不可 MISSING（其餘本 Phase 待建可暫忽略）。
- [ ] **Step 5: Commit** — `git commit -m "docs(cuisine): 骨架、總覽與安裝"`

---

### Task 6: `jiuyucuisine/commands.md` + `permissions.md`

**Files:**
- Create: `jiuyucuisine/commands.md`
- Create: `jiuyucuisine/permissions.md`

- [ ] **Step 1: 寫 `commands.md`** — 四欄（核對 `CuisineCommand.java`）：`/cuisine`（別名 `cook`/`liaoli`/`fu`；或 `open`）開釜 GUI（`cuisine.use`）、`/cuisine stats [玩家]`（查熟練度/進度）、`/cuisine reload`（admin）、`/cuisine give <玩家> <dishId> [品質]`（admin，給予料理）。
- [ ] **Step 2: 寫 `permissions.md`** — `cuisine.use`(true)、`cuisine.admin`(op，reload/give/管理)。
- [ ] **Step 3: 驗證** — 檢查 A 無 MISSING。
- [ ] **Step 4: Commit** — `git commit -m "docs(cuisine): 指令與權限"`

---

### Task 7: `jiuyucuisine/cooking.md` + `recipes.md`

**Files:**
- Create: `jiuyucuisine/cooking.md`
- Create: `jiuyucuisine/recipes.md`
- 來源：`config.yml`(`cauldron`/`gui`/`cooking`)、`recipes.yml`、`service\CookingService.java`、`model\{CookIngredient,Recipe,Dish}.java`、`gui\CauldronGui.java`。

- [ ] **Step 1: 寫 `cooking.md`** — 必含：
  - 開啟：右鍵「釜」（`cauldron.block`、`accept-any-cauldron`、`require-empty-hand`）。
  - GUI：輸入格（`gui.input-slots`）、開始烹飪按鈕（`gui.cook-slot`/材質/名稱）；關閉退還食材。
  - 比對：**精確集合比對**所有食譜，沒中就失敗。
  - 熟練度與結果：成功率 `base-success-rate + 熟練度×success-per-proficiency`（上限 `max-success-rate`）、大成功 `crit-base-rate + 熟練度×crit-per-proficiency`（品質+1）、失敗產出「失敗的料理」（`cooking.fail-dish`，留空給原版）；品質→buff 時長倍率 `quality-duration-multiplier`；成功/大成功/失敗音效。
  - 成品 PDC：寫入 `dish`(dishId) + `quality`(int)，供食用時套對應 buff/時長。
- [ ] **Step 2: 寫 `recipes.md`** — `recipes.yml` 格式：每道 `result`(MMOItems type/id)、`buff`(effect/base-duration/stacks)、`recipes`（多組做法，每組 `ingredients` 用 `TYPE ID amount` 或 `vanilla MAT amount`）；食材辨識＝MMOItems Type+ID（原生 NBT，來自副職業產物，因 MMOItems 無自由 tag）。附一道完整範例（取自或對齊 `recipes.yml`）。
- [ ] **Step 3: 驗證** — 檢查 A 無 MISSING。
- [ ] **Step 4: Commit** — `git commit -m "docs(cuisine): 烹飪流程與食譜設定"`

---

### Task 8: `jiuyucuisine/config.md` + `integration.md`

**Files:**
- Create: `jiuyucuisine/config.md`
- Create: `jiuyucuisine/integration.md`
- 來源：`config.yml` 全檔、`bridge\{MMOItemsRef,MMOBuffsBridge,CompendiumBridge}.java`、CLAUDE.md「尚未實機驗證」段。

- [ ] **Step 1: 寫 `config.md`** — `config.yml` 逐區（鍵/預設/說明）：`language`/`debug`/`autosave-ticks`、`cauldron`、`gui`、`cooking`(成功/大成功公式、fail-dish、quality-duration-multiplier、音效)、`mmobuffs`(enabled/apply-command)、`compendium`(enabled/discover-key-prefix)、`storage`、`messages`。標註熱重載（`/cuisine reload`）。
- [ ] **Step 2: 寫 `integration.md`** — 三反射橋接：`MMOItemsRef`（讀 NBT type/id、依 type+id 產生物品）、`MMOBuffsBridge`（console 指令模板 `mmobuffs.apply-command`，`{player}{effect}{duration}{stacks}`）、`CompendiumBridge`（送 `compendium unlock <player> cuisine:<dishId>`，受 `compendium.enabled` 控制）。**據實標註 CLAUDE.md 列的 3 點待實機驗證**（MMOItemsRef 反射簽名對 MMOItems 6.10、MMOBuffs 1.2.3 指令簽名、`cuisine:<dishId>` 在 Compendium 是否有對應 entry）。
- [ ] **Step 3: 驗證** — 檢查 A，**整個 `jiuyucuisine/` 範圍無 MISSING**。
- [ ] **Step 4: Commit** — `git commit -m "docs(cuisine): 設定檔與外掛連動"`

---

# Phase 3 — JuiyuAchievement（成就 + 圖鑑/稱號）

> 來源根：`程式碼\JuiyuAchievement\`。先讀 `CLAUDE.md`、`plugin.yml`、`resources\config.yml`、`resources\{titles.yml,shop.yml}`、`resources\achievements\*`、`resources\codex\*`、`src\com\jiuyu\codex\PapiHook.java`。**命名注意**：部署名 `JuiyuAchievement`、package/主類 `com.jiuyu.codex`/`JiuyuCodex`、資料夾 `plugins/JuiyuAchievement/`。

### Task 9: `juiyuachievement/` 骨架（_sidebar + README + install）

**Files:**
- Create: `juiyuachievement/_sidebar.md`
- Create: `juiyuachievement/README.md`
- Create: `juiyuachievement/install.md`

**Interfaces:**
- Produces: `/juiyuachievement/`、`/juiyuachievement/install`，及 sidebar 引用的 `commands`/`achievements`/`codex`/`rewards`/`config`/`permissions`/`placeholders`。

- [ ] **Step 1: 寫 `juiyuachievement/_sidebar.md`（精確內容）**

```md
* [⬅️ 回總站](/)
* [🏠 JuiyuAchievement 首頁](/juiyuachievement/)
* [🔌 安裝](/juiyuachievement/install)

---

* **使用**
* [💾 指令](/juiyuachievement/commands)
* [🏆 成就與觸發來源](/juiyuachievement/achievements)
* [📖 圖鑑系統](/juiyuachievement/codex)
* [🎁 獎勵：點數/稱號/商店](/juiyuachievement/rewards)

---

* **設定與整合**
* [🗒️ 設定檔](/juiyuachievement/config)
* [🧑‍⚖️ 權限](/juiyuachievement/permissions)
* [🏷️ PlaceholderAPI](/juiyuachievement/placeholders)
```

- [ ] **Step 2: 寫 `README.md`** — 必含：定位（成就 + 圖鑑系統，整合 5 大內容來源觸發）；**命名說明**（部署名 JuiyuAchievement、內部 JiuyuCodex、資料夾 `plugins/JuiyuAchievement/`、`Juiyu` 為原拼法）；功能概覽（成就引擎 + 點數/稱號/指令/通知獎勵、GUI 圖鑑/成就/稱號分頁、排行榜、記分板追蹤）；文件導覽表。
- [ ] **Step 3: 寫 `install.md`** — 環境（Paper 1.21.1 / Java 21；softdepend：MythicMobs、MMOCore、MMOItems、BeautyQuests、MythicLib、PlaceholderAPI）；安裝、重啟、設定檔位置（`achievements/`、`codex/`、`titles.yml`、`shop.yml`、`config.yml`）；YAML per-player 儲存（`playerdata/<uuid>.yml`）。
- [ ] **Step 4: 驗證** — 檢查 A，`/juiyuachievement/`、`/juiyuachievement/install` 不可 MISSING。
- [ ] **Step 5: Commit** — `git commit -m "docs(achievement): 骨架、總覽與安裝"`

---

### Task 10: `juiyuachievement/commands.md` + `permissions.md`

**Files:**
- Create: `juiyuachievement/commands.md`
- Create: `juiyuachievement/permissions.md`

- [ ] **Step 1: 寫 `commands.md`** — 四欄：`/codex`（別名 `tujian`/`cj`/`achievement`）開 GUI（圖鑑/成就/稱號分頁，`codex.use`）；管理（`codex.admin`）：`reload`、`give`、`points`、`reset`、`export`、`import`（以 CLAUDE.md「管理指令」與指令類為準，逐一給語法/範例/行為）。
- [ ] **Step 2: 寫 `permissions.md`** — `codex.use`(true)、`codex.admin`(op)。
- [ ] **Step 3: 驗證** — 檢查 A 無 MISSING。
- [ ] **Step 4: Commit** — `git commit -m "docs(achievement): 指令與權限"`

---

### Task 11: `juiyuachievement/achievements.md` + `codex.md`

**Files:**
- Create: `juiyuachievement/achievements.md`
- Create: `juiyuachievement/codex.md`
- 來源：`resources\achievements\*`、`resources\codex\*`、`config.yml`(`codex-rewards`)、CLAUDE.md「已實作功能」「API hook」。

- [ ] **Step 1: 寫 `achievements.md`** — 必含：
  - 5 大觸發來源：原生＋MythicMobs 擊殺、取得物品（原生＋MMOItems）、MMOCore 主等級/副職業等級提升、BeautyQuests 任務階段/完成。
  - 成就定義欄位（以 `achievements/*` 實際 YAML 為準）：觸發條件、`hidden`(隱藏)、`requires`(系列前置)、`reset: daily|weekly`(UTC 邊界重置)、`broadcast`(完成廣播)、完成音效、獎勵設定。
  - 一個完整成就 YAML 範例（取自 `achievements/`）。
- [ ] **Step 2: 寫 `codex.md`** — 必含：圖鑑（codex）條目定義（`codex/*`）、與成就的差異（圖鑑＝收錄、成就＝目標）、圖鑑分類 100% 全收集獎勵（`config.yml` `codex-rewards.<cat>`，主控台 `%player%` 展開）。
- [ ] **Step 3: 驗證** — 檢查 A 無 MISSING。
- [ ] **Step 4: Commit** — `git commit -m "docs(achievement): 成就觸發與圖鑑系統"`

---

### Task 12: `juiyuachievement/rewards.md` + `placeholders.md`

**Files:**
- Create: `juiyuachievement/rewards.md`
- Create: `juiyuachievement/placeholders.md`
- 來源：`resources\titles.yml`、`resources\shop.yml`、`config.yml`(`scoreboard`/`chat-prefix`)、`PapiHook.java`、CLAUDE.md。

- [ ] **Step 1: 寫 `rewards.md`** — 必含：獎勵類型（點數 / 稱號 `titles.yml` / 可設定指令 / 通知）；點數商店 `shop.yml`（格式 + 範例）；多排行榜（點數 / 完成度）；側邊欄記分板追蹤（GUI 點成就追蹤，最多 5，`scoreboard.*`）；內建聊天前綴稱號（`chat-prefix.enabled` 預設 false，建議改用 PAPI `%juiyu_title%`）。
- [ ] **Step 2: 寫 `placeholders.md`** — PAPI id `juiyu`（以 `PapiHook.java` 實際為準）：`%juiyu_title%`、`%juiyu_title_id%`、`%juiyu_points%`、`%juiyu_completed%`、`%juiyu_total%`、`%juiyu_completion%`，逐一給意義與用途。
- [ ] **Step 3: 驗證** — 檢查 A 無 MISSING。
- [ ] **Step 4: Commit** — `git commit -m "docs(achievement): 獎勵系統與 PlaceholderAPI"`

---

### Task 13: `juiyuachievement/config.md`

**Files:**
- Create: `juiyuachievement/config.md`
- 來源：`resources\config.yml` 全檔。

- [ ] **Step 1: 寫 `config.md`** — `config.yml` 逐區（鍵/預設/說明）：`language`/`debug`、`completion`(sound/broadcast-all)、`scoreboard`(enabled/title)、`chat-prefix`(enabled)、`codex-rewards`（分類全收集獎勵指令清單，含範例）。標註熱重載（`/codex reload`）。
- [ ] **Step 2: 驗證** — 檢查 A，**整個 `juiyuachievement/` 範圍無 MISSING**。
- [ ] **Step 3: Commit** — `git commit -m "docs(achievement): config.yml 設定說明"`

---

# Phase 4 — 接入總站與全站驗證

### Task 14: 接入 navbar / 根 sidebar / 首頁 README / index.html

**Files:**
- Modify: `_navbar.md`
- Modify: `_sidebar.md`
- Modify: `README.md`
- Modify: `index.html`

- [ ] **Step 1: `_navbar.md`** — 在 TwoFactorAuth 那行（`* [🔐 TwoFactorAuth — OP 二階段驗證](/twofactorauth/)`）之後，於同層級新增三行：

```md
  * [📖 JiuyuCompendium — 收集圖鑑](/jiuyucompendium/)
  * [🍲 JiuyuCuisine — 料理系統](/jiuyucuisine/)
  * [🏆 JuiyuAchievement — 成就系統](/juiyuachievement/)
```

- [ ] **Step 2: `_sidebar.md`** — 在 `* [🔐 TwoFactorAuth](/twofactorauth/)` 之後新增三行：

```md
* [📖 JiuyuCompendium](/jiuyucompendium/)
* [🍲 JiuyuCuisine](/jiuyucuisine/)
* [🏆 JuiyuAchievement](/juiyuachievement/)
```

- [ ] **Step 3: 首頁 `README.md`** — 在插件一覽表 TwoFactorAuth 那列之後新增三列：

```md
| 📖 **JiuyuCompendium** | 收集圖鑑：與內容互動（擊殺 MythicMobs、取得 MMOItems）自動解鎖條目，未解鎖顯示 ???，與成就系統連動 | [進入](/jiuyucompendium/) | ✅ |
| 🍲 **JiuyuCuisine** | 料理系統：右鍵「釜」投入副職業產物比對食譜產出料理，食用觸發 MMOBuffs，熟練度影響成功率 | [進入](/jiuyucuisine/) | ✅ |
| 🏆 **JuiyuAchievement** | 成就 + 圖鑑系統：整合擊殺/取得/等級/任務 5 大來源，含點數、稱號、商店與排行榜獎勵 | [進入](/juiyuachievement/) | ✅ |
```

- [ ] **Step 4: `index.html`** — 將 `<meta name="description">` 內容末尾追加三插件名（改為）：

```html
  <meta name="description" content="玖域（StarryRealm）自製 Minecraft 插件文件總站：EconomyCore、SubCareers、SolarTermCore、TwoFactorAuth、JiuyuCompendium、JiuyuCuisine、JuiyuAchievement">
```

- [ ] **Step 5: 驗證** — 執行檢查 A（全站），確認三插件首頁連結與所有頁面**無 MISSING**。
- [ ] **Step 6: Commit** — `git add _navbar.md _sidebar.md README.md index.html && git commit -m "feat(wiki): 總站導覽收錄圖鑑/料理/成就三插件"`

---

### Task 15: 全站驗證與收尾

**Files:** 視結果修正任何檔。

- [ ] **Step 1: 全站連結檢查** — 於 repo 根執行檢查 A，**全站無 MISSING**。修正任何遺漏。
- [ ] **Step 2: 本機預覽（檢查 B，建議）** — `npx docsify-cli serve .`，逐項確認：首頁三新插件卡片可進入；進入後左側為**該插件自己的** sidebar、頂部 navbar 可切換、「回總站」可回首頁；搜尋「料理」「圖鑑」「成就」「釜」可命中。
- [ ] **Step 3: Commit（如有修正）** — `git commit -m "fix(wiki): 死連結與導覽修正"`
- [ ] **Step 4: 回報使用者** — 三插件文件完成、全站無死連結；詢問是否 push `docs/fishing-set-bonus` / 開 PR（**勿自行執行**）。

---

## 完成定義（對照設計 §9）

- 三插件各有獨立資料夾、`_sidebar.md`、`README.md` 與全部主題分頁（jiuyucompendium 8 頁、jiuyucuisine 8 頁、juiyuachievement 9 頁內容頁）。
- 總站 navbar / 根 sidebar / 首頁表格 / index.html 描述皆收錄三插件，可從首頁進入。
- 每個指令皆有語法/範例/權限/行為；config 各鍵有說明。
- 「待驗證 / 未實作」內容據實標註。
- 全站檢查 A 無 MISSING；純前端、零建置、保留 `.nojekyll`。
