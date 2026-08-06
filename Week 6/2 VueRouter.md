# Vue Router & Form Validation - Quick Guide

## Vue Router
**Vue Router**, Vue.js official router library hai. Iska use Single Page Applications (SPAs) mein different pages ya views ke beech navigation (switch) karne ke liye kiya jata hai.

### Vue Router kyun use hota hai?

* **Multiple Pages bina Reload ke:** Iski madad se website bina poori page refresh hue alag-alag components (jaise Home, About, Contact) ke beech switch ho jaati hai.
* **Clean URLs:** Yeh URLs ko manage karta hai taaki user ko lagta hai ki woh alag URLs par ja raha hai, jabki andar se woh ek hi single page application hoti hai.
* **Parameters Passing:** Yeh URL ke through data (jaise `/about/:username`) pass karne mein madad karta hai.

Vue.js aur **Vue Router** mein URL (routes) create karne ke liye kuch main steps follow kiye jaate hain:

### 1. Router Setup Karna

Sabse pehle aapko `VueRouter` import karke apni routes define karni hoti hain. Har route ke liye ek `path` aur ek component specify kiya jata hai:

```javascript
const routes = [
  { path: '/home', component: Home },
  { path: '/about', component: About }
]

const router = new VueRouter({
  mode: 'history', // Clean URLs ke liye
  routes
})

```

### 2. URL Link Banana (Template mein)

UI ke andar user ko dusre page par bhejne ya URL change karne ke liye `<router-link>` ka use kiya jata hai. Yeh HTML ke `<a href>` ki tarah kaam karta hai lekin bina page ko refresh kiye:

```html
<!-- Isse /home URL ban jayega -->
<router-link to="/home">Go to Home</router-link>

<!-- Isse /about URL ban jayega -->
<router-link to="/about">Go to About</router-link>

```

### 3. URL se Data (Parameters) Pass Karna

Agar aapko URL ke andar koi dynamic data (jaise user ka naam ya ID) pass karna hai, toh colon (`:`) ka use karke route parameter banaya jata hai:

* **Route Define karna:**
```javascript
{ path: '/about/:username', component: AboutUser }

```


* **URL par Navigate karna (Code ke through):**
```javascript
this.$router.push('/about/john')

```


* **Component ke andar Param read karna:**
```html
<div>User: {{ $route.params.username }}</div>

```
---
Vue Router mein **Hash Mode** aur **History Mode** do alag-alag tareeqe hain URL ko manage karne ke liye. Inka main difference yeh hota hai ki URL kaisa dikhta hai aur browser server ke sath kaise communicate karta hai.

### 1. Hash Mode (Default Mode)

Hash mode mein URL ke andar ek hash sign (`#`) laga hota hai, jaise:
`http://localhost:8080/#/home`

* **Kaise kaam karta hai:** Hash (`#`) ke baad ka jo bhi hissa hota hai (jaise `#/home`), woh browser ke andar hi handle hota hai. Jab aap route change karte hain, toh browser server ko request nahi bhejta, balki sirf page ka component badal deta hai.
* **Fayda:** Iske liye kisi special server configuration ki zaroorat nahi padti. Yeh static hosting (jaise GitHub Pages) par bhi seedhe kaam karta hai.
* **Nuksaan:** URL thoda lamba aur alag lagta hai kyunki usme `#` sign hota hai.

### 2. History Mode

History mode mein URL bilkul clean aur normal websites ki tarah hota hai, jaise:
`http://localhost:8080/home` (Isme `#` nahi hota)

* **Kaise kaam karta hai:** Yeh HTML5 History API (`pushState`) ka use karta hai taaki bina page refresh kiye URL change ho sake. Clean URLs ke liye ise production mein use kiya jata hai.
* **Fayda:** URLs bilkul professional aur clean dikhte hain.
* **Nuksaan:** Isme server ko pehle se configure karna padta hai taaki page refresh karne par `404 Not Found` error na aaye.

### Hash Mode (Default) vs History Mode

| Mode | URL | When to Use |
|------|-----|-----------|
| Hash | `#/home` | Development, GitHub Pages |
| History | `/home` | Production, when you control server |

### Enable History Mode
```javascript
const router = new VueRouter({
  mode: 'history',  // Clean URLs
  routes
})
```

### ⚠️ Important: Server Configuration
404 Error: Kyunki server par actually aisi koi physical file ya folder nahi hota (Single Page Application mein sirf ek hi index.html hoti hai), isliye server ko samajh nahi aata aur woh 404 Not Found error de deta hai. 
Is problem ko fix karne ke liye, server (jaise Express.js) mein ek catch-all route lagana padta hai jo baaki sabhi requests ko wapas index.html par redirect kar de, taaki Vue Router khud route ko handle kar sake.

History mode needs server to serve `index.html` for all unknown routes.
```javascript
// Express example
app.get('*', (req, res) => {
  res.sendFile('index.html')
})
```

Without this, direct URL visits will 404.

---

