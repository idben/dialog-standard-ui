# dialog.js - 傳統版本（全域變數）

**用途**：使用 IIFE 包裝的傳統版本，適合簡單頁面或不支援 ES Module 的環境

**何時使用此檔案**：
- 使用者選擇「傳統版本」或「全域變數版本」
- 使用者要求用 `<script src="dialog.js"></script>` 方式引入
- 不需要打包工具的簡單頁面
- 需要全域 `dialog` 變數的場景

**引入方式**：
```html
<script src="dialog.js"></script>
<script>
  // 直接使用全域變數 dialog
  await dialog.alert('訊息');
</script>
```

---

## 完整程式碼

```javascript
/**
 * Dialog Standard UI - 通用對話框元件庫
 * 使用原生 <dialog> 元素取代傳統 alert/confirm/prompt
 */

const dialog = (() => {
  // 私有方法：建立基礎 dialog 元素
  function createDialogElement(type) {
    const dialogEl = document.createElement('dialog');
    dialogEl.className = `std-dialog std-dialog--${type}`;
    dialogEl.setAttribute('role', 'dialog');
    dialogEl.setAttribute('aria-modal', 'true');
    return dialogEl;
  }

  // 私有方法：清理 dialog 元素
  function cleanupDialog(dialogEl) {
    dialogEl.close();
    // 等待動畫完成後移除元素
    setTimeout(() => {
      dialogEl.remove();
    }, 300);
  }

  // 私有方法：處理 Escape 鍵關閉
  function handleEscapeClose(dialogEl, reject) {
    dialogEl.addEventListener('cancel', (e) => {
      e.preventDefault();
      cleanupDialog(dialogEl);
      reject(new Error('Dialog cancelled'));
    });
  }

  /**
   * 訊息對話框（取代 alert）
   * @param {string} message - 訊息內容
   * @param {string} title - 標題（選填，預設為「提示」）
   * @returns {Promise<void>}
   */
  function alert(message, title = '提示') {
    return new Promise((resolve) => {
      const dialogEl = createDialogElement('alert');

      dialogEl.innerHTML = `
        <div class="std-dialog__header">
          <h2 class="std-dialog__title">${title}</h2>
        </div>
        <div class="std-dialog__body">
          <p class="std-dialog__message">${message}</p>
        </div>
        <div class="std-dialog__footer">
          <button class="std-dialog__btn std-dialog__btn--primary" autofocus>確定</button>
        </div>
      `;

      document.body.appendChild(dialogEl);

      const confirmBtn = dialogEl.querySelector('.std-dialog__btn--primary');

      confirmBtn.addEventListener('click', () => {
        cleanupDialog(dialogEl);
        resolve();
      });

      dialogEl.showModal();
    });
  }

  /**
   * 確認對話框（取代 confirm）
   * @param {string} message - 訊息內容
   * @param {string} title - 標題（選填，預設為「確認」）
   * @param {Object} options - 選項 { confirmText, cancelText }
   * @returns {Promise<boolean>} - true: 確認, false: 取消
   */
  function confirm(message, title = '確認', options = {}) {
    const { confirmText = '確定', cancelText = '取消' } = options;

    return new Promise((resolve, reject) => {
      const dialogEl = createDialogElement('confirm');

      dialogEl.innerHTML = `
        <div class="std-dialog__header">
          <h2 class="std-dialog__title">${title}</h2>
        </div>
        <div class="std-dialog__body">
          <p class="std-dialog__message">${message}</p>
        </div>
        <div class="std-dialog__footer">
          <button class="std-dialog__btn std-dialog__btn--secondary" autofocus>${cancelText}</button>
          <button class="std-dialog__btn std-dialog__btn--primary">${confirmText}</button>
        </div>
      `;

      document.body.appendChild(dialogEl);

      const cancelBtn = dialogEl.querySelector('.std-dialog__btn--secondary');
      const confirmBtn = dialogEl.querySelector('.std-dialog__btn--primary');

      cancelBtn.addEventListener('click', () => {
        cleanupDialog(dialogEl);
        resolve(false);
      });

      confirmBtn.addEventListener('click', () => {
        cleanupDialog(dialogEl);
        resolve(true);
      });

      handleEscapeClose(dialogEl, () => resolve(false));

      dialogEl.showModal();
    });
  }

  /**
   * 輸入對話框（取代 prompt）
   * @param {string} message - 訊息內容
   * @param {string} placeholder - 輸入框提示文字（選填）
   * @param {string} title - 標題（選填，預設為「輸入」）
   * @param {Object} options - 選項 { defaultValue, validate }
   * @returns {Promise<string|null>} - 輸入的值或 null（取消時）
   */
  function prompt(message, placeholder = '', title = '輸入', options = {}) {
    const { defaultValue = '', validate = null } = options;

    return new Promise((resolve, reject) => {
      const dialogEl = createDialogElement('prompt');

      dialogEl.innerHTML = `
        <div class="std-dialog__header">
          <h2 class="std-dialog__title">${title}</h2>
        </div>
        <div class="std-dialog__body">
          <p class="std-dialog__message">${message}</p>
          <input
            type="text"
            class="std-dialog__input"
            placeholder="${placeholder}"
            value="${defaultValue}"
            autofocus
          />
          <p class="std-dialog__error" style="display: none;"></p>
        </div>
        <div class="std-dialog__footer">
          <button class="std-dialog__btn std-dialog__btn--secondary">取消</button>
          <button class="std-dialog__btn std-dialog__btn--primary">確定</button>
        </div>
      `;

      document.body.appendChild(dialogEl);

      const inputEl = dialogEl.querySelector('.std-dialog__input');
      const errorEl = dialogEl.querySelector('.std-dialog__error');
      const cancelBtn = dialogEl.querySelector('.std-dialog__btn--secondary');
      const confirmBtn = dialogEl.querySelector('.std-dialog__btn--primary');

      // 驗證函數
      function validateInput() {
        const value = inputEl.value.trim();

        if (validate && typeof validate === 'function') {
          const validationResult = validate(value);
          if (validationResult !== true) {
            errorEl.textContent = validationResult || '輸入無效';
            errorEl.style.display = 'block';
            return false;
          }
        }

        errorEl.style.display = 'none';
        return true;
      }

      // 確定按鈕
      confirmBtn.addEventListener('click', () => {
        if (validateInput()) {
          const value = inputEl.value.trim();
          cleanupDialog(dialogEl);
          resolve(value);
        }
      });

      // 取消按鈕
      cancelBtn.addEventListener('click', () => {
        cleanupDialog(dialogEl);
        resolve(null);
      });

      // Enter 鍵提交
      inputEl.addEventListener('keypress', (e) => {
        if (e.key === 'Enter') {
          e.preventDefault();
          confirmBtn.click();
        }
      });

      handleEscapeClose(dialogEl, () => resolve(null));

      dialogEl.showModal();
    });
  }

  /**
   * 自定義對話框
   * @param {Object} options - 自定義選項
   * @returns {Promise<any>}
   */
  function custom(options = {}) {
    const {
      title = '提示',
      content = '',
      buttons = [{ text: '確定', primary: true }],
      className = ''
    } = options;

    return new Promise((resolve, reject) => {
      const dialogEl = createDialogElement('custom');
      if (className) {
        dialogEl.classList.add(className);
      }

      const buttonsHtml = buttons.map((btn, index) => {
        const btnClass = btn.primary ? 'std-dialog__btn--primary' : 'std-dialog__btn--secondary';
        const autofocusAttr = index === 0 ? 'autofocus' : '';
        return `<button class="std-dialog__btn ${btnClass}" data-index="${index}" ${autofocusAttr}>${btn.text}</button>`;
      }).join('');

      dialogEl.innerHTML = `
        <div class="std-dialog__header">
          <h2 class="std-dialog__title">${title}</h2>
        </div>
        <div class="std-dialog__body">
          <div class="std-dialog__content">${content}</div>
        </div>
        <div class="std-dialog__footer">
          ${buttonsHtml}
        </div>
      `;

      document.body.appendChild(dialogEl);

      const btnElements = dialogEl.querySelectorAll('.std-dialog__btn');
      btnElements.forEach((btnEl) => {
        btnEl.addEventListener('click', () => {
          const index = parseInt(btnEl.dataset.index);
          cleanupDialog(dialogEl);
          resolve({ buttonIndex: index, button: buttons[index] });
        });
      });

      handleEscapeClose(dialogEl, reject);

      dialogEl.showModal();
    });
  }

  // 公開 API
  return {
    alert,
    confirm,
    prompt,
    custom
  };
})();
```

---

## 示範頁面範例

```html
<!DOCTYPE html>
<html lang="zh-TW">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Dialog Standard UI 示範</title>

  <!-- Shoelace CSS (選用) -->
  <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/@shoelace-style/shoelace@2.15.1/cdn/themes/light.css" />

  <!-- Dialog CSS -->
  <link rel="stylesheet" href="dialog.css">

  <style>
    body {
      font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif;
      background: #f8fafc;
      padding: 40px 20px;
    }
    .container {
      max-width: 900px;
      margin: 0 auto;
      background: white;
      border-radius: 8px;
      padding: 40px;
      box-shadow: 0 1px 3px rgba(0, 0, 0, 0.1);
    }
  </style>
</head>
<body>
  <div class="container">
    <h1>Dialog Standard UI 示範</h1>

    <!-- 使用 Shoelace 按鈕 (選用) -->
    <sl-button variant="primary" onclick="demoAlert()">訊息對話框</sl-button>
    <sl-button variant="primary" onclick="demoConfirm()">確認對話框</sl-button>
    <sl-button variant="primary" onclick="demoPrompt()">輸入對話框</sl-button>
  </div>

  <!-- Shoelace JS (選用) -->
  <script type="module" src="https://cdn.jsdelivr.net/npm/@shoelace-style/shoelace@2.15.1/cdn/shoelace-autoloader.js"></script>

  <!-- Dialog JS -->
  <script src="dialog.js"></script>
  <script>
    async function demoAlert() {
      await dialog.alert('操作成功！', '成功');
    }

    async function demoConfirm() {
      const confirmed = await dialog.confirm('確定要刪除嗎？', '確認');
      await dialog.alert(confirmed ? '已確認' : '已取消', '結果');
    }

    async function demoPrompt() {
      const name = await dialog.prompt('請輸入您的名字：', '王小明', '輸入');
      if (name) {
        await dialog.alert(`您好，${name}！`, '歡迎');
      }
    }
  </script>
</body>
</html>
```
