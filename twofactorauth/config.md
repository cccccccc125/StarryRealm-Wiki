# 設定（config.yml）

```yaml
twofactorauth:
  enabled: true

  encryption:
    # AES-256-GCM。變更通行碼會使所有已存密鑰失效。
    passphrase: "change-me-to-a-strong-random-string-at-least-32-chars"

  totp:
    issuer: "Minecraft Server"   # 顯示在驗證器 App 上的發行者名稱
    window: 1                    # 允許的時間窗偏移（容許時鐘些微誤差）

  verification:
    timeout-seconds: 300         # 登入驗證逾時
    max-attempts: 3              # 連續失敗上限
    lock-duration-seconds: 300   # 達上限後鎖定時長
    secure-input: false          # 改用鐵砧視窗輸入登入碼（避免出現在聊天 / proxy）

  map-display:
    auto-remove-after-seconds: 120  # QR 地圖未完成綁定時自動移除的逾時

  messages:
    # 各情境訊息（支援 & / § 色碼），如 enable-success、frozen、verify-failed…
    ...

mysql:
  host: localhost
  port: 3306
  database: twofa
  username: root
  password: ""
  pool-size: 5
  table-name: "twofactorauth_2fa"
```

## 重點欄位

| 鍵 | 預設 | 說明 |
|----|------|------|
| `twofactorauth.enabled` | `true` | 插件總開關 |
| `encryption.passphrase` | （請修改） | 密鑰加密通行碼（AES-256-GCM，至少 32 字元）；變更會使既有密鑰失效 |
| `totp.issuer` | `"Minecraft Server"` | 驗證器顯示的發行者 |
| `totp.window` | `1` | 容許的時間窗偏移 |
| `verification.timeout-seconds` | `300` | 登入驗證逾時（秒） |
| `verification.max-attempts` | `3` | 連續失敗上限 |
| `verification.lock-duration-seconds` | `300` | 鎖定時長（秒） |
| `verification.secure-input` | `false` | 登入碼改用鐵砧視窗輸入（enable/disable 仍用聊天） |
| `map-display.auto-remove-after-seconds` | `120` | QR 地圖逾時自動移除 |
| `mysql.*` | 見上 | 資料庫連線與資料表名稱 |

> `secure-input` 啟用前請先在伺服器測試。`messages` 區段可自訂所有提示文字（成功、失敗、凍結、逾時、鎖定等）。
