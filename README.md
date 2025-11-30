graph TD
    A[🚨 警報響起] --> B{網站還能開嗎?}
    B -- NO (502/404) --> C[檢查 Docker 容器狀態]
    B -- YES (能開但報錯) --> D[檢查 Application Logs]
    
    C --> E{容器活著嗎?}
    E -- NO --> F[🛠️ 執行：重啟服務]
    E -- YES --> G[🛠️ 執行：檢查網路/Port]
    
    D --> H{看到什麼錯誤?}
    H -- "Chaos Monkey" --> I[🛠️ 執行：關閉混沌模式]
    H -- "DB Connection" --> J[🛠️ 執行：檢查資料庫]
    
    F --> K[✅ 驗證修復]
    I --> K
    J --> K
    K --> L[寫入事故報告]
    
    style A fill:#ffcccc,stroke:#333,stroke-width:2px
    style F fill:#e6f3ff,stroke:#333,stroke-width:2px
    style I fill:#e6f3ff,stroke:#333,stroke-width:2px
    style J fill:#e6f3ff,stroke:#333,stroke-width:2px
    style K fill:#ccffcc,stroke:#333,stroke-width:2px
