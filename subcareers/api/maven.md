# 📦 Maven

## Repository

```xml
<repository>
    <id>phoenix</id>
    <url>https://nexus.phoenixdevt.fr/repository/maven-public/</url>
</repository>
```

## 相依插件

SubCareers 依賴以下 MMO 插件 API：

### MMOCore

```xml
<dependency>
    <groupId>net.Indyuce</groupId>
    <artifactId>MMOCore-API</artifactId>
    <version>1.13.1-SNAPSHOT</version>
    <scope>provided</scope>
</dependency>
```

### MMOItems

```xml
<dependency>
    <groupId>net.Indyuce</groupId>
    <artifactId>MMOItems-API</artifactId>
    <version>6.9.4-SNAPSHOT</version>
    <scope>provided</scope>
</dependency>
```

### MythicLib

```xml
<dependency>
    <groupId>io.lumine</groupId>
    <artifactId>MythicLib-dist</artifactId>
    <version>1.7.1-SNAPSHOT</version>
    <scope>provided</scope>
</dependency>
```

## 當前專案版本

`libs/` 目錄中的實際版本：

| 插件 | Jar 版本 |
|------|------|
| MMOCore | 1.13 |
| MMOItems | 6.10 |
| MythicLib | 1.7 |
| MythicMobs | 5.7.2 |
| Vault | 1.7.4 |
| SolarTermCore | 1.0 |

## SubCareers 依賴

```xml
<dependencies>
    <!-- Paper API -->
    <dependency>
        <groupId>io.papermc.paper</groupId>
        <artifactId>paper-api</artifactId>
        <version>1.21.1-R0.1-SNAPSHOT</version>
        <scope>provided</scope>
    </dependency>

    <!-- MythicLib -->
    <dependency>
        <groupId>io.lumine</groupId>
        <artifactId>MythicLib-dist</artifactId>
        <version>1.7.1-SNAPSHOT</version>
        <scope>provided</scope>
    </dependency>

    <!-- MMOCore -->
    <dependency>
        <groupId>net.Indyuce</groupId>
        <artifactId>MMOCore-API</artifactId>
        <version>1.13.1-SNAPSHOT</version>
        <scope>provided</scope>
    </dependency>

    <!-- MMOItems -->
    <dependency>
        <groupId>net.Indyuce</groupId>
        <artifactId>MMOItems-API</artifactId>
        <version>6.9.4-SNAPSHOT</version>
        <scope>provided</scope>
    </dependency>

    <!-- MythicMobs (optional) -->
    <dependency>
        <groupId>io.lumine</groupId>
        <artifactId>MythicMobs</artifactId>
        <version>5.7.2</version>
        <scope>provided</scope>
    </dependency>

    <!-- Vault (optional) -->
    <dependency>
        <groupId>com.github.MilkBowl</groupId>
        <artifactId>VaultAPI</artifactId>
        <version>1.7</version>
        <scope>provided</scope>
    </dependency>

    <!-- HikariCP -->
    <dependency>
        <groupId>com.zaxxer</groupId>
        <artifactId>HikariCP</artifactId>
        <version>5.1.0</version>
    </dependency>

    <!-- MySQL Connector -->
    <dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
        <version>8.0.33</version>
    </dependency>
</dependencies>
```
