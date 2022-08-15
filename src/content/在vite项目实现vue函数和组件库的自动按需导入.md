---
title: 在vite项目实现vue函数和组件库的自动按需导入
date: 2021-11-03
author: Rogepi
desc: 使用unplugin-auto-import和unplugin-vue-conponents来实现vue函数和组件库的自动按需导入

---

在一个vue项目中，我们经常需要从‘vue’，‘vue-router’等引入相应的函数方法进单个文件中

比如

```
import {  ref,  reactive,  toRefs,  watchEffect,  computed,  watch,  onUnmounted,} from 'vue'
import { useRouter } from 'vue-router'
```

这在vue3使用组合式API来编写项目hooks会使用得更多，同时为了避免全局导入组件库有时也会手动按需导入组件和样式


```
import { Button } from 'vant'
import 'vant/es/button/style/index'
```


这里我们可以使用基于**unplugin**项目的两个插件

• **unplugin-auto-import**来实现vue函数的自动导入

• **unplugin-vue-components**来实现vue组件库的自动按需导入


首先在项目中安装这两个插件


```
pnpm i unplugin-auto-import unplugin-vue-components  -D
```

然后在vite.config.ts中配置


```
// vite.config.ts
...
import AutoImport from 'unplugin-auto-import/vite'
import ViteComponents from 'unplugin-vue-components/vite'
import { VantResolver } from 'unplugin-vue-components/resolvers'
...

export default defineConfig({
  plugins: [
    ...
    AutoImport({
      include: [
        /\.[tj]sx?$/, // .ts, .tsx, .js, .jsx
        /\.vue$/,
        /\.vue\?vue/, // .vue
        /\.md$/, // .md
      ],
      dts: true,
      imports: ['vue', 'vue-router'],
    }),
    ViteComponents({
      dts: true,
      resolvers: [VantResolver()],
    }),
  ],

...
```

接着在**tsconfig.json**中配置

```
... 
"include": ["src/**/*.ts", "src/**/*.d.ts", "src/**/*.tsx","src/**/*.vue","components.d.ts","auto-imports.d.ts"],
...
```

配置完成后我们就可以直接在项目中这样使用

```
<script setup lang="ts">
  const buttonTitle = ref('主要按钮')
</script>
<template>
  <van-button type="primary">{{ buttonTitle }}</van-button>
</template>
```


运行截图

![图片](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/5fd01f217d2749a798991b13b7180d83~tplv-k3u1fbpfcp-zoom-1.image)




### References

`[1]` unplugin: *https://github.com/unjs/unplugin*

`[2]` unplugin-auto-import: *https://github.com/antfu/unplugin-auto-import*

`[3]` unplugin-vue-components: *https://github.com/antfu/unplugin-vue-components*