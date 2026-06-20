# 獎勵：點數/稱號/商店

成就系統提供多種獎勵機制，管理者可依需求自由組合。當玩家完成成就時，可獲得**點數**、**稱號**、**指令執行**與**通知**等獎勵。

## 獎勵類型

每個成就可在定義檔中設定以下四種獎勵，彼此可自由組合：

| 類型 | 成就欄位 | 說明 |
|------|----------|------|
| 點數 | `points` | 給予成就點數，可於點數商店兌換物品 |
| 稱號 | `title` | 解鎖 `titles.yml` 中定義的稱號，玩家可在 GUI 選擇佩戴 |
| 指令 | `commands` | 由主控台執行的指令清單（`%player%` 展開為玩家名稱） |
| 通知 | `broadcast` | 設為 `true` 時，完成瞬間全服廣播（另有全域開關 `completion.broadcast-all`） |

> 完成音效由 `config.yml` 的 `completion.sound` 統一控制，預設為 `UI_TOAST_CHALLENGE_COMPLETE`。詳見 [設定檔](/juiyuachievement/config)。

### 範例：綜合獎勵成就

```yaml
zombie_master:
  name: '&2殭屍宗師'
  trigger: KILL_MOB
  key: ZOMBIE
  amount: 200
  points: 20
  title: zombie_lord
  commands:
    - 'give %player% diamond 3'
  broadcast: true
```

完成後同時獲得 20 點成就點數、解鎖稱號 `zombie_lord`、獲得 3 顆鑽石，並全服廣播。

## 稱號系統

稱號定義在 `plugins/JuiyuAchievement/titles.yml`，格式為 `id: '顯示名稱'`（支援 `&` 色碼）。成就的 `title` 欄位對應此處的 ID。

### titles.yml 格式

```yaml
# 稱號定義  →  id: '顯示名稱(可用 & 顏色碼)'
# 由成就的 title: 欄位解鎖
<稱號ID>: '<顯示名稱>'
```

### 實際範例

```yaml
zombie_lord: '&2殭屍領主'
first_blood: '&c初試啼聲'
```

### 使用方式

- 玩家完成帶有 `title: zombie_lord` 的成就後，即可在 GUI 的稱號分頁選擇佩戴「<span style="color:green">殭屍領主</span>」。
- 每位玩家同時只能佩戴一個稱號（`activeTitle`），可隨時在 GUI 中切換。
- 佩戴中的稱號可透過 PlaceholderAPI `%juiyu_title%` 顯示在聊天或記分板中。詳見 [PlaceholderAPI](/juiyuachievement/placeholders)。

### 新增稱號

1. 在 `titles.yml` 中新增一行 `id: '顯示名稱'`。
2. 在成就定義中以 `title: <id>` 指向該稱號。
3. 執行 `/codex reload` 或重啟伺服器使稱號生效。

## 點數商店

玩家累積的成就點數可在點數商店中兌換物品。商店定義在 `plugins/JuiyuAchievement/shop.yml`。

### shop.yml 格式

```yaml
<商品ID>:
  name: '<顯示名稱>'      # 支援 & 色碼
  icon: <Material>         # GUI 圖示（Bukkit Material 名稱）
  cost: <點數>             # 花費的成就點數
  lore:                    # GUI 說明文字（選填）
    - '<說明>'
  commands:                # 購買時由主控台執行（%player% = 玩家名）
    - '<指令>'
```

### 實際範例

以下取自 `shop.yml` 的實際內容：

```yaml
diamond_pack:
  name: '&b鑽石包'
  icon: DIAMOND
  cost: 20
  lore:
    - '兌換 5 顆鑽石'
  commands:
    - 'give %player% diamond 5'

xp_bottle:
  name: '&a經驗瓶'
  icon: EXPERIENCE_BOTTLE
  cost: 10
  lore:
    - '兌換 8 瓶附魔之瓶'
  commands:
    - 'give %player% experience_bottle 8'
```

### 欄位說明

| 欄位 | 必填 | 說明 |
|------|------|------|
| `name` | 是 | 商品顯示名稱（支援 `&` 色碼） |
| `icon` | 是 | GUI 中顯示的圖示（Bukkit `Material` 名稱） |
| `cost` | 是 | 購買所需的成就點數 |
| `lore` | 否 | GUI 中商品的說明文字（字串清單） |
| `commands` | 是 | 購買時由主控台（Console）執行的指令清單，`%player%` 展開為玩家名稱 |

### 新增商品

1. 在 `shop.yml` 中新增一個商品區塊。
2. 設定 `name`、`icon`、`cost` 與 `commands`。
3. 執行 `/codex reload` 或重啟伺服器使商品生效。

> 商店在 GUI 中開啟，玩家的目前點數與商品花費一目瞭然。購買成功後扣除對應點數，購買失敗（點數不足）會收到提示。

## 多排行榜

插件提供兩種排行榜，可在 GUI 的進度總覽頁中查看：

| 排行榜 | 排序依據 | 說明 |
|--------|----------|------|
| 點數排行 | 成就點數 | 依玩家累積的成就點數高低排名 |
| 完成度排行 | 已完成成就數 | 依玩家完成的成就數量排名 |

排行榜資料來自各玩家的 `playerdata/<uuid>.yml`，其中的 `name` 欄位供排行榜顯示玩家名稱。

## 側邊欄記分板追蹤

玩家可在成就 GUI 中點擊成就進行**追蹤**，被追蹤的成就進度會顯示在畫面側邊的記分板上。

### 功能說明

- 每位玩家最多同時追蹤 **5** 個成就。
- 在 GUI 中點擊成就即可切換追蹤狀態。
- 側邊欄即時顯示各追蹤成就的名稱與當前進度。

### config.yml 設定

```yaml
scoreboard:
  enabled: true
  title: '&6&l成就追蹤'
```

| 欄位 | 預設值 | 說明 |
|------|--------|------|
| `scoreboard.enabled` | `true` | 是否啟用側邊欄記分板 |
| `scoreboard.title` | `&6&l成就追蹤` | 記分板標題（支援 `&` 色碼） |

> 詳見 [設定檔](/juiyuachievement/config)。

## 內建聊天前綴稱號

插件內建了一個簡易的聊天前綴功能，會在玩家發言時顯示其佩戴中的稱號。

### config.yml 設定

```yaml
chat-prefix:
  enabled: false
```

| 欄位 | 預設值 | 說明 |
|------|--------|------|
| `chat-prefix.enabled` | `false` | 是否啟用內建聊天前綴 |

> **建議**：若伺服器已使用其他聊天插件（如 EssentialsChat、LuckPerms prefix 等），建議維持 `enabled: false`，改用 PlaceholderAPI 的 `%juiyu_title%` 佔位符整合到現有聊天格式中，彈性更高。詳見 [PlaceholderAPI](/juiyuachievement/placeholders)。

## 相關章節

- [成就與觸發來源](/juiyuachievement/achievements) — 成就定義與觸發條件
- [圖鑑系統](/juiyuachievement/codex) — 圖鑑分類全收集獎勵
- [PlaceholderAPI](/juiyuachievement/placeholders) — 佔位符（含 `%juiyu_title%`）
- [設定檔](/juiyuachievement/config) — `config.yml` 完整設定
- [指令](/juiyuachievement/commands) — `/codex points` 等管理指令
