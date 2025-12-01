```mermaid
graph TD
    Start[🚨 警報響起] --> Check{網站還活著嗎?}

    %% 分支 1: 服務死掉 (502/404)
    %% 修改：不再檢查 Docker 容器，改為檢查服務狀態 (例如 Render)
    Check -- NO (502/404) --> CheckStatus[檢查服務/平台狀態]
    CheckStatus --> IsRunning{服務在跑嗎?}
    IsRunning -- NO --> Restart[🛠️ 執行方案 A: 重啟服務]
    IsRunning -- YES --> CheckConfig[🛠️ 執行方案 B: 檢查平台設定]

    %% 分支 2: 服務活著但報錯
    %% 修改：移除 DB Error，保留 Chaos Monkey 和一般錯誤
    Check -- YES (能開但報錯) --> CheckLogs[🔍 檢查錯誤日誌]
    CheckLogs --> ErrorType{錯誤類型?}
    ErrorType -- "Chaos Monkey" --> StopChaos[🛠️ 執行方案 C: 關閉混沌模式]
    ErrorType -- "其他錯誤" --> CheckCode[🛠️ 執行方案 D: 檢查程式碼/重啟]

    %% 匯聚與驗證
    Restart --> Verify[✅ 驗證修復]
    CheckConfig --> Verify
    StopChaos --> Verify
    CheckCode --> Verify

    Verify --> End((結案/寫報告))

    %% 樣式設計 (保持一致)
    style Start fill:#ffcccc,stroke:#cc0000,stroke-width:2px
    style Restart fill:#e6f3ff,stroke:#0066cc,stroke-width:2px,stroke-dasharray: 5 5
    style StopChaos fill:#e6f3ff,stroke:#0066cc,stroke-width:2px,stroke-dasharray: 5 5
    style CheckCode fill:#e6f3ff,stroke:#0066cc,stroke-width:2px,stroke-dasharray: 5 5
    style CheckConfig fill:#e6f3ff,stroke:#0066cc,stroke-width:2px,stroke-dasharray: 5 5
    style Verify fill:#ccffcc,stroke:#009900,stroke-width:4px
```
