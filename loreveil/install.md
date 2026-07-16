# 安裝

## 環境需求

| 項目 | 需求 |
|------|------|
| 伺服器 | Paper 1.21.1（`api-version: 1.21`） |
| Java | 21 |
| 前置插件 | **ProtocolLib 5.3.0+**（必裝，缺少時 LoreVeil 會自動停用） |

## 步驟

1. 安裝前置插件 [ProtocolLib](https://www.spigotmc.org/resources/protocollib.1997/)。
2. 將 `LoreVeil-1.0.0.jar` 放入 `plugins/`。
3. 重啟伺服器。看到 `LoreVeil 已啟動。` 即完成。

## 注意事項

- **無設定檔**：LoreVeil 沒有 `config.yml`，裝好即用。
- **無資料庫**：玩家偏好存於各自的 PDC（隨玩家資料檔保存），不需要外部儲存。
- 支援 `/reload`：重載時會自動為線上玩家補載偏好與開啟中的容器狀態。

指令用法見 [指令](/loreveil/commands)。
