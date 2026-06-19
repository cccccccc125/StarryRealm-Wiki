# 圖鑑系統

圖鑑（Codex）是 JuiyuAchievement 的**收錄系統**——玩家在遊戲中與生物或物品互動時，系統自動將其記錄到個人圖鑑。圖鑑條目定義在 `plugins/JuiyuAchievement/codex/` 目錄下的 YAML 檔案中。

## 圖鑑與成就的差異

| | 圖鑑（Codex） | 成就（Achievement） |
|---|---|---|
| 性質 | **收錄**——記錄玩家見過/取得的事物 | **目標**——需達成特定條件才能完成 |
| 觸發方式 | 擊殺生物或取得物品時**自動發現** | 由 trigger + key + amount 定義的**進度條件** |
| 進度 | 無進度；發現即解鎖 | 有數量/等級進度，達標後完成 |
| 獎勵 | 分類全收集獎勵（見下方） | 每個成就各自設定的點數/稱號/指令 |
| 重複性 | 一次性（已發現不會重複觸發） | 可設定每日/每週重複 |
| 定義位置 | `codex/*.yml` | `achievements/*.yml` |

## 圖鑑條目定義

每筆圖鑑條目以**條目 ID** 作為 YAML 頂層鍵。欄位說明如下：

| 欄位 | 必填 | 類型 | 預設值 | 說明 |
|------|------|------|--------|------|
| `name` | 是 | 字串 | 條目 ID | 顯示名稱（支援 `&` 色碼） |
| `category` | 否 | 字串 | `一般` | 分類名稱（GUI 分頁顯示用，可自由命名） |
| `icon` | 否 | 字串 | `PAPER` | GUI 圖示（Bukkit `Material` 名稱） |
| `discover` | 是 | 字串 | `""` | 發現用的比對鍵（見下方格式說明） |
| `lore` | 否 | 字串清單 | `[]` | GUI 中顯示的條目描述 |

### discover 鍵格式

`discover` 欄位決定何種遊戲事件會觸發該條目的發現。格式為 `前綴:識別名`（全部自動轉為小寫比對）：

| 前綴 | 來源 | 識別名格式 | 範例 |
|------|------|-----------|------|
| `mob:` | 原生 Minecraft 生物擊殺 | `EntityType` 名稱（小寫） | `mob:zombie` |
| `mythic:` | MythicMobs 自定義生物擊殺 | MythicMobs 內部名稱（小寫） | `mythic:skeletalking` |
| `item:` | 原生 Minecraft 物品取得 | `Material` 名稱（小寫） | `item:diamond` |
| `mmoitem:` | MMOItems 物品取得 | `type.id`（小寫） | `mmoitem:sword.flame_blade` |

同時，任務推進也會觸發圖鑑發現——BeautyQuests 監聽器會以 `quest:<questId>` 格式呼叫發現。

## 完整範例

以下範例取自 `codex/mobs.yml` 與 `codex/items.yml`：

```yaml
# codex/mobs.yml — 怪物圖鑑
zombie:
  name: '&2殭屍'
  category: '原生怪物'
  icon: ZOMBIE_HEAD
  discover: 'mob:zombie'
  lore:
    - '夜晚常見的不死生物'

skeleton:
  name: '&f骷髏'
  category: '原生怪物'
  icon: SKELETON_SKULL
  discover: 'mob:skeleton'
  lore:
    - '擅長遠程射擊的不死生物'

spider:
  name: '&8蜘蛛'
  category: '原生怪物'
  icon: SPIDER_EYE
  discover: 'mob:spider'
  lore:
    - '能攀爬牆壁的節肢生物'
```

```yaml
# codex/items.yml — 物品圖鑑
diamond:
  name: '&b鑽石'
  category: '物品'
  icon: DIAMOND
  discover: 'item:diamond'
  lore:
    - '珍貴的寶石，可用於頂級裝備'

# MMOItems 物品範例 (discover 用 mmoitem:type.id 小寫)
# example_sword:
#   name: '&6範例之劍'
#   category: 'MMOItems'
#   icon: DIAMOND_SWORD
#   discover: 'mmoitem:sword.example'
```

## 分類全收集獎勵

當玩家集滿某個分類（`category`）的**所有**圖鑑條目後，系統會自動發放一次性獎勵。獎勵在 `config.yml` 的 `codex-rewards` 區段定義：

```yaml
codex-rewards:
  原生怪物:
    - 'give %player% diamond 5'
  物品:
    - 'give %player% emerald 10'
    - 'say %player% 集滿了物品圖鑑！'
```

### 設定說明

| 項目 | 說明 |
|------|------|
| 鍵名 | 分類名稱，需與圖鑑條目的 `category` 欄位**完全一致** |
| 值 | 字串清單，每條為一個**由主控台執行**的指令 |
| `%player%` | 自動展開為達成全收集的玩家名稱 |

- 獎勵為**一次性**——同一玩家對同一分類只會領取一次（以 `claimedRewards` 記錄）。
- 指令由主控台（Console）執行，因此可使用 `give`、`lp` 等需要權限的指令。
- 若 `codex-rewards` 下未定義某分類，則該分類集滿時不會發放獎勵。

### 全收集判定流程

1. 玩家發現一筆新圖鑑條目。
2. 系統檢查該條目所屬的 `category`，列出該分類下的所有條目。
3. 若玩家已發現該分類的所有條目，且尚未領取過該分類的獎勵，則執行 `codex-rewards.<category>` 中定義的所有指令。
4. 標記該分類獎勵為已領取，不會重複發放。

## 新增圖鑑條目

1. 在 `plugins/JuiyuAchievement/codex/` 下新增或編輯 `.yml` 檔案。
2. 以條目 ID 作為頂層鍵，填寫 `name`、`discover`、`category` 等欄位。
3. 若該分類需要全收集獎勵，在 `config.yml` 的 `codex-rewards` 下新增對應的分類獎勵。
4. 執行 `/codex reload` 或重啟伺服器使條目生效。

> 一個 YAML 檔案可包含多筆圖鑑條目，建議依分類拆分檔案（如 `mobs.yml`、`items.yml`）方便管理。

## 相關章節

- [成就與觸發來源](/juiyuachievement/achievements) — 成就系統與觸發條件
- [獎勵：點數/稱號/商店](/juiyuachievement/rewards) — 成就獎勵系統
- [設定檔](/juiyuachievement/config) — `config.yml` 完整設定
- [指令](/juiyuachievement/commands) — `/codex reload` 等管理指令
