### 1. Basic Form Validation & DOM Deletion (`v-if`)

* **Concept:** Form fields (`name`, `age`) are bound to data models via `v-model`, and error lists are conditionally added or removed from the DOM using `v-if="errors.length"`.

```html
<div id="app">
  <p v-if="errors.length">
    <b>Please correct the following error(s):</b>
    <ul>
      <li v-for="error in errors">{{ error }}</li>
    </ul>
  </p>
  <form @submit="checkForm" action="https://vuejs.org" method="post">
    <p>
      <label for="name">Name:</label>
      <input type="text" v-model="name" id="name">
    </p>
    <p>
      <label for="age">Age:</label>
      <input type="number" v-model="age" id="age">
    </p>
    <p>
      <input type="submit" value="Submit">
    </p>
  </form>
</div>

<script>
  const app = Vue.createApp({
    data() {
      return { errors: [], name: null, age: null }
    },
    methods: {
      checkForm(e) {
        this.errors = [];
        if (!this.name) this.errors.push('Name required.');
        if (!this.age) this.errors.push('Age required.');
        if (this.errors.length === 0) return true;
        e.preventDefault();
      }
    }
  }).mount('#app');
</script>
```


---

### 2. Disabling Browser Defaults & Custom Validation (`novalidate="true"`)
* **Concept:** Adding `novalidate="true"` to the form tells the browser to stop automatic validation so your custom JavaScript rules take full control.

```html
<form @submit="checkForm" action="https://vuejs.org" method="post" novalidate="true">
  <p>
    <label for="email">Email:</label>
    <input type="email" v-model="email" id="email">
  </p>
  <p><input type="submit" value="Submit"></p>
</form>

<script>
  // Inside methods:
  checkForm(e) {
    this.errors = [];
    if (!this.email) {
      this.errors.push('Email required.');
    } else if (!this.validEmail(this.email)) {
      this.errors.push('Valid email required.');
    }
    if (this.errors.length === 0) return true;
    e.preventDefault(); // Blocks submit if validation fails [00:06:10]
  }
</script>
```

---


### 3. Aggregate / Multi-Field Validation (Budget Limit Example)
* **Concept:** Checking conditions across multiple numerical inputs to ensure they sum up precisely to a required target (e.g., total budget equals 100) before allowing submission.


```javascript

// Method checking custom aggregate rules
checkForm(e) {
  this.errors = [];
  
  // Assuming feature1, feature2, feature3 are bound via v-model
  let total = Number(this.feature1) + Number(this.feature2) + Number(this.feature3);
  
  if (total !== 100) {
    this.errors.push('Total must be 100!');
  }
  
  if (this.errors.length === 0) {
    return true;
  }
  
  e.preventDefault(); // Prevents post action if budget doesn't match [00:18:37]
}
```

---

### 4. Asynchronous Server-Side Validation (`fetch` API)

* **Concept:** Performing an asynchronous check against an external backend or server endpoint before finalizing form acceptance.

```javascript
async checkForm(e) {
  e.preventDefault(); // Stop default submit to wait for server response
  
  try {
    let response = await fetch(`https://your-api-endpoint.netlify.app/validate?name=${this.name}`);
    
    if (response.status === 204) {
      // Validation passed on server, manually submit or proceed
      alert("Success!");
    } else if (response.status === 400) {
      this.errors.push("Invalid name passed from server.");
    }
  } catch (err) {
    console.error("Server validation failed", err);
  }
}
```
