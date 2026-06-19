# 開發者 API

EconomyCore 將 `CurrencyAPI` 註冊為 Bukkit 服務，供第三方插件存取雙幣別。

## 取得 API

```java
import com.economycore.api.CurrencyAPI;
import org.bukkit.plugin.RegisteredServiceProvider;

RegisteredServiceProvider<CurrencyAPI> rsp =
        Bukkit.getServicesManager().getRegistration(CurrencyAPI.class);
if (rsp == null) {
    // EconomyCore 未載入或服務尚未註冊
    return;
}
CurrencyAPI economy = rsp.getProvider();
```

> 在 `plugin.yml` 將 `EconomyCore` 設為 `depend` 或 `softdepend`，並編譯時引用 EconomyCore 的 jar（無需 Maven 倉庫）。

## 方法總覽

所有金額參數型別為 `double`；內部會正規化（銅錢四捨五入 2 位、**元寶須為正整數**，否則回傳 `INVALID_AMOUNT` 失敗）。

| 方法 | 用途 |
|------|------|
| `getBalance(UUID, CurrencyType)` | 查詢餘額（回傳 `BigDecimal`） |
| `deposit(UUID, double, CurrencyType, TransactionSource)` | 發放（系統 → 玩家） |
| `withdraw(UUID, double, CurrencyType, TransactionSource)` | 扣除（玩家 → 系統） |
| `transfer(UUID sender, UUID receiver, double, CurrencyType, TransactionSource)` | 轉帳（玩家 → 玩家） |
| `refund(UUID adminId, String txId, String senderIp)` | 退款指定交易 |
| `isCoinAvailable()` | 銅錢功能是否可用（Vault 是否掛載） |

`deposit / withdraw / transfer` 皆有可指定 `TransactionType`、`senderIp`、`note`、`operator`（稽核管理員名）的多載。

## 範例：發放元寶

```java
import com.economycore.model.*;

// ⚠ 內含 DB / Vault 存取，請在非同步執行緒呼叫
TransactionResult result = economy.deposit(
        uuid, 100, CurrencyType.GEM,
        TransactionType.REWARD, TransactionSource.QUEST, null, "任務獎勵");

if (result.success()) {
    BigDecimal balance = result.newReceiverBalance();  // 發放後餘額
} else {
    FailureReason reason = result.failureReason();      // 失敗原因
}
```

## 重要：執行緒模型

`CurrencyAPI` 的方法為**同步阻塞**呼叫（內部含 DB / Vault 存取）。請自行安排於**非同步執行緒**執行，並在需要時用 scheduler 切回主執行緒處理遊戲邏輯。每筆操作都會自動寫入[金流日誌](/economycore/ledger)。
