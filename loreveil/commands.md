# 指令

主指令 `/lore`。僅玩家可執行。

| 指令 | 權限（預設） | 說明 |
|------|------|------|
| `/lore` | `loreveil.lore.toggle`（true） | 切換物品描述顯示/隱藏 |
| `/lore show` | `loreveil.lore.toggle`（true） | 顯示完整物品描述 |
| `/lore hide` | `loreveil.lore.toggle`（true） | 隱藏物品描述（收合成一行） |

## 範例

```
/lore          # 切換：隱藏 ⇄ 顯示
/lore hide     # §7已隱藏詳細描述。
/lore show     # §a已開啟詳細描述。
```

- 提供 Tab 補全：`show` / `hide`。
- 切換後立即重送背包封包，tooltip **即時更新**，不需重開背包。
- 偏好會持久保存：離線重登、伺服器重啟後維持上次的選擇（預設為顯示）。
