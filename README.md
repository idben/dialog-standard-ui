# Dialog Standard UI

[![GitHub](https://img.shields.io/badge/GitHub-idben%2Fdialog--standard--ui-blue?logo=github)](https://github.com/idben/dialog-standard-ui)
[![License](https://img.shields.io/badge/License-MIT-green.svg)](https://github.com/idben/dialog-standard-ui/blob/main/LICENSE)
[![Claude Code Skill](https://img.shields.io/badge/Claude_Code-Skill-purple)](https://github.com/idben/dialog-standard-ui)

使用 HTML 原生 `<dialog>` 元素建構的現代化對話框元件庫，完全取代傳統的 `alert()`、`confirm()`、`prompt()`。

## 📥 快速安裝

### 方法 1：使用 Plugin 指令安裝（推薦）

在 Claude Code 中執行：

```bash
# 步驟 1：添加 marketplace
/plugin marketplace add idben/dialog-standard-ui

# 步驟 2：安裝 plugin（建議使用 --scope user，所有專案都能使用）
/plugin install dialog-standard-ui@dialog-standard-ui-marketplace --scope user

# 或使用互動式介面
/plugin
# 然後在 Discover 分頁中找到並點擊安裝
```

> ⚠️ **安裝範圍說明**
>
> | 範圍 | 指令 | 說明 |
> |------|------|------|
> | **user**（推薦） | `--scope user` | 使用者級別，所有專案都能使用 |
> | project | `--scope project` | 僅限當前專案，其他專案需重新安裝 |
> | local | `--scope local` | 本地級別，不會被 git 追蹤 |
>
> **已知問題**：`--scope project` 目前可能無法正確限制範圍（[#14202](https://github.com/anthropics/claude-code/issues/14202)），建議使用 `--scope user` 明確全域安裝。

### 方法 2：手動複製到個人目錄

```bash
# 1. 克隆專案
git clone https://github.com/idben/dialog-standard-ui.git

# 2. 複製 skill 到個人目錄
cp -r dialog-standard-ui/skills/dialog-standard-ui ~/.claude/skills/

# 3. 重新啟動 Claude Code
```

### 方法 3：克隆專案直接使用

```bash
# 克隆專案
git clone https://github.com/idben/dialog-standard-ui.git
cd dialog-standard-ui

# 啟動 Claude Code（skill 會自動載入）
claude
```

## ✨ 特色

- ✅ 使用原生 `<dialog>` 元素，符合 Web 標準
- ✅ 完全取代 `window.alert()`, `window.confirm()`, `window.prompt()`
- ✅ 支援 Promise/async-await，程式碼更簡潔
- ✅ 提供兩種版本：傳統版本與 ES Module 版本
- ✅ Shoelace 風格：純色、扁平化設計
- ✅ 完整的無障礙支援（ARIA、鍵盤操作、焦點管理）
- ✅ 現代化動畫效果
- ✅ 響應式設計
- ✅ 深色模式支援
- ✅ 零依賴，僅需引入 CSS

## 📦 檔案說明

```
├── dialog.js           # 傳統版本（全域變數）
├── dialog-module.js    # ES Module 版本
├── dialog.css          # 樣式檔案（共用）
├── index.html           # 完整示範（傳統版本）
└── demo-module.html    # ES Module 示範
```

## 🚀 使用方式

### 🤖 方式 0：使用 Claude Code Skill 自動生成（推薦）

本專案提供 Claude Code skill，可以自動生成所有檔案和程式碼。

**使用步驟**：

1. **克隆或下載此專案**（包含 `skills/dialog-standard-ui/` 目錄）

2. **在專案目錄中啟動 Claude Code**
   ```bash
   cd agent-skill-01
   claude
   ```

3. **觸發 skill**

   在對話中提及以下任一關鍵字即可自動啟動：
   ```
   alert、confirm、prompt、modal、popup、對話框、彈出視窗、訊息框、確認框、輸入框
   ```

   範例對話：
   ```
   你：你有什麼 skill?
   Claude：(從對話中看看有無 dialog-standard-ui 的字樣)
   你：幫我建立一個 alert 對話框系統
   Claude：我會使用 Dialog Standard UI skill 來建立...
   ```

4. **選擇版本**

   Claude 會詢問你：
   - 要使用傳統版本還是 ES Module 版本？
   - 是否需要示範頁面？
   - 是否整合 Shoelace 組件？

5. **自動生成檔案**

   Claude 會根據你的選擇生成：
   - `dialog.css`（樣式檔案）
   - `dialog.js` 或 `dialog-module.js`（邏輯檔案）
   - `demo.html` 或 `demo-module.html`（示範頁面，選用）

**優點**：
- ✅ 自動生成完整、正確的程式碼
- ✅ 根據需求客製化（版本、示範頁面、UI 框架）
- ✅ 包含最佳實踐和完整文檔
- ✅ 避免手動複製貼上錯誤

---

### 方式 1：傳統引入（全域變數）

適用於簡單頁面或不支援 ES Module 的環境。

```html
<!DOCTYPE html>
<html lang="zh-TW">
<head>
  <link rel="stylesheet" href="dialog.css">
</head>
<body>
  <button onclick="showDialog()">顯示對話框</button>

  <script src="dialog.js"></script>
  <script>
    async function showDialog() {
      await dialog.alert('Hello World!');
    }
  </script>
</body>
</html>
```

### 方式 2：ES Module 引入

適用於現代瀏覽器和打包工具（Webpack, Vite, Rollup）。

```html
<!DOCTYPE html>
<html lang="zh-TW">
<head>
  <link rel="stylesheet" href="dialog.css">
</head>
<body>
  <button id="btn">顯示對話框</button>

  <script type="module">
    import dialog from './dialog-module.js';

    document.getElementById('btn').addEventListener('click', async () => {
      await dialog.alert('Hello World!');
    });
  </script>
</body>
</html>
```

## 📚 API 文件

### dialog.alert(message, title?)

顯示訊息對話框（取代 `window.alert()`）。

**參數**:
- `message` (string) - 訊息內容，支援 HTML
- `title` (string, 選填) - 標題，預設為「提示」

**返回**: `Promise<void>`

**範例**:
```javascript
// 基本用法
await dialog.alert('操作成功！');

// 自訂標題
await dialog.alert('資料已儲存', '成功');

// HTML 內容
await dialog.alert('<strong>粗體</strong><br>換行文字');
```

### dialog.confirm(message, title?, options?)

顯示確認對話框（取代 `window.confirm()`）。

**參數**:
- `message` (string) - 訊息內容
- `title` (string, 選填) - 標題，預設為「確認」
- `options` (object, 選填):
  - `confirmText` (string) - 確認按鈕文字，預設為「確定」
  - `cancelText` (string) - 取消按鈕文字，預設為「取消」

**返回**: `Promise<boolean>` - `true` 表示確認，`false` 表示取消

**範例**:
```javascript
// 基本用法
const confirmed = await dialog.confirm('確定要刪除嗎？');
if (confirmed) {
  console.log('使用者確認刪除');
}

// 自訂按鈕文字
const result = await dialog.confirm(
  '確定要送出嗎？',
  '確認',
  { confirmText: '送出', cancelText: '再想想' }
);
```

### dialog.prompt(message, placeholder?, title?, options?)

顯示輸入對話框（取代 `window.prompt()`）。

**參數**:
- `message` (string) - 訊息內容
- `placeholder` (string, 選填) - 輸入框提示文字
- `title` (string, 選填) - 標題，預設為「輸入」
- `options` (object, 選填):
  - `defaultValue` (string) - 預設值
  - `validate` (function) - 驗證函數，返回 `true` 表示通過，返回字串表示錯誤訊息

**返回**: `Promise<string|null>` - 輸入的值，或 `null`（取消時）

**範例**:
```javascript
// 基本用法
const name = await dialog.prompt('請輸入您的名字：', '王小明');
if (name) {
  console.log('輸入的名字：', name);
}

// 帶驗證
const email = await dialog.prompt(
  '請輸入 Email：',
  '',
  '輸入',
  {
    validate: (value) => {
      if (!value) return '請輸入 Email';
      if (!/^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(value)) {
        return 'Email 格式不正確';
      }
      return true;
    }
  }
);
```

### dialog.custom(options)

顯示自訂對話框。

**參數**:
- `options` (object):
  - `title` (string) - 標題
  - `content` (string) - 內容（支援 HTML）
  - `buttons` (array) - 按鈕陣列，每個按鈕包含：
    - `text` (string) - 按鈕文字
    - `primary` (boolean) - 是否為主要按鈕
  - `className` (string, 選填) - 自訂 CSS class

**返回**: `Promise<object>` - 包含 `buttonIndex` 和 `button` 屬性

**範例**:
```javascript
const result = await dialog.custom({
  title: '選擇操作',
  content: '請選擇要執行的操作：',
  buttons: [
    { text: '取消', primary: false },
    { text: '儲存', primary: false },
    { text: '送出', primary: true }
  ]
});

console.log('選擇的按鈕索引：', result.buttonIndex);
console.log('選擇的按鈕：', result.button);
```

## ⌨️ 鍵盤操作

- **Tab**: 在對話框內的元素間切換焦點
- **Enter**: 確認操作（聚焦於按鈕時）或送出輸入（輸入框）
- **Escape**: 關閉對話框
- **Space**: 按下聚焦的按鈕

## ♿ 無障礙功能

- ✅ **ARIA 標籤**: 所有對話框包含適當的 `role` 和 `aria-modal` 屬性
- ✅ **焦點管理**: 開啟時自動聚焦，關閉後返回觸發元素
- ✅ **焦點鎖定**: 焦點被限制在對話框內
- ✅ **語意化標記**: 使用正確的 HTML 元素
- ✅ **鍵盤完全可操作**: 所有功能都可透過鍵盤完成
- ✅ **深色模式支援**: 自動適應系統深色模式
- ✅ **動畫偏好**: 尊重 `prefers-reduced-motion` 設定

## 🎨 樣式自訂

所有樣式都定義在 `dialog.css` 中，可以透過覆寫 CSS 變數或 class 來自訂：

```css
/* 自訂主色調 */
.std-dialog__btn--primary {
  background: #your-color;
}

/* 自訂圓角 */
.std-dialog {
  border-radius: 12px;
}

/* 自訂陰影 */
.std-dialog {
  box-shadow: 0 4px 16px rgba(0, 0, 0, 0.2);
}
```

## 🌐 瀏覽器支援

- ✅ Chrome 37+
- ✅ Edge 79+
- ✅ Firefox 98+
- ✅ Safari 15.4+

對於舊版瀏覽器，需要使用 [dialog-polyfill](https://github.com/GoogleChrome/dialog-polyfill)。

## 📝 範例頁面

- **index.html**: 完整功能展示（傳統版本）
- **demo-module.html**: ES Module 用法展示

## 🔧 在打包工具中使用

### Vite

```javascript
import dialog from './dialog-module.js';
import './dialog.css';

await dialog.alert('Hello World!');
```

### Webpack

```javascript
import dialog from './dialog-module.js';
import './dialog.css';

await dialog.alert('Hello World!');
```

## 🎯 在 Vue/React 中使用

ES Module 版本 (`dialog-module.js`) 是標準的 JavaScript 模組，完全相容 Vue 和 React。

### 在 Vue 3 中使用

```vue
<template>
  <div>
    <button @click="handleAlert">顯示訊息</button>
    <button @click="handleConfirm">確認操作</button>
    <button @click="handlePrompt">輸入資料</button>
  </div>
</template>

<script setup>
import dialog from './dialog-module.js'
import './dialog.css'

const handleAlert = async () => {
  await dialog.alert('操作成功！', '成功')
}

const handleConfirm = async () => {
  const confirmed = await dialog.confirm('確定要刪除嗎？', '確認')
  if (confirmed) {
    console.log('使用者確認刪除')
  }
}

const handlePrompt = async () => {
  const name = await dialog.prompt('請輸入您的名字：', '王小明', '輸入')
  if (name) {
    console.log('使用者輸入：', name)
  }
}
</script>
```

### 在 React 中使用

```jsx
import { useState } from 'react'
import dialog from './dialog-module.js'
import './dialog.css'

function App() {
  const handleAlert = async () => {
    await dialog.alert('操作成功！', '成功')
  }

  const handleConfirm = async () => {
    const confirmed = await dialog.confirm('確定要刪除嗎？', '確認')
    if (confirmed) {
      console.log('使用者確認刪除')
    }
  }

  const handlePrompt = async () => {
    const name = await dialog.prompt('請輸入您的名字：', '王小明', '輸入')
    if (name) {
      console.log('使用者輸入：', name)
    }
  }

  return (
    <div>
      <button onClick={handleAlert}>顯示訊息</button>
      <button onClick={handleConfirm}>確認操作</button>
      <button onClick={handlePrompt}>輸入資料</button>
    </div>
  )
}

export default App
```

### 在 Vue 2 中使用

```vue
<template>
  <div>
    <button @click="handleAlert">顯示訊息</button>
    <button @click="handleConfirm">確認操作</button>
  </div>
</template>

<script>
import dialog from './dialog-module.js'
import './dialog.css'

export default {
  methods: {
    async handleAlert() {
      await dialog.alert('操作成功！', '成功')
    },
    async handleConfirm() {
      const confirmed = await dialog.confirm('確定要刪除嗎？', '確認')
      if (confirmed) {
        console.log('使用者確認刪除')
      }
    }
  }
}
</script>
```

### 關鍵優勢

✅ **標準 ES Module**：無需額外轉換，直接 import
✅ **框架無關**：純 JavaScript + 原生 `<dialog>`，不依賴任何框架
✅ **零衝突**：不會與 Vue/React 的虛擬 DOM 衝突，因為 dialog 元素直接掛載到 `document.body`
✅ **Promise API**：完美搭配 async/await，符合現代開發習慣
✅ **TypeScript 友善**：可輕鬆加入型別定義檔 (.d.ts)

### SSR 注意事項

如果使用 Nuxt 或 Next.js 等 SSR 框架：

**Nuxt 3:**
```vue
<script setup>
import dialog from './dialog-module.js'

// 確保只在 client-side 執行
const handleClick = async () => {
  if (process.client) {
    await dialog.alert('操作成功！')
  }
}
</script>
```

**Next.js:**
```jsx
'use client'  // 標記為 Client Component

import dialog from './dialog-module.js'

export default function MyComponent() {
  const handleClick = async () => {
    await dialog.alert('操作成功！')
  }

  return <button onClick={handleClick}>顯示對話框</button>
}
```

## 🔌 作為 Claude Code Plugin 使用

本專案已配置為 Claude Code Plugin，可以透過以下方式安裝：

### 方法 1：從 GitHub 安裝

```bash
# 使用 Claude Code 安裝
claude --plugin https://github.com/idben/dialog-standard-ui
```

### 方法 2：複製到個人技能目錄

```bash
# 克隆專案
git clone https://github.com/idben/dialog-standard-ui.git

# 複製 skill 到個人目錄
cp -r dialog-standard-ui/skills/dialog-standard-ui ~/.claude/skills/
```

### 方法 3：加入到 Marketplace

如果你想建立自己的 marketplace 或加入現有的 marketplace：

1. **使用此專案的 marketplace.json**
   ```bash
   # 使用本專案的 marketplace
   claude --marketplace https://raw.githubusercontent.com/idben/dialog-standard-ui/main/.claude-plugin/marketplace.json
   ```

2. **或加入到你的 marketplace.json**
   ```json
   {
     "plugins": [
       {
         "name": "dialog-standard-ui",
         "source": "https://github.com/idben/dialog-standard-ui"
       }
     ]
   }
   ```

### Plugin 結構

本專案包含完整的 plugin 配置：

```
dialog-standard-ui/
├── .claude-plugin/
│   ├── plugin.json          # Plugin 元數據
│   └── marketplace.json     # Marketplace 配置
├── skills/                   # Skills 資料夾（符合官方規範）
│   └── dialog-standard-ui/
│       ├── SKILL.md                 # 主要 skill 定義
│       ├── dialog-css.md            # CSS 程式碼 reference
│       ├── dialog-traditional.md    # 傳統版本 reference
│       └── dialog-module.md         # ES Module 版本 reference
└── README.md
```

---

## 📄 授權

MIT License - 詳見 [LICENSE](LICENSE) 檔案

本專案基於 `/dialog-standard-ui` skill 建構。

## 🤝 貢獻

歡迎提交 Issue 或 Pull Request！

---

🎨 **設計風格**: 採用 Shoelace 風格的扁平化設計
📦 **技術棧**: 原生 JavaScript, HTML5 `<dialog>`, CSS3
⚡ **零依賴**: 無需任何第三方函式庫
