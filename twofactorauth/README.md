# TwoFactorAuth

為 **OP 帳號**加上 **TOTP 二階段驗證**的安全插件。OP 玩家加入時會被凍結，必須輸入驗證器 App 的 6 位數驗證碼才能行動，防止 OP 帳號被盜用後直接操作伺服器。

## 安全模型

- **保護對象**：OP 玩家。非 OP 不受影響（若非 OP 卻仍標記啟用，加入時會自動取消其 2FA 狀態）。
- **標準 TOTP**：相容 Google Authenticator、Microsoft Authenticator、Authy 等。
- **密鑰加密儲存**：TOTP 密鑰以 **AES-256-GCM** 加密後存入 MySQL。
- **Fail-closed**：若加入時無法讀取 2FA 狀態（資料庫異常），直接拒絕該 OP 進入，不放行。
- **鎖定持久化**：失敗次數與鎖定狀態存於資料庫、跨連線保留，無法靠重連清除。

## 運作概觀

```
OP 加入伺服器
   │ 被凍結（無法移動 / 互動），隱藏加入訊息
   ├─ 尚未綁定 → 解凍並提示 /2fa enable 設定
   └─ 已綁定   → 維持凍結，提示輸入 6 位數驗證碼
                 │ 於聊天框（或鐵砧安全輸入）輸入驗證碼
                 ▼
            驗證成功 → 解凍，正常遊玩
            連續失敗達上限 → 暫時鎖定
```

## 文件導覽

| 章節 | 說明 |
|------|------|
| [🔌 安裝](/twofactorauth/install) | 環境與 MySQL 設定 |
| [🔑 綁定與驗證流程](/twofactorauth/setup) | 如何設定與登入驗證 |
| [💾 指令](/twofactorauth/commands) | `/2fa` 指令 |
| [🧑‍⚖️ 權限](/twofactorauth/permissions) | 權限節點 |
| [🗒️ 設定](/twofactorauth/config) | `config.yml` |
| [📮 事件 API](/twofactorauth/events) | `TwoFAVerificationEvent` |
