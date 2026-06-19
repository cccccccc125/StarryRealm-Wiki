# 權限

| 權限節點 | 預設 | 說明 |
|----------|------|------|
| `twofactorauth.2fa.enable` | op | 允許啟用 2FA（`/2fa enable`） |
| `twofactorauth.2fa.disable` | op | 允許停用 2FA（`/2fa disable`） |
| `twofactorauth.2fa.reset` | false | 允許重置**他人**的 2FA（`/2fa reset <玩家>`） |

- `/2fa status` 不需權限。
- `/2fa enable` 除了權限外，也要求執行者本身是 **OP**。
- `twofactorauth.2fa.reset` 預設關閉，請只授予可信任的管理群組；持有者仍須先完成自己的 2FA 驗證才能執行 reset。

設定範例（LuckPerms）：

```
lp group admin permission set twofactorauth.2fa.reset true
```
