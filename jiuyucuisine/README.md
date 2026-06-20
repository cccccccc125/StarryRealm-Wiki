# JiuyuCuisine

玖域伺服器的**料理系統**。玩家右鍵「釜」開啟烹飪 GUI，把副職業產物（MMOItems）投入鍋中，系統比對食譜後產出料理；食用料理觸發 **MMOBuffs** 狀態效果。

## 運作概念

```
玩家右鍵「釜」方塊
   │ 開啟烹飪 GUI
   │ 將 MMOItems 副職業產物放入食材格
   │ 按下「開始烹飪」
   ▼
系統以食材的 MMOItems Type+ID 比對所有食譜
   ├─ 命中食譜 → 熟練度判定
   │    ├─ 成功 → 產出料理（品質依熟練度）
   │    └─ 大成功 → 產出更高品質料理
   ├─ 未命中 → 失敗，產出「失敗的料理」
   └─ 首次製作 → 解鎖 JiuyuCompendium 圖鑑（items.food）
                 │
                 ▼
        食用料理 → 觸發 MMOBuffs 狀態（時長依品質倍率）
```

## 核心設計

- **一料理多食譜 + 自由探索鍋**：同一道料理可以有多組食譜做法；鍋內食材會比對所有食譜，沒有命中則失敗。
- **失敗不退還**：烹飪失敗時食材不會歸還，改為產出「失敗的料理」物品。
- **無等級，只有熟練度**：沒有料理等級門檻，純粹依據該料理的製作次數（熟練度）影響成功率與大成功機率。
- **食材辨識 = MMOItems Type+ID**：食材來自副職業產物，以原生 NBT 上的 `MMOITEMS_ITEM_TYPE` / `MMOITEMS_ITEM_ID` 識別。
- **食用觸發 MMOBuffs**：料理成品帶有 PDC 標記（`dish` + `quality`），食用時依品質與食譜定義套用 MMOBuffs 狀態效果，時長隨品質星級倍率調整。
- **首次製作解鎖圖鑑**：首次成功製作某道料理時，透過 `CompendiumBridge` 通知 JiuyuCompendium 解鎖 `items.food` 條目。

## 文件導覽

| 章節 | 說明 |
|------|------|
| [🔌 安裝](/jiuyucuisine/install) | 環境需求與安裝步驟 |
| [💾 指令](/jiuyucuisine/commands) | `/cuisine` 指令 |
| [🍳 烹飪流程與熟練度](/jiuyucuisine/cooking) | 烹飪 GUI、熟練度、成功率與大成功 |
| [📖 食譜設定](/jiuyucuisine/recipes) | `recipes.yml` 食譜定義 |
| [🗒️ 設定檔](/jiuyucuisine/config) | `config.yml` 設定項 |
| [🧑‍⚖️ 權限](/jiuyucuisine/permissions) | 權限節點 |
| [🔌 外掛連動](/jiuyucuisine/integration) | MMOItems、MMOBuffs、JiuyuCompendium 連動 |
