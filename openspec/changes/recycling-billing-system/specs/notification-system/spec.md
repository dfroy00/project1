## ADDED Requirements

### Requirement: Email 通知發送
系統 SHALL 支援透過 Email 發送通知，並支援附加檔案。

#### Scenario: 發送 Email 通知
- **WHEN** 觸發通知且客戶設定 Email 通知
- **THEN** 系統發送 Email 至客戶信箱

#### Scenario: Email 附加檔案
- **WHEN** 發送帳單通知 Email
- **THEN** 系統自動附加 PDF 對帳單檔案

### Requirement: LINE 通知發送
系統 SHALL 支援透過 LINE 發送通知。

#### Scenario: 發送 LINE 通知
- **WHEN** 觸發通知且客戶設定 LINE 通知
- **THEN** 系統發送 LINE 訊息至客戶

### Requirement: 通知審核機制
系統 SHALL 提供人工審核機制，通知需經審核後才發送。

#### Scenario: 建立待審核通知
- **WHEN** 系統產生通知（帳單、提醒等）
- **THEN** 通知進入「待審核」狀態，不自動發送

#### Scenario: 審核通過發送
- **WHEN** 使用者審核通過並點擊發送
- **THEN** 系統發送通知至目標對象

#### Scenario: 批次審核發送
- **WHEN** 使用者選擇多筆待審核通知並點擊批次發送
- **THEN** 系統批次發送所有選取的通知

### Requirement: 月結帳單通知
系統 SHALL 在月結帳單產生後建立待審核通知，經人工確認後發送給客戶與內部人員。

#### Scenario: 產生待審核帳單通知
- **WHEN** 月結帳單產生完成
- **THEN** 系統建立待審核的帳單通知

#### Scenario: 審核後通知客戶
- **WHEN** 使用者審核通過帳單通知
- **THEN** 系統依客戶偏好方式發送帳單通知（含 PDF 附件）

#### Scenario: 通知內部人員
- **WHEN** 月結帳單產生完成
- **THEN** 系統通知內部人員帳單已產生待審核

### Requirement: 未收款提醒
系統 SHALL 定期檢查未收款帳單並通知內部人員。

#### Scenario: 發送未收款提醒
- **WHEN** 帳單超過設定天數未收款
- **THEN** 系統發送提醒通知至內部人員

### Requirement: 合約到期提醒
系統 SHALL 在合約到期前通知內部人員。

#### Scenario: 發送合約到期提醒
- **WHEN** 合約進入到期前 30 天
- **THEN** 系統發送提醒通知至內部人員

### Requirement: 通知紀錄
系統 SHALL 記錄所有已發送的通知供查詢。

#### Scenario: 查詢通知歷史
- **WHEN** 使用者查看通知紀錄
- **THEN** 系統顯示通知發送時間、對象、內容、狀態
