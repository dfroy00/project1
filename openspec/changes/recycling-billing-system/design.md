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

**客戶 (Customer)**
```
┌────────────────────┐
│      Customer      │
│────────────────────│
│ id                 │
│ short_name         │  ← 簡稱（廠商名稱）
│ erp_name           │  ← ERP 名稱
│ payment_type       │  ← 收付款類型（收/付）
│ requires_invoice   │  ← 是否開發票
│ invoice_company    │  ← 發票開立公司別
│ invoice_title      │  ← 發票抬頭
│ tax_id             │  ← 統編
│ payment_terms      │  ← 付款期限（次月10前/月結60天等）
│ trip_unit_price    │  ← 車趟單價
│ collection_freq    │  ← 清運頻率
│ notify_method      │  ← 通知方式（LINE/MAIL/電話/司機親送）
│ line_id            │  ← LINE ID
│ email              │
│ phone              │
│ mailing_address    │  ← 寄送地址
│ notes              │  ← 備註
│ created_at         │
│ updated_at         │
└────────────────────┘
```

**附加費用設定 (CustomerFee)**
```
┌────────────────────┐
│    CustomerFee     │
│────────────────────│
│ id                 │
│ customer_id        │
│ fee_name           │  ← 費用名稱（保麗龍、冷盤、PE膜、大白桶等）
│ fee_type           │  ← 計費方式（per_month/per_trip/per_vehicle）
│ unit_price         │  ← 單價
└────────────────────┘
```

**合約 (Contract) - 適用於有簽約客戶**
```
┌────────────────────┐       ┌────────────────────┐
│     Contract       │──1:N──│   ContractItem     │
│────────────────────│       │────────────────────│
│ id                 │       │ id                 │
│ customer_id        │       │ contract_id        │
│ start_date         │       │ item_name          │
│ end_date           │       │ unit_price         │
│ type (年約/季約)    │       │ price_type (+/-)   │
│ status             │       └────────────────────┘
└────────────────────┘
```

**交易紀錄 (Transaction)**
```
┌────────────────────┐       ┌────────────────────┐
│    Transaction     │──1:N──│  TransactionItem   │
│────────────────────│       │────────────────────│
│ id                 │       │ id                 │
│ customer_id        │       │ transaction_id     │
│ date               │       │ item_name          │
│ trip_number        │       │ quantity           │
│ vehicle_no         │       │ unit_price         │
│ driver             │       │ subtotal           │
│ total_amount       │       │ price_source       │
│ payment_type (+/-) │       │ (contract/floating)│
└────────────────────┘       └────────────────────┘
```

**月結收付款 (Payment)**
```
┌────────────────────┐
│      Payment       │
│────────────────────│
│ id                 │
│ customer_id        │
│ year_month         │
│ total_receivable   │  ← 應收
│ total_payable      │  ← 應付
│ net_amount         │  ← 結餘
│ status             │  ← 未收款/已收款
│ due_date           │  ← 應付日期（依客戶付款期限計算）
│ paid_date          │
│ paid_amount        │
└────────────────────┘
```



### 3. 排程匯入策略

**決定：CSV 匯入優先，預留 API 擴充**

**既有客戶資料 CSV 格式（已確認）：**
| 欄位 | 說明 | 範例 |
|------|------|------|
| 廠商名稱 | 客戶簡稱 | 王子製藥 |
| zcar/ERP名稱 | ERP 系統名稱 | 王子製藥-新埔廠 |
| 收付款類型 | 收/付 | 收 |
| 是否開發票 | 是/否 | 是 |
| 發票開立公司別 | 開票公司 | 新竹 |
| 款項支付方式 | 付款期限 | 匯款 (次月10前) |
| 車趟單價 | 每趟費用 | 2000 |
| 保麗龍費用 | 附加費 | 保麗龍500/月 |
| 冷盤費用 | 附加費 | 冷盤500/月 |
| 其他附加費用 | 其他費用 | PE膜2500/車 |
| 清運頻率 | 收運週期 | 每週二.五 |
| 款項通知方式 | 通知管道 | LINE/MAIL/電話/司機親送 |
| LINE_Notify_Token | LINE Token | - |
| Email | 電子郵件 | - |
| 電話 | 聯絡電話 | - |
| 發票抬頭 | 發票名稱 | 王子製藥(股)公司 |
| 統編 | 統一編號 | 35048803 |
| 寄送地址 | 帳單地址 | - |
| 付款期限 | 同款項支付方式 | - |
| 備註 | 其他說明 | - |

**匯入流程：**
1. 使用者上傳 CSV（日期、客戶、趟次、車號、司機）
2. 系統比對客戶名稱（廠商名稱 或 ERP名稱），建立或關聯
3. 產生交易紀錄骨架（待填入品項）
4. 系統自動帶入客戶的車趟單價與附加費用
5. 使用者補填品項、數量、確認價格

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

**客戶通知方式（依既有資料）：**
| 方式 | 說明 |
|------|------|
| LINE | 透過 LINE Messaging API 發送 |
| MAIL | 透過 Email 發送（可附加 PDF） |
| 電話 | 系統產生通知，人工電話聯繫 |
| 司機親送 | 系統產生請款單，由司機親自送達 |

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
5. 司機親送：列印請款單供司機攜帶

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

1. ~~既有排程系統的 CSV 匯出格式為何？需要取得範例檔案~~ → **已確認：客戶資料 CSV 格式已取得**
2. ~~LINE 通知要用 LINE Notify 還是 LINE Messaging API？~~ → **已確認：LINE Messaging API**
3. ~~月結週期是每月幾號結算？~~ → **已確認：每月 15 日左右**
4. 未收款提醒要在超過幾天後發送？
5. 排程系統的車趟 CSV 格式為何？（日期、客戶、趟次、車號、司機等欄位順序）
