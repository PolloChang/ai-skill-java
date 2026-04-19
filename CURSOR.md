# Cursor 編輯器指引

本專案使用 Cursor 作為主力的 AI 輔助開發工具。

為了讓 Cursor 能夠最佳化地協助您編寫 Java 21 程式碼，我們已經在 `.cursor/rules/` 目錄下配置了專屬的 `.mdc` 規則檔。

## 觸發方式

- 當您在編輯 `.java` 或 `build.gradle` 檔案時，Cursor 會自動讀取 `java-master-guidelines.mdc` 。
- 您可以在 Cursor 的 Chat 視窗中輸入 `@java-master` (如果您有定義 Custom rule tag) 或是直接提問，Cursor 會以 Java 21 與 Spring Boot 的最佳實踐來回答。

## 參考文件

開發前請務必閱讀 `EXAMPLES.md` ，裡面包含了我們團隊認可的 Picocli 與 Spring Data JPA 標準寫法。