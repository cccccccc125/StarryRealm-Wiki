# 事件 API

TwoFactorAuth 在每次登入驗證後觸發事件，供其他插件連動（記錄、告警、獎勵等）。

## TwoFAVerificationEvent

`com.twofactorauth.event.TwoFAVerificationEvent`

| 項目 | 說明 |
|------|------|
| 類型 | 一般 `Event`（非 Cancellable） |
| 觸發時機 | 玩家完成一次登入驗證後 |
| `getPlayer()` | 觸發驗證的玩家 |
| `getResult()` | 結果列舉 `Result` |
| `isSuccess()` | 是否成功（等同 `getResult() == SUCCESS`） |

`Result` 列舉：

| 值 | 意義 |
|----|------|
| `SUCCESS` | 驗證成功 |
| `FAILURE` | 驗證碼錯誤 |
| `LOCKED` | 失敗次數達上限、帳號鎖定 |

## 監聽範例

```java
import com.twofactorauth.event.TwoFAVerificationEvent;
import org.bukkit.event.EventHandler;
import org.bukkit.event.Listener;

public class MyListener implements Listener {

    @EventHandler
    public void onVerify(TwoFAVerificationEvent event) {
        switch (event.getResult()) {
            case SUCCESS -> getLogger().info(event.getPlayer().getName() + " 通過 2FA 驗證");
            case FAILURE -> { /* 記錄可疑嘗試 */ }
            case LOCKED  -> { /* 通知管理員：帳號被鎖定 */ }
        }
    }
}
```
