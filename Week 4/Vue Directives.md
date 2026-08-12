Here is the corrected and polished version of your text:

---

## Vue.js Built-in Directives Reference

Here are the 15 built-in Vue.js directives:

* **`v-text`**: Updates the element's text content.
```html
<span v-text="msg"></span>

```


* **`v-html`**: Updates the element's innerHTML.
```html
<div v-html="rawHtml"></div>

```


* **`v-show`**: Toggles visibility via CSS `display`.
```html
<div v-show="isVisible">Hello!</div>

```


* **`v-if`**: Conditionally renders an element.
```html
<div v-if="type === 'A'">A</div>

```


* **`v-else`**: The "else" block for `v-if`.
```html
<div v-else>Not A</div>

```


* **`v-else-if`**: The "else if" block for `v-if`.
```html
<div v-else-if="type === 'B'">B</div>

```


* **`v-for`**: Renders a list based on source data.
```html
<li v-for="item in items">{{ item }}</li>

```


* **`v-on`**: Attaches event listeners (e.g., `v-on:click`).
```html
<button v-on:click="doSomething">Click</button>

```


* **`v-bind`**: Dynamically binds attributes (e.g., `v-bind:href`).
```html
<a v-bind:href="url">Link</a>

```


* **`v-model`**: Enables two-way data binding on form inputs.
```html
<input v-model="message" />

```


* **`v-slot`**: Used for named or scoped slots.
```html
<template v-slot:header>Title</template>

```


* **`v-pre`**: Skips compilation for the element and its children.
```html
<span v-pre>{{ this will not be compiled }}</span>

```


* **`v-once`**: Renders the element once, skipping future updates.
```html
<span v-once>{{ msg }}</span>

```


* **`v-memo`**: Memoizes a sub-tree of the template for performance.
```html
<div v-memo="[valueA, valueB]">...</div>

```


* **`v-cloak`**: Hides uncompiled templates until the component is ready.
```html
<div v-cloak>{{ message }}</div>

```



---

> **Are you using these for your web project development, perhaps in one of your Flask or data-driven applications?**
