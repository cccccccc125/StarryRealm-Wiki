# 📊 收集圖鑑系統

## 概述

每個副職業都有獨立的收集圖鑑，追蹤玩家解鎖的物品。搭配里程碑系統提供永久經驗加成。

## 圖鑑 GUI

從主選單 `/subcareers` 點選任一職業後，點選圖鑑按鈕（螢光墨囊）即可查看。

### 佈局

- 54 格翻頁式 GUI
- 已解鎖物品：顯示實際物品圖示
- 未解鎖物品：灰色染料 + CustomModelData 999
- 回到上一頁：Slot 53（或 49）

## 資料儲存

### MySQL (`subcareers_collections`)

| 欄位 | 類型 | 說明 |
|------|------|------|
| uuid | VARCHAR(36) | 玩家 UUID |
| career | VARCHAR(50) | 職業名稱 |
| item_id | VARCHAR(100) | 物品 ID |
| unlocked | TINYINT(1) | 是否解鎖 |
| best_record | DOUBLE | 最佳紀錄值 |

UNIQUE (uuid, career, item_id)

### YAML (`userdata/<uuid>.yml`)

```yaml
collection:
  mining:
    coal:
      unlocked: true
      best_record: 150.0
    iron:
      unlocked: true
      best_record: 80.0
    _fortune_:
      unlocked: true
      best_record: 10.0
    _milestone_:
      unlocked: true
      best_record: 1.0
```

## Meta Key

以 `_` 開頭的 item_id 為內部屬性，不會顯示在圖鑑中：

| Key | 職業 | 說明 |
|------|------|------|
| `_fortune_` | mining | 幸運屬性值 |
| `_speed_` | mining | 速度屬性值 |
| `_tier_` | alchemy | 煉藥爐階級 (0/1/2) |
| `_milestone_` | 全部 | 里程碑等級 |
| `_total_caught_` | fishing | 總釣魚次數 |

## 里程碑系統

### 計算方式

```
milestone = unlockedCount / 10（無條件捨去）
```

每解鎖 10 種物品，里程碑 +1。

### 效果

```
經驗加成 = 1 + milestone × 0.01
```

即每 10 個解鎖物品 = +1% 永久經驗加成，全職業通用。

### 里程碑通知

達到新的里程碑時發送聊天訊息通知玩家。

## 解鎖機制

| 職業 | 解鎖條件 |
|------|------|
| 採礦 | 破壞礦脈節點（以節點 Tier ID 記錄） |
| 伐木 | 砍伐原木（以 `Material.name().toLowerCase()` 記錄） |
| 耕作 | 收穫作物（以作物 Material 名稱記錄） |
| 釣魚 | 釣到魚（以 `loot_id` 記錄），含 weight/length 紀錄 |
| 草藥 | 完成採集（以 herb ID 記錄） |
| 煉金 | 首次成功煉製配方（以 recipe ID 記錄） |
| 鍛造 | 未使用（目前無鍛造圖鑑追蹤） |
| 刻印 | 未使用（刻印列表為靜態顯示） |

## 釣魚特殊紀錄

釣魚系統額外追蹤：

- **個人最佳** — 每種魚的最大重量/長度
- **伺服器紀錄** — 全服每種魚的歷史最佳重量/長度 + 保持者資訊
- 破個人紀錄：聊天通知 + 升級音效
- 破伺服器紀錄：全服廣播

### 釣魚紀錄資料表 (`subcareers_fishing_records`)

| 欄位 | 說明 |
|------|------|
| loot_id | 魚種 ID |
| max_weight | 伺服器最重紀錄 (kg) |
| max_length | 伺服器最長紀錄 (cm) |
| holder_name | 保持者名稱 |
| holder_uuid | 保持者 UUID |
