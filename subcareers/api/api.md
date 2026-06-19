# 🔧 Plugin API

## 取得主插件實例

```java
import me.cc.subcareers.SubCareers;

SubCareers plugin = (SubCareers) Bukkit.getPluginManager().getPlugin("SubCareers");
```

## 核心存取器

```java
public class SubCareers extends JavaPlugin {

    // === 設定管理 ===
    public ConfigManager getConfigManager()

    // === 資料層 ===
    public DatabaseManager getDatabaseManager()
    public PlayerDataManager getPlayerDataManager()
    public FishingDataManager getFishingDataManager()
    public RecordManager getRecordManager()

    // === 各系統 ===
    public Fishing getFishingSystem()
    public NodeManager getNodeManager()
    public ForgingManager getForgingManager()
    public EnchantingManager getEnchantingManager()
    public HerbSystem getHerbSystem()

    // === 經濟 ===
    public Economy getEconomy()    // Vault
}
```

## PlayerDataManager — 玩家收集資料

```java
public class PlayerDataManager {

    // 取得單一收集資料
    public CareerCollectionData getCollectionData(UUID uuid, String career, String itemId)

    // 取得職業所有收集
    public Map<String, CareerCollectionData> getCareerCollection(UUID uuid, String career)

    // 解鎖物品
    public void unlockItem(UUID uuid, String career, String itemId)

    // 鎖定物品（煉金 forget 用）
    public void lockItem(UUID uuid, String career, String itemId)

    // 更新最佳紀錄（只在更大時更新）
    public void updateBestRecord(UUID uuid, String career, String itemId, double value)

    // 強制寫入值（屬性用）
    public void setMetaValue(UUID uuid, String career, String itemId, double value)

    // 計算已解鎖數量（排除 _ 開頭的 meta key）
    public int countUnlocked(UUID uuid, String career)

    // 取得里程碑等級（每 10 個 +1）
    public int getMilestoneLevel(UUID uuid, String career)

    // 計數器（如 total_caught）
    public double getCounter(UUID uuid, String career, String counterId)
    public void incrementCounter(UUID uuid, String career, String counterId)
}
```

## CareerCollectionData

```java
public class CareerCollectionData {
    public UUID getUuid()
    public String getCareer()
    public String getItemId()
    public boolean isUnlocked()
    public void setUnlocked(boolean unlocked)
    public double getBestRecord()
    public void updateBestRecord(double value)  // 只在更大時更新
}
```

## ForgingManager — 鍛造

```java
public class ForgingManager {
    public boolean isRegisteredForge(Location loc)
    public void registerForge(Location loc)
    public void unregisterForge(Location loc)
    public boolean isForgeHammer(ItemStack item)
    public ForgingSession getSession(UUID uuid)
    public void addSession(UUID uuid, ForgingSession session)
    public void removeSession(UUID uuid)
}
```

## EnchantingManager — 附魔

```java
public class EnchantingManager {

    // 建立附魔捲軸
    public ItemStack createScroll(String enchantId, int level)

    // 檢查工具是否有附魔
    public boolean hasEnchant(ItemStack tool, String enchantId)

    // 取得附魔等級
    public int getEnchantLevel(ItemStack tool, String enchantId)

    // 取得效果數值
    public double getEnchantChance(ItemStack tool, String enchantId)

    // 查詢
    public List<String> getEnchantIds()
    public int getMaxLevel(String enchantId)
}
```

## FishbagManager — 魚袋

```java
public class FishbagManager {
    public void openFishbag(Player player)
    public boolean addFishToBag(UUID uuid, ItemStack fish)
    public ItemStack[] getBagContentsFromFile(UUID uuid)
    public void saveBagContents(UUID uuid, ItemStack[] contents)
}
```

## 使用範例

### 檢查玩家是否解鎖某礦物

```java
SubCareers plugin = (SubCareers) Bukkit.getPluginManager().getPlugin("SubCareers");
PlayerDataManager pdm = plugin.getPlayerDataManager();

CareerCollectionData data = pdm.getCollectionData(player.getUniqueId(), "mining", "diamond_node");
if (data != null && data.isUnlocked()) {
    // 玩家已解鎖鑽石
}
```

### 給予附魔捲軸

```java
EnchantingManager em = plugin.getEnchantingManager();
ItemStack scroll = em.createScroll("vein_sense", 2);
player.getInventory().addItem(scroll);
```

### 檢查釣竿的釣魚力量

```java
// 讀取 PDC
NamespacedKey key = new NamespacedKey(plugin, "fishing_power");
ItemStack rod = player.getInventory().getItemInMainHand();
Integer power = rod.getItemMeta().getPersistentDataContainer().get(key, PersistentDataType.INTEGER);
```

## PDC 命名空間鍵

SubCareers 使用 `NamespacedKey(plugin, ...)` 儲存以下 PDC 標籤：

| Key | 類型 | 用途 |
|------|------|------|
| `fishing_power` | INTEGER | 釣竿力量值 |
| `fishing_line_data` | STRING (Base64) | 釣線資料 |
| `fishing_hook_data` | STRING (Base64) | 釣鉤資料 |
| `fish_weight` | DOUBLE | 魚的重量 |
| `fish_length` | DOUBLE | 魚的長度 |
| `fish_base_price` | DOUBLE | 魚的基礎售價 |
| `fish_tier` | INTEGER | 魚的 Tier |
| `fish_loot_id` | STRING | 魚的 loot ID |
| `enchant_scroll_id` | STRING | 附魔捲軸 ID |
| `enchant_scroll_level` | INTEGER | 附魔捲軸等級 |
| `enchant_<id>` | INTEGER | 已附魔工具的附魔等級 |
| `forge_quality` | STRING | 鍛造品質星級 |
| `forge_score` | DOUBLE | 鍛造評分 |
