📘 SRE 維運手冊 (Runbook): [填寫服務名稱，例如：登入系統]⚠️ 緊急狀態處理原則： 先恢復服務 (Mitigate)，再尋找根因 (Root Cause)。請依序執行以下步驟。屬性內容嚴重等級 (Severity)🔴 SEV-1 (Critical) / 🟡 SEV-2 (Warning)觸發警報 (Trigger)[請開發填寫：例如 High_Login_Failure_Rate > 5%]影響範圍 (Impact)[請開發填寫：例如 所有用戶無法登入]負責團隊 (Owner)[填寫團隊名稱]最後更新 (Updated)2025-12-011. 🗺️ 故障排除導航圖 (Troubleshooting Map)(此圖表由 Mermaid 自動渲染，無需截圖)graph TD
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
2. 🔍 診斷步驟 (Diagnosis)請依序執行以下指令確認問題根源。步驟 2.1：檢查服務存活狀態 (Health Check)確認服務是否能回應 HTTP 請求。# [請開發人員填寫檢查指令]
curl -I http://[你的網址]/
預期結果： HTTP/1.1 200 OK失敗結果： Connection refused 或 502 Bad Gateway步驟 2.2：查看即時日誌 (Live Logs)若服務存活但功能異常，請查看最近的 Error Log。# [請開發人員填寫 Log 查詢指令]
# 例如：Render/Docker logs
docker logs [container_name] --tail 50 | grep "ERROR"
3. 🛠️ 修復方案 (Mitigation)根據診斷結果選擇對應的劇本。🟢 劇本 A：服務卡死/記憶體洩漏 (Generic Fix)適用情況： 網站完全打不開 (502/504)，或回應極慢。# [請開發人員填寫重啟指令]
docker restart [app_name]
# 或在 Render 後台點擊 "Manual Deploy" -> "Clear Cache & Deploy"
🟠 劇本 B：混沌測試導致崩潰 (Chaos Monkey)適用情況： Log 出現 Chaos Monkey struck! 或隨機 500 錯誤。# [請開發人員填寫關閉指令]
# 1. 設定環境變數
export CHAOS_MODE=False
# 2. 重新啟動應用
python app.py
🔵 劇本 C：資料庫連線失敗適用情況： Log 出現 Connection refused 或 SQLAlchemyError。# [請開發人員填寫 DB 檢查指令]
# 檢查連線字串或重啟 DB 實例
4. ✅ 驗證與溝通 (Verification)執行修復後，請務必確認服務已恢復。使用者面驗證： 使用無痕視窗嘗試登入一次。指標面驗證： 檢查 Prometheus/Grafana，確認 login_error_rate 歸零。📞 升級流程 (Escalation Policy)若 15 分鐘內 無法解決，請依序聯絡：Primary On-Call: [開發人員姓名] (Slack: @dev_name)Manager: [PM 姓名] (Slack: @pm_name)設計師動作： 請設計師將首頁切換為 「系統維護中 (Maintenance Mode)」 靜態頁面。此文件由 SRE Team 維護，最近更新於 2025/12/01
