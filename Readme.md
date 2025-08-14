局部註冊

import { createApp } from 'vue';
import Dropdown from '@/components/Dropdown.vue';
import App from './App.vue';
createApp(App).component('dropdown', Dropdown).mount('#app');


模板

<dropdown
  v-model="selectdata.LicenName"
  :items="licennamelist"
  :custom-item-display="it => it.NAME"
/>
