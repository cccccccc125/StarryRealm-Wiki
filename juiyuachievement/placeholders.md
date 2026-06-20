# PlaceholderAPI 佔位符

JuiyuAchievement 透過 [PlaceholderAPI](https://www.spigotmc.org/resources/placeholderapi.6245/) 提供佔位符，可在聊天插件、記分板、TAB 列表等任何支援 PAPI 的位置顯示成就相關資訊。

## 基本資訊

| 項目 | 值 |
|------|-----|
| 擴充 ID | `juiyu` |
| 前綴 | `%juiyu_` |
| 需求 | 安裝 PlaceholderAPI |

## 佔位符一覽

| 佔位符 | 回傳值 | 說明 |
|--------|--------|------|
| `%juiyu_title%` | 字串 | 玩家目前佩戴的稱號**顯示名稱**（含色碼）；未佩戴時回傳空字串 |
| `%juiyu_title_id%` | 字串 | 玩家目前佩戴的稱號 **ID**（對應 `titles.yml` 的鍵名）；未佩戴時回傳空字串 |
| `%juiyu_points%` | 整數 | 玩家目前持有的成就點數 |
| `%juiyu_completed%` | 整數 | 玩家已完成的成就數量 |
| `%juiyu_total%` | 整數 | 伺服器成就總數（所有已載入的成就定義） |
| `%juiyu_completion%` | 整數（0–100） | 玩家的成就完成度百分比（四捨五入至整數） |

## 詳細說明

### %juiyu_title%

回傳玩家佩戴中稱號的**顯示名稱**（即 `titles.yml` 中定義的值，含 `&` 色碼轉換後的結果）。

- 若玩家未佩戴任何稱號，回傳空字串。
- 適合直接嵌入聊天格式，作為玩家暱稱前綴。

**使用情境**：在聊天插件的格式中加入 `%juiyu_title%`，即可讓玩家名稱前顯示其成就稱號。

### %juiyu_title_id%

回傳玩家佩戴中稱號的 **ID**（即 `titles.yml` 中的鍵名，如 `zombie_lord`）。

- 若玩家未佩戴任何稱號，回傳空字串。
- 適合用於條件判斷（如搭配其他 PAPI 條件插件）。

### %juiyu_points%

回傳玩家目前持有的成就點數總額。

- 點數會因購買商店物品而減少。
- 適合顯示在記分板或 TAB 列表中。

### %juiyu_completed%

回傳玩家已完成的成就總數。搭配 `%juiyu_total%` 可顯示進度。

### %juiyu_total%

回傳伺服器目前載入的成就總數。此為全域值，所有玩家查詢結果相同。

### %juiyu_completion%

回傳玩家的成就完成度百分比，計算方式為 `已完成數 ÷ 成就總數 × 100`，四捨五入至整數（範圍 0–100）。

- 若伺服器無任何成就定義（總數為 0），回傳 `0`。

## 使用範例

```
# 聊天格式（搭配聊天插件）
%juiyu_title% %player_name%: %message%

# 記分板
成就點數: %juiyu_points%
進度: %juiyu_completed%/%juiyu_total% (%juiyu_completion%%)

# 條件判斷（搭配 PAPI 條件插件）
%juiyu_title_id%    → 可判斷玩家是否佩戴特定稱號
```

## 相關章節

- [獎勵：點數/稱號/商店](/juiyuachievement/rewards) — 稱號與點數系統
- [設定檔](/juiyuachievement/config) — `chat-prefix` 內建前綴設定
- [成就與觸發來源](/juiyuachievement/achievements) — 成就定義與觸發條件
