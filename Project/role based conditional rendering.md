Ab sab kuch tumhare router ke saath match karte hue karte hain. Tumhara router `localStorage` ke `token` aur `role` use karta hai, to wahi rakhenge. 4 steps hain.

## Step 1: `frontend/src/auth.js` banao

```js
import { ref, computed } from 'vue'

export const token = ref(localStorage.getItem('token'))
export const role = ref(localStorage.getItem('role'))
export const isLoggedIn = computed(() => !!token.value)

export function saveLogin(newToken, newRole) {
  token.value = newToken
  role.value = newRole
  localStorage.setItem('token', newToken)
  localStorage.setItem('role', newRole)
}

export function logout() {
  token.value = null
  role.value = null
  localStorage.removeItem('token')
  localStorage.removeItem('role')
}
```

## Step 2: `LoginView.vue` mein login ke baad save karo

Flask ke login response mein `role` bhi bhejna hoga (`jsonify(access_token=access_token, role=user.role)`).

```js
import { useRouter } from 'vue-router'
import { saveLogin } from '@/auth'

const router = useRouter()
const dashboards = { Admin: '/admin', Staff: '/staff', Trekker: '/user' }

// API call successful hone ke baad:
saveLogin(res.data.access_token, res.data.role)
router.push(dashboards[res.data.role])
```

## Step 3: `frontend/src/components/AppNavbar.vue` banao

Sirf wahi links rakhe hain jinke pages tumhare paas abhi hain. Baaki pages banne par add kar lena.

```vue
<script setup>
import { useRouter } from 'vue-router'
import { isLoggedIn, role, logout } from '@/auth'

const router = useRouter()

function handleLogout() {
  logout()
  router.push('/login')
}
</script>

<template>
  <nav class="navbar navbar-expand-lg bg-body-tertiary">
    <div class="container-fluid">
      <RouterLink class="navbar-brand" to="/">Trek Partner</RouterLink>

      <button class="navbar-toggler" type="button"
        data-bs-toggle="collapse" data-bs-target="#navbarNav"
        aria-controls="navbarNav" aria-expanded="false" aria-label="Toggle navigation">
        <span class="navbar-toggler-icon"></span>
      </button>

      <div class="collapse navbar-collapse" id="navbarNav">
        <ul class="navbar-nav ms-auto">

          <template v-if="isLoggedIn">
            <li v-if="role === 'Admin'" class="nav-item">
              <RouterLink class="nav-link" to="/admin">Home</RouterLink>
            </li>
            <li v-else-if="role === 'Staff'" class="nav-item">
              <RouterLink class="nav-link" to="/staff">Home</RouterLink>
            </li>
            <li v-else-if="role === 'Trekker'" class="nav-item">
              <RouterLink class="nav-link" to="/user">Home</RouterLink>
            </li>

            <li class="nav-item">
              <a class="nav-link" href="#" @click.prevent="handleLogout">Logout</a>
            </li>
          </template>

          <template v-else>
            <li class="nav-item"><RouterLink class="nav-link" to="/login">Login</RouterLink></li>
            <li class="nav-item"><RouterLink class="nav-link" to="/register">Register</RouterLink></li>
          </template>

        </ul>
      </div>
    </div>
  </nav>
</template>
```

Agar Bootstrap ka JS (bundle) tumne `main.js` ya `index.html` mein add nahi kiya, to hamburger button mobile par kaam nahi karega. Uske liye `index.html` mein Bootstrap JS bundle ka script add karo.

## Step 4: `App.vue` mein navbar dikhao

```vue
<script setup>
import AppNavbar from '@/components/AppNavbar.vue'
</script>

<template>
  <AppNavbar />
  <RouterView />
</template>
```

## Router mein ek chhota fix

Router mein `localStorage` ki jagah `auth.js` use karna better hai, taaki sab jagah ek hi source rahe:

```js
import { token, role } from '@/auth'

router.beforeEach((to) => {
  if (to.meta.requiresAuth && !token.value) return '/login'

  if (to.meta.role && to.meta.role !== role.value) {
    return dashboards[role.value] || '/login'
  }

  if (to.meta.guestOnly && token.value && dashboards[role.value]) {
    return dashboards[role.value]
  }
})
```

## Aakhir mein check karo

1. Flask login response mein `role` hai aur uski spelling exactly `Admin`, `Staff`, `Trekker` hai.
2. Backend ke protected routes par role check lagao (`role_required`), kyunki frontend ka navbar aur router sirf dikhane ke liye hain, asli security Flask karta hai.
3. Jab koi API call 401 de (token expire), tab `logout()` chalake `/login` par bhej do.

Agar chaho to main axios ka interceptor bhi likh dun, jo har request mein token bhejega aur 401 par khud logout kara dega.
