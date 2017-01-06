# vue-router 使用

## 新建 router.js

```javascript
import Vue from 'vue'
import VueRouter from 'vue-router'

Vue.use(VueRouter)

import App from './App'
import Bar from './Bar'
import Home from './Home'

export default new VueRouter({
  mode: 'history',
  routes: [
    {
      path: '/',
      redirect: '/home'
    },
    {
      path: '/home',
      component: Home
    },
    {
      path: '/app',
      component: App
    },
    {
      path: '/bar',
      component: Bar
    },
  ]
})
```

## 在 main.js 中初始化

```javascript
import Vue from 'vue'
import router from './router'

/* eslint-disable no-new */
new Vue({
  router
}).$mount('#app')
```

## index.html

```html
<!DOCTYPE html>
<html>

<head>
  <meta charset="utf-8">
  <title>vuedemo</title>
</head>

<body>
  <div id="app">
    <router-view></router-view>
  </div>
  <!-- built files will be auto injected -->
</body>

</html>
```

router 会将组件渲染到 router-view 控件中。
