## ADDED Requirements

### Requirement: CSV 檔案上傳
系統 SHALL 允許使用者上傳 CSV 格式的排程資料。

#### Scenario: 成功上傳 CSV
- **WHEN** 使用者上傳有效的 CSV 檔案
- **THEN** 系統解析檔案並顯示預覽

### Requirement: CSV 欄位對應
系統 SHALL 支援以下欄位：日期、客戶名稱、趟次編號、車號、司機。

#### Scenario: 欄位自動對應
- **WHEN** CSV 欄位名稱符合預設格式
- **THEN** 系統自動對應欄位

#### Scenario: 手動調整欄位對應
- **WHEN** CSV 欄位名稱不符合預設格式
- **THEN** 使用者可手動指定欄位對應

### Requirement: 客戶名稱比對
系統 SHALL 將 CSV 中的客戶名稱與系統內客戶進行比對。

#### Scenario: 完全比對成功
- **WHEN** CSV 客戶名稱與系統客戶完全相符
- **THEN** 系統自動關聯

#### Scenario: 模糊比對建議
- **WHEN** CSV 客戶名稱無完全相符
- **THEN** 系統提供相似客戶建議供使用者選擇

#### Scenario: 建立新客戶
- **WHEN** CSV 客戶名稱無法比對且使用者確認
- **THEN** 系統建立新客戶資料

### Requirement: 匯入確認
系統 SHALL 在匯入前顯示預覽，讓使用者確認後才正式匯入。

#### Scenario: 確認匯入
- **WHEN** 使用者確認預覽內容無誤
- **THEN** 系統建立對應的交易紀錄骨架（待填品項）
