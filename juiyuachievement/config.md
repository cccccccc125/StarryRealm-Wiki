# 設定檔（config.yml）

本頁說明 `plugins/JuiyuAchievement/config.yml` 的所有設定區段。修改後可用 `/codex reload` 熱重載，不需重啟伺服器。

## 一般設定

| 鍵 | 預設值 | 說明 |
|----|--------|------|
| `language` | `zh_TW` | 插件語系 |
| `debug` | `false` | 啟用除錯日誌 |

```yaml
language: zh_TW
debug: false
```

## completion — 完成特效

控制成就完成時的音效與廣播行為。

| 鍵 | 預設值 | 說明 |
|----|--------|------|
| `completion.sound` | `UI_TOAST_CHALLENGE_COMPLETE` | 完成音效（Bukkit `Sound` 名稱） |
| `completion.broadcast-all` | `false` | 是否所有成就完成都全服廣播；設為 `false` 時，僅 `broadcast: true` 的個別成就會廣播 |

```yaml
completion:
  sound: UI_TOAST_CHALLENGE_COMPLETE
  broadcast-all: false
```

> 個別成就可在定義檔中設定 `broadcast: true` 覆寫此全域開關。詳見 [獎勵：點數/稱號/商店](/juiyuachievement/rewards)。

## scoreboard — 側邊欄記分板

控制追蹤成就進度的側邊欄記分板顯示。玩家可在 GUI 中點擊成就進行追蹤（最多 5 個），被追蹤的成就進度會顯示在側邊欄上。

| 鍵 | 預設值 | 說明 |
|----|--------|------|
| `scoreboard.enabled` | `true` | 是否啟用側邊欄記分板 |
| `scoreboard.title` | `&6&l成就追蹤` | 記分板標題（支援 `&` 色碼） |

```yaml
scoreboard:
  enabled: true
  title: '&6&l成就追蹤'
```

> 記分板追蹤的完整功能說明見 [獎勵：點數/稱號/商店 — 側邊欄記分板追蹤](/juiyuachievement/rewards)。

## chat-prefix — 內建聊天前綴

插件內建簡易的聊天前綴功能，啟用後玩家發言時會自動顯示佩戴中的稱號。

| 鍵 | 預設值 | 說明 |
|----|--------|------|
| `chat-prefix.enabled` | `false` | 是否啟用內建聊天前綴 |

```yaml
chat-prefix:
  enabled: false
```

> **建議**：若伺服器已使用其他聊天插件（如 EssentialsChat、LuckPerms prefix 等），建議維持 `enabled: false`，改用 PlaceholderAPI 的 `%juiyu_title%` 佔位符整合到現有聊天格式中。詳見 [PlaceholderAPI](/juiyuachievement/placeholders)。聊天前綴的功能說明見 [獎勵：點數/稱號/商店 — 內建聊天前綴稱號](/juiyuachievement/rewards)。

## codex-rewards — 圖鑑分類全收集獎勵

當玩家集滿某個圖鑑分類（`category`）的**所有**條目後，系統會由主控台（Console）執行此區段定義的指令清單，作為一次性獎勵。

| 鍵 | 預設值 | 說明 |
|----|--------|------|
| `codex-rewards.<分類名>` | `{}`（預設無定義） | 分類名稱對應的指令清單；鍵名需與圖鑑條目的 `category` 欄位**完全一致** |

每條指令中的 `%player%` 會自動展開為達成全收集的玩家名稱。

### 設定範例

```yaml
codex-rewards:
  原生怪物:
    - 'give %player% diamond 5'
  物品:
    - 'give %player% emerald 10'
    - 'say %player% 集滿了物品圖鑑！'
```

上例中，集滿「原生怪物」分類全部圖鑑的玩家會獲得 5 顆鑽石；集滿「物品」分類的玩家會獲得 10 顆綠寶石並全服公告。

### 注意事項

- 獎勵為**一次性**——同一玩家對同一分類只會領取一次（以 `claimedRewards` 記錄）。
- 指令由主控台執行，因此可使用 `give`、`lp` 等需要權限的指令。
- 未定義的分類在集滿時不會觸發任何獎勵。

> 圖鑑分類全收集獎勵的完整判定流程見 [圖鑑系統 — 分類全收集獎勵](/juiyuachievement/codex)。

## 熱重載

修改 `config.yml` 後，執行 `/codex reload` 即可重載所有設定，無需重啟伺服器。需要 `codex.admin` 權限。

## 相關章節

- [成就與觸發來源](/juiyuachievement/achievements) — 成就定義與觸發條件
- [圖鑑系統](/juiyuachievement/codex) — 圖鑑條目定義與分類全收集獎勵
- [獎勵：點數/稱號/商店](/juiyuachievement/rewards) — 完成特效、記分板、聊天前綴的功能說明
- [PlaceholderAPI](/juiyuachievement/placeholders) — 佔位符（含 `%juiyu_title%`）
- [指令](/juiyuachievement/commands) — `/codex reload` 等管理指令
