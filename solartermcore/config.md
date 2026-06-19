# 設定（config.yml）

```yaml
# 受節氣天氣影響的世界清單（請填正確的世界名稱）
affected-worlds:
  - "world"
  - "activity"

# 未特別設定的節氣套用此預設天氣權重
default-weather:
  clear: 60
  rain: 30
  thunder: 10

# 個別節氣的天氣權重覆寫（可只列要客製的節氣）
solar-terms:
  穀雨:
    weather:
      clear: 20
      rain: 70
      thunder: 10
  大暑:
    weather:
      clear: 85
      rain: 10
      thunder: 5
  驚蟄:
    weather:
      clear: 40
      rain: 20
      thunder: 40
```

## 欄位說明

| 鍵 | 說明 |
|----|------|
| `affected-worlds` | 由本插件接管天氣的世界名稱清單；清單外世界不受影響 |
| `default-weather.{clear,rain,thunder}` | 預設天氣權重（未在 `solar-terms` 覆寫的節氣使用） |
| `solar-terms.<節氣名>.weather.{clear,rain,thunder}` | 指定節氣的天氣權重覆寫 |

改完執行 `/term reload` 生效。

> 節氣進度（目前節氣、上次日期）存於 `data.yml`，**不在** `config.yml`，以免 reload 時被覆寫。天氣機制詳見 [節氣與天氣](/solartermcore/weather)。
