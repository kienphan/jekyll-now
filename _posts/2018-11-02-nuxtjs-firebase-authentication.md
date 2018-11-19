---
layout: post
title: Authentication Nuxt.js App với Firebase
---

Đang chuỗi ngày trải nghiệm Nuxt.js, muốn code chơi chơi với nó 1 tgian rồi mới đi tìm niềm vui mới. Mà để chơi chơi, ta hay thường code những cái gọi là sideproject thì mới cảm thấy cuộc chơi thú vị hơn. Authentication là thành phần không thể thiếu trong các App cần lưu dữ liệu định danh người dùng.
Vậy bài viết này với mục đích đấy. 

### Sao lại là Firebase?
Firebase thì con hàng của Goofle nổi tiếng sẵn rồi, không cần giới thiệu thêm. Nó có Spark Plan free phần authentication, free luôn cả hosting, database thì có realtime DB/firestore với free quote cũng đủ dùng, deploy cũng nhanh, rất phù hợp với việc code chơi chơi mà cũng ra sản phẩm nhanh, không cần quan tâm đến server.

### Chuẩn bị gì

- Trước tiên cần phải có Google Account

Đăng nhập vào [Firebase console](https://console.firebase.google.com)
Add new Project hoặc chọn project có sẵn rồi, copy các thông tin liên quan đến project như API key, projectId hay storage bucket,...
- Đã có hiểu biết về Nuxtjs, Vuex, ... Mình có viết 1 bài giới thiệu về Nuxt.js [tại đây](https://kienphan.github.io/gt-nuxtjs/)

### Khởi tạo 1 project Nuxt.js

Sử dụng `create-nuxt-app` (mặc định đã có từ bản NPM `5.2.0`)

```
npx create-nuxt-app <project-name>
```

Trong thư mục `/plugins` tạo 1 plugin là `firebase.js`, điền các thông tin về project như API_KEY, PROJECTID,... tương ứng vào.

```javascript
import firebase from 'firebase'

if (!firebase.apps.length) {
  firebase.initializeApp({
    apiKey: <APIKEY>,
    authDomain: <AUTHDOMAIN>,
    databaseURL: <DATABASEURL>,
    projectId: <PROJECTID>,
    storageBucket: <STORAGEBUCKET>,
    messagingSenderId: <MESSAGINGSENDERID>
  })
}

export default firebase
```

Thực ra trong production thì sẽ ko paste các biến như trên mà sẽ lưu thông tin vào các biến `.env`. Nhưng trong phạm vi bài viết sẽ làm vậy cho nhanh :v

### Sửa `nuxt.config.js`

```javascript
const webpack = require('webpack')

module.exports = {
  // ...
  build: {
    extend (config, { isDev, isClient }) {
      // ...
      config.plugins.push(
        new webpack.EnvironmentPlugin([
          'APIKEY',
          'AUTHDOMAIN',
          'DATABASEURL',
          'PROJECTID',
          'STORAGEBUCKET',
          'MESSAGINGSENDERID'
        ])
      )
    }
  }
}
```

### Sửa phần `store`

`Store` với `vuex` để quản lý trạng thái dữ liệu trong ứng dụng. Ở phần này, ta sẽ quản lý trạng thái login của user với `vuex`

### State
State sẽ lưu trạng thái login của user

```javascript
export const state = () => ({
  user: null
})
```

### getters、actions、mutations
Các phần sau này sẽ thực hiện sau

### Thực hiện Page 
Tạo 2 file trong thư mục `pages`, các thành phần trong page cũng làm đơn giản dễ hiểu
- `index.vue` (root component)
- `login.vue`(login component)

### index.vue

```html
<template>
<div class="container">
  <h1>index</h1>
</div>
</template>

<script>
export default {
}
</script>

<style scoped>
</style>
```

### login.vue

```html
<template>
<div class="container">
  <h1>index</h1>
</div>
</template>

<script>
export default {
}
</script>

<style scoped>
</style>
```

### Thực hiện Middleware
Để xác thực user, ta thường có 1 middleware để xác định người truy cập đã login hay chưa. Kịch bản như sau:

- Nếu truy cập vào root (/), nếu chưa login thì redirect về trang login
- Nếu đã login mà truy cập vào trang Login thì sẽ redirect về trang index

Như vậy mỗi khi access vào 1 route, middleware sẽ check trạng thái này. Thật hay, `Nuxt.js` có cung cấp tính năng middleware này luôn giúp ta có thế dễ dàng định nghĩa. 
Với middleware lần này thì kiểm tra trạng thái user đã được lưu vào trong `store` hay chưa để kiểm tra.

### tiếp tục thực hiện Store

```javascript
export const getters = {
  isAuthenticated (state) {
    return !!state.user
  }
}
```

Middleware sẽ sử dụng `getters` này của store để gọi xử lý check trạng thái user. Trong thư mục `middleware` định nghĩa 1 middleware tên là `authentication.js`

### middleware/authentication.js

```javascript
export default function ({ store, route, redirect }) {
  if (!store.getters.isAuthenticated && route.name !== 'login') {
    redirect('/login')
  }
  if (store.getters.isAuthenticated && route.name === 'login') {
    redirect('/')
  }
}
```

Thực hiện xong middleware thì chỉnh lại `nuxt.config.js`

```javascript
module.exports = {
  // ...
  router: {
    middleware: 'authenticated'
  }
}
```

### Firebase Authentication

Ta dùng Firebase Authentication để thực hiện tính năng Login.

- `firebase.auth.Auth`
- `firebase.auth.GoogleAuthProvider`

Firebase Login Flow thì sẽ như sau:
- User bấm vào nút Login
- App sẽ gọi đến hàm `signInWithRedirect` (có thể thay Redirect bằng Popup cũng được)
- Di chuyển đến màn hình Login của Google
- Thực hiện login với Google
- Return màn hình ứng dụng, lấy thông tin authentication từ `onAuthStateChanged`rồi redirect về root.

Tạo button login ở trong component `login`, click event sẽ thực hiện với `Vuex` `action`

### Thêm action vào Store

```javascript
import firebase from '@/plugins/firebase'

const googleProvider = new firebase.auth.GoogleAuthProvider()

export const actions = {
  login () {
    return new Promise((resolve, reject) => {
      firebase.auth().signInWithRedirect(googleProvider)
        .then(() => resolve())
        .catch((err) => reject(err))
    })
  }
}
```

### Chỉnh sửa login template

```html
<template>
<div class="container">
  <h1>login</h1>
  <input type='button' value='Login with Google' @click='loginGoogle'>
</div>
</template>

<script>
import { mapActions } from 'vuex'

export default {
  methods: {
    ...mapActions([
      'login'
    ]),

    loginGoogle () {
      this.login()
        .then(() => console.log('resloved'))
        .catch((err) => console.log(err))
    }
  }
}
</script>

<style scoped>
</style>
```

### Thêm action và mutations vào Store

```javascript
export const mutations = {
  setUser (state, payload) {
    state.user = payload
  }
}

export const actions = {
  // ...
  setUser ({ commit }, payload) {
    commit('setUser', payload)
  }
}
```

### `onAuthStateChanged`

Khi mà Google redirect về root thì thông tin của User vẫn chưa được lưu vào trong `store`. Nhưng mà trong middleware `authentication.js`, nếu store chưa lưu trạng thái login thì sẽ bị redirect về trang login, vậy cần thực hiện `onAuthStateChanged` vào login component

### login template

```javascript
async mounted () {
  let user = await new Promise((resolve, reject) => {
    firebase.auth().onAuthStateChanged((user) => resolve(user))
  })
  this.setUser(user) // setUser is mapped action from vuex
  if (user) {
    this.$router.push('/') // if non-null user given, go to root page.
  }
}
```

Khi mà access vào trang index thì flow như sau dựa vào trạng thái user

- Vào trang index
- Vì thông tin user chưa được lưu vào trong Store cho nên redirect về trang Login
- Trong login thì gọi hook `mounted`、gọi hàm `onAuthStateChanged` để lấy thông tin user từ Firebase
- Lấy thông tin, xử lý lưu vào store, trở lại trang Index

### Tính năng Logout

Để logout thì ta sử dụng API này của Firebase 

```javascript
firebase.auth.Auth#signOut
```
Để logout thì có 1 button logout ở Index
Ta thêm action vào vuex store để gọi sự kiên onclick ở logout

```javascript
export const actions = {
  // ...
  logout ({ commit }) {
    return new Promise((resolve, reject) => {
      firebase.auth().signOut()
        .then(() => {
          commit('setUser', null)
          resolve()
        })
    })
  }
  // ...
}
```

### index.vue

```javascript
<template>
<div class="container">
  <h1>index</h1>
  <input type='button' value='Logout' @click='doLogout' />
</div>
</template>

<script>
import { mapActions } from 'vuex'

export default {
  methods: {
    ...mapActions([
      'logout'
    ]),

    doLogout () {
      this.logout()
        .then(() => {
          this.$router.push('/login')
        })
        .catch((err) => console.log(err))
    }
  }
}
</script>

<style scoped>
</style>
```

### Tham khảo

https://www.davidroyer.me/blog/nuxtjs-firebase-auth/

https://hackernoon.com/vue-nuxt-firebase-auth-database-ssr-example-tutorial-facebook-login-setup-authentication-starter-app-a6dfde0133fc

https://blog.shimar.me/2018/03/31/nuxt-firebase-authentication.html