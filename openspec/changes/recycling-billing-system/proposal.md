## Why

環保回收公司需要一套系統來管理向客戶收取回收物的計費作業。目前缺乏統一的工具來追蹤交易紀錄、管理浮動價格與合約價格、產生月結報表，以及追蹤收付款狀態。手動作業容易出錯且效率低。

## What Changes

- 建立全新的網頁應用系統
- 整合既有排程系統資料（CSV 匯入）
- 自動化月結報表產生與通知發送
- 建立收付款追蹤機制

## Capabilities

### New Capabilities

- `customer-management`: 客戶基本資料管理，包含聯絡方式（Email/LINE）
- `contract-management`: 合約管理，包含期限、品項價格、到期提醒
- `schedule-import`: 從既有排程系統匯入收運資料（CSV 格式）
- `transaction-records`: 交易紀錄管理，含品項、數量、價格（合約價或浮動價）
- `monthly-billing`: 月結報表產生，依趟次呈現，輸出 PDF/Excel
- `payment-tracking`: 收付款狀態追蹤（月結）
- `notification-system`: 通知系統，支援 Email（含附件）/LINE，人工審核後發送，含帳單通知、未收款提醒、合約到期提醒

### Modified Capabilities

<!-- 無，這是全新系統 -->

## Impact

- 新建網頁應用系統（前後端）
- 需要資料庫儲存客戶、合約、交易、收付款資料
- 需串接 Email 發送服務
- 需串接 LINE Messaging API
- 未來預計擴充 ERP 立帳串接
