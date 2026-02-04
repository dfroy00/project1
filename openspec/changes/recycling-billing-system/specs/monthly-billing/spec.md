## ADDED Requirements

### Requirement: 月結報表產生
系統 SHALL 允許產生指定月份的月結報表。

#### Scenario: 產生單一客戶報表
- **WHEN** 使用者選擇客戶與月份並點擊產生
- **THEN** 系統產生該客戶當月對帳單

#### Scenario: 批次產生所有客戶報表
- **WHEN** 使用者選擇月份並點擊批次產生
- **THEN** 系統為所有有交易的客戶產生報表

### Requirement: 報表依趟次呈現
報表 SHALL 依日期與趟次分組顯示交易明細。

#### Scenario: 同日多趟顯示
- **WHEN** 客戶同一天有多趟收運
- **THEN** 報表分別顯示每趟的品項明細

### Requirement: 報表彙總金額
報表 SHALL 顯示：本月應收總額、本月應付總額、結餘金額。

#### Scenario: 計算結餘
- **WHEN** 報表產生時
- **THEN** 結餘 = 應收總額 - 應付總額

### Requirement: 匯出 PDF 格式
系統 SHALL 支援將報表匯出為 PDF 檔案。

#### Scenario: 下載 PDF
- **WHEN** 使用者點擊匯出 PDF
- **THEN** 系統產生並下載 PDF 檔案

### Requirement: 匯出 Excel 格式
系統 SHALL 支援將報表匯出為 Excel 檔案。

#### Scenario: 下載 Excel
- **WHEN** 使用者點擊匯出 Excel
- **THEN** 系統產生並下載 Excel 檔案

### Requirement: 內部彙總表
系統 SHALL 產生內部用的月度彙總表，包含所有客戶的應收應付統計。

#### Scenario: 檢視內部彙總
- **WHEN** 使用者選擇月份並檢視內部彙總
- **THEN** 系統顯示當月所有客戶的彙總統計
