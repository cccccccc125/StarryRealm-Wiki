# 節氣與天氣

切換節氣時，插件會為**受管世界**（`affected-worlds`）依該節氣的天氣權重隨機挑選一種天氣。

## 受管世界

- 只有列在 `affected-worlds` 的世界由本插件接管天氣。
- 受管世界的**自然天氣變化會被鎖定**（攔截原版的 `WeatherChange` / `ThunderChange`），只有插件在換節氣時才會改天氣——確保天氣與節氣一致。
- 此外，所有世界**禁用「睡覺跳過夜晚」**（攔截夜晚的 `TimeSkip`），讓時間照常流動。
- 不在清單中的世界維持原版天氣，不受影響。

## 天氣權重

天氣以權重隨機（總和為比例）挑選三種狀態之一：

| 狀態 | 效果 |
|------|------|
| `clear` | 晴天 |
| `rain` | 下雨 |
| `thunder` | 雷雨 |

每個節氣可在 `config.yml` 設定自己的權重；未設定的節氣套用 `default-weather`。

```yaml
default-weather:
  clear: 60
  rain: 30
  thunder: 10

solar-terms:
  穀雨:           # 穀雨多雨
    weather: { clear: 20, rain: 70, thunder: 10 }
  大暑:           # 大暑炎熱晴朗
    weather: { clear: 85, rain: 10, thunder: 5 }
  驚蟄:           # 驚蟄多雷
    weather: { clear: 40, rain: 20, thunder: 40 }
```

> 權重不必加總為 100，插件會以總和為分母換算機率。若某節氣權重總和為 0 會跳過天氣更新並於後台警告。

## 二十四節氣名稱

立春、雨水、驚蟄、春分、清明、穀雨、立夏、小滿、芒種、夏至、小暑、大暑、立秋、處暑、白露、秋分、寒露、霜降、立冬、小雪、大雪、冬至、小寒、大寒。

設定欄位詳見 [設定](/solartermcore/config)。
