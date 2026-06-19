# 拍賣行指令

主指令 `/ah`（別名 `/auction`）。多數操作也可由 GUI 完成（見 [GUI 介面](/economycore/auction/gui)），指令為備援。

| 指令 | 權限（預設） | 說明 |
|------|------|------|
| `/ah` | `auction.use`（true） | 開啟拍賣行主介面（GUI） |
| `/ah sell <auction\|buyout> <價格> [時長]` | `auction.sell`（true） | 上架**手持**物品 |
| `/ah list [頁碼]` | `auction.use` | 以聊天列表瀏覽掛單（每頁 10 筆，可點擊操作） |
| `/ah buy <掛單ID>` | `auction.use` | 一口價購買 |
| `/ah bid <掛單ID> <出價>` | `auction.use` | 對競拍出價 |
| `/ah cancel <掛單ID>` | `auction.use` | 下架自己的掛單（競拍須無人出價） |
| `/ah mailbox` | `auction.use` | 查看信箱（可點擊領取） |
| `/ah claim <信箱ID>` | `auction.use` | 領取指定信箱物品 |

> 無參數或無法辨識的子指令會顯示指令說明。

## 範例

```
/ah                                  # 開主介面
/ah sell buyout 5000                 # 手持物品上架一口價 5000 銅錢
/ah sell auction 1000 72             # 上架競拍，起標 1000，持續 72 小時
/ah list 2                           # 瀏覽第 2 頁
/ah buy 3f9c2a1b-...                 # 一口價購買
/ah bid 3f9c2a1b-... 1500            # 出價 1500
/ah cancel 3f9c2a1b-...              # 下架
/ah mailbox                          # 查看信箱
```

時長省略時用設定的預設值（預設 48 小時），上限見 [拍賣行設定](/economycore/auction/config)。各種失敗訊息（價格無效、銅錢不足、出價過低、已被禁拍等）的對應規則見各功能子頁。
