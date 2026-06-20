# 權限

| 權限節點 | 預設 | 說明 |
|----------|------|------|
| `codex.use` | true | 允許開啟主介面 GUI（`/codex`） |
| `codex.admin` | op | 允許使用管理指令（`reload`、`give`、`points`、`reset`、`export`、`import`） |

- `codex.use` 預設所有玩家皆持有；若需限制存取，可在權限插件中將其設為 `false`。
- `codex.admin` 預設僅 OP 持有，涵蓋所有管理子指令。

設定範例（LuckPerms）：

```
lp group admin permission set codex.admin true
```

指令用法詳見 [指令](/juiyuachievement/commands)。
