# 設定檔（config.yml）

本頁說明 `plugins/JiuyuCuisine/config.yml` 的所有設定區段。修改後可用 `/cuisine reload` 熱重載，不需重啟伺服器。

## 一般設定

| 鍵 | 預設值 | 說明 |
|----|--------|------|
| `language` | `zh_TW` | 插件語系 |
| `debug` | `false` | 啟用除錯日誌 |
| `autosave-ticks` | `6000` | 自動存檔間隔（tick；6000 tick = 5 分鐘） |

```yaml
language: zh_TW
debug: false
autosave-ticks: 6000
```

## cauldron — 釜設定

控制玩家右鍵哪種方塊會開啟烹飪 GUI。詳細烹飪流程見 [烹飪流程與熟練度](/jiuyucuisine/cooking)。

| 鍵 | 預設值 | 說明 |
|----|--------|------|
| `cauldron.block` | `CAULDRON` | 觸發烹飪 GUI 的方塊類型 |
| `cauldron.accept-any-cauldron` | `true` | 是否也接受 `WATER_CAULDRON`、`LAVA_CAULDRON` 等變體 |
| `cauldron.require-empty-hand` | `false` | 設為 `true` 時必須空手右鍵才會開啟（避免與打水等操作衝突） |

```yaml
cauldron:
  block: CAULDRON
  accept-any-cauldron: true
  require-empty-hand: false
```

## gui — 烹飪介面

| 鍵 | 預設值 | 說明 |
|----|--------|------|
| `gui.title` | `&8玖域釜` | GUI 標題（支援 `&` 色碼） |
| `gui.input-slots` | `9` | 食材格數量（格子編號 0 到 N-1） |
| `gui.cook-slot` | `26` | 「開始烹飪」按鈕所在的格子編號 |
| `gui.cook-button-material` | `BLAZE_POWDER` | 烹飪按鈕的顯示材質 |
| `gui.cook-button-name` | `&6▶ 開始烹飪` | 烹飪按鈕的顯示名稱 |

```yaml
gui:
  title: '&8玖域釜'
  input-slots: 9
  cook-slot: 26
  cook-button-material: BLAZE_POWDER
  cook-button-name: '&6▶ 開始烹飪'
```

## cooking — 烹飪判定

控制熟練度公式、失敗產出、品質時長倍率與音效。完整的烹飪流程說明見 [烹飪流程與熟練度](/jiuyucuisine/cooking)。

### 成功率與大成功

| 鍵 | 預設值 | 說明 |
|----|--------|------|
| `cooking.base-success-rate` | `0.70` | 熟練度 0 時的基礎成功率（70%） |
| `cooking.success-per-proficiency` | `0.01` | 每 1 點熟練度增加的成功率（+1%） |
| `cooking.max-success-rate` | `0.98` | 成功率上限（98%） |
| `cooking.crit-base-rate` | `0.05` | 大成功基礎機率（5%） |
| `cooking.crit-per-proficiency` | `0.004` | 每 1 點熟練度增加的大成功機率（+0.4%） |

```
成功率 = base-success-rate + 熟練度 × success-per-proficiency（上限 max-success-rate）
大成功率 = crit-base-rate + 熟練度 × crit-per-proficiency
```

### 失敗的料理

| 鍵 | 預設值 | 說明 |
|----|--------|------|
| `cooking.fail-dish.mmoitem-type` | `''`（空） | 失敗料理的 MMOItems 類型；留空則使用原版物品 |
| `cooking.fail-dish.mmoitem-id` | `''`（空） | 失敗料理的 MMOItems ID；留空則使用原版物品 |

兩者皆為空時，失敗產出為原版 `POISONOUS_POTATO`（顯示名稱「失敗的料理」）。

### 品質時長倍率

料理食用後觸發的 MMOBuffs 狀態時長 = buff 基礎時長 × 品質倍率。

| 鍵 | 預設值 | 說明 |
|----|--------|------|
| `cooking.quality-duration-multiplier.1` | `0.8` | 品質 1 星的時長倍率 |
| `cooking.quality-duration-multiplier.2` | `1.0` | 品質 2 星的時長倍率 |
| `cooking.quality-duration-multiplier.3` | `1.3` | 品質 3 星的時長倍率 |

### 音效

| 鍵 | 預設值 | 說明 |
|----|--------|------|
| `cooking.sound-success` | `ENTITY_PLAYER_BURP` | 成功時音效 |
| `cooking.sound-crit` | `ENTITY_PLAYER_LEVELUP` | 大成功時音效 |
| `cooking.sound-fail` | `BLOCK_FIRE_EXTINGUISH` | 失敗時音效 |

```yaml
cooking:
  base-success-rate: 0.70
  success-per-proficiency: 0.01
  max-success-rate: 0.98
  crit-base-rate: 0.05
  crit-per-proficiency: 0.004
  fail-dish:
    mmoitem-type: ''
    mmoitem-id: ''
  quality-duration-multiplier:
    1: 0.8
    2: 1.0
    3: 1.3
  sound-success: ENTITY_PLAYER_BURP
  sound-crit: ENTITY_PLAYER_LEVELUP
  sound-fail: BLOCK_FIRE_EXTINGUISH
```

## mmobuffs — MMOBuffs 連動

控制料理食用時套用 MMOBuffs 狀態效果的行為。詳見 [外掛連動](/jiuyucuisine/integration)。

| 鍵 | 預設值 | 說明 |
|----|--------|------|
| `mmobuffs.enabled` | `true` | 是否啟用 MMOBuffs 狀態效果 |
| `mmobuffs.apply-command` | `mmobuffs effect add {player} {effect} {duration} {stacks}` | 套用 buff 的指令模板；`{player}`、`{effect}`、`{duration}`、`{stacks}` 會被替換為實際值 |

```yaml
mmobuffs:
  enabled: true
  apply-command: 'mmobuffs effect add {player} {effect} {duration} {stacks}'
```

> **待驗證**：此指令模板需對 MMOBuffs 1.2.3 的實際指令簽名進行實機校正。詳見 [外掛連動 — 待驗證事項](/jiuyucuisine/integration)。

## compendium — 圖鑑連動

控制料理首次製作時是否通知 JiuyuCompendium 解鎖圖鑑條目。詳見 [外掛連動](/jiuyucuisine/integration)。

| 鍵 | 預設值 | 說明 |
|----|--------|------|
| `compendium.enabled` | `true` | 是否啟用圖鑑解鎖連動 |
| `compendium.discover-key-prefix` | `cuisine` | 解鎖 key 前綴；實際送出的 key 形如 `cuisine:<dishId>` |

```yaml
compendium:
  enabled: true
  discover-key-prefix: 'cuisine'
```

> **待驗證**：`cuisine:<dishId>` 在 JiuyuCompendium 不一定有對應的 entry。詳見 [外掛連動 — 待驗證事項](/jiuyucuisine/integration)。

## storage — 儲存後端

> **注意**：目前**僅實作 YAML** 儲存。config.yml 中的 `SQLITE` / `MYSQL` 選項為保留欄位，尚未實作（預計比照 JiuyuCompendium 的 JdbcProgressRepository 模式實作）。設定為 `SQLITE` 或 `MYSQL` 時，實際仍會使用 YAML。

| 鍵 | 預設值 | 說明 |
|----|--------|------|
| `storage.type` | `YAML` | 儲存後端類型（目前僅 `YAML` 可用） |
| `storage.table-prefix` | `jiuyu_cuisine_` | JDBC 模式下的資料表前綴（尚未實作） |
| `storage.sqlite.file` | `cuisine.db` | SQLite 資料庫檔名（尚未實作） |
| `storage.mysql.host` | `localhost` | MySQL 主機（尚未實作） |
| `storage.mysql.port` | `3306` | MySQL 連接埠（尚未實作） |
| `storage.mysql.database` | `minecraft` | MySQL 資料庫名稱（尚未實作） |
| `storage.mysql.username` | `root` | MySQL 帳號（尚未實作） |
| `storage.mysql.password` | `''` | MySQL 密碼（尚未實作） |
| `storage.mysql.jdbc-url` | `''` | 自訂 JDBC URL（尚未實作） |

```yaml
storage:
  type: YAML
  table-prefix: jiuyu_cuisine_
  sqlite:
    file: cuisine.db
  mysql:
    host: localhost
    port: 3306
    database: minecraft
    username: root
    password: ''
    jdbc-url: ''
```

## messages — 訊息

| 鍵 | 預設值 | 說明 |
|----|--------|------|
| `messages.no-recipe` | `&c這些食材煮不出東西…（沒有對應食譜）` | 食材組合未命中任何食譜 |
| `messages.success` | `&a烹飪成功！得到 &e%dish% &7(品質 %quality%★)` | 烹飪成功訊息（`%dish%` = 料理名稱、`%quality%` = 品質星級） |
| `messages.crit` | `&6大成功！火候完美 —— &e%dish% &7(品質 %quality%★)` | 大成功訊息 |
| `messages.fail` | `&c失敗了…變成一盤失敗的料理。` | 烹飪失敗訊息 |
| `messages.discovered` | `&b[料理] &f你領悟了新食譜：&e%dish%` | 首次製作解鎖圖鑑時的通知（`%dish%` = 料理名稱） |

```yaml
messages:
  no-recipe: '&c這些食材煮不出東西…（沒有對應食譜）'
  success: '&a烹飪成功！得到 &e%dish% &7(品質 %quality%★)'
  crit: '&6大成功！火候完美 —— &e%dish% &7(品質 %quality%★)'
  fail: '&c失敗了…變成一盤失敗的料理。'
  discovered: '&b[料理] &f你領悟了新食譜：&e%dish%'
```

## 熱重載

修改 `config.yml` 後，執行 `/cuisine reload` 即可重載所有設定與食譜資料，無需重啟伺服器。需要 `cuisine.admin` 權限。

## 相關章節

- [烹飪流程與熟練度](/jiuyucuisine/cooking) — 熟練度公式與品質判定的完整說明
- [食譜設定](/jiuyucuisine/recipes) — `recipes.yml` 食譜定義格式
- [外掛連動](/jiuyucuisine/integration) — MMOItems、MMOBuffs、JiuyuCompendium 連動機制與待驗證事項
- [指令](/jiuyucuisine/commands) — `/cuisine` 指令
- [權限](/jiuyucuisine/permissions) — 權限節點
