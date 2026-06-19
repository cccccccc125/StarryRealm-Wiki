# 安裝與設定

## 環境需求

| 項目 | 需求 |
|------|------|
| 伺服器 | Paper 1.21.1 |
| Java | **JDK 21**（Paper 1.21.1 執行需 Java 21） |
| 資料庫 | MySQL 或 MariaDB |
| 銅錢相依 | Vault + 經濟提供者（EssentialsX / CMI） |
| 選用 | MMOItems（拍賣行品質排序）、CMI（外部經濟監聽） |

## 建置

需求：JDK 21、Maven 3.9+、可連線下載相依套件。

```bash
mvn clean package
```

產出 `target/EconomyCore-1.0.0.jar`，放入伺服器 `plugins/` 即可。

> 打包時 HikariCP 與 MySQL Connector 已 relocate（`com.economycore.libs.*`），不會與其他插件衝突。

## 安裝步驟

1. 建立 MySQL / MariaDB 資料庫（預設名稱 `economy`，可於設定調整）。插件啟動時會**自動建表**；亦可手動執行專案 `sql/` 下的腳本。
2. 將 jar 放入 `plugins/`，啟動一次以產生 `config.yml`。
3. 編輯 `config.yml` 的 `database` 區段填入連線資訊（見下）。
4. 若要使用銅錢功能，先安裝 **Vault** 與一個經濟提供者（EssentialsX / CMI）。
5. `/ecoadmin reload` 或重啟套用設定。

## 資料庫設定

`config.yml`：

```yaml
database:
  host: localhost
  port: 3306
  database: economy
  username: root
  password: ""
  pool-size: 10            # HikariCP 連線池大小
  connection-timeout: 10000  # 取得連線逾時（毫秒）
```

完整設定欄位請見 [設定檔](/economycore/config)。

## 關於 Vault 可選

Vault 掛載失敗時插件仍正常啟動，**僅停用銅錢功能**並於 console 警告，不影響元寶與金流日誌。
