# 權限

| 權限節點 | 預設 | 說明 |
|----------|------|------|
| `cuisine.use` | true | 允許開啟釜烹飪 GUI（`/cuisine`、`/cuisine open`） |
| `cuisine.admin` | op | 允許使用管理指令（`reload`、`give`） |

- `cuisine.use` 預設所有玩家皆持有；若需限制釜的使用，可在權限插件中將其設為 `false`。
- `cuisine.admin` 預設僅 OP 持有，涵蓋所有管理子指令。

設定範例（LuckPerms）：

```
lp group admin permission set cuisine.admin true
```

指令用法詳見 [指令](/jiuyucuisine/commands)。
