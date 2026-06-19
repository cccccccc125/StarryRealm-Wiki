# 指令

主指令 `/2fa`（別名 `/twofa`、`/2factor`）。僅玩家可執行。

| 指令 | 權限（預設） | 說明 |
|------|------|------|
| `/2fa enable` | `twofactorauth.2fa.enable`（op） | 為自己啟用 2FA（僅 OP，產生 QR 地圖） |
| `/2fa disable` | `twofactorauth.2fa.disable`（op） | 停用自己的 2FA |
| `/2fa status` | 無 | 查看自己的 2FA 狀態（啟用時間、最後驗證時間） |
| `/2fa reset <玩家>` | `twofactorauth.2fa.reset`（false） | 重置指定玩家的 2FA（管理用） |

## 範例

```
/2fa enable          # 開始綁定（OP）
/2fa status          # 2FA：已啟用 / 啟用時間 / 最後驗證
/2fa disable         # 停用
/2fa reset Steve     # 重置 Steve 的 2FA（需 reset 權限且自己已通過驗證）
```

- 提供 Tab 補全：`enable` / `disable` / `status`，`reset` 僅對有權限者顯示並可補全線上玩家名稱。
- `/2fa reset` 的執行者若仍被登入凍結（尚未驗證），會被拒絕，避免以 reset 繞過 2FA。

綁定與驗證的完整步驟見 [綁定與驗證流程](/twofactorauth/setup)。
