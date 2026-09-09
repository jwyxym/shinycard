# ShinyCard

Vue 3 的交互式镭射卡片组件。

## 在其他 Vue 项目中使用

```sh
npm install shinycard
```

```vue
<script setup lang="ts">
import { ShinyCard } from 'shinycard'
// 若构建工具没有自动加载 package 的 style 字段，取消下一行注释：
// import 'shinycard/style.css'
</script>

<template>
  <ShinyCard src="https://example.com/card.png" alt="示例卡片" :max-tilt="12" />
</template>
```

Props：`src`（必填图片地址）、`alt`（默认空字符串）、`disabled`（默认 `false`）和 `maxTilt`（默认 `12`，范围 0–30）。

## 构建和发布

```sh
npm install
npm run build
npm pack --dry-run
```

确认 npm 包名可用后，登录并发布：

```sh
npm login
npm publish
```

若改为 `@你的账号/shinycard` 这类 scoped 公共包，首次发布使用 `npm publish --access public`。

This template should help get you started developing with Vue 3 in Vite.

## Recommended IDE Setup

[VS Code](https://code.visualstudio.com/) + [Vue (Official)](https://marketplace.visualstudio.com/items?itemName=Vue.volar) (and disable Vetur).

## Recommended Browser Setup

- Chromium-based browsers (Chrome, Edge, Brave, etc.):
  - [Vue.js devtools](https://chromewebstore.google.com/detail/vuejs-devtools/nhdogjmejiglipccpnnnanhbledajbpd)
  - [Turn on Custom Object Formatter in Chrome DevTools](http://bit.ly/object-formatters)
- Firefox:
  - [Vue.js devtools](https://addons.mozilla.org/en-US/firefox/addon/vue-js-devtools/)
  - [Turn on Custom Object Formatter in Firefox DevTools](https://fxdx.dev/firefox-devtools-custom-object-formatters/)

## Type Support for `.vue` Imports in TS

TypeScript cannot handle type information for `.vue` imports by default, so we replace the `tsc` CLI with `vue-tsc` for type checking. In editors, we need [Volar](https://marketplace.visualstudio.com/items?itemName=Vue.volar) to make the TypeScript language service aware of `.vue` types.

## Customize configuration

See [Vite Configuration Reference](https://vite.dev/config/).

## Project Setup

```sh
npm install
```

### Compile and Hot-Reload for Development

```sh
npm run dev
```

### Type-Check, Compile and Minify for Production

```sh
npm run build
```
