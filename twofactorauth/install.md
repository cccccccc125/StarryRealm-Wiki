# 安裝

## 環境需求

| 項目 | 需求 |
|------|------|
| 伺服器 | Paper 1.21（`api-version: 1.21`，`load: POSTWORLD`） |
| 資料庫 | MySQL（儲存加密後的 TOTP 密鑰與狀態） |
| 驗證器 | 任一 TOTP App（Google / Microsoft Authenticator、Authy…） |

## 步驟

1. 將 `TwoFactorAuth` jar 放入 `plugins/`，啟動一次產生 `config.yml`。
2. 設定 MySQL 連線（`mysql` 區段）。
3. **務必修改加密通行碼** `twofactorauth.encryption.passphrase` 為一段強隨機字串（至少 32 字元）。
   - ⚠️ 變更通行碼會**使所有已存的密鑰失效**，需重新綁定。
4. 視需要調整 `totp.issuer`（顯示在驗證器 App 上的發行者名稱）與驗證參數。
5. 重啟伺服器。OP 玩家加入後即受 2FA 保護。

## MySQL 設定

```yaml
mysql:
  host: localhost
  port: 3306
  database: twofa
  username: root
  password: ""
  pool-size: 5
  table-name: "twofactorauth_2fa"
```

完整設定見 [設定](/twofactorauth/config)。綁定流程見 [綁定與驗證流程](/twofactorauth/setup)。
