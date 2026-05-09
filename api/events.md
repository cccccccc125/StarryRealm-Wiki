# 📮 事件

## 自訂事件

SubCareers 目前使用 Bukkit 內建事件進行監聽，尚無自訂事件類別。

若需要為其他插件提供擴展點，可在以下流程中加入自訂事件：

## 主要監聽事件對照表

| Bukkit 事件 | 監聽器 | 用途 |
|------|------|------|
| `PlayerFishEvent` | `FishingEventListener` | 釣魚客製化掉落 |
| `PlayerToggleSneakEvent` | `HerbSystem` | 草藥採集觸發 |
| `PlayerInteractEvent` | `AlchemyFurnaceListener` | 煉藥爐互動 |
| `PlayerInteractEvent` | `ForgingListener` | 鍛造站/鐵砧/鍋釜互動 |
| `BlockBreakEvent` | `MiningListener` | 礦脈節點破壞 |
| `BlockBreakEvent` | `LoggingListener` | 伐木破壞 |
| `BlockBreakEvent` | `FarmingListener` | 作物破壞 |
| `BlockBreakEvent` | `ForgingListener` | 保護鍛造站 |
| `BlockBreakEvent` | `AlchemyFurnaceListener` | 保護煉藥爐 |
| `BlockPlaceEvent` | `AlchemyFurnaceListener` | 保護煉藥爐區域 |
| `BlockPlaceEvent` | `IslandManager` | 空島保護 |
| `PrepareAnvilEvent` | `EnchantingManager` | 附魔捲軸應用 |
| `InventoryClickEvent` | 多個 GUI | GUI 互動處理 |
| `InventoryCloseEvent` | 多個 GUI | GUI 關閉儲存 |
| `EntityDamageByEntityEvent` | `CombatListener` | 吸血附魔 |
| `PlayerJoinEvent` | `SubCareers` | 非同步載入資料 + 遷移 |
| `PlayerJoinEvent` | `IslandManager` | 確保空島世界已載入 |
| `PlayerQuitEvent` | `SubCareers` | 非同步儲存 + 清除快取 |
| `PlayerQuitEvent` | `ForgingListener` | 歸還材料 |
| `PlayerQuitEvent` | `HerbSystem` | 清理採集中狀態 |
| `PlayerDeathEvent` | `ForgingListener` | 結束鍛造（不退還材料） |
| `PlayerMoveEvent` | `HerbSystem` | 中斷草藥採集 |
| `EntityDamageEvent` | `HerbSystem` | 中斷草藥採集 |
| `MythicMobInteractEvent` | `FishMerchantListener` | 魚商人 NPC 互動 |
| `SolarTermChangeEvent` | `FishingLootManager` | 節氣變化通知 |
