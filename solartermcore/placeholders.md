# 佔位符（PlaceholderAPI）

安裝 **PlaceholderAPI** 後，SolarTermCore 註冊以下佔位符：

| 佔位符 | 回傳 |
|--------|------|
| `%current_term%` | 目前節氣名稱（例如 `穀雨`） |

## 使用範例

可用於任何支援 PlaceholderAPI 的插件（記分板、聊天前綴、TAB 清單、全息圖等），例如：

```
&7今日節氣：&e%current_term%
```

> 擴充設定為 `persist`，`/papi reload` 後佔位符仍有效，不需重啟伺服器。
