# 成就與觸發來源

成就系統讓管理者以 YAML 定義各種目標，玩家達成條件後自動獲得點數、稱號與自訂獎勵。成就定義檔位於 `plugins/JuiyuAchievement/achievements/`，每個檔案可包含多個成就，插件啟動時自動載入。

## 觸發來源

插件整合 5 大觸發來源，涵蓋原生與第三方外掛事件：

### 1. 擊殺生物 — 原生 + MythicMobs

| trigger 值 | 說明 | key 格式 |
|-------------|------|----------|
| `KILL_MOB` | 擊殺原生 Minecraft 生物 | `EntityType` 名稱（如 `ZOMBIE`），`*` = 任意 |
| `KILL_MYTHIC` | 擊殺 MythicMobs 自定義生物 | MythicMobs 內部名稱（如 `SkeletalKing`） |

- 原生生物：監聽 `EntityDeathEvent`，以 `EntityType` 名稱比對。
- MythicMobs：監聽 `MythicMobDeathEvent`，以 `getKiller()` + `getMobType().getInternalName()` 比對。

### 2. 取得物品 — 原生 + MMOItems

| trigger 值 | 說明 | key 格式 |
|-------------|------|----------|
| `OBTAIN_ITEM` | 取得原生 Minecraft 物品 | `Material` 名稱（如 `DIAMOND`） |
| `OBTAIN_MMOITEM` | 取得 MMOItems 物品 | `TYPE.ID` 格式（如 `SWORD.FLAME_BLADE`） |

- MMOItems 偵測透過 NBT 標籤 `MMOITEMS_ITEM_TYPE` + `MMOITEMS_ITEM_ID` 識別。

### 3. MMOCore 等級提升

| trigger 值 | 說明 | key 格式 |
|-------------|------|----------|
| `MMOCORE_LEVEL` | 主職業等級達到目標 | `*`（固定） |
| `MMOCORE_PROFESSION` | 副職業等級達到目標 | 副職業名稱（如 `mining`） |

- 監聽 `PlayerLevelUpEvent`，透過 `hasProfession()` 區分主職業與副職業。
- 等級型觸發採**取最高值**邏輯（非累加），當 `getNewLevel()` >= `amount` 時達成。

### 4. BeautyQuests 任務

| trigger 值 | 說明 | key 格式 |
|-------------|------|----------|
| `QUEST_STAGE` | 推進任務階段或完成任務 | `questId`（數字），`*` = 任意任務 |

- 監聽 `QuesterSetStageEvent`（階段推進）與 `QuesterQuestFinishEvent`（任務完成），兩者皆計為一次觸發。

### 觸發類型總覽

| trigger | 來源插件 | key | amount 意義 |
|---------|----------|-----|-------------|
| `KILL_MOB` | 原生 | EntityType 或 `*` | 擊殺數量 |
| `KILL_MYTHIC` | MythicMobs | 內部名稱 | 擊殺數量 |
| `OBTAIN_ITEM` | 原生 | Material 名稱 | 取得數量 |
| `OBTAIN_MMOITEM` | MMOItems | `TYPE.ID` | 取得數量 |
| `MMOCORE_LEVEL` | MMOCore | `*` | 目標等級 |
| `MMOCORE_PROFESSION` | MMOCore | 副職業名稱 | 目標等級 |
| `QUEST_STAGE` | BeautyQuests | questId 或 `*` | 推進次數 |

## 成就定義欄位

每個成就以**成就 ID** 作為 YAML 頂層鍵。以下為所有可用欄位：

| 欄位 | 必填 | 類型 | 預設值 | 說明 |
|------|------|------|--------|------|
| `name` | 是 | 字串 | 成就 ID | 顯示名稱（支援 `&` 色碼） |
| `lore` | 否 | 字串清單 | `[]` | GUI 中顯示的說明文字 |
| `icon` | 否 | 字串 | `PAPER` | GUI 圖示（Bukkit `Material` 名稱） |
| `category` | 否 | 字串 | `一般` | 分類名稱（GUI 分頁顯示用，可自由命名） |
| `trigger` | 是 | 字串 | — | 觸發類型（見上方觸發類型總覽） |
| `key` | 否 | 字串 | `*` | 觸發目標鍵（依 trigger 類型不同，格式不同） |
| `amount` | 否 | 整數 | `1` | 達成所需數量或目標等級（最小值 1） |
| `points` | 否 | 整數 | `0` | 完成時給予的成就點數 |
| `title` | 否 | 字串 | 無 | 完成時解鎖的稱號 ID（對應 `titles.yml`） |
| `commands` | 否 | 字串清單 | `[]` | 完成時由主控台執行的指令，`%player%` 展開為玩家名稱 |
| `hidden` | 否 | 布林 | `false` | 隱藏成就——達成前在 GUI 中顯示為 `???` |
| `requires` | 否 | 字串 | 無 | 前置成就 ID；該成就完成後本成就才開放（用於系列成就） |
| `reset` | 否 | 字串 | `none` | 重置週期：`daily`（每日 00:00 UTC 重置）或 `weekly`（每週一 00:00 UTC 重置） |
| `broadcast` | 否 | 布林 | `false` | 完成時是否全服廣播（適合 Boss 首殺、稀有成就） |

> **完成音效**由 `config.yml` 的 `completion.sound` 統一控制，預設為 `UI_TOAST_CHALLENGE_COMPLETE`。個別成就無法單獨設定音效；若需全域關閉廣播，可設定 `completion.broadcast-all: false`。
>
> 詳見 [設定檔](/juiyuachievement/config)。

### 系列成就

透過 `requires` 欄位可串連多個成就為系列。前置成就未完成時，後續成就不會推進進度。搭配 `hidden: true` 可讓系列後續成就在解鎖前完全隱藏。

### 每日 / 每週重複成就

設定 `reset: daily` 或 `reset: weekly` 後，成就的進度與完成狀態會在 UTC 日/週邊界自動清除，玩家可重複完成並再次獲得獎勵。

## 完整範例

以下範例取自 `achievements/example3.yml`，展示每日重置、系列前置、隱藏成就與全服廣播等進階功能：

```yaml
# 每日重複成就：每日 00:00 UTC 重置進度
daily_hunt:
  name: '&b每日狩獵'
  lore:
    - '每日擊殺 20 隻怪物 (每日 00:00 UTC 重置)'
  icon: IRON_AXE
  category: '每日'
  trigger: KILL_MOB
  key: '*'
  amount: 20
  points: 5
  reset: daily

# 系列成就：需先完成 zombie_slayer，且達成前隱藏顯示
zombie_master:
  name: '&2殭屍宗師'
  lore:
    - '完成殭屍剋星後，再擊殺 200 隻殭屍'
  icon: ZOMBIE_HEAD
  category: '狩獵'
  trigger: KILL_MOB
  key: ZOMBIE
  amount: 200
  points: 20
  requires: zombie_slayer
  hidden: true
  title: zombie_lord

# Boss 首殺廣播 (MythicMobs)
boss_first:
  name: '&c討伐者'
  lore:
    - '擊殺 Boss：SkeletalKing'
  icon: WITHER_SKELETON_SKULL
  category: '挑戰'
  trigger: KILL_MYTHIC
  key: SkeletalKing
  amount: 1
  points: 30
  broadcast: true
```

### 範例解析

**每日狩獵**（`daily_hunt`）：
- 觸發類型 `KILL_MOB`，`key: '*'` 代表任意怪物。
- `amount: 20` 需擊殺 20 隻，`reset: daily` 使進度每日 00:00 UTC 歸零。
- 每次達成可重複獲得 5 點成就點數。

**殭屍宗師**（`zombie_master`）：
- `requires: zombie_slayer` 需先完成「殭屍剋星」成就。
- `hidden: true` 使其在前置成就完成前於 GUI 顯示為 `???`。
- 完成後解鎖稱號 `zombie_lord`（對應 `titles.yml`）。

**討伐者**（`boss_first`）：
- 觸發類型 `KILL_MYTHIC`，`key: SkeletalKing` 指定 MythicMobs 的 Boss 內部名稱。
- `broadcast: true` 使完成時全服廣播。

## 新增成就

1. 在 `plugins/JuiyuAchievement/achievements/` 下新增或編輯 `.yml` 檔案。
2. 以成就 ID 作為頂層鍵，填寫 `trigger`、`key`、`amount` 等必要欄位。
3. 執行 `/codex reload` 或重啟伺服器使成就生效。

> 一個 YAML 檔案可包含多個成就定義，可依分類拆分多個檔案（如 `hunt.yml`、`collection.yml`）方便管理。

## 相關章節

- [圖鑑系統](/juiyuachievement/codex) — 圖鑑條目與全收集獎勵
- [獎勵：點數/稱號/商店](/juiyuachievement/rewards) — 獎勵系統詳細說明
- [設定檔](/juiyuachievement/config) — `config.yml` 完整設定
- [指令](/juiyuachievement/commands) — `/codex give`、`/codex reload` 等管理指令
