# 🏷️ 佔位符

## PlaceholderAPI 整合

SubCareers 註冊了 PlaceholderAPI 擴展（若有安裝 PlaceholderAPI）。

## 可用佔位符

| 佔位符 | 回傳值 | 說明 |
|------|------|------|
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
# 顯示釣竿力量
%subcareers_fishing_power%

# 顯示總釣魚次數
%subcareers_total_caught%
```
