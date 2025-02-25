## TypeScript 中的解構賦值（Destructuring Assignment）結合參數類型定義的寫法

Source: src/feedback.ts (Lines 7-13)
```typescript:src/feedback.ts#L7-L13
export async function generateFeedback({
  query,
  numQuestions = 3,
}: {
  query: string;
  numQuestions?: number;
}) { 
// 函數代碼...
}
```

這個寫法可以拆解為幾個部分：

1. `{ query, numQuestions = 3 }` - 這是參數的解構賦值部分
   - 允許從一個物件中提取特定的屬性作為參數
   - `numQuestions = 3` 設定了預設值

2. `: { query: string; numQuestions?: number; }` - 這是 TypeScript 的型別定義部分
   - 定義了一個物件型別
   - `query` 必須是字串
   - `numQuestions` 是可選的數字（注意 `?` 符號）

這種寫法的好處是：

1. **命名參數**：不需要記住參數的順序，可以用名稱來傳遞參數
2. **可選參數**：很容易處理可選參數，不用寫多個函式多載
3. **預設值**：可以優雅地設定預設值
4. **型別安全**：TypeScript 可以確保傳入的參數型別正確

使用範例：
```typescript
// 使用預設值
await generateFeedback({ query: "你的問題" });

// 指定所有參數
await generateFeedback({ query: "你的問題", numQuestions: 5 });
```

這比傳統的參數寫法更具可讀性和維護性，特別是當函式有多個可選參數時。


## index.d.ts 型別定義檔

Source: node_modules\ai\dist\index.d.ts/feedback.ts (Lines 938)
```typescript:node_modules\ai\dist\index.d.ts/feedback.ts#L938
declare function generateObject<OBJECT>(options: Omit<CallSettings, 'stopSequences'> & Prompt & {
// 型另宣告...
}
```

讓我解釋這段程式碼的幾個關鍵部分：

1. `declare` 關鍵字：
- 這是在型別定義檔案（.d.ts）中使用的關鍵字
- 用於告訴 TypeScript 這個函式存在，但實作在別處
- `.d.ts` 檔案主要用於提供型別資訊，不包含實際實作
- 這是一種型別宣告（Type Declaration）的方式

1. `Omit<CallSettings, 'stopSequences'>`:
- `Omit` 是 TypeScript 的內建工具型別（Utility Type）
- 作用是從型別中移除特定的屬性
- 這裡是從 `CallSettings` 型別中移除 'stopSequences' 屬性
- 例如：
````typescript
interface Person {
  name: string;
  age: number;
  address: string;
}

// 結果會是 { name: string; address: string; }
type PersonWithoutAge = Omit<Person, 'age'>;
````

1. `&` 運算子：
- 在 TypeScript 型別系統中代表交集型別（Intersection Types）
- 將多個型別合併成一個新型別
- 新型別會包含所有來源型別的所有屬性
- 例如：
````typescript
type A = { x: string };
type B = { y: number };
type C = A & B;  // { x: string; y: number; }
````

所以整個參數型別 `Omit<CallSettings, 'stopSequences'> & Prompt & { ... }` 的意思是：
1. 取得 `CallSettings` 型別但排除 'stopSequences' 屬性
2. 與 `Prompt` 型別合併
3. 再與花括號內定義的額外屬性合併

這樣的型別設計可以：
- 重用既有型別
- 精確控制需要的屬性
- 組合多個型別定義
- 確保型別安全


## feedback.ts - generateFeedback 函數解說

這是 TypeScript 中常見的物件參數模式。

在 `generateFeedback` 中的 `generateObject` 呼叫：
```typescript
const userFeedback = await generateObject({
    model: o3MiniModel,        // 必要參數：指定使用的語言模型
    system: systemPrompt(),     // 來自 Prompt 型別的參數
    prompt: `Given ...`,        // 來自 Prompt 型別的參數
    schema: z.object({...}),    // 必要參數：定義輸出結構
});
```

這個呼叫對應到型別定義：
```typescript
declare function generateObject<OBJECT>(options: 
    Omit<CallSettings, 'stopSequences'> &  // 第一部分型別
    Prompt &                               // 第二部分型別（包含 system 和 prompt）
    {                                      // 第三部分型別
        model: LanguageModel;
        schema: z.Schema<OBJECT>;
        // ... 其他可選參數
    }
): Promise<GenerateObjectResult<OBJECT>>;
```

這種設計有幾個優點：
1. **彈性**：可以只傳入需要的參數，其他使用預設值
2. **可讀性**：參數名稱明確，不用記住參數順序
3. **型別安全**：TypeScript 會檢查所有必要參數是否都有提供
4. **擴展性**：未來要增加新參數很容易，不會破壞現有的程式碼

實際上，雖然型別定義看起來很複雜，但使用時就像傳入一個普通的設定物件一樣簡單。TypeScript 會在編譯時期確保物件的結構符合要求。


## reportProgress 函式

Source: src\deep-research.ts (Lines 187 - 190)
```typescript:src\deep-research.ts#L187-L190
  const reportProgress = (update: Partial<ResearchProgress>) => {
    Object.assign(progress, update);
    onProgress?.(progress);
  };
```

這段代碼是一個用於報告研究進度的函式，讓我為您詳細解析：

1. **函式簽名**：
```typescript
(update: Partial<ResearchProgress>) => void
```

- `update` 參數使用 `Partial<ResearchProgress>` 型別
- `Partial` 是 TypeScript 的內建工具型別，表示所有屬性都是可選的
- 這意味著可以只傳入部分進度更新，而不需要提供完整的 `ResearchProgress` 物件

1. **函式實作**：
```typescript
Object.assign(progress, update);
```

- 使用 `Object.assign` 將更新合併到現有的 `progress` 物件中
- 這會將 `update` 的屬性複製到 `progress` 物件
- 如果 `progress` 中已有相同屬性，會被 `update` 的值覆蓋
- 如果 `progress` 中沒有該屬性，則會新增

1. **回調呼叫**：
```typescript
onProgress?.(progress);
```

- 使用可選鏈（Optional Chaining）呼叫 `onProgress` 回調
- 如果 `onProgress` 存在，則呼叫它並傳入更新後的 `progress`
- 如果 `onProgress` 是 `undefined`，則不會執行任何操作

使用範例：
```typescript
interface ResearchProgress {
  completed: number;
  total: number;
  status: string;
}

let progress: ResearchProgress = {
  completed: 0,
  total: 100,
  status: 'Starting'
};

// 只更新完成數量
reportProgress({ completed: 10 });

// 只更新狀態
reportProgress({ status: 'Processing' });

// 更新多個屬性
reportProgress({ completed: 50, status: 'Halfway' });
```


這段程式碼的優點：
1. **彈性**：可以只更新部分進度
2. **型別安全**：TypeScript 會檢查傳入的屬性是否符合 `ResearchProgress` 的型別
3. **可選回調**：`onProgress` 是可選的，使用時更靈活
4. **物件合併**：使用 `Object.assign` 可以輕鬆更新物件狀態

這樣的設計非常適合用於追蹤和報告長時間運行的任務進度。


## 何謂 SERP queries

Source: src\deep-research.ts (Lines 45 - 87)
```typescript:src\deep-research.ts#L45-L87
// take en user query, return a list of SERP queries
async function generateSerpQueries({//...參數
}) {
//...函數本體
}
```

SERP 是 Search Engine Results Page（搜尋引擎結果頁面）的縮寫。SERP queries 指的是用來在搜尋引擎中獲取結果的查詢關鍵字。讓我用具體例子說明：

假設使用者輸入的原始查詢是：
```text
"如何學習 TypeScript"
```


`generateSerpQueries` 函式可能會生成以下 SERP queries：
```typescript
[
  "TypeScript 學習指南",
  "TypeScript 入門教學",
  "TypeScript vs JavaScript 差異",
  "TypeScript 最佳實踐",
  "TypeScript 線上課程推薦"
]
```

這些衍生的查詢用於：
1. 擴大搜尋範圍，獲取更多相關資訊
2. 從不同角度探索主題
3. 獲取不同類型的資源（教學、比較、課程等）
4. 提高搜尋結果的覆蓋率

生成 SERP queries 的常見策略：
1. **同義詞替換**：例如 "學習" → "教學"、"指南"
2. **問題擴展**：例如 "如何..." → "為什麼..."、"何時使用..."
3. **領域特定術語**：例如加入 "最佳實踐"、"常見錯誤" 等專業術語
4. **格式變化**：例如加入 "PDF"、"影片"、"GitHub 專案" 等格式關鍵字
5. **地域化調整**：例如加入 "台灣"、"中文" 等地域關鍵字

在程式中的應用：
```typescript
async function generateSerpQueries({
  query, // 原始使用者查詢
  numQueries = 5, // 預設生成 5 個查詢
  language = 'zh-TW' // 預設使用繁體中文
}) {
  // 使用 LLM 生成多個搜尋查詢
  // 返回格式例如：{ queries: string[] }
}
```

這樣設計的好處：
1. 提高搜尋結果的多樣性
2. 避免單一查詢的偏見
3. 增加找到高質量結果的機率
4. 支援多語言搜尋最佳化


