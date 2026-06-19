# 事件與 API

JiuyuCompendium 提供 Bukkit 自訂事件、PlaceholderAPI 佔位符以及成就橋接，供其他插件與圖鑑系統整合。

## CompendiumDiscoverEvent

`com.jiuyu.compendium.api.CompendiumDiscoverEvent`

當圖鑑條目被解鎖時觸發。此事件為一般 `Event`，**不可取消**（未實作 `Cancellable`）。

| 項目 | 說明 |
|------|------|
| 類型 | `Event`（非 `Cancellable`） |
| 觸發時機 | 玩家解鎖圖鑑條目時（含自動解鎖與手動解鎖） |
| `getPlayer()` | 解鎖該條目的玩家（`Player`） |
| `getEntry()` | 被解鎖的圖鑑條目（`CompendiumEntry`） |
| `getCause()` | 解鎖原因（`DiscoveryCause` 列舉） |
| `isNewlyDiscovered()` | 該條目是否為首次解鎖（`boolean`；若玩家先前已解鎖同一條目則為 `false`） |

### DiscoveryCause 列舉

| 值 | 意義 |
|----|------|
| `COMMAND` | 透過管理指令 `/compendium unlock` 解鎖 |
| `INVENTORY_SCAN` | 登入或背包點擊時掃描到 MMOItems 物品 |
| `ITEM_PICKUP` | 玩家撿取 MMOItems 物品 |
| `ITEM_CLICK` | 玩家點擊背包中的 MMOItems 物品 |
| `MYTHIC_MOB_KILL` | 擊殺 MythicMobs 自定義生物 |
| `VANILLA_MOB_KILL` | 擊殺原生 Minecraft 生物 |
| `CONFIG_SYNC` | 執行 `/compendium sync` 同步時觸發 |

### 監聽範例

```java
import com.jiuyu.compendium.api.CompendiumDiscoverEvent;
import org.bukkit.event.EventHandler;
import org.bukkit.event.Listener;

public class MyListener implements Listener {

    @EventHandler
    public void onDiscover(CompendiumDiscoverEvent event) {
        if (!event.isNewlyDiscovered()) return;

        String playerName = event.getPlayer().getName();
        String entryId = event.getEntry().id();
        String cause = event.getCause().name();

        getLogger().info(playerName + " 解鎖了 " + entryId + "（原因：" + cause + "）");
    }
}
```

> 在 `plugin.yml` 中將 `JiuyuCompendium` 設為 `depend` 或 `softdepend`，並編譯時引用 JiuyuCompendium 的 jar。

## PlaceholderAPI 佔位符

PAPI 擴充 ID 為 `jiuyucompendium`，由 `PapiHook` 提供。需安裝 [PlaceholderAPI](https://www.spigotmc.org/resources/placeholderapi.6245/)。

### 全域佔位符

| 佔位符 | 回傳值 |
|--------|--------|
| `%jiuyucompendium_total%` | 圖鑑條目總數 |
| `%jiuyucompendium_unlocked%` | 該玩家已解鎖的條目數 |
| `%jiuyucompendium_percent%` | 該玩家的解鎖百分比 |

### 條目查詢

| 佔位符 | 回傳值 |
|--------|--------|
| `%jiuyucompendium_entry_<entryId>%` | 該玩家是否已解鎖指定條目（`true` / `false`） |

### 分類查詢

| 佔位符 | 回傳值 |
|--------|--------|
| `%jiuyucompendium_category_<categoryId>_percent%` | 該玩家在指定分類的解鎖百分比 |
| `%jiuyucompendium_category_<categoryId>_unlocked%` | 該玩家在指定分類已解鎖的條目數 |

> `<categoryId>` 為分類 ID，例如 `mobs`、`items`。分類清單見 [分類與條目](/jiuyucompendium/categories)。

### 使用範例

```
# 顯示在記分板
%jiuyucompendium_unlocked% / %jiuyucompendium_total% (%jiuyucompendium_percent%)

# 查詢某條目是否解鎖
%jiuyucompendium_entry_mythic_boss01%

# 查詢 mobs 分類進度
%jiuyucompendium_category_mobs_percent%
```

## 成就橋接（Achievement Bridge）

`AchievementBridge` 在圖鑑條目解鎖時，透過 Java 反射呼叫成就插件的 API，將解鎖事件鏡像同步到成就系統。

### 運作方式

1. 解鎖條目時，`AchievementBridge.mirrorDiscovery(player, entry)` 被呼叫。
2. 依 `achievement-bridge.plugin-names` 清單依序尋找已啟用的成就插件。
3. 以反射呼叫成就插件的 `content().discover(player, key)` 方法。
4. 根據設定決定送出哪些 key：
   - **`mirror-discover-key: true`**（預設）— 送出條目的原始 discover key（如 `mythic:boss_id`、`mmoitem:sword.id`）。
   - **`mirror-compendium-key: true`** — 額外送出 `compendium:<entryId>` 格式的 key。僅在成就插件的設定有使用此格式時才需啟用。

### 設定

成就橋接由 `config.yml` 的 `achievement-bridge` 區段控制，詳見 [設定檔](/jiuyucompendium/config)。

### 注意事項

- 橋接以反射實作，不直接依賴成就插件的 jar，因此成就插件不在線時不會造成錯誤。
- 若目標插件沒有相容的 `content().discover(Player, String)` 方法，橋接會靜默跳過並在除錯模式下記錄日誌。
- 反射呼叫失敗時會以 warning 等級記錄到伺服器日誌。

## 相關章節

- [設定檔](/jiuyucompendium/config) — `achievement-bridge` 與 `discovery` 設定
- [解鎖機制](/jiuyucompendium/discovery) — 條目如何被解鎖
- [分類與條目](/jiuyucompendium/categories) — 分類 ID 對照表
