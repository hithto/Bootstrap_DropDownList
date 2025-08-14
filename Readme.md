# Dropdown 元件

自訂可搜尋、可清空的下拉式選單元件，支援物件或字串資料來源，具備 RWD 與外部點擊關閉功能。

## 功能特點
- **可搜尋**：開啟選單時可輸入關鍵字，即時過濾選項。
- **可清空**：當前有選取值時，右側箭頭會切換為紅色 `×`，點擊即可清空選擇。
- **外部點擊關閉**：點擊元件外部會自動關閉選單。
- **物件/字串通用**：可傳入物件陣列並透過 `custom-item-display` 自訂顯示文字。
- **RWD 支援**：小螢幕時展開為全寬，桌面尺寸保留原寬度。
- **樣式客製**：樣式定義於 `dropdown.css`，可依專案風格覆蓋。

## 安裝與引入

將 `dropdown.js` 與 `dropdown.css` 複製到專案中，並在 Vue 3 專案中註冊元件。

### 局部註冊
```js
import { createApp } from 'vue';
import Dropdown from '@/components/Dropdown.vue';
import App from './App.vue';

createApp(App)
  .component('dropdown', Dropdown)
  .mount('#app');
