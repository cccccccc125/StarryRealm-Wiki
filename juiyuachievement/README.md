# JuiyuAchievement

**成就 + 圖鑑系統**，整合 5 大內容來源的觸發事件，為玩家提供點數、稱號與排行榜等獎勵機制。

## 命名說明

| 項目 | 名稱 |
|------|------|
| 部署插件名 | `JuiyuAchievement`（plugin.yml `name`） |
| 內部 package / 主類 | `com.jiuyu.codex` / `JiuyuCodex` |
| 資料夾 | `plugins/JuiyuAchievement/` |

`Juiyu` 是作者最初的拼法；內部程式碼使用 `Jiuyu` / `JiuyuCodex`，但兩者指的是**同一個插件**。文件中統一以部署名 **JuiyuAchievement** 稱呼。

## 觸發來源

插件監聽以下 5 類事件作為成就與圖鑑的觸發來源：

1. 原生生物擊殺 + **MythicMobs** 自定義生物擊殺
2. 取得物品（原生 + **MMOItems**）
3. **MMOCore** 主等級 / 副職業等級提升
4. **BeautyQuests** 任務階段 / 完成

## 功能概覽

- **成就引擎** — 支援隱藏成就、系列成就（前置解鎖）、每日/每週可重複成就
- **獎勵系統** — 點數、稱號、可設定指令、完成通知與全服廣播
- **GUI 介面** — 圖鑑 / 成就 / 稱號三大分頁，圖鑑與成就各有分類頁籤
- **點數商店** — 用成就點數兌換獎勵（`shop.yml`）
- **排行榜** — 點數排行與完成度排行
- **記分板追蹤** — 側邊欄 Scoreboard 顯示追蹤中的成就進度（最多 5 項）
- **聊天前綴稱號** — 可選的內建稱號前綴（預設關閉，亦可透過 PAPI 整合其他聊天插件）
- **管理指令** — `/codex reload|give|points|reset|export|import`

> **注意：** 遊戲內 GUI 成就編輯器尚未實作，目前所有成就與圖鑑內容皆透過 YAML 設定檔管理。

## 文件導覽

| 章節 | 說明 |
|------|------|
| [🔌 安裝](/juiyuachievement/install) | 環境需求與安裝步驟 |
| [💾 指令](/juiyuachievement/commands) | `/codex` 指令與別名 |
| [🏆 成就與觸發來源](/juiyuachievement/achievements) | 5 類觸發、隱藏/系列/可重複成就 |
| [📖 圖鑑系統](/juiyuachievement/codex) | 圖鑑條目與分類收集獎勵 |
| [🎁 獎勵：點數/稱號/商店](/juiyuachievement/rewards) | 點數、稱號、商店與廣播 |
| [🗒️ 設定檔](/juiyuachievement/config) | `config.yml` 設定項 |
| [🧑‍⚖️ 權限](/juiyuachievement/permissions) | 權限節點 |
| [🏷️ PlaceholderAPI](/juiyuachievement/placeholders) | PAPI 變數（id `juiyu`） |
