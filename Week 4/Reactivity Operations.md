# Reactivity Options

Vue.js mein Reactivity Options (ya reactivity options) woh core components hain jo data ko reactive banate hain, usme changes ko track karte hain, aur data update hone par actions ya computations perform karte hain.
Vue.js ke standard Options API mein yeh main chaar options hote hain:

---

### 1. **`data`**

* **Kaam:** Component ka state/data define karne ke liye use hota hai. Yeh ek function hota hai jo ek object return karta hai, jisme reactive properties hoti hain.
* **Example:**
```javascript
data() {
    return {
        count: 0,
        name: 'Zeeshan'
    }
}

```



### 2. **`computed`**

* **Kaam:** Existing reactive data par calculation ya formatting karke ek naya cached data return karne ke liye. Jab tak dependent data change nahi hoti, yeh re-evaluate nahi hota.
* **Example:**
```javascript
computed: {
    doubleCount() {
        return this.count * 2;
    }
}

```



### 3. **`watch`**

* **Kaam:** Kisi specific data property ya route/expression par nazar rakhne aur uske change hone par koi side-effect (jaise function run karna ya API call karna) perform karne ke liye.
* **Example:**
```javascript
watch: {
    count(newVal, oldVal) {
        console.log(`Count badal kar ${newVal} ho gaya hai.`);
    }
}

```



### 4. **`methods`**

* **Kaam:** Component ke andar functions define karne ke liye. Halanki yeh direct reactivity option nahi hain, lekin data ko mutate karne aur events handle karne ke liye reactivity system ke sath jude hote hain.
* **Example:**
```javascript
methods: {
    increment() {
        this.count++;
    }
}

```
