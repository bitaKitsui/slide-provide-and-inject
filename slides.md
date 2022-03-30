---
# try also 'default' to start simple
theme: seriph
# random image from a curated Unsplash collection by Anthony
# like them? see https://unsplash.com/collections/94734566/slidev
background: https://source.unsplash.com/collection/94734566/1920x1080
# apply any windi css classes to the current slide
class: 'text-center'
# https://sli.dev/custom/highlighters.html
highlighter: shiki
# show line numbers in code blocks
lineNumbers: false
# some information about the slides, markdown enabled
info: |
  ## Slidev Starter Template
  Presentation slides for developers.

  Learn more at [Sli.dev](https://sli.dev)
# persist drawings in exports and build
drawings:
  persist: false
---

# Provide / Inject

---

# ProvideとInjectってVue3からの機能？
実はVue2からあるっぽい
- [特別な問題に対処する - 依存性の注入](https://jp.vuejs.org/v2/guide/components-edge-cases.html#%E4%BE%9D%E5%AD%98%E6%80%A7%E3%81%AE%E6%B3%A8%E5%85%A5)
- 一応「特別な問題」的な扱いを受けている
- Vue3以降("Composition API"以降)で効果を発揮する機能？

---

# Props Drilling
<img src="/prop-drilling.11201220.png" alt="">

---

# Props Drilling
- 複雑なコンポーネントツリーの場合にpropsのバケツリレーが生じること
- 値の受け渡しのみを行うコンポーネントが出てきてしまう
- コード自体の見通しも良くない

---

# Solution
<img src="/provide-inject.3e0505e4.png" alt="">

---

# About
- `Provide`: 親コンポーネントから子孫コンポーネントへ依存関係を提供できる
- `Inject`: 子孫関係にあるコンポーネント側で依存関係を注入できる

---

# How to use
## Provide
- 第一引数: keyとなる値
- 第二引数: valueとなる値

```ts
<script setup lang="ts">
import { provide, ref } from 'vue'

const number = ref(0)
provide('number', number)
        
provide('message', 'hello')
</script>
```

---

# How to use
## Inject
- 第一引数: keyとなる値（必須）
- 第二引数: defaultValue
- 第三引数: ファクトリー関数

```ts
<script setup lang="ts">
import { inject } from 'vue'

const number = inject('number')
        
const foo = inject('foo', 'default value')
</script>
```

---

# App-level Provide
アプリケーション全体で使い回す値も定義できる
- コンポーネントを利用しないPlugins機能を使う際に便利
- Plugins: Vue内でプラグインを作成し利用できる機能

```ts
import { createApp } from 'vue'

const app = createApp({})

app.provide(/* key */ 'message', /* value */ 'hello!')
```

---

# Reactiveな値との併用
何も考えずにReactiveな値（ref）をProvideすると、<br/>
利用するコンポーネント側でReactiveな値かどうかを判別できない！
- [公式](https://vuejs.org/guide/components/provide-inject.html#working-with-reactivity)には`computed()`使えとあるがイマイチわからない。。

```ts
<script setup lang="ts">
import { inject } from 'vue'

const number = inject('number')
        
console.log(number) // => RefImpl { ... }
console.log(number.value) // => 0
</script>
```

---

# まとめ
- propsのバケツリレーを防げる
- コンポーネント間での値の受け渡しの見通しが良くなる

---

# ところで。。
- Props Drillingが生じそうな場合は設計を見直すべき？
- 仮にProvide / Injectを使う場合はVuexやPiniaとどう使い分ける？
  - Reactの場合、useContextとReduxなどどうしている？