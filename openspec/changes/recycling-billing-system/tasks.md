# Implementation Tasks

## Phase 0: 專案初始化

- [ ] 建立 Next.js 專案 (TypeScript + App Router)
- [ ] 設定 Tailwind CSS
- [ ] 設定 Prisma + PostgreSQL 連線
- [ ] 建立 Prisma Schema (所有資料模型)
- [ ] 執行 Prisma Migration
- [ ] 設定 Zod 驗證結構
- [ ] 建立專案目錄結構

## Phase 1: 客戶管理 (customer-management)

- [ ] 建立 Customer Prisma Model（含完整欄位）
  - [ ] short_name, erp_name, payment_type, tax_id, invoice_title 等
- [ ] 建立 CustomerFee Prisma Model（附加費用）
- [ ] 建立客戶 API Routes (CRUD)
  - [ ] POST /api/customers - 建立客戶
  - [ ] GET /api/customers - 查詢客戶列表
  - [ ] GET /api/customers/[id] - 取得單一客戶
  - [ ] PUT /api/customers/[id] - 更新客戶
  - [ ] DELETE /api/customers/[id] - 刪除客戶
- [ ] 建立客戶附加費用 API Routes
  - [ ] POST /api/customers/[id]/fees - 新增附加費用
  - [ ] PUT /api/customers/[id]/fees/[feeId] - 更新附加費用
  - [ ] DELETE /api/customers/[id]/fees/[feeId] - 刪除附加費用
- [ ] 建立客戶 Zod Schema
- [ ] 建立客戶列表頁面 (/customers)
- [ ] 建立客戶新增/編輯表單元件
- [ ] 實作客戶搜尋功能（廠商名稱 + ERP 名稱）
- [ ] 實作收付款類型篩選
- [ ] 實作通知方式設定 (LINE/MAIL/電話/司機親送)
- [ ] 建立附加費用管理介面
- [ ] 實作客戶 CSV 匯入功能
  - [ ] POST /api/customers/import - 匯入客戶 CSV
  - [ ] 建立匯入預覽頁面
  - [ ] 實作欄位對應

## Phase 2: 合約管理 (contract-management)

- [ ] 建立 Contract + ContractItem Prisma Models
- [ ] 建立合約 API Routes
  - [ ] POST /api/contracts - 建立合約
  - [ ] GET /api/contracts - 查詢合約列表
  - [ ] GET /api/contracts/[id] - 取得單一合約
  - [ ] PUT /api/contracts/[id] - 更新合約
  - [ ] POST /api/contracts/[id]/items - 新增合約品項
- [ ] 建立合約 Zod Schema
- [ ] 建立合約列表頁面 (/contracts)
- [ ] 建立合約新增/編輯表單
- [ ] 建立合約品項管理介面
- [ ] 實作合約狀態自動判斷 (有效/即將到期/已過期)
- [ ] 實作合約到期提醒排程

## Phase 3: 排程匯入 (schedule-import)

- [ ] 建立 CSV 上傳 API Route
  - [ ] POST /api/import/csv - 上傳並解析 CSV
- [ ] 建立 CSV 解析工具 (papaparse)
- [ ] 建立欄位對應邏輯
- [ ] 實作客戶名稱比對
  - [ ] 完全比對
  - [ ] 模糊比對建議
  - [ ] 建立新客戶選項
- [ ] 建立匯入預覽頁面 (/import)
- [ ] 建立匯入確認介面
- [ ] 實作批次建立交易紀錄

## Phase 4: 交易紀錄 (transaction-records)

- [ ] 建立 Transaction + TransactionItem Prisma Models
- [ ] 建立交易 API Routes
  - [ ] POST /api/transactions - 建立交易
  - [ ] GET /api/transactions - 查詢交易列表
  - [ ] GET /api/transactions/[id] - 取得單一交易
  - [ ] PUT /api/transactions/[id] - 更新交易
  - [ ] POST /api/transactions/[id]/items - 新增交易品項
  - [ ] PUT /api/transactions/[id]/items/[itemId] - 更新品項
- [ ] 建立交易 Zod Schema
- [ ] 實作價格自動帶入邏輯
  - [ ] 檢查合約價格
  - [ ] 標示價格來源 (合約價/浮動價)
- [ ] 建立交易列表頁面 (/transactions)
- [ ] 建立交易詳情頁面
- [ ] 建立品項新增/編輯介面
- [ ] 實作正負金額處理

## Phase 5: 月結報表 (monthly-billing)

- [ ] 建立月結報表 API Routes
  - [ ] GET /api/reports/monthly - 取得月結資料
  - [ ] POST /api/reports/monthly/generate - 產生月結報表
  - [ ] GET /api/reports/monthly/export/pdf - 匯出 PDF
  - [ ] GET /api/reports/monthly/export/excel - 匯出 Excel
- [ ] 實作月結計算邏輯
  - [ ] 依客戶彙總
  - [ ] 依趟次分組
  - [ ] 計算應收/應付/結餘
- [ ] 建立報表頁面 (/reports)
- [ ] 建立客戶對帳單模板
- [ ] 建立內部彙總表模板
- [ ] 實作 PDF 產生 (react-pdf 或 puppeteer)
- [ ] 實作 Excel 產生 (exceljs)

## Phase 6: 收付款追蹤 (payment-tracking)

- [ ] 建立 Payment Prisma Model
- [ ] 建立收付款 API Routes
  - [ ] GET /api/payments - 查詢收付款列表
  - [ ] PUT /api/payments/[id] - 更新收付款狀態
- [ ] 建立收付款 Zod Schema
- [ ] 建立收付款列表頁面 (/payments)
- [ ] 實作狀態篩選 (未收款/已收款)
- [ ] 實作標示已收款功能
- [ ] 實作逾期標示邏輯
- [ ] 建立應收/應付分開檢視

## Phase 7: 通知系統 (notification-system)

- [ ] 建立 Notification Prisma Model (含待審核狀態)
- [ ] 設定 Email 服務 (SendGrid)
  - [ ] 建立 SendGrid 帳號
  - [ ] 設定 API Key
  - [ ] 建立 Email 發送工具（支援附件）
- [ ] 設定 LINE Messaging API
  - [ ] 建立 LINE Official Account
  - [ ] 設定 Channel Access Token
  - [ ] 建立 LINE 發送工具
- [ ] 建立通知 API Routes
  - [ ] GET /api/notifications - 查詢通知紀錄（含待審核）
  - [ ] GET /api/notifications/pending - 查詢待審核通知
  - [ ] POST /api/notifications/[id]/approve - 審核通過並發送
  - [ ] POST /api/notifications/batch-approve - 批次審核發送
  - [ ] DELETE /api/notifications/[id] - 取消通知
- [ ] 建立通知模板
  - [ ] 月結帳單通知模板（附加 PDF）
  - [ ] 未收款提醒模板
  - [ ] 合約到期提醒模板
- [ ] 建立通知審核頁面 (/notifications)
  - [ ] 待審核列表
  - [ ] 預覽通知內容
  - [ ] 單筆/批次發送功能
- [ ] 實作各通知管道
  - [ ] LINE 發送
  - [ ] Email 發送（含 PDF 附件）
  - [ ] 電話通知清單產生
  - [ ] 司機親送請款單列印
- [ ] 實作排程產生待審核通知 (每月 15 日左右)
- [ ] 建立通知歷史紀錄頁面

## Phase 8: 整合與測試

- [ ] 整合測試所有 API
- [ ] UI/UX 調整
- [ ] 錯誤處理完善
- [ ] 載入狀態處理
- [ ] 響應式設計調整

## Phase 9: 部署

- [ ] 設定環境變數
- [ ] 部署 PostgreSQL (Supabase/Neon/Railway)
- [ ] 部署 Next.js (Vercel)
- [ ] 設定 Domain
- [ ] 設定 HTTPS
- [ ] 教育訓練文件
