# 🏷️ 佔位符

## PlaceholderAPI 整合

SubCareers 註冊了 PlaceholderAPI 擴展（若有安裝 PlaceholderAPI）。

## 可用佔位符

| 佔位符 | 回傳值 | 說明 |
|------|------|------|
| `%subcareers_milestone_<career>%` | 數字 | 指定職業的里程碑等級 |
| `%subcareers_unlocked_<career>%` | 數字 | 指定職業已解鎖的物品數 |
| `%subcareers_collection_<career>_<item>%` | true/false | 指定物品是否已解鎖 |
| `%subcareers_record_<career>_<item>%` | 數字 | 指定物品的最佳紀錄值 |
| `%subcareers_fortune%` | 數字 | 採礦幸運值 |
| `%subcareers_speed%` | 數字 | 採礦速度值 |
| `%subcareers_tier_alchemy%` | bottom/middle/top | 煉藥爐階級 |
| `%subcareers_fishing_power%` | 數字 | 手持釣竿的釣魚力量 |
| `%subcareers_fishbag_count%` | 數字 | 魚袋中的魚數量 |
| `%subcareers_total_caught%` | 數字 | 總釣魚次數 |

## career 參數值

| 值 | 對應職業 |
|------|------|
| `logging` | 伐木 |
| `farming` | 耕作 |
| `fishing` | 釣魚 |
| `mining` | 採礦 |
| `herb` | 草藥 |
| `alchemy` | 煉金 |
| `forging` | 鍛造 |
| `enchanting` | 刻印 |

## 使用範例

```
# 顯示玩家採礦里程碑
%subcareers_milestone_mining%

# 檢查是否解鎖鑽石
%subcareers_collection_mining_diamond%

# 顯示釣竿力量
%subcareers_fishing_power%
```
