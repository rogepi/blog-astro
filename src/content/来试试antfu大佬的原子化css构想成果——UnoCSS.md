---
title: 来试试antfu大佬的原子化css构想成果——UnoCSS
date: 2021-11-10
author: Rogepi
desc: 原子化CSS库UnoCSS上手

---

> Anthony Fu大佬真的是我目前的偶像，狂热的开源爱好者，人长得又帅，做的东西又好



前段时间看到了antfu[重新构想原子化CSS](https://antfu.me/posts/reimagine-atomic-css-zh)的博客文章，这里我作为一个前端小白来看看大佬的最新作品UnoCSS有什么神奇之处



## 什么是原子化CSS（Atomic CSS)

与原子化相对应的就是组件化，比如在以前我们使用的bootstrap，它提供了现成的样式解决方案，或者自己编写的一个样式class也是组件化，原子化就是将一个css类只对应一个规则，比如编写一个btn，组件化的开发方式是

```html
<button type="button btn-success" class="btn">Basic</button>
```

原子化

```html
<button class="py-2 px-4 font-semibold rounded-lg shadow-md text-white bg-green-500 hover:bg-green-700">
  Click me
</button>
```

这时候有人就问了，这不是相当于style属性吗

```html
<button style="padding: 1rem, 2rem; font-family: 'semi'; font-weight: bold; ...">
  Click me
</button>
```



那原子化CSS的优势在哪儿呢（个人看法）？

- 不用想类名！相信很多人在编写样式类的时候经常会纠结类名，原子化提供的类名都是能够一眼就能知道大概意思又比直接编写style更加简洁

- ”无需离开您的HTML，即可快速建立现代网站“，这是Tailwindcss官网的引入语，确实，对于现在组件化开发的方式来说，单个组件文件相比以前一个html对应一个页面来说代码量要小很多，可能就几行html的组件代码，直接在html中编写样式是个更好的选择

- 利用原子化框架提供的预设原子类，极大地提高开发效率，将更多时间用在页面构造而不是重复地编写相似的代码

- IDE支持，VS Code 的 Tailwind CSS 智能提示扩展涵盖了所有的类。在编辑器内既可得到智能的自动完成建议、提示及类定义等功能，而且无需配置。

  

## 常用的原子化CSS框架

- Tailwind CSS

  ![image-20211110133850023](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/c8f8f663026f4f0cad5e3d7c2f362d55~tplv-k3u1fbpfcp-zoom-1.image)

  应该是知名度最大的工具优先CSS框架了，现在已经更新到2.0.2，第一次上手原子化CSS就是使用的TailwindCSS提供的预设工具类，2.0版本加入了深色模式、JIT引擎等

  

- Windi CSS

  ![image-20211110133952747](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/d67aa7eab4c64979b0e8918387a1317a~tplv-k3u1fbpfcp-zoom-1.image)

  Windi CSS的诞生是为了弥补Tainwind CSS在开发时的一些短板，比如热更新慢，预设不够灵活，它在支持TailWind CSS的所有工具类的情况下，极大地提高了开发模式下热更新的速度

  ![image-20211110133454767](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/b4e43ce8937f4d8f9313bd3a2c861c50~tplv-k3u1fbpfcp-zoom-1.image)

  自动值推导也更加地灵活

  ![image-20211110133714529](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/cea129ddd5d0442bb219ce460b550c83~tplv-k3u1fbpfcp-zoom-1.image)

  支持属性化模式

  ![image-20211110134707903](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/35b9a148edc14aef9316cdb0eedb003b~tplv-k3u1fbpfcp-zoom-1.image)

  在使用上WindiCSS也更加地方便，不需要安装更多额外的插件，所以Windi CSS也是我目前开发的主力工具

  

## UnoCSS

讲这么多，我们终于回到主题UnoCSS上

Windi CSS已经足够优秀，但antfu大佬还是不够满意，对于框架预设外的自定义工具的额外配置上，还是比较繁琐，而且配置的方式也不够简便

> 由于 Windi 需要与 Tailwind 兼容，它还必须使用与 Tailwind 完全相同的配置项。尽管数字推断的问题得到了解决，但如果你想添加一些自定义的工具，这将是一场噩梦。



所以经过重新构想原子化CSS，UnoCSS出现了

> [**UnoCSS**](https://github.com/antfu/unocss) - 具有高性能且极具灵活性的即时原子化 CSS 引擎。

对，没错，它不是像TailWind CSS和Windi CSS属于框架，而是一个引擎，它没有提供预设的原子化CSS工具类

UnoCSS通过编写规则来定制工具类

静态规则

```js
rules: [
	['m-1', { margin: '0.25rem' }]
]
```

使用正则表达式来做动态规则

```ts
rules: [
  [/^m-(\d)$/, ([, d]) => ({ margin: `${d / 4}rem` })],
  [/^p-(\d)$/, (match) => ({ padding: `${match[1] / 4}rem` })],
]
```

当然这些比较常见的规则unocss已经提供了预设在`@unocss/preset-uno`中

算了，不copy大佬的博客了，我直接上手体验吧



## UnoCSS上手

老样子，首先初始化一个vite项目

```shell
pnpm create vite unocss-demo -- --template vue-ts
```

安装unocss和三个预设，第一个是工具类预设，第二个是属性化模式支持，第三个是icon支持

```shell
pnpm i -D unocss @unocss/preset-uno @unocss/preset-attributify @unocss/preset-icons
```

在vite.config.ts中引入

```ts
import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'

import Unocss from 'unocss/vite'
import { presetUno, presetAttributify, presetIcons } from 'unocss'

// https://vitejs.dev/config/
export default defineConfig({
  plugins: [
    vue(),
    Unocss({
      presets: [
          presetUno(), 
          presetAttributify(), 
          presetIcons()],
    }),
  ],
})
```

在main.ts中引入uno.css

```ts
import { createApp } from 'vue'
import App from './App.vue'

import 'uno.css'

createApp(App).mount('#app')
```

在app.vue中编写一个button

```vue
  <button
    class="
      py-2
      px-4
      font-semibold
      rounded-lg
      shadow-md
      text-white
      bg-green-500
      hover:bg-green-700
      border-none
      cursor-pointer
    "
  >
    Click me
  </button>
```

显示效果

![image-20211110141523453](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/ed0b9c06f8a6482a96f61e20402ff046~tplv-k3u1fbpfcp-zoom-1.image)

接下来我们看一个打开一个神奇的工具UnoCSS Inspector，打开[localhost:3000/_unocss](http://localhost:3000/__unocss#/)，可以看到

![image-20211110141833035](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/c4bc9fd6044a4ef8931ae9f400fa564a~tplv-k3u1fbpfcp-zoom-1.image)

这是一个可以直观地查看unocss通过预设规则生成了什么工具类，可以查看构建的css文件大小，css规则数量以帮助我们更加方便地调整和优化代码，可以看到它生成的css文件还经过压缩只有0.41k，生成的最终uno.css只有1.4k！

![image-20211110142610464](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/bad539249ed84c298f22c88471ed83a9~tplv-k3u1fbpfcp-zoom-1.image)

这里我同时用tailwind和windi做同样的button看看结果怎么样

tailwind css 4.3m!!!

![image-20211110143936525](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/7be3743ed6cc4d55a0a0ffbb07fdc699~tplv-k3u1fbpfcp-zoom-1.image)

windi css 3.6k

![image-20211110144627039](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/f1c2ad9c46a644099cad0ae756c86b41~tplv-k3u1fbpfcp-zoom-1.image)

先不说生产环境，在开发环境uno完胜，在性能上uno是遥遥领先的



我们再来看看属性化模式预设[`@unocss/preset-attributify`](https://github.com/antfu/unocss/blob/main/packages/preset-attributify) 

使用属性化可以增强代码的可读性，比如上面的button可以改写成

```html
  <button
    p="y-2 x-4"
    font="semibold"
    shadow="lg"
    text="white"
    bg="green-500 hover:green-700"
    border="rounded-lg none "
    cursor="pointer"
  >Click me</button>
```



纯CSS图标支持，使用`@unocss/preset-icons`预设，再配合iconify图标框架提供的图标集，我们可以直接用css使用上w个图标而不需要过多的配置！

首先我们去[icones](https://icones.js.org/)官网（方便浏览和使用iconify）浏览我们需要的icon，比如这里我用到了Google Material Icons图标集里面的baseline-add-circle图标

![image-20211110150254457](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/cea4ada9f27e4433ac008eac8aae65e6~tplv-k3u1fbpfcp-zoom-1.image)

我们先记住Google Material Icons所在的网页路径是ic

![image-20211110150333679](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/5ac6d0f2b645404abff02e4c7448cb03~tplv-k3u1fbpfcp-zoom-1.image)

然后安装这个图标集

```shell
pnpm i -D @iconify-json/ic
```

包名后面的路径跟icones官网的路径是相对应的，其他图标集同理

然后我们在html中就可以直接使用这个图标，还能用unocss对它进行样式定制甚至让它动起来！

```html
<div class="i-ic-baseline-add-circle text-3xl bg-green-500" />
```

图标的使用语法是`i+${图标集缩写名}+${图标名}`，这里的图标集是ic，图标名是baseline-add-circle

显示效果

![image-20211110151041782](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/44298cc5f5564c1d9287427e59a5404f~tplv-k3u1fbpfcp-zoom-1.image)



我们继续使用UnoCSS Inspector查看生成的代码

![image-20211110151500357](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/debd8c2a31da4117a76e40c658ce853c~tplv-k3u1fbpfcp-zoom-1.image)

可以看到这里新增了一个i-ic-baseline-add-circle类，这个类就是一个svg图标

添加了一个图标css文件只增加了0.4k多的大小，图标也是按需引入的，引入一个图标集并不会把所有的图标都打包进去



## 上手心得

我现在是非常喜欢这个UnoCSS，（对不起WindiCSS，它真的太香了），但其实antfu大佬是WindiCSS的团队成员，也就是说如果UnoCSS发展不错的话，它可能成为Windi CSS v4的新引擎

![image-20211110152237456](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/84174c0c63934364801b3643830e5439~tplv-k3u1fbpfcp-zoom-1.image)



下一步我应该使用UnoCSS来搭建我的博客（像antfu大佬那样）



[1]antfu/unocss: https://github.com/antfu/unocss

[2]antfu'sblog: https://antfu.me

[3]重新构想原子化CSS: https://antfu.me/posts/reimagine-atomic-css-zh

