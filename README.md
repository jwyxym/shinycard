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