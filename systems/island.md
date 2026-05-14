# 🏝️ 空島系統

## 概述

每位玩家可擁有獨立的耕作空島。空島是一個無生物群系影響、永晝、和平難度的超平坦虛空世界，附帶基礎平台與起始物資。

## 空島規格

| 屬性 | 設定 |
|------|------|
| 世界類型 | `WorldType.FLAT`（超平坦） |
| 結構生成 | 無 |
| 日夜循環 | 關閉 |
| 天氣 | 關閉 |
| 難度 | 和平 |
| 世界邊界 | 1000 × 1000 |
| 起始平台 | 8×6 草地 + 1 棵橡樹 |
| 起始物資 | 水桶 + 岩漿桶（放入起始箱） |

## 指令

| 指令 | 說明 |
|------|------|
| `/island` | 傳送到自己的空島（不存在則自動建立） |
| `/island leave` | 返回進入前的位置 |
| `/island help` | 顯示幫助 |
| `/island tpset` | 設定自訂傳送點 |
| `/island visit <player>` | 拜訪已受邀玩家的空島 |
| `/island invite <player>` | 邀請玩家（當次連線有效） |
| `/island uninvite <player>` | 取消邀請 |
| `/island kick <player>` | 踢出島上訪客 |
| `/island delete <player> [confirm]` | 管理員刪除玩家空島 |

## 權限

| 權限 | 說明 |
|------|------|
| `subcareers.admin` | 執行 `/island delete` |

其餘指令無需權限。

## 世界管理

- 閒置 5 分鐘後自動卸載世界（節省記憶體）
- 玩家登入時自動載入
- 玩家死亡後在自己的空島重生

## 保護

- 非島主無法破壞或放置方塊（LOWEST 優先級監聽）
- 訪客需受邀才能 `/island visit`

## 邀請機制

- 邀請僅在**當次伺服器連線**有效
- 伺服器重啟後邀請失效
- 島主可隨時 `/island kick` 踢出訪客

## 資料儲存

空島世界對照表儲存在 `plugins/SubCareers/islands.yml`：

```yaml
<player_uuid>:
  world: "island_<uuid>"
  spawn: "x,y,z"
```
