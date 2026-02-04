## Context

環保回收公司目前使用手動方式處理計費作業，包含紙本或 Excel 記錄交易、手動計算月結金額、人工寄送帳單。公司已有一套網頁排程系統管理收運車次，但計費與排程系統分離，導致資料重複輸入與不一致。

**現況限制：**
- 既有排程系統無法確認是否有 API，初期以 CSV 匯入為主
- 內部行政人員使用，無複雜權限需求
- 回收品項無固定清單，需支援自由輸入

**利害關係人：**
- 內部行政人員（主要使用者）
- 客戶（收到月結帳單）
- 財務部門（未來 ERP 串接）

## Goals / Non-Goals

**Goals:**
- 建立統一的交易紀錄與計費系統
- 自動產生月結報表（依客戶、依趟次）
- 追蹤收付款狀態
- 支援 Email/LINE 通知
- 整合既有排程系統資料

**Non-Goals:**
- 不取代既有排程系統
- 不處理 ERP 立帳（第二階段）
- 不做庫存管理
- 不做路線規劃或車隊管理

## Decisions

### 1. 技術架構

```
┌─────────────────────────────────────────────────────────────┐
│                      Frontend                                │
│              Next.js 14+ (App Router) + TypeScript          │
│                      Tailwind CSS                           │
└─────────────────────┬───────────────────────────────────────┘
                      │ REST API / Server Actions
┌─────────────────────▼───────────────────────────────────────┐
│                      Backend                                 │
│           Next.js API Routes + TypeScript                   │
│                 Zod (Runtime 驗證)                          │
└─────────────────────┬───────────────────────────────────────┘
                      │ Prisma ORM
┌─────────────────────▼───────────────────────────────────────┐
│                     Database                                 │
│                   PostgreSQL                                │
└─────────────────────────────────────────────────────────────┘
```

**技術棧總覽：**
| 層級 | 技術 | 說明 |
|------|------|------|
| Frontend | Next.js 14+ + TypeScript | App Router、Server Components |
| Styling | Tailwind CSS | 快速開發、響應式設計 |
| Backend | Next.js API Routes | 前後端整合、簡化部署 |
| ORM | Prisma | 型別安全、自動產生 TypeScript 型別 |
| 驗證 | Zod | Runtime 型別驗證 |
| Database | PostgreSQL | 關聯式資料、交易支援 |

**選擇理由：**
- TypeScript 全端：前後端共享型別定義，減少錯誤
- Next.js：整合前後端，簡化部署架構
- Prisma：型別安全的 ORM，與 TypeScript 完美整合
- PostgreSQL：關聯式資料適合交易紀錄，支援 Decimal 精確計算

**專案結構：**
```
recycling-billing-system/
├── src/
│   ├── app/              # Next.js App Router
│   │   ├── (dashboard)/  # 主要頁面群組
│   │   ├── api/          # API Routes
│   │   └── layout.tsx
│   ├── components/       # React 元件
│   ├── lib/              # 共用函式
│   │   ├── db.ts         # Prisma client
│   │   └── validations/  # Zod schemas
│   └── types/            # TypeScript 型別
├── prisma/
│   └── schema.prisma     # 資料庫 Schema
└── package.json
```

**金額處理：**
- 使用 PostgreSQL `DECIMAL(10,2)` 儲存金額
- Prisma 會自動對應為 `Decimal` 型別
- 避免浮點數精度問題

### 2. 資料模型

┌──────────────┐       ┌──────────────┐       ┌──────────────┐
│   Customer   │──1:N──│   Contract   │──1:N──│ContractItem  │
│──────────────│       │──────────────│       │──────────────│
│ id           │       │ id           │       │ id           │
│ name         │       │ customer_id  │       │ contract_id  │
│ email        │       │ start_date   │       │ item_name    │
│ line_id      │       │ end_date     │       │ unit_price   │
│ notify_method│       │ type         │       │ price_type   │
└──────────────┘       └──────────────┘       └──────────────┘
│
│ 1:N
▼
┌──────────────┐       ┌──────────────┐
│ Transaction  │──1:N──│TransactionItem│
│──────────────│       │──────────────│
│ id           │       │ id           │
│ customer_id  │       │ transaction_id│
│ date         │       │ item_name    │
│ trip_number  │       │ quantity     │
│ vehicle_no   │       │ unit_price   │
│ driver       │       │ subtotal     │
│ total_amount │       │ price_source │
└──────────────┘       └──────────────┘
│
│ 1:1
▼
┌──────────────┐
│   Payment    │
│──────────────│
│ id           │
│ customer_id  │
│ year_month   │
│ total_receivable│
│ total_payable│
│ status       │
│ paid_date    │
└──────────────┘



### 3. 排程匯入策略

**決定：CSV 匯入優先，預留 API 擴充**

匯入流程：
1. 使用者上傳 CSV（日期、客戶、趟次、車號、司機）
2. 系統比對客戶名稱，建立或關聯
3. 產生交易紀錄骨架（待填入品項）
4. 使用者補填品項、數量、確認價格

### 4. 價格計算邏輯

輸入品項
│
▼
檢查客戶是否有有效合約
│
├── 有 → 檢查該品項是否在合約內
│         │
│         ├── 是 → 使用合約價
│         └── 否 → 手動輸入浮動價
│
└── 無 → 手動輸入浮動價



### 5. 通知系統

**決定：使用第三方服務**
- Email：SendGrid 或 AWS SES
- LINE：LINE Messaging API（功能完整）

**通知觸發點：**
| 事件 | 對象 | 時機 |
|------|------|------|
| 月結帳單產生 | 客戶 + 內部 | 每月 15 日左右（手開發票） |
| 未收款提醒 | 內部 | 超過付款期限 |
| 合約到期提醒 | 內部 | 到期前 30 天 |

**通知發送流程：**
1. 系統產生通知 → 進入「待審核」狀態
2. 使用者人工審核內容
3. 確認後點擊發送（支援批次發送）
4. Email 自動附加 PDF 對帳單

### 6. 報表產生

**決定：後端產生，支援 PDF/Excel**
- PDF：使用 Puppeteer 或 PDFKit
- Excel：使用 ExcelJS

## Risks / Trade-offs

| 風險 | 影響 | 緩解措施 |
|------|------|----------|
| 排程系統無法匯出 CSV | 無法整合 | 提供手動輸入介面作為備案 |
| 客戶名稱不一致 | 匯入比對失敗 | 提供模糊比對 + 人工確認機制 |
| LINE API 限制 | 通知發送失敗 | Email 作為備援通知方式 |
| 品項名稱自由輸入 | 同物不同名 | 未來可加入常用品項建議功能 |

## Migration Plan

**階段 1：核心功能上線**
1. 部署資料庫與後端 API
2. 部署前端應用
3. 匯入既有客戶資料（若有）
4. 教育訓練

**階段 2：通知功能**
1. 串接 Email 服務
2. 串接 LINE 服務
3. 設定自動通知排程

**Rollback 策略：**
- 保留原有作業方式 30 天
- 資料庫每日備份

## Open Questions

1. 既有排程系統的 CSV 匯出格式為何？需要取得範例檔案
2. LINE 通知要用 LINE Notify（免費但功能少）還是 LINE Messaging API（需付費）？
3. ~~月結週期是每月幾號結算？~~ → **已確認：每月 15 日左右**
4. 未收款提醒要在超過幾天後發送？
