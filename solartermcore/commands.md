# 指令

主指令 `/term`。

| 指令 | 權限（預設） | 說明 |
|------|------|------|
| `/term` | 無 | 顯示目前節氣 |
| `/term next` | `solarterm.admin`（op） | 強制切換到下一個節氣 |
| `/term set <節氣>` | `solarterm.admin` | 強制設定為指定節氣 |
| `/term reload` | `solarterm.admin` | 重新載入 `config.yml` 並重讀進度 |

- 任何人都能用 `/term` 查目前節氣；管理子指令需 `solarterm.admin`。
- 提供 Tab 補全：子指令與節氣名稱（僅對有權限者顯示）。

## 範例

```
/term                 # 目前節氣為：穀雨
/term next            # 切到下一個節氣（雨水→驚蟄…）
/term set 大暑        # 直接設為大暑
/term reload          # 重載設定
```

> `/term next` 與 `/term set` 會立即觸發換節氣的效果：全服廣播、更新受管世界天氣、觸發 [`SolarTermChangeEvent`](/solartermcore/events)。可用節氣名稱見 [節氣與天氣](/solartermcore/weather)。
