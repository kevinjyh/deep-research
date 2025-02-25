我是一個從未學過 TypeScript 語法但曾寫過 Chrome Extension 的 javasript，以及一些 Python 小型程式的經驗。
我現在想要藉由研究這個 TyepScript 專案所有的代碼功能，請你建議我的整體研究路線圖

以您的背景，我為您規劃一個循序漸進的 TypeScript 專案學習路線圖：

# TypeScript 專案學習路線圖

## 第一階段：基礎設施理解（2-3天）

### 1. 專案配置文件
首先從這些配置文件開始，因為它們決定了專案的基本架構：

```typescript:tsconfig.json
{
  "compilerOptions": {
    // 這裡定義了 TypeScript 的編譯選項
  }
}
```

需要理解的文件：
- `package.json`：專案依賴和指令
- `tsconfig.json`：TypeScript 配置
- `.prettierrc`：代碼格式化規則
- `docker-compose.yml`：容器配置

重點：
- 先不要深入理解每個配置項
- 著重了解這些文件的基本作用
- 特別關注 `package.json` 中的 `scripts` 部分

## 第二階段：核心概念學習（3-4天）

### 1. TypeScript 類型系統
從簡單的文件開始：

```typescript:src/ai/providers.ts
const openai = createOpenAI({
  apiKey: process.env.OPENAI_KEY!,
  baseURL: process.env.OPENAI_ENDPOINT || 'https://api.openai.com/v1',
} as CustomOpenAIProviderSettings);
```

學習重點：
- 基本類型定義
- 型別註解
- 介面（Interface）
- 泛型（Generics）

### 2. 非同步處理
研究 `src/run.ts` 中的非同步操作：

```typescript
async function run() {
  const initialQuery = await askQuestion('What would you like to research? ');
  // ...
}
```

重點：
- `async/await` 語法
- Promise 處理
- 錯誤處理

## 第三階段：專案架構分析（4-5天）

### 1. 核心功能模組
按照以下順序研究各個模組：

1. **輸入處理**
```typescript:src/run.ts
// 研究用戶輸入如何被處理
```

2. **AI 整合**
```typescript:src/ai/providers.ts
// 了解 AI 服務如何被整合
```

3. **研究引擎**
```typescript:src/deep-research.ts
// 研究核心邏輯
```

4. **輸出管理**
```typescript:src/output-manager.ts
// 了解結果如何輸出
```

## 第四階段：進階功能研究（5-7天）

### 1. 文本處理
研究 `src/ai/text-splitter.ts`：
- 文本分割邏輯
- 遞迴處理方法

### 2. 進度追蹤
分析 `src/progress-manager.ts`：
- 進度條實現
- 終端輸出控制

### 3. 測試用例
研究 `src/ai/text-splitter.test.ts`：
- 單元測試編寫
- 測試用例設計

## 第五階段：實作練習（1-2週）

### 1. 小型功能擴展
建議的練習項目：
1. 添加新的進度顯示方式
2. 實現結果快取功能
3. 添加新的輸出格式

### 2. 代碼重構
嘗試將 Class 改寫為 Function：
```typescript
// 從
export class ProgressManager {
  // ...
}

// 改寫為
export function createProgressManager() {
  // ...
}
```

## 學習建議

1. **循序漸進**
- 先運行專案
- 逐步修改配置
- 觀察變化

2. **實踐為主**
- 每學習一個概念就嘗試修改代碼
- 製作自己的分支進行實驗
- 保持提交記錄

3. **重點關注**
- TypeScript 型別系統
- 非同步程式設計
- 函數式程式設計
- 模組化設計

4. **參考資源**
- TypeScript 官方文檔
- JavaScript 到 TypeScript 遷移指南
- 函數式程式設計教程

5. **開發工具**
- 使用 VS Code
- 安裝 TypeScript 相關擴展
- 設置代碼提示

這個學習路線圖預計需要 3-4 週完成，具體時間可根據您的學習進度調整。建議每天固定時間學習，並在 GitHub 上記錄學習過程。
