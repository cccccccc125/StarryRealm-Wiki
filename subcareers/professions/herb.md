# 🌿 草藥 (Herb)

## 概述

草藥系統包含兩種模式：**普通草藥**（單人蹲下採集）和**大型草藥**（多人協作攻擊式採集）。搭配 BossBar 進度條、重生冷卻、以及節氣加成。

## 設定檔 (`herb/herb.yml`)

### 草藥位置定義格式

```yaml
herbs:
  ginseng:
    display-name: "&e人參"
    gather-seconds: 5.0
    respawn-seconds: "120-300"    # 隨機範圍
    min-level: 1
    exp-reward: 15.0
    drops:
      drop_1:
        item: mmoitems/MATERIAL/GINSENG
        amount: "1-2"
        chance: 100.0
        display-name: "&e人參"
      drop_2:
        item: minecraft/GOLDEN_APPLE
        amount: "1-1"
        chance: 5.0
        command: "give %player% golden_apple 1"

  ganoderma:
    display-name: "&5靈芝"
    gather-seconds: 8.0
    respawn-seconds: "180-600"
    min-level: 5
    exp-reward: 30.0
    drops:
      # ...
```

### 大型草藥定義格式

```yaml
large_herbs:
  ancient_tree:
    display-name: "&6千年神木"
    anchor-block: "OAK_LOG"       # 定位點方塊類型
    structure-blocks:              # 結構體所有方塊
      - "OAK_LOG"
      - "OAK_LEAVES"
    scan-height: 20                # 向上掃描高度
    gather-radius: 5               # 採集半徑
    max-hp: 500.0                  # 總血量
    base-damage: 5.0               # 基礎每秒傷害
    min-level: 10
    exp-reward: 100.0
    respawn-seconds: 3600          # 1 小時重生
    participation-drops:           # 所有參與者獲得
      drop_participation:
        item: mmoitems/MATERIAL/ANCIENT_WOOD
        amount: "1-1"
        chance: 100.0
    drops:                         # 貢獻度掉落（傷害佔比）
      drop_contribution:
        item: mmoitems/MATERIAL/ANCIENT_CORE
        amount: "1-1"
        chance: 100.0
```

## 普通草藥採集

### 觸發

玩家在草藥方塊上蹲下（`PlayerToggleSneakEvent`）。

### 機制

1. BossBar 顯示採集進度
2. 每秒增加進度（`1.0 / gather-seconds`）
3. 以下動作會中斷採集：
   - 放開蹲下
   - 移動
   - 受到傷害

### 完成獎勵

- 掉落物品（支援 `minecraft/`、`mmoitems/`、MONEY、EXP、MythicMobs 生怪）
- MMOCore 草藥經驗

### 重生

方塊在採集後消失，隨機 `respawn-seconds` 範圍後恢復。

## 大型草藥協作

### 觸發條件

1. 管理員在世界中放置結構體（由 `anchor-block` + `structure-blocks` 定義）
2. 玩家在 `gather-radius` 範圍內蹲下即可參與

### 傷害計算

```
每秒傷害 = baseDamage × (1 + level × 0.05)
```

- `level` = 玩家當前草藥等級（從 MMOCore 讀取）
- 總血量 `max-hp` 隨參與人數動態不變

### BossBar

顯示剩餘 HP，所有參與者共同觀看。

### 獎勵分配

| 類型 | 對象 | 說明 |
|------|------|------|
| 參與獎勵 | 所有參與者 | 只要參與就獲得 |
| 貢獻獎勵 | 按傷害佔比 | 傷害越高分越多 |

### 重生冷卻

儲存在 `herb/cooldowns.yml`，伺服器重啟後仍保留。

## 節氣加成

需安裝 SolarTermCore。在 `herb.yml` 中設定：

```yaml
term-multipliers:
  "立春": 1.5
  "春分": 1.3
  "驚蟄": 1.2
```

當節氣變化時（`SolarTermChangeEvent`），更新倍率。經驗公式會乘上當前節氣的倍率。

## 相關刻印

| 刻印 | 效果 |
|------|------|
| `herbalist` (藥感 Lv1-3) | 5/10/15% 經驗加成 |
