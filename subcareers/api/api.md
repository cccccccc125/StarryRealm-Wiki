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
