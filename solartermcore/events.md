# 事件 API

SolarTermCore 在每次節氣切換時觸發自訂事件，供其他插件連動（例如副職業、活動、獎勵）。

## SolarTermChangeEvent

`me.cc.solarTermCore.SolarTermChangeEvent`

| 項目 | 說明 |
|------|------|
| 類型 | 一般 `Event`（非 Cancellable） |
| 觸發時機 | 每次節氣改變（每日推進、`/term next`、`/term set`） |
| `getNewTermName()` | 取得切換後的節氣名稱（String） |

## 監聽範例

```java
import me.cc.solarTermCore.SolarTermChangeEvent;
import org.bukkit.event.EventHandler;
import org.bukkit.event.Listener;

public class MyListener implements Listener {

    @EventHandler
    public void onTermChange(SolarTermChangeEvent event) {
        String term = event.getNewTermName();
        // 例如：節氣變「冬至」時發放獎勵、調整生怪等
        getLogger().info("節氣已切換為：" + term);
    }
}
```

> 此事件在主執行緒同步觸發；廣播與天氣更新已由插件處理，監聽端只需專注自己的連動邏輯。
