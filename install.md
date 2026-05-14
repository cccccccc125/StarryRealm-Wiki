# 🔌 安裝指南

## 環境需求

| 需求 | 版本 | 備註 |
|------|------|------|
| Paper | 1.21.1 | 使用 Spigot API，建議 Paper |
| Java | 21+ | 編譯目標 Java 21 |
| MMOCore | 1.13+ | 必要（softdepend） |
| MMOItems | 6.10+ | 必要（softdepend） |
| MythicLib | 1.7+ | 必要（softdepend） |
| MythicMobs | 5.7.2+ | 可選 — 魚商人 NPC、草藥生怪 |
| Vault | 1.7.4+ | 可選 — 經濟系統（賣魚） |
| SolarTermCore | 1.0+ | 可選 — 節氣加成系統 |
| PlaceholderAPI | 2.x+ | 可選 — 佔位符支援 |

## 安裝步驟

1. 將 `SubCareers.jar` 放入 `plugins/` 資料夾
2. 確保所有相依插件已安裝並正常運作
3. 重啟伺服器
4. 插件會自動在 `plugins/SubCareers/` 產生所有設定檔

## 建議安裝順序

```
1. Vault
2. MythicLib
3. MMOItems
4. MMOCore
5. MythicMobs
6. SolarTermCore
7. SubCareers
```

## 設定 MySQL（選用）

編輯 `plugins/SubCareers/config.yml`：

```yaml
storage-type: mysql

mysql:
  host: localhost
  port: 3306
  database: subcareers
  username: root
  password: your_password
  pool:
    maximum-pool-size: 10
    minimum-idle: 5
    max-lifetime: 1800000
    connection-timeout: 30000
    idle-timeout: 600000
```

若不使用 MySQL，設 `storage-type: yaml`，資料會存在 `plugins/SubCareers/userdata/`。

## 驗證安裝

執行 `/subcareers`，應看到 9 格副職業總覽 GUI。執行 `/subcareers help` 確認所有指令可用。
