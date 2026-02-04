## ADDED Requirements

### Requirement: 付款狀態追蹤
系統 SHALL 為每個月結帳單追蹤付款狀態：未收款、已收款。

#### Scenario: 標示已收款
- **WHEN** 使用者將帳單標示為已收款並輸入收款日期
- **THEN** 系統更新狀態為已收款

### Requirement: 月結帳單列表
系統 SHALL 提供月結帳單列表，顯示各客戶各月的收付款狀態。

#### Scenario: 篩選未收款帳單
- **WHEN** 使用者篩選狀態為未收款
- **THEN** 系統顯示所有未收款的帳單

### Requirement: 應收應付分開追蹤
系統 SHALL 分開追蹤應收（客戶付給公司）與應付（公司付給客戶）。

#### Scenario: 檢視應收明細
- **WHEN** 使用者查看應收帳款
- **THEN** 系統顯示所有應收款項與狀態

#### Scenario: 檢視應付明細
- **WHEN** 使用者查看應付帳款
- **THEN** 系統顯示所有應付款項與狀態

### Requirement: 逾期提醒
系統 SHALL 在帳單超過指定天數未收款時標示為逾期。

#### Scenario: 標示逾期
- **WHEN** 帳單超過 30 天未收款
- **THEN** 系統標示為逾期並觸發提醒
