# 💾 指令

## 主指令 `/subcareers`

| 指令 | 權限 | 說明 |
|------|------|------|
| `/subcareers` | 無 | 開啟副職業總覽 GUI |
| `/subcareers help` | 無 | 顯示幫助訊息 |

## 管理指令

| 指令 | 權限 | 說明 |
|------|------|------|
| `/subcareers reload` | `subcareers.admin` | 重新載入所有設定檔 |
| `/subcareers exp add <player> <career> <amount>` | `subcareers.admin` | 增加玩家副職業經驗 |
| `/subcareers exp set <player> <career> <amount>` | `subcareers.admin` | 設定玩家副職業經驗 |

### 支援的 career 名稱

| 英文 ID | 中文別名 |
|---------|---------|
| `logging` | `伐木` |
| `farming` | `耕作` |
| `fishing` | `釣魚` |
| `mining` | `採礦` |
| `herb` | `草藥` |
| `pharma` | `製藥` |
| `forging` | `鍛造` |
| `enchanting` | `刻印` |

## 刻印指令

| 指令 | 權限 | 說明 |
|------|------|------|
| `/subcareers enchant give <player> <刻印ID> <等級>` | `subcareers.admin` | 給予刻印捲軸 |
| `/subcareers enchant list` | 無 | 列出所有刻印 |

## 強化系統指令

| 指令 | 權限 | 說明 |
|------|------|------|
| `/subcareers enhance` | 無 | 開啟裝備強化介面 |
| `/subcareers starupgrade` | 無 | 開啟裝備升星介面 |
| `/subcareers refine` | 無 | 開啟裝備精煉介面（100% 成功） |
| `/subcareers fusion` | 無 | 開啟礦物融合介面 |
| `/fusion` | 無 | 開啟礦物融合介面（快捷指令） |
| `/subcareers givescroll <player> <類型>` | `subcareers.admin` | 給予強化消耗品 |

強化消耗品類型：`lucky <1-10>`（機率石）/ `protection`（保護石）/ `anti_retreat`（防退石）/ `restoration`（復原石）/ `inheritance`（傳承石）/ `refine`（精煉石）

## 鍛造指令

| 指令 | 權限 | 說明 |
|------|------|------|
| `/subcareers forging bind` | `subcareers.admin` | 登記鍛爐（看向高爐） |
| `/subcareers forging unbind` | `subcareers.admin` | 取消登記 |
| `/subcareers forging reload` | `subcareers.admin` | 重新載入鍛造配方 |
| `/subcareers forging givehammer` | `subcareers.admin` | 給予鍛造錘 |

## 鍛造站指令 `/forge`

| 指令 | 權限 | 說明 |
|------|------|------|
| `/forge bind` | `subcareers.admin` | 將面對的高爐登記為鍛爐 |
| `/forge unbind` | `subcareers.admin` | 取消登記（鍛造中無法解除） |
| `/forge learn <player> <recipe>` | `subcareers.admin` | 讓玩家學習配方 |
| `/forge forget <player> <recipe>` | `subcareers.admin` | 讓玩家遺忘配方 |
| `/forge giveblueprint <player> <recipe> [數量]` | `subcareers.admin` | 發放鍛造圖紙 |
| `/forge list` | `subcareers.admin` | 列出配方與進行中的鍛造 |
| `/forge reload` | `subcareers.admin` | 重新載入鍛造配方 |
| `/forge help` | 無 | 顯示幫助 |

## 製藥指令 `/pharma`

| 指令 | 權限 | 說明 |
|------|------|------|
| `/pharma wand` | `pharma.admin` | 取得選取工具（框選製藥爐範圍） |
| `/pharma create <bottom\|middle\|top> <name>` | `pharma.admin` | 建立製藥爐 |
| `/pharma delete <name>` | `pharma.admin` | 刪除製藥爐（煉製中無法刪除） |
| `/pharma learn <player> <recipe>` | `pharma.admin` | 讓玩家學習配方 |
| `/pharma forget <player> <recipe>` | `pharma.admin` | 讓玩家遺忘配方 |
| `/pharma giverecipe <player> <recipe> [數量]` | `pharma.admin` | 發放藥方書 |
| `/pharma unlocktier <player> <bottom\|middle\|top>` | `pharma.admin` | 設定玩家解鎖的爐階 |
| `/pharma checktier <player>` | `pharma.admin` | 查詢玩家當前解鎖的爐階 |
| `/pharma list` | `pharma.admin` | 列出所有製藥爐與配方 |
| `/pharma reload` | `pharma.admin` | 重新載入設定 |
| `/pharma help` | 無 | 顯示幫助 |

## 草藥指令

| 指令 | 權限 | 說明 |
|------|------|------|
| `/subcareers herb reload` | `herb.admin` | 重新載入草藥設定 |

## 採礦指令

| 指令 | 權限 | 說明 |
|------|------|------|
| `/subcareers mining setnode <tier>` | `subcareers.admin` | 設定節點（看向的方塊） |
| `/subcareers mining delnode` | `subcareers.admin` | 刪除節點（看向的方塊） |
| `/subcareers mining list` | `subcareers.admin` | 列出所有礦脈節點 |

## 釣魚指令

| 指令 | 權限 | 說明 |
|------|------|------|
| `/subcareers fishing rodeditor` | 無 | 開啟釣竿編輯器 GUI |
| `/subcareers fishing fishbag` | 無 | 開啟魚袋 GUI |
| `/rodeditor` | 無 | 開啟釣竿編輯器（快捷指令） |
| `/fishbag` | 無 | 開啟魚袋（快捷指令） |

## 空島指令 `/island`

| 指令 | 權限 | 說明 |
|------|------|------|
| `/island` | 無 | 傳送到自己的空島（不存在則建立） |
| `/island leave` | 無 | 返回進入前的位置 |
| `/island help` | 無 | 顯示幫助 |
| `/island tpset` | 無 | 設定自訂傳送點 |
| `/island visit <player>` | 無 | 拜訪受邀玩家的空島 |
| `/island invite <player>` | 無 | 邀請玩家（僅當次連線有效） |
| `/island uninvite <player>` | 無 | 取消邀請 |
| `/island kick <player>` | 無 | 踢出島上訪客 |
| `/island reset` | 無 | 重置自己的空島（雙重確認後刪除並重建） |
| `/island template` | `subcareers.admin` | 建立/編輯新島範本世界 |
| `/island delete <player> [confirm]` | `subcareers.admin` | 刪除玩家空島 |
