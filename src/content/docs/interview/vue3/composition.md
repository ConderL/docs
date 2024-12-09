---
title: composition
---

#### Vue 3 中的 Composition API 与 Vue 2.x 的 Options API 有什么区别？

主要区别在于设计思想和实现方式。简单来说，Composition API 提供了一种更灵活、可复用性更高的方式来组织代码和实现组件功能，而 Options API 则是基于选项的语法结构来定义组件。

### 1. 组织方式
- Options API 使用的是一种声明式的方式，通过 data、methods、computed 等选项来组织代码。
- Composition API 则是通过 setup 函数，将逻辑按功能模块拆分为多个可复用的函数或变量，更具模块化和可读性。

### 2. 复用性
- 在 Options API 中，复用逻辑主要通过 mixins 和 HOC（高阶组件）来实现，但这些方式可能会导致命名冲突和逻辑碎片化等问题。
- Composition API 可以使用钩子函数和自定义钩子，从而方便地复用和组合不同的功能模块，减少了命名冲突的可能性。

### 3.TypeScript 友好
- Options API 对 TypeScript 的支持相对较弱，类型推断和代码提示方面表现不佳。
- Composition API 在设计之初就考虑了对 TypeScript 的友好支持，使得开发者可以更方便地进行类型检查和推断。

## 扩展
除了上述的主要区别，这里再拓展一些与 Composition API 和 Options API 相关的知识点：

### 1. 生命周期钩子
在 Options API 中，我们有 created、mounted 等声明式的生命周期钩子。而在 Composition API 中，这些生命周期钩子需要通过 `import { onMounted, onCreated } from 'vue'` 来显式引入和调用。这使得状态逻辑可以更加集中和简洁。

### 2. 响应式系统
在 Options API 中，Vue 会自动将 data 转化为响应式数据，而在 Composition API 中，我们需要显式地使用 `ref` 和 `reactive` 创建响应式数据。例如：
```js
const count = ref(0)
const state = reactive ({ count: 0 })
```

### 3. 模板引用
在 Options API 中，我们可以通过 `this.$refs` 来访问模板引用，而在 Composition API 中，可以使用 `ref`  组合式 API，更加直观和灵活。例如：
```js
const myDiv = ref(null)
onMounted(() => {
  console.log(myDiv.value) // DOM
})
```

### 4. Vue 生态系统的支持
虽然 Composition API 是 Vue 3 引入的新特性，但 Vue 核心团队一直在努力确保它与 Vue 2.x 生态系统中的插件和库兼容。因此，我们可以在现有项目中逐步过渡到 Composition API，而不必完全抛弃已有的代码和工具。
