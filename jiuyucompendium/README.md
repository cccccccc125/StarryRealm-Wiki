# JiuyuCompendium

**互動即解鎖**的收集圖鑑（圖譜）插件。玩家在遊戲中與內容互動就自動解鎖圖鑑條目，無需任務或指令。

## 運作概念

```
玩家在遊戲中互動
   ├─ 擊殺 MythicMobs 自定義生物 → 自動解鎖該生物條目
   └─ 取得 MMOItems 物品（撿取／背包掃描）→ 自動解鎖該物品條目
                 │
                 ▼
        已解鎖 → GUI 顯示名稱與圖示
        未解鎖 → GUI 顯示 ???
```

- 擊殺 MythicMobs 自定義生物 → 自動解鎖該生物條目。
- 取得 MMOItems 物品（撿取／背包掃描）→ 自動解鎖該物品條目。
- 未解鎖的條目在 GUI 中顯示為 `???`。

## 規模

圖鑑共有 **5 大類**：

| 大類 | 說明 |
|------|------|
| `mobs` | 自定義生物 |
| `professions` | 採礦、伐木、鍊藥、附魔、鍛造、耕作、釣魚 |
| `appearance` | 坐騎、寵物、時裝 |
| `multiplayer` | 公會、結義、俠緣、師徒 |
| `items` | 武器、防具、飾品、藥品、道具、料理 |

實機載入驗證紀錄：**5 categories, 672 entries**。

## 成就連動

解鎖圖鑑條目時，插件會將原始 discover key 鏡像送至成就插件 **JuiyuAchievement**（透過 `AchievementBridge` 反射呼叫），同時提供 PlaceholderAPI 變數（PAPI id `jiuyucompendium`）。

## 文件導覽

| 章節 | 說明 |
|------|------|
| [🔌 安裝](/jiuyucompendium/install) | 環境需求與安裝步驟 |
| [💾 指令](/jiuyucompendium/commands) | `/compendium` 指令 |
| [🗂️ 分類與條目](/jiuyucompendium/categories) | 5 大類樹與條目結構 |
| [🔓 解鎖機制](/jiuyucompendium/discovery) | 自動解鎖、手動解鎖與外部橋接 |
| [🗒️ 設定檔](/jiuyucompendium/config) | `config.yml` 設定項 |
| [🧑‍⚖️ 權限](/jiuyucompendium/permissions) | 權限節點 |
| [🔧 事件與 API](/jiuyucompendium/api) | `CompendiumDiscoverEvent` 與外部整合 |
