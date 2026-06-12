# 🏷️ 佔位符

## PlaceholderAPI 整合

SubCareers 註冊了 PlaceholderAPI 擴展（identifier：`subcareers`，PlaceholderAPI 為
softdepend，未安裝時僅略過註冊）。

排行榜資料每 **5 分鐘**（`config.yml` 的 `leaderboard.refresh-minutes` 可調）
非同步從 MySQL 刷新進記憶體快取，佔位符讀取零查詢；魚種紀錄榜直接讀全服紀錄
快取、即時反映。

## 排行榜佔位符

`<rank>` 為名次 1~10。查無資料時，`name` 回傳「—」、數值回傳「0」。

| 佔位符 | 說明 |
|------|------|
| `%subcareers_top_collect_<career>_<rank>_name%` | 圖鑑解鎖數榜——玩家名稱 |
| `%subcareers_top_collect_<career>_<rank>_value%` | 圖鑑解鎖數榜——解鎖數 |
| `%subcareers_top_caught_<rank>_name%` | 釣魚累計榜——玩家名稱 |
| `%subcareers_top_caught_<rank>_value%` | 釣魚累計榜——累計釣起數 |
| `%subcareers_top_level_<profession>_<rank>_name%` | 職業等級榜——玩家名稱 |
| `%subcareers_top_level_<profession>_<rank>_value%` | 職業等級榜——等級 |
| `%subcareers_record_<lootid>_name%` | 魚種全服紀錄——保持者名稱 |
| `%subcareers_record_<lootid>_weight%` | 魚種全服紀錄——重量（kg，兩位小數） |
| `%subcareers_record_<lootid>_length%` | 魚種全服紀錄——長度（cm，一位小數） |

`<lootid>` 為 `fishing/loot.yml` 中的魚種鍵名（可含底線）。

## 參數值

**`<career>`（圖鑑榜，6 種採集/製作職業）：**

| 值 | 對應職業 |
|------|------|
| `mining` | 採礦 |
| `logging` | 伐木 |
| `farming` | 耕作 |
| `fishing` | 釣魚 |
| `herb` | 草藥 |
| `alchemy` | 煉金 |

**`<profession>`（等級榜，8 種副職業，使用 MMOCore 職業鍵）：**

`mining` / `logging` / `farming` / `fishing` / `herbalism`（草藥）/ `alchemy` / `forging` / `enchanting`

> 注意：草藥在圖鑑榜是 `herb`、在等級榜是 `herbalism`（MMOCore 職業鍵）。

## 職業等級榜的資料來源

等級榜讀取快照表 `subcareers_profession_levels`：玩家**在線時**每個刷新週期、
以及**登出時**寫入。剛裝上本功能時榜單是空的，玩家上線過一輪後才會有資料。

## 使用範例

```
# 採礦等級榜第一名
%subcareers_top_level_mining_1_name% - Lv.%subcareers_top_level_mining_1_value%

# 釣魚圖鑑解鎖數榜第三名
%subcareers_top_collect_fishing_3_name%（%subcareers_top_collect_fishing_3_value% 種）

# 某魚種的全服最重紀錄
%subcareers_record_golden_carp_name%：%subcareers_record_golden_carp_weight% kg
```
