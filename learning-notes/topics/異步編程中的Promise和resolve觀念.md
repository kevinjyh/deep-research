Source: src/run.ts (Lines 15-27)
```typescript:src/run.ts#L15-L27
const rl = readline.createInterface({
  input: process.stdin,
  output: process.stdout,
});

// Helper function to get user input
function askQuestion(query: string): Promise<string> {
  return new Promise(resolve => {
    rl.question(query, answer => {
      resolve(answer);
    });
  });
}
```


用更簡單的方式來解釋這個概念。

想象一下，你和朋友在玩一個傳紙條的遊戲：

1. **傳紙條的過程**
   - 你寫了一個問題在紙條上："你喜歡什麼顏色？"
   - 你把紙條交給朋友
   - 你告訴朋友："看到問題後，把你的答案寫在紙條上，然後還給我"
   - 你現在需要等待，直到朋友把紙條還給你

2. **Promise 是什麼**
   - Promise 就像是一個空紙盒，你告訴別人："我這個盒子裡將來會有東西"
   - 現在盒子是空的（pending 狀態），但將來會放入某個東西（resolved 狀態）

3. **在程式中如何運作**
   - `new Promise(resolve => { ... })` 創建了那個空紙盒
   - `resolve` 是一個特殊的功能，它的工作是「往盒子裡放東西」
   - `rl.question(query, callback)` 是向用戶提問，並說「當有答案時，請執行這個 callback」

現在，讓我們結合起來看看原始代碼：

```typescript
function askQuestion(query: string): Promise<string> {
  return new Promise(resolve => {
    rl.question(query, answer => {
      resolve(answer);
    });
  });
}
```

分解來看：

1. `return new Promise(...)` - 創建一個空盒子，並告訴別人「這個盒子將來會有字符串」

2. `resolve =>` - 這是接收一個特殊功能的參數，這個功能叫 `resolve`。它是盒子自帶的「放東西進盒子」的功能。

3. `rl.question(query, ...)` - 向用戶提問

4. `answer => { resolve(answer); }` - 這是告訴系統「當用戶回答了，把答案放進盒子裡」

簡潔版本：
```typescript
function askQuestion(query: string): Promise<string> {
  return new Promise(resolve => {
    rl.question(query, resolve);
  });
}
```

這裡其實是跳過了中間步驟，直接說「當用戶回答了，用 resolve 功能把答案放進盒子」。

**最關鍵的部分**：
- `resolve` 不是調用自己！它是從 Promise 接收的「放東西進盒子」的功能
- 在 `rl.question` 的第二個參數位置，我們需要提供「當有答案時要做什麼」的指示
- 我們的指示是「用 resolve 功能把答案放進盒子」

希望這樣的解釋能夠幫助你理解這個概念。異步編程確實比較抽象，需要時間來適應這種思維方式。