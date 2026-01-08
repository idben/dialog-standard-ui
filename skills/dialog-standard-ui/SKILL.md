---
name: dialog-standard-ui
description: 使用 HTML <dialog> 元素建構現代化網頁互動介面。當使用者提及 alert、confirm、prompt、modal、popup、對話框、彈出視窗、訊息框、確認框、輸入框時自動使用。完全取代傳統的 window.alert()、window.confirm()、window.prompt() 及第三方 modal 套件。提供一致性、無障礙性的 UI 解決方案。
---

# Dialog Standard UI

這個技能專注於使用 HTML 原生的 `<dialog>` 元素來建立所有互動介面，完全取代傳統的 `alert()`、`confirm()`、`prompt()` 以及各種 modal/popup 元件。提供一致性、可訪問性、且現代化的使用者體驗。

## 核心原則

當這個技能啟用時，你必須遵循以下原則：

1. **禁止使用傳統彈出方式**
   - 絕不使用 `window.alert()`、`window.confirm()`、`window.prompt()`
   - 避免使用第三方 modal 套件（如 Bootstrap Modal、jQuery UI Dialog 等）
   - 不使用自定義的 overlay div 模擬 modal

2. **統一使用 `<dialog>` 元素**
   - 所有需要使用者互動的彈出介面都使用 `<dialog>` 元素
   - 使用 `showModal()` 方法顯示模態對話框（帶遮罩層）
   - 使用 `show()` 方法顯示非模態對話框（無遮罩層）
   - 使用 `close()` 方法關閉對話框

3. **保持可訪問性**
   - 對話框必須包含適當的 ARIA 標籤
   - 支援鍵盤操作（Enter 確認、Escape 取消）
   - 焦點管理要符合無障礙標準
   - 提供清晰的標題和說明文字

4. **一致的視覺設計**
   - 建立標準化的樣式系統
   - 確保不同類型的對話框（訊息、確認、輸入）有一致的外觀
   - 使用現代化的 CSS 設計（backdrop、動畫、響應式）

## 實作指南

### 基本結構

每個 `<dialog>` 元素應包含：
- 標題區塊（`<h2>` 或 `<h3>`）
- 內容區塊（訊息、表單等）
- 操作按鈕區塊（確認、取消等）

### 對話框類型

1. **訊息對話框（取代 alert）**
   - 單一確認按鈕
   - 顯示訊息或通知
   - 自動聚焦於確認按鈕

2. **確認對話框（取代 confirm）**
   - 確認和取消兩個按鈕
   - 返回 Promise 以處理使用者選擇
   - 支援自定義按鈕文字

3. **輸入對話框（取代 prompt）**
   - 包含表單輸入欄位
   - 驗證使用者輸入
   - 返回 Promise 以取得輸入值

4. **複雜表單對話框**
   - 支援多個表單欄位
   - 包含驗證邏輯
   - 可以包含多步驟流程

### JavaScript API 設計

建議提供簡潔的 API：

```javascript
// 訊息對話框
await dialog.alert('操作成功！');

// 確認對話框
const confirmed = await dialog.confirm('確定要刪除這個項目嗎？');

// 輸入對話框
const name = await dialog.prompt('請輸入您的名字：');

// 自定義對話框
await dialog.show({
  title: '標題',
  content: '內容',
  buttons: ['確定', '取消']
});
```

### CSS 樣式規範

#### 自動置中機制（重要！）

當使用 `showModal()` 時，瀏覽器會自動將對話框置中顯示，**不需要**手動設定 `position`、`top`、`left` 等 CSS 屬性。這是透過以下預設樣式實現的：

```css
/* 瀏覽器對 dialog 的預設樣式 */
dialog {
  position: fixed;
  inset: 0;              /* 等同於 top: 0; right: 0; bottom: 0; left: 0; */
  margin: auto;          /* 關鍵！自動計算邊距實現水平和垂直置中 */
  width: fit-content;    /* 自適應內容寬度 */
  height: fit-content;   /* 自適應內容高度 */
}
```

**重點說明：**
- `inset: 0` 將對話框的四個邊都設為 0
- `margin: auto` 會自動計算上下左右的邊距，實現完美置中
- 這個機制讓你無需手動計算位置，對話框會自動在視窗中央顯示

**實作範例：**
```css
/* ✅ 正確：明確設定自動置中所需的屬性 */
dialog {
  position: fixed;
  inset: 0;
  margin: auto;

  /* 可以設定寬高限制 */
  max-width: 90vw;
  width: 480px;
  max-height: 90vh;
}

/* ❌ 錯誤：省略關鍵屬性或使用手動置中 */
dialog {
  /* 缺少 position, inset, margin - 可能導致置中失敗 */
  width: 480px;
}

dialog {
  /* 使用舊式手動置中方式會破壞自動置中機制 */
  position: fixed;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
}
```

#### 樣式設計原則

- **必須明確設定自動置中所需的屬性**：`position: fixed;`、`inset: 0;`、`margin: auto;`
- 使用 `::backdrop` 偽元素設計遮罩層背景
- 提供開啟/關閉動畫效果（使用 `@starting-style` 和 `transition`）
- 確保在各種螢幕尺寸下都能正常顯示
- 支援深色/淺色主題
- **不要用其他值覆寫自動置中屬性**（例如 `top: 50%`、`left: 50%`、`transform: translate(-50%, -50%)` 等手動置中方式）
- 可以設定 `max-width`、`max-height` 來限制對話框大小

### 事件處理

- 監聽 `close` 事件處理對話框關閉
- 使用 `returnValue` 屬性傳遞結果
- 正確處理 Escape 鍵關閉行為（`showModal()` 預設支援）
- 支援點擊 backdrop 關閉（可選）

### 焦點管理

- 使用 `showModal()` 時，焦點會自動移至對話框內第一個可聚焦元素
- 使用 `autofocus` 屬性指定預設聚焦的元素
- **不要**在 `<dialog>` 元素本身加上 `tabindex` 屬性
- 對話框關閉後，焦點會自動返回觸發元素

```html
<dialog>
  <h2>確認刪除</h2>
  <p>此操作無法復原</p>
  <button autofocus>取消</button>  <!-- 預設聚焦在取消按鈕 -->
  <button>確定刪除</button>
</dialog>
```

### 動畫效果

使用現代 CSS 為對話框添加開啟/關閉動畫：

```css
/* 開啟狀態的樣式 */
dialog[open] {
  opacity: 1;
  transform: scale(1);
}

/* 關閉狀態的樣式 */
dialog {
  opacity: 0;
  transform: scale(0.9);
  transition:
    opacity 0.3s ease-out,
    transform 0.3s ease-out,
    display 0.3s allow-discrete,
    overlay 0.3s allow-discrete;
}

/* 定義初始狀態 */
@starting-style {
  dialog[open] {
    opacity: 0;
    transform: scale(0.9);
  }
}

/* 背景遮罩動畫 */
dialog::backdrop {
  background-color: rgba(0, 0, 0, 0.5);
  transition:
    background-color 0.3s ease-out,
    overlay 0.3s allow-discrete;
}

@starting-style {
  dialog[open]::backdrop {
    background-color: rgba(0, 0, 0, 0);
  }
}
```

## 使用範例

### 範例 1: 簡單訊息通知
```javascript
// 取代: alert('資料已儲存');
await showDialog({
  type: 'alert',
  message: '資料已儲存',
  title: '成功'
});
```

### 範例 2: 確認操作
```javascript
// 取代: if (confirm('確定要離開嗎？')) { ... }
const confirmed = await showDialog({
  type: 'confirm',
  message: '確定要離開嗎？您的變更將不會被儲存。',
  title: '確認離開'
});
if (confirmed) {
  // 執行離開操作
}
```

### 範例 3: 使用者輸入
```javascript
// 取代: const name = prompt('請輸入名稱：');
const name = await showDialog({
  type: 'prompt',
  message: '請輸入專案名稱：',
  placeholder: '我的專案',
  validate: (value) => value.length > 0
});
```

### 範例 4: 複雜表單
```javascript
const result = await showDialog({
  type: 'form',
  title: '新增使用者',
  fields: [
    { name: 'username', label: '使用者名稱', type: 'text', required: true },
    { name: 'email', label: '電子郵件', type: 'email', required: true },
    { name: 'role', label: '角色', type: 'select', options: ['管理員', '使用者'] }
  ]
});
```

## 技術要求

- 使用原生 HTML5 `<dialog>` 元素
- 確保瀏覽器相容性（提供 polyfill 建議）
- 使用現代 JavaScript（ES6+、async/await、Promise）
- CSS 使用 Flexbox/Grid 進行佈局
- 支援行動裝置觸控操作
- 程式碼需要模組化且可重用

## 品質檢查清單

實作時確保：
- [ ] 完全不使用 `alert()`、`confirm()`、`prompt()`
- [ ] 所有彈出介面都使用 `<dialog>` 元素
- [ ] 鍵盤操作完全正常（Tab、Enter、Escape）
- [ ] 焦點管理符合無障礙標準
- [ ] 提供適當的 ARIA 標籤
- [ ] 樣式一致且美觀
- [ ] 支援響應式設計
- [ ] API 簡潔易用
- [ ] 程式碼有適當的錯誤處理
- [ ] 提供完整的文件和註解

## 注意事項

### 顯示方式的重要區別

- **正確做法**：使用 `dialog.showModal()` 方法顯示模態對話框
  - 會自動置中顯示
  - 自動添加背景遮罩（`::backdrop`）
  - 焦點被鎖定在對話框內
  - 支援 ESC 鍵關閉

- **錯誤做法**：不要使用 HTML `open` 屬性來顯示模態對話框
  ```html
  <!-- ❌ 錯誤：不會有遮罩層，不會置中 -->
  <dialog open>內容</dialog>
  ```
  - 使用 `open` 屬性只會創建非模態對話框
  - 不會有背景遮罩
  - 不會自動置中
  - 焦點不會被鎖定

### 其他注意事項

- 舊版瀏覽器可能需要 polyfill（Safari < 15.4）
- 確保 `<dialog>` 元素正確加入 DOM 後再呼叫 `showModal()`
- 避免巢狀對話框造成的 z-index 問題
- **必須在 CSS 中明確設定自動置中所需的三個關鍵屬性**：`position: fixed;`、`inset: 0;`、`margin: auto;`（不要省略或依賴瀏覽器預設值）
- **不要用其他值覆寫這些屬性**，例如使用 `top: 50%; left: 50%; transform: translate(-50%, -50%);` 等手動置中方式會破壞自動置中機制
- 記得在對話框關閉後清理資源（如事件監聽器）
- 考慮使用者體驗，避免過度使用彈出視窗
- **不要在 `<dialog>` 元素本身添加 `tabindex` 屬性**

---

## 📦 完整實作程式碼

當使用者需要建立 Dialog Standard UI 時，根據以下條件讀取對應的 reference 檔案來生成程式碼。

### Reference 檔案說明

本 skill 提供三個 reference 檔案，包含完整的實作程式碼：

#### 1. dialog-css.md
**檔案路徑**：`.claude/skills/dialog-standard-ui/dialog-css.md`
**讀取時機**：
- 使用者要求建立 Dialog Standard UI 時（無論哪個版本都需要）
- 需要樣式檔案時
- **必讀**：所有情況都需要讀取此檔案

**內容**：完整的 `dialog.css` 樣式程式碼（300 行）

---

#### 2. dialog-traditional.md
**檔案路徑**：`.claude/skills/dialog-standard-ui/dialog-traditional.md`
**讀取時機**：
- 使用者選擇「傳統版本」或「全域變數版本」
- 使用者要求用 `<script src="dialog.js"></script>` 方式引入
- 不需要打包工具的簡單頁面
- 明確要求使用全域 `dialog` 變數

**內容**：
- 完整的 `dialog.js` 程式碼（266 行，使用 IIFE）
- 傳統版本的示範頁面 HTML

---

#### 3. dialog-module.md
**檔案路徑**：`.claude/skills/dialog-standard-ui/dialog-module.md`
**讀取時機**：
- 使用者選擇「ES Module 版本」或「模組版本」
- 使用者要求用 `import` 方式引入
- 用於 Vue、React、Angular 等現代框架
- 需要打包工具（Vite、Webpack、Rollup）的專案
- 使用者明確要求模組化

**內容**：
- 完整的 `dialog-module.js` 程式碼（270 行，使用 export default）
- ES Module 版本的示範頁面 HTML
- Vue 使用範例
- React 使用範例

---


## 🎯 使用說明

### 建立檔案的標準流程

當使用者要求建立 Dialog Standard UI 時，按照以下流程操作：

#### 步驟 1：詢問使用者選擇

在開始建立檔案前，先詢問使用者：
1. **版本選擇**：傳統版本（全域變數）還是 ES Module 版本？
2. **示範頁面**：是否需要 demo.html 示範頁面？
3. **UI 框架**：是否整合 Shoelace 組件？

#### 步驟 2：讀取對應的 reference 檔案

根據使用者的選擇，讀取對應的檔案：

1. **必讀**：`dialog-css.md`
   - 所有情況都需要讀取
   - 生成 `dialog.css` 檔案

2. **擇一**：根據版本選擇讀取
   - 傳統版本：讀取 `dialog-traditional.md`，生成 `dialog.js`
   - ES Module：讀取 `dialog-module.md`，生成 `dialog-module.js`

3. **選用**：如需示範頁面
   - 從對應的 reference 檔案中取得示範 HTML
   - 根據是否整合 Shoelace 調整 HTML 內容

#### 步驟 3：生成檔案

按照以下順序建立檔案：
1. 先建立 `dialog.css`（從 `dialog-css.md` 複製）
2. 再建立 `dialog.js` 或 `dialog-module.js`（從對應 reference 複製）
3. 如需示範，建立 `demo.html` 或 `demo-module.html`

#### 步驟 4：測試驗證

確認：
- 對話框能正確顯示且置中
- 所有按鈕功能正常
- 鍵盤操作（Tab、Enter、Escape）正常
- 樣式符合 Shoelace 設計風格

---

### 📋 決策樹

```
使用者要求建立 Dialog Standard UI
    │
    ├─→ 詢問：要哪個版本？
    │      ├─→ 傳統版本
    │      │     ├─→ 讀取 dialog-css.md
    │      │     ├─→ 讀取 dialog-traditional.md
    │      │     └─→ 生成 dialog.css + dialog.js
    │      │
    │      └─→ ES Module 版本
    │            ├─→ 讀取 dialog-css.md
    │            ├─→ 讀取 dialog-module.md
    │            └─→ 生成 dialog.css + dialog-module.js
    │
    ├─→ 詢問：需要示範頁面嗎？
    │      ├─→ 是：從 reference 取得示範 HTML
    │      └─→ 否：只生成核心檔案
    │
    └─→ 詢問：整合 Shoelace 嗎？
           ├─→ 是：在 HTML 中加入 Shoelace CDN
           └─→ 否：使用原生 HTML 元素
```

---

### 工作流程

1. **識別需求**：當使用者提及 alert、confirm、prompt、modal、對話框等關鍵字時啟動
2. **詢問選擇**：版本（傳統/ES Module）、示範頁面、Shoelace 整合
3. **讀取 reference 檔案**：
   - 必讀：`dialog-css.md`
   - 擇一：`dialog-traditional.md` 或 `dialog-module.md`
4. **生成檔案**：
   - 從 reference 檔案複製完整程式碼
   - 按需調整 HTML（Shoelace 整合）
5. **測試驗證**：確認對話框正確顯示且置中

### 重要提醒

- **從 reference 檔案複製完整程式碼**，不要省略任何部分
- **CSS 必須包含自動置中的三個關鍵屬性**：`position: fixed;`、`inset: 0;`、`margin: auto;`
- **傳統版本使用 IIFE 包裝**，ES Module 版本使用 `export default`
- **Shoelace 是選用的**，但使用時能提供更統一的 UI 體驗
- **所有對話框都使用 `showModal()` 方法**，不使用 `open` 屬性
- **不要手動撰寫程式碼**，一律從 reference 檔案複製以確保完整性
