# 告警與 Discord

EconomyCore 對四類事件提供告警：**大額、高頻、失敗、退款 / 管理**。可同時輸出至 console 與 Discord。

## 大額交易（console + Discord）

成功交易金額**超過**門檻時觸發：

```yaml
alert-threshold:
  coin: 100000     # 銅錢 > 100,000
  gem: 1000        # 元寶 > 1,000
```

## 高頻交易偵測（velocity）

針對「**玩家主動發起**」的成功交易計數（排除系統發放、管理操作、退款），於時間窗內達上限即告警，並對同一玩家套用冷卻節流避免洗頻。

```yaml
velocity:
  enabled: true
  window-seconds: 60
  max-transactions: 10     # 60 秒內達 10 筆即告警
```

## Discord 通知（Webhook）

透過 Discord Webhook 即時通知（Java 內建 HttpClient，零額外相依）。未啟用時為零成本 no-op，不影響交易效能。

```yaml
discord:
  enabled: true
  webhook-url: "https://discord.com/api/webhooks/xxx/yyy"
  username: "EconomyCore"
  mention: "<@&身分組ID>"     # 選填，放在訊息最前面以觸發提及
  cooldown-seconds: 30        # 同類告警對同一玩家的最短間隔
  notify:
    large-amount: true        # 大額交易
    high-frequency: true      # 高頻交易（velocity）
    failed: true              # 失敗交易
    admin-and-refund: true    # 退款與管理員操作
```

設定步驟：頻道設定 → 整合 → Webhook 建立並複製網址貼到 `webhook-url`，將 `enabled` 設為 `true`，執行 `/ecoadmin reload` 即時生效。

## 外部 / 原生經濟監聽

CMI / EssentialsX 的原生 `/pay`、商店等會直接改其內部餘額、**不經過 Vault**，原本會繞過稽核。EconomyCore 以反射註冊 CMI 的 `CMIUserBalanceChangeEvent`（CMI 為 soft-dependency，未安裝自動略過），把這些外部銅錢異動補記為 `source=EXTERNAL` 的帳本紀錄並納入告警 / 高頻偵測。

EconomyCore 自身透過 Vault 造成的異動會以重入旗標標記略過，避免重複記錄。

```yaml
monitor-external-economy: true   # 開關，/ecoadmin reload 即時生效
```
