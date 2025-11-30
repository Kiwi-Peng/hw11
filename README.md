graph TD
%% 定義節點
    Start[🚨 警報響起] --> Check{網站還活著嗎?}
    
    %% 分支 1: 服務死掉
    Check -- NO (502/404) --> CheckContainer[檢查容器狀態]
    CheckContainer --> IsRunning{容器在跑嗎?}
    IsRunning -- NO --> Restart[🛠️ 執行方案 A: 重啟服務]
    IsRunning -- YES --> CheckNet[🛠️ 執行方案 B: 檢查網路設定]
    
    %% 分支 2: 服務活著但報錯
    Check -- YES (能開但報錯) --> CheckLogs[🔍 檢查錯誤日誌]
    CheckLogs --> ErrorType{錯誤類型?}
    ErrorType -- "Chaos Monkey" --> StopChaos[🛠️ 執行方案 C: 關閉混沌模式]
    ErrorType -- "DB Error" --> CheckDB[🛠️ 執行方案 D: 檢查資料庫連線]
    
    %% 匯聚與驗證
    Restart --> Verify[✅ 驗證修復]
    CheckNet --> Verify
    StopChaos --> Verify
    CheckDB --> Verify
    
    Verify --> End((結案/寫報告))

    %% 樣式設計 (設計師定義)
    style Start fill:#ffcccc,stroke:#cc0000,stroke-width:2px
    style Restart fill:#e6f3ff,stroke:#0066cc,stroke-width:2px,stroke-dasharray: 5 5
    style StopChaos fill:#e6f3ff,stroke:#0066cc,stroke-width:2px,stroke-dasharray: 5 5
    style CheckDB fill:#e6f3ff,stroke:#0066cc,stroke-width:2px,stroke-dasharray: 5 5
    style Verify fill:#ccffcc,stroke:#009900,stroke-width:4px

# [請開發人員填寫檢查指令]
curl -I http://[你的網址]/

# [請開發人員填寫 Log 查詢指令]
# 例如：Render/Docker logs
docker logs [container_name] --tail 50 | grep "ERROR"

# [請開發人員填寫重啟指令]
docker restart [app_name]
# 或在 Render 後台點擊 "Manual Deploy" -> "Clear Cache & Deploy"

# [請開發人員填寫關閉指令]
# 1. 設定環境變數
export CHAOS_MODE=False
# 2. 重新啟動應用
python app.py

# [請開發人員填寫 DB 檢查指令]
# 檢查連線字串或重啟 DB 實例


