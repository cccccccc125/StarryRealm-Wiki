# 拍賣行設定

拍賣行設定位於 `config.yml` 的 `auction:` 區段。改完執行 `/ecoadmin reload` 生效。

```yaml
auction:
  # 競拍最小加價幅度（固定值，銅錢）
  min-increment-default: 1.0

  # 賴帳（得標卻付不出款）記點門檻與禁拍
  strike-threshold: 3        # strike 視窗內達此次數即禁拍
  strike-window-days: 7      # strike 累計視窗（天）
  ban-duration-hours: 24     # 達門檻後禁拍時長（小時）

  settlement:
    scan-interval-seconds: 5 # 到期掃描間隔
    batch-size: 50           # 每輪結算處理上限

  listing:
    default-duration-hours: 48   # 上架預設時長
    max-duration-hours: 168      # 上架時長上限（7 天）
    max-active-per-player: 10    # 每人同時上架上限

  # MMOItems 品質(tier id)→ 排序權重；數字越大品質越高（「品質高到低」排序用）
  tier-weights:
    COMMON: 1
    UNCOMMON: 2
    RARE: 3
    EPIC: 4
    LEGENDARY: 5
    MYTHIC: 6

  fee:
    enabled: false           # v1 停用手續費
    listing-fee: 0           # 上架費（銅錢）
    sale-cut-percent: 0      # 成交抽成（百分比）
```

## 欄位說明

| 鍵 | 預設 | 說明 |
|----|------|------|
| `min-increment-default` | `1.0` | 競拍每次出價的最小加價幅度（銅錢） |
| `strike-threshold` | `3` | 賴帳記點達此次數即禁拍 |
| `strike-window-days` | `7` | 記點累計視窗（天） |
| `ban-duration-hours` | `24` | 禁拍時長（小時） |
| `settlement.scan-interval-seconds` | `5` | 到期掃描間隔（秒） |
| `settlement.batch-size` | `50` | 每輪結算處理上限 |
| `listing.default-duration-hours` | `48` | 上架預設時長 |
| `listing.max-duration-hours` | `168` | 上架時長上限（7 天） |
| `listing.max-active-per-player` | `10` | 每人同時上架上限 |
| `tier-weights.<tier>` | 見上 | MMOItems 品質的排序權重（數字越大越高，未列者為 0） |
| `fee.enabled` | `false` | 是否啟用手續費（v1 停用） |
| `fee.listing-fee` | `0` | 上架費（銅錢） |
| `fee.sale-cut-percent` | `0` | 成交抽成（百分比） |

> `tier-weights` 的 tier id 由伺服器的 MMOItems 設定決定，上面僅為範例；未列出的 tier 權重視為 0。
