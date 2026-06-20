# 權限

| 權限節點 | 預設 | 說明 |
|----------|------|------|
| `compendium.use` | true | 允許開啟圖鑑 GUI（`/compendium`） |
| `compendium.admin` | op | 允許使用管理指令（`reload`、`sync`、`unlock`、`lock`、`reset`、`stats`、`save`） |

- `compendium.use` 預設所有玩家皆持有；若需限制圖鑑存取，可在權限插件中將其設為 `false`。
- `compendium.admin` 預設僅 OP 持有，涵蓋所有管理子指令。

設定範例（LuckPerms）：

```
lp group admin permission set compendium.admin true
```

指令用法詳見 [指令](/jiuyucompendium/commands)。
