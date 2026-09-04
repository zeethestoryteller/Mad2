# Page Composition

* **Original (Traditional Web Apps):**
  * All pages are static or dynamic HTML files delivered entirely from a server .
  * Navigating from one page to another requires a full page reload and a new request to the server.


* **Vue-like Frameworks (Single Page Applications):**
  * The application is broken down into modular **components** rather than distinct individual HTML files.
  * Different parts of the application correspond directly to these components, turning the web app into a cohesive client-side application rather than just a linear sequence of pages.
 
```html
<div id="app">
  <h1>Hello App!</h1>
  <p>
    <!-- use router-link component for navigation. -->
    <!-- specify the link by passing the `to` prop. -->
    <!-- `<router-link>` will be rendered as an `<a>` tag by default -->
    <router-link to="/foo">Go to Foo</router-link>
    <router-link to="/bar">Go to Bar</router-link>
  </p>

  <!-- route outlet -->
  <!-- component matched by the route will render here -->
  <router-view></router-view>
</div>

```

* **`<router-link to="...">`**:
  * Used for navigation instead of standard anchor tags.
  * By default, it renders as an `<a>` tag with an `href` attribute, but when clicked, it prevents a full page reload and instead handles the routing internally via JavaScript.


* **`<router-view>`**:
  * Acts as a **route outlet**.
  * Whatever component matches the current active route (such as the component for `/foo` or `/bar`) is dynamically rendered right inside this area.
 
# Example

1. **Define Route Components:**
Sabse pehle, `Foo` aur `Bar` naam ke chote components define kiye gaye hain jinhe render karwana hai.
```javascript
const Foo = { template: '<div>foo</div>' }
const Bar = { template: '<div>bar</div>' }

```


2. **Map Routes to Components:**
`routes` array ke andar yeh bataya gaya hai ki kis URL path par kaun sa component aana chahiye (jaise `/foo` path par `Foo` component aur `/bar` par `Bar` component).
```javascript
const routes = [
  { path: '/foo', component: Foo },
  { path: '/bar', component: Bar }
]

```


3. **Create the Router Instance:**
`new VueRouter()` ka use karke ek router instance banaya gaya hai aur usme routes pass kiye gaye hain. Yahan `routes` likhna `routes: routes` ka shorthand syntax hai.
```javascript
const router = new VueRouter({
  routes // short for `routes: routes`
})

```


4. **Create and Mount the Root Instance:**
Last mein, main Vue app banate waqt is router ko configure kiya gaya hai aur `#app` element par mount kar diya gaya hai.
```javascript
const app = new Vue({
  router
}).$mount('#app')

```
---
**Advantages**

* **Clickable links to transition between components:** Aapko alag-alag full HTML pages fetch karne ki zarurat nahi padti; aap simple clickable links ke zariye alag-alag components ke beech switch kar sakte hain, jisse browser ko naye HTML pages load nahi karne padte.
* **Clicks handled by client JS, no need to hit server:** Saare page transitions aur clicks client-side JavaScript (Vue Router) ke through handle hote hain, isliye har baar server par request bhejne ki zarurat nahi padti.
* **Can replace parts of existing page - limit refreshes:** Pura page refresh karne ke bajaye, yeh existing page ke sirf us hisse (`<router-view>`) ko replace karta hai jo change hona chahiye, jisse user experience ek smooth desktop application jaisa ho jata hai.

 ---

**Dynamic routes**
```JavaScript

// User component definition
const User = {
  template: '<div>User {{ $route.params.id }}</div>'
}

// Dynamic route
const router = new VueRouter({
  routes: [
    // dynamic segments start with a colon
    { path: '/user/:id', component: User }
  ]
})
```

* **User Component with Parameters (`$route.params.id`):**
  * Code mein ek `User` component define kiya gaya hai jiska template dynamic value display karta hai: `<div>User {{ $route.params.id }}</div>`.
  * Iska matlab hai ki URL mein jo bhi ID pass ki jayegi (jaise `1` ya `2`), wo dynamically component ke andar render hogi.


* **Dynamic Segments (`path: '/user/:id'`):**
  * Router configuration ke andar route path mein colon (`:id`) ka use karke dynamic segment banaya gaya hai, jo kisi bhi user ID ke liye match ho jata hai.
 
 ---
 
**Impact on reactivity**


* **Component Reuse Issue:** Jab aap ek hi dynamic route ke alag-alag instances ke beech navigate karte hain (jaise `/user/one` se `/user/two` par jana), toh Vue default taur par naya component banane ke bajaye **same component ko reuse** kar leta hai. Iski wajah se screen par zaroori reactive updates trigger nahi hote.
* **Watcher Solution:** Is problem ko fix karne ke liye `$route` object par ek **watcher** lagaya jata hai, jo route changes (`to` aur `from`) ko track karta hai taaki zaroorat padne par component ya data ko properly update kiya ja sake.


```javascript
const User = {
  template: '...',
  watch: {
    $route(to, from) {
      // react to route changes...
    }
  }
}

```

---

### More features

* **Nested routes**
  * `router-view` inside a component


* **Named routes**
  * readability and maintainability


* **Named views**
  * associate multiple components with different `router-view` by name


* **HTML5 history mode**
  * push URLs into the history of the browser
  * allow more natural navigation
  * Better user experience - not fundamentally different concept
 
---

### Why router?

* **Routes handled in JS**
* **Routes associated with components**
* **Navigation inside a single app - updates from server only on demand**

**Single Page Applications**



  
