# 技術文件 - 專案管理儀表板系統

## 文件版本
- **版本**: 1.0
- **最後更新**: 2025-11-08
- **維護單位**: 開發團隊

---

## 目錄
1. [系統概述](#系統概述)
2. [檔案結構說明](#檔案結構說明)
3. [資料結構定義](#資料結構定義)
4. [處理邏輯說明](#處理邏輯說明)
5. [部署與維護](#部署與維護)

---

## 系統概述

### 系統目的
本系統為專案管理儀表板，用於追蹤和管理多個硬體專案的問題（Issues）。系統提供以下核心功能：
- 專案概覽與篩選
- 問題追蹤與管理
- 跨專案問題分析
- 團隊工作負載統計
- 問題趨勢圖表

### 技術架構
- **前端框架**: React 19
- **建置工具**: Vite 7.2
- **UI 框架**: Tailwind CSS 3.4
- **圖表庫**: Recharts 3.4
- **圖示庫**: Lucide React

### 目標使用者
- 專案經理（PM）
- 工程團隊主管
- 品質保證（QA）團隊
- 高階管理層

---

## 檔案結構說明

### 1. 專案根目錄

```
my-project-dashboard/
├── src/                    # 原始碼目錄
├── public/                 # 靜態資源
├── index.html             # HTML 入口檔案
├── package.json           # 專案依賴設定
├── vite.config.js         # Vite 建置設定
├── tailwind.config.js     # Tailwind CSS 設定
├── postcss.config.js      # PostCSS 設定
└── eslint.config.js       # ESLint 程式碼檢查設定
```

### 2. 原始碼結構 (src/)

```
src/
├── main.jsx                      # 應用程式入口
├── App.jsx                       # 主要應用程式組件（路由控制）
├── ProjectDashboard.jsx          # 專案儀表板頁面
├── IssueManagement.jsx           # 問題管理頁面
│
├── components/                   # React 組件
│   ├── ui/                       # 基礎 UI 組件
│   │   ├── button.jsx           # 按鈕組件
│   │   ├── card.jsx             # 卡片組件
│   │   ├── input.jsx            # 輸入框組件
│   │   └── select.jsx           # 下拉選單組件
│   │
│   ├── IssueFilters.jsx         # 問題篩選器組件
│   ├── IssueStatistics.jsx      # 問題統計組件
│   └── IssueTable.jsx           # 問題表格組件
│
├── data/                         # 資料定義
│   ├── projects.js              # 專案資料（CPU/GPU 規格）
│   └── issues.js                # 分組問題資料
│
├── features/                     # 功能模組
│   └── issues/                  # 問題管理功能
│       ├── data.js              # 問題範例資料
│       └── utils.js             # 問題處理工具函數
│
└── lib/                          # 工具函式庫
    └── utils.js                 # 通用工具函數
```

### 3. 核心檔案說明

#### 3.1 應用程式層級

| 檔案 | 說明 | 主要功能 |
|------|------|----------|
| `main.jsx` | React 應用程式入口 | 初始化 React DOM，掛載根組件 |
| `App.jsx` | 主應用程式組件 | 視圖路由控制、頁面切換邏輯 |
| `index.html` | HTML 模板 | 定義應用程式容器、載入 JavaScript |

#### 3.2 頁面組件

| 檔案 | 說明 | 主要功能 |
|------|------|----------|
| `ProjectDashboard.jsx` | 專案儀表板 | 專案卡片展示、CPU/GPU 篩選、Common Issues 顯示 |
| `IssueManagement.jsx` | 問題管理頁面 | 單一專案的詳細問題列表、趨勢圖表、相關問題分析 |

#### 3.3 資料檔案

| 檔案 | 說明 | 資料類型 |
|------|------|----------|
| `data/projects.js` | 專案規格資料 | 專案名稱、CPU 平台、GPU 型號、年份 |
| `data/issues.js` | 分組問題資料 | 依問題類別分組的 Issue 列表 |
| `features/issues/data.js` | 問題範例資料 | 單一專案的 Issue 範例、團隊顏色、圖表資料 |

#### 3.4 工具函數

| 檔案 | 說明 | 提供的函數 |
|------|------|-----------|
| `features/issues/utils.js` | 問題處理工具 | `getWeeksAgo()`, `filterIssues()`, `sortIssues()` |
| `lib/utils.js` | 通用工具 | CSS 類別合併等通用功能 |

---

## 資料結構定義

### 1. 專案資料結構 (projects.js)

```javascript
{
  name: String,           // 專案代號（如 "ZKA", "ZKB"）
  cpu: [                  // CPU 配置（陣列，支援多個選項）
    {
      series: String,     // CPU 系列（如 "Intel Raptor Lake"）
      platform: String    // CPU 平台（如 "Intel Raptor Lake H"）
    }
  ],
  gpu: [                  // GPU 配置（陣列，支援多個選項）
    {
      series: String,     // GPU 系列（如 "GN22"）
      model: String       // GPU 型號（如 "GN22-X4"）
    }
  ],
  year: Number            // 專案年份（如 2023, 2024, 2025）
}
```

**資料範例**:
```javascript
{
  name: 'ZKA',
  cpu: [
    { series: 'Intel Raptor Lake', platform: 'Intel Raptor Lake H' },
    { series: 'Intel Raptor Lake', platform: 'Intel Raptor Lake H Refresh' }
  ],
  gpu: [
    { series: 'GN22', model: 'GN22-X4' },
    { series: 'GN23', model: 'GN23-X6' }
  ],
  year: 2023
}
```

### 2. 問題資料結構 (issues.js)

#### 2.1 分組問題結構
```javascript
{
  group: String,          // 問題群組名稱
  summary: String,        // 群組摘要描述
  issues: [               // 該群組的問題列表
    {
      project: String,    // 專案代號
      id: String,         // 問題 ID（格式：ISSUE-XXX）
      description: String,// 問題描述
      failureRate: Number,// 失敗率（0-10）
      team: String,       // 負責團隊
      status: String,     // 狀態："Open" 或 "Closed"
      openDate: String    // 開啟日期（格式：YYYY-MM-DD）
    }
  ]
}
```

**資料範例**:
```javascript
{
  group: 'Thermal throttling / fan behavior',
  summary: 'Thermal issues related to throttling and fan curve abnormality',
  issues: [
    {
      project: 'ZKB',
      id: 'ISSUE-102',
      description: 'Thermal throttling on Meteor Lake under sustained load',
      failureRate: 8,
      team: 'Thermal',
      status: 'Open',
      openDate: '2025-09-18'
    }
  ]
}
```

### 3. 團隊定義

```javascript
const teams = ['EE', 'ME', 'Thermal', 'Power', 'Driver', 'BIOS', 'EC'];

const teamColors = {
  EE: '#2563eb',        // 藍色
  ME: '#f97316',        // 橙色
  Thermal: '#22c55e',   // 綠色
  Power: '#a855f7',     // 紫色
  Driver: '#ef4444',    // 紅色
  BIOS: '#0ea5e9',      // 天藍色
  EC: '#94a3b8'         // 灰藍色
};
```

**團隊說明**:
- **EE**: Electrical Engineering（電機工程）
- **ME**: Mechanical Engineering（機械工程）
- **Thermal**: 散熱團隊
- **Power**: 電源管理團隊
- **Driver**: 驅動程式團隊
- **BIOS**: BIOS 韌體團隊
- **EC**: Embedded Controller（嵌入式控制器）團隊

### 4. 圖表資料結構

```javascript
{
  week: String,          // 週次範圍（如 "2025-09-14 ~ 2025-09-20"）
  [team]: Number,        // 各團隊當前 Issue 數量
  [team]_opened: Number, // 各團隊本週新增 Issue 數量
  [team]_closed: Number  // 各團隊本週關閉 Issue 數量
}
```

**資料範例**:
```javascript
{
  week: '2025-09-14 ~ 2025-09-20',
  EE: 3, EE_opened: 2, EE_closed: 1,
  ME: 2, ME_opened: 1, ME_closed: 0,
  Thermal: 2, Thermal_opened: 1, Thermal_closed: 1,
  Power: 1, Power_opened: 1, Power_closed: 0,
  Driver: 2, Driver_opened: 2, Driver_closed: 0,
  BIOS: 1, BIOS_opened: 1, BIOS_closed: 0,
  EC: 2, EC_opened: 1, EC_closed: 0
}
```

---

## 處理邏輯說明

### 1. 專案儀表板邏輯 (ProjectDashboard.jsx)

#### 1.1 專案篩選邏輯

**篩選條件**:
1. **關鍵字搜尋**: 依專案名稱模糊搜尋
2. **CPU 篩選**: 依選定的 CPU 平台篩選
3. **GPU 篩選**: 依選定的 GPU 型號篩選
4. **專案選取**: 被選中的專案始終顯示（即使不符合其他篩選條件）

**篩選規則**:
```javascript
if (selectedCPUs.length > 0 && selectedGPUs.length > 0) {
  // 交集模式：必須同時符合 CPU 和 GPU
  return keywordOK && cpuOK && gpuOK;
} else if (selectedCPUs.length > 0) {
  // 只看 CPU
  return keywordOK && cpuOK;
} else if (selectedGPUs.length > 0) {
  // 只看 GPU
  return keywordOK && gpuOK;
} else {
  // 無篩選，只看關鍵字
  return keywordOK;
}
```

#### 1.2 Common Issues 顯示邏輯

**區塊篩選規則**:
- 如果某個 Issue 群組中**有任何一個 Issue 符合篩選條件**，則顯示該群組的**所有 Issues**
- 符合條件的 Issue 會以**藍色背景高亮**顯示

**高亮條件**:
1. Issue 所屬專案符合 CPU/GPU 篩選
2. Issue 所屬專案在已選專案列表中，或
3. Issue 的內容（ID/描述）符合搜尋關鍵字

#### 1.3 動態 CPU/GPU 選項

系統會根據當前可見的專案動態生成 CPU 和 GPU 選項：
- **初始狀態**: 顯示所有可能的 CPU/GPU 選項
- **選定 GPU 後**: CPU 選項只顯示與已選 GPU 搭配的型號
- **選定 CPU 後**: GPU 選項只顯示與已選 CPU 搭配的型號

### 2. 問題管理邏輯 (IssueManagement.jsx)

#### 2.1 篩選器

**支援的篩選條件**:
| 篩選器 | 選項 | 說明 |
|--------|------|------|
| Team | All / EE / ME / Thermal / Power / Driver / BIOS / EC | 依負責團隊篩選 |
| Week Range | All / Within 1 week / 1-2 weeks / 2-4 weeks / Over 4 weeks | 依開啟時間篩選 |
| Status | All / Open / Closed | 依問題狀態篩選 |
| Keyword | 自由文字 | 搜尋 Issue ID 或描述 |

#### 2.2 Top 10 vs All Issues

**Top 10 模式**:
- 只顯示**狀態為 Open** 的 Issues
- 依 **failureRate 由高到低**排序
- 取前 **10 筆**

**All Issues 模式**:
- 顯示所有 Issues（包含 Open 和 Closed）
- 依用戶選定的排序鍵排序

#### 2.3 週數計算邏輯

```javascript
function getWeeksAgo(today, dateString) {
  const date = new Date(dateString);
  const diffMs = today - date;
  return Math.floor(diffMs / (1000 * 60 * 60 * 24 * 7));
}
```

#### 2.4 相關問題分析

**觸發條件**: 點擊任何 Issue ID

**顯示內容**:
1. 被選中 Issue 的詳細資訊
2. 相關問題列表（示範資料）
3. 相關問題的團隊分布統計（Top 3）

**統計邏輯**:
```javascript
const relatedTeamStats = relatedIssues.reduce((acc, issue) => {
  acc[issue.team] = (acc[issue.team] || 0) + 1;
  return acc;
}, {});

const topTeams = Object.entries(relatedTeamStats)
  .map(([team, count]) => ({
    team,
    percent: ((count / total) * 100).toFixed(1)
  }))
  .sort((a, b) => b.percent - a.percent)
  .slice(0, 3);
```

### 3. 圖表顯示邏輯

#### 3.1 趨勢圖表 (LineChart)

**資料來源**: `chartData` 陣列（最近 5 週）

**顯示內容**:
- X 軸: 週次範圍
- Y 軸: Issue 數量（自動刻度）
- 線條: 每個團隊一條線（7 條線）

**互動功能**:
1. **Hover Tooltip**: 顯示該週各團隊的詳細數據
   - Current: 當前 Issue 數量
   - Opened: 本週新增數量
   - Closed: 本週關閉數量

2. **Legend 長按高亮**: 長按某團隊名稱，該團隊線條加粗並顯示數據點

#### 3.2 Y 軸刻度自動計算

```javascript
// 根據最大值動態決定刻度間隔
let tickStep;
if (maxIssueCount <= 3) tickStep = 1;
else if (maxIssueCount <= 10) tickStep = 2;
else if (maxIssueCount <= 25) tickStep = 5;
else if (maxIssueCount <= 50) tickStep = 10;
else if (maxIssueCount <= 100) tickStep = 20;
else tickStep = Math.ceil(maxIssueCount / 5 / 10) * 10;

const maxRounded = Math.max(tickStep * 2, Math.ceil(maxIssueCount / tickStep) * tickStep);
```

### 4. 排序邏輯

```javascript
function sortIssues(data, key, asc = true) {
  const arr = [...data];
  const dir = asc ? 1 : -1;

  return arr.sort((a, b) => {
    if (key === 'failureRate')
      return (a[key] - b[key]) * dir;

    if (key === 'openDate')
      return (new Date(a[key]) - new Date(b[key])) * dir;

    const av = (a[key] ?? '').toString();
    const bv = (b[key] ?? '').toString();
    return av.localeCompare(bv) * dir;
  });
}
```

**支援的排序鍵**:
- `id`: 字串排序
- `description`: 字串排序
- `team`: 字串排序
- `failureRate`: 數字排序
- `openDate`: 日期排序
- `status`: 字串排序

---

## 部署與維護

### 1. 開發環境設定

```bash
# 安裝依賴
npm install

# 啟動開發伺服器
npm run dev

# 檢查程式碼品質
npm run lint
```

### 2. 生產環境建置

```bash
# 建置生產版本
npm run build

# 預覽建置結果
npm run preview
```

建置完成後，產出檔案位於 `dist/` 資料夾。

### 3. 資料更新流程

#### 3.1 新增專案

在 `src/data/projects.js` 中新增專案物件：

```javascript
{
  name: '新專案代號',
  cpu: [
    { series: 'CPU 系列', platform: 'CPU 平台' }
  ],
  gpu: [
    { series: 'GPU 系列', model: 'GPU 型號' }
  ],
  year: 2025
}
```

#### 3.2 新增問題

在 `src/data/issues.js` 中找到對應的群組，新增 Issue：

```javascript
{
  project: '專案代號',
  id: 'ISSUE-XXX',
  description: '問題描述',
  failureRate: 7,
  team: 'EE',
  status: 'Open',
  openDate: '2025-11-08'
}
```

如需新增問題群組：

```javascript
{
  group: '新群組名稱',
  summary: '群組摘要',
  issues: [
    // Issue 列表
  ]
}
```

#### 3.3 更新圖表資料

在 `src/features/issues/data.js` 的 `chartData` 陣列中新增週次資料：

```javascript
{
  week: '2025-11-09 ~ 2025-11-15',
  EE: 5, EE_opened: 3, EE_closed: 2,
  ME: 4, ME_opened: 2, ME_closed: 1,
  // ... 其他團隊
}
```

### 4. 常見維護任務

#### 4.1 修改團隊顏色

修改 `src/features/issues/data.js` 中的 `teamColors`：

```javascript
export const teamColors = {
  EE: '#新的顏色碼',
  // ...
};
```

#### 4.2 調整篩選選項

修改 `src/features/issues/data.js` 中的 `teams` 陣列：

```javascript
export const teams = ['EE', 'ME', 'Thermal', '新團隊'];
```

#### 4.3 修改時間範圍選項

在 `IssueManagement.jsx` 中修改 `weekOptions`：

```javascript
const weekOptions = [
  { label: 'All Weeks', value: 'All' },
  { label: '新的時間範圍', value: 'custom' },
  // ...
];
```

並在 `filterIssues()` 函數中新增對應的邏輯。

### 5. 效能優化建議

1. **大量資料處理**:
   - 目前使用 `useMemo` 和 `useDeferredValue` 進行效能優化
   - 如 Issue 數量超過 1000 筆，建議實作虛擬滾動（Virtual Scrolling）

2. **圖表效能**:
   - 圖表資料已限制為最近 5 週
   - 如需顯示更長時間範圍，建議實作資料抽樣或分頁

3. **快取策略**:
   - 專案和 Issue 資料建議實作 Service Worker 快取
   - 圖表資料可考慮 localStorage 暫存

### 6. 故障排除

| 問題 | 可能原因 | 解決方法 |
|------|----------|----------|
| 專案卡片不顯示 | 資料格式錯誤 | 檢查 `projects.js` 格式是否正確 |
| 篩選器無反應 | 資料鍵值不符 | 確認 Issue 的 `team` 值在 `teams` 陣列中 |
| 圖表顯示異常 | chartData 格式錯誤 | 確認每週資料包含所有團隊的數值 |
| 相關問題不顯示 | project 不存在 | 確認 Issue 的 `project` 值在 `projects.js` 中存在 |

---

## 聯絡資訊

如有技術問題或需要協助，請聯絡：
- **開發團隊**: [請填寫聯絡方式]
- **技術文件維護**: [請填寫聯絡方式]

---

**文件結束**
