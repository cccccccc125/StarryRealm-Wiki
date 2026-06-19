# 🗄️ 資料儲存系統

## 雙模式設計

SubCareers 支援兩種資料儲存模式，透過 `config.yml` 的 `storage-type` 切換。

| 模式 | 設定值 | 適用場景 |
|------|------|------|
| YAML | `yaml` | 小型伺服器、單機測試 |
| MySQL | `mysql` | 大型伺服器、多伺服器同步 |

## MySQL 模式

### 連線池

使用 HikariCP 管理 MySQL 連線，可設定參數：

```yaml
mysql:
  pool:
    maximum-pool-size: 10
    minimum-idle: 5
    max-lifetime: 1800000      # 30 分鐘
    connection-timeout: 30000   # 30 秒
    idle-timeout: 600000        # 10 分鐘
```

### 資料表結構

#### `subcareers_fishbag` — 魚袋

| 欄位 | 類型 | 約束 |
|------|------|------|
| uuid | VARCHAR(36) | PK |
| slot | INT | PK (0-26) |
| item_data | MEDIUMTEXT | Base64 ItemStack |

#### `subcareers_fishing_records` — 伺服器釣魚紀錄

| 欄位 | 類型 | 約束 |
|------|------|------|
| loot_id | VARCHAR(100) | PK |
| max_weight | DOUBLE | |
| max_length | DOUBLE | |
| holder_name | VARCHAR(16) | |
| holder_uuid | VARCHAR(36) | |
| updated_at | TIMESTAMP | |

## YAML 模式

### 玩家資料 (`userdata/<uuid>.yml`)

```yaml
fishbag:
  0: "<base64 item data>"
  1: "<base64 item data>"
```

### 伺服器紀錄 (`server_records.yml`)

```yaml
fishing:
  COMMON_CARP:
    max_weight: 5.2
    max_length: 62.0
    holder_name: "Player123"
    holder_uuid: "xxx-xxx-xxx"
```

## 非同步寫入

- 玩家資料採用非同步寫入，避免阻塞主執行緒
- 每 10 秒背景排程 flush 髒資料
- 玩家離線時立即 flush + 清除快取
- 伺服器關閉時全域 flush
