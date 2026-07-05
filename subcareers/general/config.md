# 🗒️ 設定檔

## 目錄結構

所有設定檔位於 `plugins/SubCareers/`：

```
plugins/SubCareers/
├── config.yml                  # 主設定（MySQL / 魚商人）
├── pharmaceutical/
│   └── pharmaceutical.yml      # 製藥爐 + 配方（含每份費用 cost）
├── enhancement.yml             # 強化機率/費用 + 升星/精煉費用 + 消耗品
├── quality-materials.yml       # 品質素材定義
├── enchanting/
│   └── enchanting.yml          # 附魔定義
├── farming/
│   └── farming.yml             # 作物經驗 / 稀有度 / 掉落
├── fishing/
│   ├── settings.yml            # 釣魚主設定
│   ├── tiers.yml               # 力量 Tier 門檻
│   ├── loot.yml                # 魚種掉落表
│   └── regions.yml             # 釣魚區域
├── forging/
│   ├── forging.yml             # 鍛造設定 + 配方（含每次費用 cost）
│   ├── stat-scaling.yml        # 強化/升星/精煉倍率
│   └── forges.yml              # 已綁定鍛爐（自動管理）
├── herb/
│   ├── herb.yml                # 草藥定義
│   └── cooldowns.yml           # 大型草藥冷卻（自動管理）
├── logging/
│   └── logging.yml             # 樹種經驗 / 掉落
├── mining/
│   ├── mining.yml              # 礦脈 Tier 設定
│   └── nodes.yml               # 節點位置（自動管理）
├── islands.yml                 # 空島對照表（自動管理）
├── userdata/
│   └── <uuid>.yml              # YAML 模式玩家資料
└── server_records.yml          # YAML 模式伺服器紀錄
```

## 主設定 (`config.yml`)

```yaml
# 儲存模式：sqlite（預設，免架 DB）或 mysql
storage-type: sqlite

mysql:
  host: localhost
  port: 3306
  database: subcareers
  username: root
  password: password
  pool:
    maximum-pool-size: 10
    minimum-idle: 5
    max-lifetime: 1800000
    connection-timeout: 30000
    idle-timeout: 600000

# 魚商人設定
fish-merchant:
  npc-name: "FishMerchant"
```

## 重新載入

修改設定檔後執行 `/subcareers reload` 即可套用，不需重啟伺服器。

部分檔案（如 `pharmaceutical.yml`、`herb.yml`、`forging.yml`）有獨立的 reload 子指令，詳見 [💾 指令](/subcareers/general/commands.md)。

## 物品格式

所有設定檔中的物品均使用以下兩種格式：

### 原版物品
```yaml
item: minecraft/DIAMOND
amount: "1-3"       # 可用範圍 "min-max"
chance: 50.0        # 機率百分比
```

### MMOItems
```yaml
item: mmoitems/SWORD/IRON_BLADE
amount: "1-1"
chance: 100.0
```

格式為 `mmoitems/<TYPE>/<ID>`，其中 TYPE 對應 MMOItems 的物品類型（如 SWORD、MATERIAL、CONSUMABLE、TOOL 等）。

## 特殊獎勵類型

部分設定（如草藥）支援非物品獎勵：

| 關鍵字 | 說明 |
|------|------|
| `MONEY` | 給予金錢（需 Vault） |
| `EXP` | 給予職業經驗 |
| `command` 欄位 | 執行指令（如 `give %player% apple 1`） |
