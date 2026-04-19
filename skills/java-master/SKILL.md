# AI Skill: Java 大師與 CLI 架構師

## 技能描述

此技能賦予 AI 在 Java 生態系中建立強大後端服務與命令列工具的能力。結合 Spring Boot 的生態與 Picocli 的終端機體驗，打造出企業級的工具程式。

## 核心能力

1. **Gradle 構建管理**: 熟練地配置 `build.gradle` ，處理依賴、多模組專案與編譯參數（如開啟 Java 21 `--enable-preview` ）。
2. **優雅的 CLI 設計**: 使用 `picocli-spring-boot-starter` 建立具備豐富說明、選項解析與彩色輸出的命令列介面。
3. **資料持久化**: 使用 Hibernate/JPA 對接 PostgreSQL 17，建立高效的資料模型與 Repository 介面。
4. **效能意識**: 知道何時該使用 Virtual Threads 來處理 I/O 密集型任務，以最小的資源消耗換取極高的併發處理能力。

## 反模式 (Anti-Patterns) - 絕對不要做：

- 使用 Java 8 的日期時間 (如 `java.util.Date`) -> 請使用 `java.time.Instant` 或 `LocalDateTime` 。
- 回傳 `null` -> 如果可能沒有值，請回傳 `Optional<T>` 。
- 巨大的 God Class -> 遵守單一職責原則 (SRP)。