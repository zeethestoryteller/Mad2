JavaScript mein `&&` (Logical AND) ke alawa kuch aur main logical operators bhi hote hain. Aaiye in sabko aasaan shabdon mein samajhte hain:

---

### 1. `||` (Logical OR) Operator

Yeh `&&` ka bilkul ulta kaam karta hai:

* Yeh pehli **"truthy"** value dhoondhta hai.
* Agar pehli value truthy mil gayi, toh yehi ruk jayega aur wahi value return kar dega.
* Agar pehli value **falsy** hui, toh ye aage badhega aur aakhiri wali value return kar dega.

**Examples:**

* `false || "Hello"` ➔ `"Hello"` (kyunki pehli falsy thi, toh doosri wali mil gayi)
* `"Apple" || "Banana"` ➔ `"Apple"` (kyunki pehli hi truthy mil gayi)
* `0 || null || 5` ➔ `5` (sabhi falsy the, toh aakhiri wali 5 bachi)

---

### 2. `!` (Logical NOT) Operator

Yeh kisi bhi value ke boolean (true/false) state ko **ulta** (invert) kar deta hai:

* Truthy value ko `false` bana deta hai.
* Falsy value ko `true` bana देता hai.

**Double NOT (`!!`):**
Aapne kabhi-kabhi `!!value` dekha hoga. Iska use kisi bhi value ko strictly `true` ya `false` (boolean) mein convert karne ke liye kiya jata hai.

* `!! 5` ➔ `true` (kyunki 5 truthy hai, pehle `!` ne use `false` kiya, aur doosre `!` ne wapas `true` kar diya)
* `!! 0` ➔ `false`

---

### 3. `??` (Nullish Coalescing) Operator

Yeh `||` jaisa lagta hai lekin thoda alag kaam karta hai. Yeh sirf tab kaam karta hai jab pehli value **`null`** ya **`undefined`** ho:

* Agar pehli value `null` ya `undefined` hai, toh ye doosri value return karta hai.
* Agar pehli value `0`, `false`, ya empty string `""` bhi hai, toh yeh unhe valid value maanta hai aur wahi return kar deta hai (jabki `||` unko falsy maan kar skip kar deta).

**Example:**

* `null ?? "Default Value"` ➔ `"Default Value"`
* `0 ?? 42` ➔ `0` (kyunki `0` null ya undefined nahi hai, isliye `0` hi return hoga)
