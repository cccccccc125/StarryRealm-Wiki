# SubCareers Wiki

SubCareers 是一個 Minecraft RPG 副職業系統插件，基於 MMOCore + MMOItems + MythicLib + MythicMobs 建構。

提供 **9 種副職業**（伐木、耕作、釣魚、採礦、草藥、煉金、鍛造、刻印），搭配自訂 GUI、收集圖鑑、里程碑系統、MySQL/YAML 雙重資料儲存，以及獨立的農場空島系統與強化/升星/礦物融合等進階系統。

## 快速導覽

| 章節 | 說明 |
|------|------|
| [🔌 安裝指南](install.md) | 環境需求、安裝步驟、相依插件設定 |
| [💾 指令](general/commands.md) | 所有指令與用法 |
| [🧑‍⚖️ 權限](general/permissions.md) | 權限節點清單 |
| [🏷️ 佔位符](general/placeholders.md) | PlaceholderAPI 佔位符 |
| [🗒️ 設定檔](general/config.md) | 各設定檔結構與說明 |

## 副職業

| 副職業 | 說明 |
|------|------|
| [⛏️ 採礦](professions/mining.md) | 礦脈節點、連鎖挖掘、幸運/速度屬性 |
| [🪓 伐木](professions/logging.md) | 樹木判定、連鎖砍伐、原木收集 |
| [🌾 耕作](professions/farming.md) | 作物收穫、自動補種、鋤頭耐久 |
| [🎣 釣魚](professions/fishing.md) | 釣魚力量、魚袋、魚商、分解桶 |
| [🌿 草藥](professions/herb.md) | 蹲下採集、大型草藥協作、BossBar |
| [⚗️ 煉金](professions/alchemy.md) | 三段式煉藥爐、QTE 小遊戲、配方圖鑑 |
| [🔨 鍛造](professions/forging.md) | 加熱/敲打/淬火三階段、品質評分 |
| [✨ 刻印](professions/enchanting.md) | 11 種自訂刻印、鐵砧應用、PDC 儲存 |

## 系統

| 系統 | 說明 |
|------|------|
| [🏝️ 空島](systems/island.md) | 個人耕作空島、邀請訪客 |
| [📊 收集圖鑑](systems/collections.md) | 圖鑑解鎖、里程碑加成 |
| [🗄️ 資料儲存](systems/database.md) | MySQL / YAML 雙模式、HikariCP 連線池 |
| [🪵 品質素材](systems/quality-materials.md) | 伐木/採礦產出品質素材、鍛造數值加成 |
| [⭐ 強化系統](systems/enhancement.md) | 裝備強化 +0~+20、升星 0~5 星 |
| [🪨 礦物融合](systems/ore-fusion.md) | 礦物融合配方 GUI |

## API

| 章節 | 說明 |
|------|------|
| [📦 Maven](api/maven.md) | 依賴設定（開發者） |
| [🔧 Plugin API](api/api.md) | Java API 使用方法 |
| [📮 事件](api/events.md) | 可監聽的自訂事件 |
