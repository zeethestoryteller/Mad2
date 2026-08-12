### 1. Introduction to Vuex & Store Concepts

* **What is Vuex?** It is an official state management library specifically built for the Vue.js framework, designed to manage application-wide states.
* **Global Store:** Yeh ek central container ya dabba hota hai jiske andar yeh poora state (aur usko badalne ke tareeqe) rakha jata hai. Kyunki yeh global hota hai, isliye aapke app ka koi bhi component kahin se bhi ismein rakhe data ko access ya update kar sakta hai.

*  **State** (Data): Yeh aapke app ka data ya information hoti hai (jaise user ka naam, shopping cart ke items, ya counter ki value). Yeh woh saari cheezein hain jo aapki screen par dikhti hain ya backend se manage hoti hain.

* **Asaan misaal:** Maan lijiye ek bada restaurant hai.

  * State us restaurant ka stock (jaise kitni plate biryani bachi hai) hai.

  * Store woh central store room hai jahan woh poora stock rakha hai, jise restaurant ka koi bhi waiter ya chef check kar sakta hai.

---

### 2. Vuex Store Structure, State, and Components 

* **Store Instantiation:** The store is created as a new instance of the `Vuex.Store` class at the root level of the application.
* **State vs. Data:** In standard Vue components, data is managed locally. In Vuex, this is replaced by the centralized `state` object.
* **Component State Access:** Components can read global state values directly or through computed properties.

Vuex Store Definition
```javascript
const store = new Vuex.Store({
  state: {
    count: 1
  },
  mutations: {
    increment (state) {
      // mutate state
      state.count++
    }
  }
})
```
Component Usage
```javascript
const Counter = {
  template: `<div>{{ count }}</div>`,
  computed: {
    count () {
      return store.state.count
    }
  }
}
```

---

### 3. Shared State Tree & Local State [[03:10](https://www.youtube.com/watch?v=ml8gFCV7l8s&t=190)]

* **Shared State Tree:** Because component nesting forms a tree structure, the Vuex state object mirrors this tree layout.
* **Local State Retention:** Components are still allowed to have local state (`data`) for variables that do not need to be shared across siblings or parents, preventing clutter in the global store.

---

### 4. Getter Methods & Global Store Access via `this.$store` [[04:41](https://www.youtube.com/watch?v=ml8gFCV7l8s&t=281)]

* **Getters:** Used for convenience to compute derived state based on store state values, avoiding repetitive logic across components.
* **`this.$store` Injection:** Registering Vuex automatically injects the `$store` property into all child components, allowing them to access the global store seamlessly.

```JavaScript
//Todo List

const store = new Vuex.Store({
  state: {
    todos: [
      { id: 1, text: 'Learn Vue', done: true },
      { id: 2, text: 'Learn Vuex', done: false }
    ]
  },
  getters: {
    // Define a getter to filter completed todos
    doneTodos: state => {
      return state.todos.filter(todo => todo.done);
    }
  }
});
```
```JavaScript

//Accessing Getters in Components

const TodoList = {
  template: `
    <ul>
      <li v-for="todo in doneTodos" :key="todo.id">{{ todo.text }}</li>
    </ul>
  `,
  computed: {
    doneTodos() {
      // Accessing the getter from the store
      return this.$store.getters.doneTodos;
    }
  }
};
```
---

### 5. Making Changes via Committing Mutations [[05:42](https://www.youtube.com/watch?v=ml8gFCV7l8s&t=342)]

* **Controlled Updates:** To maintain predictability and traceability, state variables are never modified directly.
* **Mutations:** Changes must happen explicitly through functions called **mutations**.
* **Committing Mutations:** You trigger a mutation by calling `store.commit()`, which resembles executing a database transaction or a version control commit.

```javascript
// Defining a mutation
mutations: {
  increment(state, n) {
    state.count += n;
  }
}

// Committing a mutation with arguments
store.commit('increment', 10);

// Committing a mutation without arguments
store.commit('increment');

// Alternatively, passing an entire payload object
store.commit({
  type: 'increment',
  amount: 10
});

```

---

### 6. Synchronous Nature of Mutations [[07:36](https://www.youtube.com/watch?v=ml8gFCV7l8s&t=456)]

* **Why Synchronous?** Mutations must be completely synchronous.
* **Reasoning:** Asynchronous callbacks (like timers or API requests) make it impossible to track exact states sequentially, breaking **time-travel debugging**.

---

### 7. Dev Tools and Time Travel Debugging [[07:53](https://www.youtube.com/watch?v=ml8gFCV7l8s&t=473)]

* **State Logging:** Vue dev tools record every committed mutation along with its timestamp and request details.
* **Time Travel:** Because mutations are logged sequentially, developers can reset the application state and replay mutations up to step $n-1$ to inspect past states.
---

### 8. Actions

* Mutations Synchronous Hone Chahiye: Mutations ke andar koi bhi asynchronous (async) call allow nahi hoti, kyunki agar operations asynchronous honge toh state changes ko accurately track aur time-travel debug karna mushkil ho jayega.

* Actions Async Functionality Handle Kar Sakte Hain: Jahaan asynchronous operations ki zaroorat ho, wahan hum Actions ka use karte hain.

* Actions State Ko Directly Change Nahi Karte: Actions khud state ko mutate nahi karte, balki woh andar se mutations ko commit karte hain.

```javascript
actions: {
  increment ({ commit }) {
    commit('increment')
  }
}

store.dispatch('increment')
```
**Defining the Action (actions: `{ increment ({ commit }) }`)**

  * Yahan increment naam ka ek action define kiya gaya hai jo context object me se { commit } method ko extract karta hai.

  * Yeh action andar ek mutation ko call (commit('increment')) kar raha hai.

**Dispatching the Action (`store.dispatch('increment')`)**

  *  Jaise mutations ko run karne ke liye store.commit() use hota hai, waise hi actions ko trigger karne ke liye store.dispatch() ka use kiya jata hai.

  *  Yeh function asynchronous tasks ko handle karne ke baad eventually mutation ko commit kar deta hai.


# **Introduction to Actions for Asynchronous Operations**


* **Actions:** Since mutations cannot handle asynchronous code, Vuex introduces **actions**.
* **Dispatching Actions:** Instead of committing an action directly, you **dispatch** it using `store.dispatch()`.
* **Flow:** An action can handle asynchronous tasks (like server requests) and then commit mutations once a response is received.

```javascript
// Defining an action handling async work and committing mutations
actions: {
  checkoutCart({ commit }, cartData) {
    api.processCheckout(cartData, 
      success => { commit('CHECKOUT_SUCCESS', success); },
      failure => { commit('CHECKOUT_FAILURE', failure); }
    );
  }
}

// Dispatching the action
store.dispatch('checkoutCart', orderDetails);

```
# **Action Composition**

Vuex actions are asynchronous by nature (often returning JavaScript **Promises**). **Action composition** allows you to chain or coordinate multiple actions together. This means one action can trigger (`dispatch`) another action, wait for it to finish, and then execute subsequent tasks or commit mutations.

```javascript
// assuming `getData()` and `getOtherData()` return Promises
actions: {
  async actionA ({ commit }) {
    commit('gotData', await getData())
  },
  async actionB ({ dispatch, commit }) {
    await dispatch('actionA') // wait for `actionA` to finish
    commit('gotOtherData', await getOtherData())
  }
}

```

1. **`actionA`:**
* Declared as an `async` function.
* It waits (`await`) for an asynchronous function `getData()` to resolve, and then commits the mutation `gotData` with the retrieved payload.


2. **`actionB`:**
* Also declared as an `async` function, destructuring both `{ dispatch, commit }`.
* It uses `await dispatch('actionA')` to explicitly pause execution until `actionA` has completed successfully.
* Once `actionA` finishes, it moves forward to fetch `getOtherData()` and commits the `gotOtherData` mutation.

---

### 9. Summary and When to Use Vuex 
* **Complexity Tradeoff:** Vuex introduces boilerplate and structural overhead.
* **Decision Rule:** For small applications with simple component trees, standard Vue props and local state are sufficient. Vuex should only be adopted when application state management becomes complex and hard to maintain across multiple views.
