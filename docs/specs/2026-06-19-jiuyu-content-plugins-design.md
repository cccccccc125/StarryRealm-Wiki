# 玖域內容插件 Wiki 擴充設計（圖鑑 / 料理 / 成就）

- 日期：2026-06-19
- 狀態：設計（待實作）
- repo：`cccccccc125/StarryRealm-Wiki`（本機 `Desktop/subcareers-wiki`），分支 `docs/fishing-set-bonus`（直接於此分支進行，逐插件 commit）

## 1. 背景與目標

現有 wiki 已是「玖域自製插件（StarryRealm）」多插件總站，收錄 EconomyCore、SubCareers、SolarTermCore、TwoFactorAuth 四個插件，每個插件自成一區（資料夾 + 獨立 `_sidebar.md` + `README.md` + 主題分頁）。

SubCareers 近期已**移除內建圖鑑/里程碑內容**，因伺服器改用「外部圖鑑系統」。本次要補上的正是該外部系統與其周邊內容插件，把三個玖域自製內容插件加入總站：

| 插件 | 部署名 | package / 主類 | 主要指令 | 角色 |
|---|---|---|---|---|
| JiuyuCompendium | `JiuyuCompendium` | `com.jiuyu.compendium` | `/compendium`（`comp`/`jc`/`tujian`） | 收集圖鑑（互動自動解鎖） |
| JiuyuCuisine | `JiuyuCuisine` | `com.jiuyu.cuisine` | `/cuisine`（`cook`/`liaoli`/`fu`） | 料理系統（釜烹飪） |
| JuiyuAchievement | `JuiyuAchievement`（內部 `JiuyuCodex`） | `com.jiuyu.codex` / `JiuyuCodex` | `/codex`（`tujian`/`cj`/`achievement`） | 成就 + 圖鑑/稱號獎勵 |

三者皆 Paper 1.21.1 / Java 21、作者 Natsu、屬同一玖域伺服器生態，並彼此連動（料理首次製作→通知圖鑑解鎖；圖鑑解鎖→鏡像 key 給成就插件）。

**目標**：為三個插件各建一個完整文件區（比照 economycore 的深度），並接入總站導覽（navbar / 根 sidebar / 首頁表格 / index.html 描述）。文件全程 **zh-TW**、以使用方法為導向、內容據權威來源撰寫不臆測。

## 2. 範圍與非目標

**範圍內**
- 三個插件各一資料夾（`jiuyucompendium/`、`jiuyucuisine/`、`juiyuachievement/`），含 `_sidebar.md` + `README.md` + 主題分頁。
- 接入總站：`_navbar.md`、根 `_sidebar.md`、首頁 `README.md` 各加三條目；`index.html` meta description 補三插件名。
- 指令頁一律提供「語法 / 範例 / 權限 / 行為」。

**非目標（YAGNI）**
- 不修改既有四插件內容。
- 不新增文件框架（續用 docsify CDN、零建置、保留 `.nojekyll`）。
- 不做多語系。
- 不自動 push / 不自動開 PR（完成後回報，由使用者決定）。
- 不為「尚未實作」的功能寫成已實作（料理的 SQLite/MySQL、成就的遊戲內編輯器等，標註為未做或待驗證）。

## 3. 命名與導覽接線

- **資料夾命名**沿用既有慣例「plugin.yml 名稱小寫」：`jiuyucompendium/`、`jiuyucuisine/`、`juiyuachievement/`（`juiyu` 維持 user 原拼法，與部署名一致）。
- **導覽標籤**刻意區分兩個「圖鑑」相關插件：
  - 📖 **JiuyuCompendium — 收集圖鑑（自動解鎖）**
  - 🍲 **JiuyuCuisine — 料理系統**
  - 🏆 **JuiyuAchievement — 成就系統（含稱號/圖鑑獎勵）**
- 接線位置（既有四插件之後追加）：
  - `_navbar.md`：插件下拉新增三條（連 `/jiuyucompendium/` 等）。
  - 根 `_sidebar.md`：插件清單新增三條。
  - 根 `README.md`：「插件一覽」表格新增三列（含說明與 ✅ 狀態）。
  - `index.html`：`<meta name="description">` 追加三插件名。

## 4. 資訊架構（新增結構）

```
jiuyucompendium/
  _sidebar.md  README.md  install.md  commands.md
  categories.md  discovery.md  config.md  permissions.md  api.md
jiuyucuisine/
  _sidebar.md  README.md  install.md  commands.md
  cooking.md  recipes.md  config.md  permissions.md  integration.md
juiyuachievement/
  _sidebar.md  README.md  install.md  commands.md
  achievements.md  codex.md  rewards.md  config.md  permissions.md  placeholders.md
```

每個 `_sidebar.md` 開頭固定為「⬅️ 回總站 → 本插件首頁 → 安裝」，連結一律 `/<plugin>/...` 絕對路徑（比照既有 sidebar）。

## 5. 各插件頁面內容（必含章節 + 權威來源）

> **權威來源優先序**：各插件 `CLAUDE.md` 交接文件 → `plugin.yml` → `resources/config.yml`（及 `recipes.yml` / `categories.yml` / `achievements/` / `codex/` / `titles.yml` / `shop.yml`）→ `src/**`（必要時讀實作確認簽名/鍵名）。位置：`Desktop/目前開發插件/程式碼/<plugin>/`。

### 5.1 JiuyuCompendium（`jiuyucompendium/`）

- **README** — 定位（互動即解鎖的收集圖鑑）、未解鎖顯示 `???`、5 大類 / 672 條目、與 JuiyuAchievement 連動、導覽表。
- **install** — 環境（Paper 1.21.1 / Java 21；softdepend：MythicMobs、MMOItems、MythicLib、MMOCore、PlaceholderAPI、JuiyuAchievement、JiuyuCodex）、安裝、首次啟動產生 config、儲存後端（YAML/SQLite/MySQL，缺驅動回退 YAML）。
- **commands** — `/compendium`（開 GUI）、`help`；管理（`compendium.admin`）：`reload`、`sync`、`unlock <玩家> <entryId|discoverKey>`、`lock`、`reset <玩家>`、`stats [玩家]`、`save`。逐一給語法/範例/權限/行為。
- **categories** — 5 大類樹（`mobs`/`professions`/`appearance`/`multiplayer`/`items`）、`categories.yml` 結構、`config.yml` 的 `auto-sync.mmoitems.type-map`（MMOItems type → `category.subcategory`）、`entries/manual.yml` 手動條目、`generated/` 自動掃描檔。
- **discovery** — 互動即解鎖機制：擊殺 MythicMobs / 取得 MMOItems（撿取＋背包掃描）；scanner **直接讀對方 plugin 的 YAML**（刻意不呼叫其 API）；`discovery.*` 設定（notify/sound/scan 時機/track-vanilla-mobs）。
- **config** — `config.yml` 逐區：`storage`、`discovery`、`gui`、`messages`、`achievement-bridge`、`auto-sync`（mythicmobs / mmoitems / type-map / type-icons）、`category-rewards`。
- **permissions** — `compendium.use`(true)、`compendium.admin`(op)。
- **api** — `CompendiumDiscoverEvent`（`api/CompendiumDiscoverEvent.java`，欄位/觸發時機/監聽範例）、PAPI id `jiuyucompendium`（`bridge/PapiHook.java`）、成就橋接（`bridge/AchievementBridge`，反射呼叫 `content().discover(player,key)`、`mirror-discover-key`）。

### 5.2 JiuyuCuisine（`jiuyucuisine/`）

- **README** — 定位（釜烹飪：MMOItems 副職業產物 → 食譜比對 → 料理 → MMOBuffs）、無等級只有熟練度、失敗不退還、首次製作解鎖圖鑑、導覽表。
- **install** — 環境（softdepend：MMOItems、MythicLib、MMOCore、MMOBuffs、PlaceholderAPI、JiuyuCompendium）、安裝、初始化順序註記（settings→recipeBook→storage/bridges→cooking）。
- **commands** — `/cuisine`（或 `open`，開釜 GUI）、`stats [玩家]`、`reload`(admin)、`give <玩家> <dishId> [品質]`(admin)。
- **cooking** — 右鍵釜開 GUI（`cauldron.*` 設定）→ 投入食材 → 精確集合比對所有食譜 → 熟練度判定（`base-success-rate` + `success-per-proficiency`、`crit-*`）→ 成功/大成功(品質+1)/失敗（產出「失敗的料理」）；品質→buff 時長倍率；成品 PDC 寫入 `dish`+`quality`。
- **recipes** — `recipes.yml` 格式：每道 `result`(MMOItems type/id)、`buff`(effect/base-duration/stacks)、`recipes`（多組做法，ingredients 用 `TYPE ID amount` 或 `vanilla MAT amount`）；食材辨識＝MMOItems Type+ID。
- **config** — `config.yml` 逐區：`cauldron`、`gui`、`cooking`、`mmobuffs`、`compendium`、`storage`、`messages`。
- **permissions** — `cuisine.use`(true)、`cuisine.admin`(op)。
- **integration** — 三個反射橋接：`MMOItemsRef`（讀/產生物品）、`MMOBuffsBridge`（console 指令模板 `apply-command`）、`CompendiumBridge`（送 `compendium unlock <player> cuisine:<dishId>`）；**標註待實機驗證的 3 點**（MMOItemsRef 反射簽名、MMOBuffs 指令簽名、Compendium 解鎖 key 對應），來源 `CLAUDE.md`。

### 5.3 JuiyuAchievement（`juiyuachievement/`）

- **README** — 定位（成就 + 圖鑑系統，整合 5 大來源）、命名說明（部署名 `JuiyuAchievement`、內部 `JiuyuCodex`、資料夾 `plugins/JuiyuAchievement/`）、導覽表。
- **install** — 環境（softdepend：MythicMobs、MMOCore、MMOItems、BeautyQuests、MythicLib、PlaceholderAPI）、安裝、設定檔位置。
- **commands** — `/codex`（開 GUI：圖鑑/成就/稱號分頁）；管理（`codex.admin`）：`reload`、`give`、`points`、`reset`、`export`、`import`。
- **achievements** — 成就定義與 5 大觸發來源（原生＋MythicMobs 擊殺、取得物品原生＋MMOItems、MMOCore 主等級/副職業等級、BeautyQuests 任務階段/完成）；隱藏成就 `hidden`、系列 `requires`、每日/每週 `reset`(UTC 邊界)、完成 `broadcast`、完成音效。
- **codex** — 圖鑑（codex）條目（`codex/`）、圖鑑分類 100% 全收集獎勵（`config.yml` `codex-rewards.<cat>`，主控台執行）。
- **rewards** — 獎勵類型：點數 / 稱號（`titles.yml`）/ 可設定指令 / 通知；點數商店（`shop.yml`）；多排行榜（點數/完成度）；側邊欄記分板追蹤（最多 5）；內建聊天前綴稱號（`chat-prefix.enabled` 預設 false）。
- **config** — `config.yml`：`completion`(sound/broadcast-all)、`scoreboard`、`chat-prefix`、`codex-rewards`。
- **permissions** — `codex.use`(true)、`codex.admin`(op)。
- **placeholders** — PAPI id `juiyu`（`PapiHook.java`）：`%juiyu_title%`、`title_id`、`points`、`completed`、`total`、`completion`。

## 6. 風格與一致性規範

- 語言 zh-TW；標題與導覽用既有 emoji 風格。
- 指令表欄位固定：語法 / 範例 / 權限 / 行為。
- 站內連結用 `/<plugin>/<page>` 絕對路徑；sidebar 連結可省略 `.md`（同既有慣例）。
- README 結尾附「文件導覽」表格（連本區各頁），比照 twofactorauth/README.md。

## 7. 驗證方式（取代單元測試）

沿用既有計畫的兩段驗證：

1. **死連結/檔案存在檢查（自動）**：於 repo 根執行既有 PowerShell 片段（見 `docs/plans/2026-06-19-starryrealm-plugins-wiki.md`），排除 `docs/specs|plans`，確認**全站無 DEAD**。
2. **本機預覽（人工，選用）**：`npx docsify-cli serve .` 開 `http://localhost:3000`，確認三插件可從首頁/navbar 進入、各自 sidebar 正確、搜尋可命中（如「料理」「圖鑑」「成就」「釜」）。

## 8. Git 工作流

- 於現有分支 `docs/fishing-set-bonus` 進行（使用者指定）。
- 逐插件分組 commit（骨架 / 內容頁 / 接入導覽），commit 訊息結尾加：
  `Co-Authored-By: Claude Opus 4.8 <noreply@anthropic.com>`
- **不** push、**不**開 PR；完成並通過死連結檢查後回報，由使用者決定後續。

## 9. 完成定義

- 三個插件各有獨立資料夾、`_sidebar.md`、`README.md` 與全部主題分頁（共約 25 內容頁）。
- 總站 navbar / 根 sidebar / 首頁表格 / index.html 描述皆已收錄三插件，可從首頁進入。
- 每個指令皆有語法/範例/權限/行為；config 各鍵有說明。
- 「待驗證 / 未實作」內容據實標註，未寫成已完成。
- 全站死連結檢查無 DEAD；純前端、零建置、保留 `.nojekyll`。
