# SolarTermCore

為 Minecraft 伺服器加入**二十四節氣**循環的輕量插件。節氣每天推進、隨節氣切換世界天氣，並提供 PlaceholderAPI 佔位符與自訂事件供其他插件連動。

## 運作概念

- **二十四節氣**依序循環：

  | 季 | 節氣 |
  |----|------|
  | 🌱 春 | 立春、雨水、驚蟄、春分、清明、穀雨 |
  | ☀️ 夏 | 立夏、小滿、芒種、夏至、小暑、大暑 |
  | 🍂 秋 | 立秋、處暑、白露、秋分、寒露、霜降 |
  | ❄️ 冬 | 立冬、小雪、大雪、冬至、小寒、大寒 |

- **每過一個真實日推進一個節氣**（以 `Asia/Taipei` 時區判斷跨日），完整循環約 24 天。伺服器若離線數日，下次啟動會一次補進對應的節氣數。
- 切換節氣時會**全服廣播**目前節氣（依季節變色：春綠、夏紅、秋金、冬藍），並更新受管世界的天氣、觸發 `SolarTermChangeEvent`。
- 進度存於插件資料夾的 `data.yml`（不污染可編輯的 `config.yml`）。

## 文件導覽

| 章節 | 說明 |
|------|------|
| [🔌 安裝](/solartermcore/install) | 環境需求與安裝 |
| [💾 指令](/solartermcore/commands) | `/term` 用法 |
| [🌦️ 節氣與天氣](/solartermcore/weather) | 天氣權重與受管世界 |
| [🏷️ 佔位符](/solartermcore/placeholders) | PlaceholderAPI |
| [🗒️ 設定](/solartermcore/config) | `config.yml` |
| [📮 事件 API](/solartermcore/events) | `SolarTermChangeEvent` |
