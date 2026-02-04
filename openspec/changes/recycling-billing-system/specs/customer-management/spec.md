## ADDED Requirements

### Requirement: 建立客戶資料
系統 SHALL 允許使用者建立客戶基本資料，包含名稱、聯絡人、電話、地址、Email、LINE ID。

#### Scenario: 成功建立客戶
- **WHEN** 使用者填寫客戶名稱並送出
- **THEN** 系統建立客戶資料並顯示成功訊息

#### Scenario: 客戶名稱重複
- **WHEN** 使用者輸入已存在的客戶名稱
- **THEN** 系統顯示警告並詢問是否仍要建立

### Requirement: 編輯客戶資料
系統 SHALL 允許使用者編輯已存在的客戶資料。

#### Scenario: 成功編輯客戶
- **WHEN** 使用者修改客戶資料並儲存
- **THEN** 系統更新客戶資料並顯示成功訊息

### Requirement: 查詢客戶列表
系統 SHALL 提供客戶列表頁面，支援搜尋與分頁。

#### Scenario: 搜尋客戶
- **WHEN** 使用者輸入客戶名稱關鍵字
- **THEN** 系統顯示符合條件的客戶列表

### Requirement: 設定通知方式
系統 SHALL 允許為每位客戶設定偏好的通知方式（Email/LINE/兩者皆是）。

#### Scenario: 設定 Email 通知
- **WHEN** 使用者選擇 Email 作為通知方式
- **THEN** 系統儲存設定並於通知時使用 Email 發送
