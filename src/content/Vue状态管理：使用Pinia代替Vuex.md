---
title: Vue状态管理：使用Pinia代替Vuex
date: 2021-11-03
author: Rogepi
desc: Vue官方下一代状态管理库Pinia上手体验

---

## Pinia是什么

Pinia是一个vue的状态管理方案，是vuex团队成员开发，实现了很多vuex5的提案，更加地轻量化且有devtools的支持

vuex4一直被人诟病对于typescript的支持不良好，vuex5也迟迟未来

所以有了pinia的出现

相较于vuex，

-   pinia无需创建复杂的包装器来支持typescript，对于typescript类型判断是天然支持的，享受ide带来的自动补全，减少开发的心智负担，减少vue开发过程中的面向字符串编程

<!---->

-   减去了mutations的概念，只保留state,getters和anctions三个概念，减少代码冗余
-   无需手动添加store，创建的store会在使用时自动添加
-   没有模块module的概念，不用面对一个store下嵌套着许多模块，使用单文件store（有点类似redux/toolkit的一个reducer），可以直接导入其他store使用

Pinia文档有这么一段话

![image-20211103151648525](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/089c68a30e8f40a9aab589978d6e90d4~tplv-k3u1fbpfcp-zoom-1.image)

> Pinia 试图尽可能接近 Vuex 的理念。 它旨在测试 Vuex 下一次迭代的提案，并且取得了成功，因为我们目前有一个针对 Vuex 5 的开放 RFC，其 API 与 Pinia 使用的 API 非常相似。 请注意，我（Eduardo），Pania 的作者，是 Vue.js 核心团队的一员，并积极参与了 Router 和 Vuex 等 API 的设计。 我个人对这个项目的意图是重新设计使用全局 Store 的体验，同时保持 Vue 平易近人的哲学。 我让 Pania 的 API 与 Vuex 一样接近，因为它不断向前发展，以便人们可以轻松地迁移到 Vuex 或什至在未来融合两个项目（在 Vuex 下）。

所以现在学习Pinia，相当于提前学习Vuex5就是说

## Pinia简单上手

首先我们初始化一个vite+vue+ts工程

```
pnpm create vite pinia-demo -- --template vue-ts
```

安装pinia

```
pnpm i pinia
```

打开项目，编辑src目录下的mian.ts文件，引入Pinia

```
// main.ts
​
import { createApp } from 'vue'
import App from './App.vue'
​
import { createPinia } from 'pinia'
​
createApp(App).use(createPinia()).mount('#app')
```

在src目录下创建一个store文件夹用来存放状态管理，然后新建一个counter.ts，我们来做一个简单的计数器状态应用

```
import { defineStore } from 'pinia'
​
export const useCounterStore = defineStore('counter', {
  state: () => {
    return {
      count: 0,
    }
  },
  getters: {
    doubleCount: (state) => state.count * 2,
  },
  actions: {
    increment() {
      this.count++
    },
  },
})
```

Pinia通过defineStore函数来创建一个store，它接收一个id用来标识store，以及store选项

![image-20211103172246184](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/5df72331d59442028bab6f0335e225a4~tplv-k3u1fbpfcp-zoom-1.image)

我们也可以使用一个回调函数来返回options，回调函数体内的写法类似vue的setup()写法，比如上面的定义可以写成

```
export const useCounterStore = defineStore('counter', () => {
  const count = ref(0)
  function doubleCount() {
    return count.value * 2
  }
  function increment() {
    count.value++
  }
​
  return { count, increment }
})
```

接着我们在App.vue中使用store

```
<script setup lang="ts">
import { useCounterStore } from './store/counter'
const useCounter = useCounterStore()
</script>
​
<template>
  <h2>{{ useCounter }}</h2>
  <h2>{{ useCounter.count }}</h2>
  <h2>{{ useCounter.doubleCount() }}</h2>
  <button @click="useCounter.increment">increment</button>
</template>
​
<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
```

在使用Pinia的过程中可以发现自动补全是相当优秀

![image-20211103173529625](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/2bc9ca8cea774077932461dfda769b69~tplv-k3u1fbpfcp-zoom-1.image)

浏览器运行如下

![image-20211103173626396](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/ad0a7ccc7ad24430b60f087197667267~tplv-k3u1fbpfcp-zoom-1.image)

打开开发者工具查看vue devtool

![image-20211103173826083](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/70919fc1fd294c2e958e1fc09f7b300f~tplv-k3u1fbpfcp-zoom-1.image)

vue devtool支持对Pinia状态的增删改查！

Pinia有多种对状态的修改方式

-   使用actions，如上面所示

<!---->

-   直接在状态上修改

    ```
    const countPlus_1 = useCounter.count++
    ```

<!---->

-   使用store的$patch函数，支持选项和回调函数两种写法，回调函数适用于状态为数组或其他之类的需要调用状态方法进行修改

    ```
    const countPlus_2 = useCounter.$patch({ count: useCounter.count + 1 })
    ```

    ```
    const countPlus_3 = useCounter.$patch((state) => state.count++)
    ```

对状态的结构需要使用StoreToRefs函数

```
const { count } = storeToRefs(useCounter)
```

## 使用体验

Pinia的学习和使用是相当友好的，看一遍官方文档就能上手，在上手过程中可以明显地感受到相对于vuex更加快捷，编码体验优秀。

状态管理对于小项目来说更注重于方便和快捷（甚至可以不想需要），所以像vuex是稍微有些复杂了，像vue3出beta的时候就有人说可以使用组合式api比如provide跟inject配合来替代vuex，所以Pinia这个轻量级状态管理方案的出现是相当及时的。
