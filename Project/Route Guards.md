# Summary: Route Guards for Login and Roles in Vue

## The problem
After logging in, users could still open `/login`, and anyone could type `/admin` in the URL. The app needed rules about **who can see which page**.

## The core idea: a route guard
A **route guard** is a function that runs *before* every navigation. It looks at the destination and decides: allow it, or redirect somewhere else. In Vue Router this is `router.beforeEach`.

```javascript
router.beforeEach((to) => {
  // return nothing  -> allow
  // return '/path'  -> redirect there
})
```

## The three pieces working together

**1. Login stores identity**
```javascript
localStorage.setItem('token', res.data.access_token)
localStorage.setItem('role', res.data.user.role)
this.$router.replace(dashboards[role])
```

**2. Routes are labelled with `meta`**
```javascript
{ path: '/login', meta: { guestOnly: true } }                    // only for logged-out users
{ path: '/admin', meta: { requiresAuth: true, role: 'Admin' } }  // only for logged-in Admins
```

**3. The guard reads `meta` and decides**
```javascript
router.beforeEach((to) => {
  const token = localStorage.getItem('token')
  const role = localStorage.getItem('role')

  if (to.meta.requiresAuth && !token) return '/login'                          // not logged in
  if (to.meta.role && to.meta.role !== role) return dashboards[role] || '/login' // wrong role
  if (to.meta.guestOnly && token && dashboards[role]) return dashboards[role]    // already logged in
})
```

## The `dashboards` map
```javascript
const dashboards = { Admin: '/admin', Staff: '/staff', Trekker: '/user' }
```
It converts a role into that role's home page. It's used after login and in the guard's redirects. Forgetting to define it was the bug that caused a `ReferenceError` and broke the guard.

## Rules in plain English

| Situation | What happens |
|---|---|
| Logged out, opens `/admin` | Sent to `/login` |
| Logged in as Trekker, opens `/admin` | Sent to `/user` |
| Logged in, opens `/login` or `/register` | Sent to their dashboard |
| Logged in, opens their own dashboard | Allowed |

## Small details that matter
- **`replace` vs `push`:** `replace` removes the login page from history, so Back doesn't return to it.
- **Order of checks:** not logged in, then wrong role, then guest-only.
- **Logout must clear both** `token` and `role`, or the guard still thinks you're logged in.
- **No duplicate checks:** the extra `if (to.meta.role)` block was redundant and was removed.
- **Stale token, no role:** clear storage and send to login so the user isn't stuck.

## The most important caveat
Route guards are **only for user experience**. They hide pages in the browser, but anyone can edit `localStorage` in dev tools. **Real security lives in your Flask backend**, which must verify the JWT and the user's role on every protected API endpoint.

**In one line:** the guard decides which *pages* to show, and the backend decides which *data* to give.
