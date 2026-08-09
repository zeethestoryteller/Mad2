# Introduction & Problem with Default Apps

In standard web architecture, data is stored on a server, and the browser acts purely as a client or front-end. Every time a Vue application page is reloaded, the entire app re-initializes, starting with a fresh instance and losing any current local inputs.

## Why Persistent Storage?
* **Offline Capabilities:** Allows apps to work offline (e.g., while traveling in an airplane).
* **Reduced Server Load:** Avoids unnecessary server requests for small, client-specific utilities (e.g., Tic-Tac-Toe scores, weather app city lists).
* **Configuration Management:** Stores local user preferences and configuration information.

---

## Three Browser Storage Options
Modern browsers provide three primary mechanisms for local storage:

### 1. Cookies
* Usually managed by servers to track state/authentication via round-trips.
* Can also be managed directly using JavaScript.
* **Limitation:** Strict size limits (ranging from several hundred kilobytes up to ~1 MB).

### 2. Local Storage
* **API:** Built-in Web Storage API functioning as a key-value store (strings for both keys and values).
* **Complex Objects:** Must be serialized/deserialized using JavaScript methods:
  * `JSON.stringify()`: Converts complex objects into a continuous string format for storage.
  * `JSON.parse()`: Reconstructs the original object when reading back.
* **Persistence:** Persists even when the browser or machine is closed and restarted.
* **Scope:** Bound strictly to a specific browser and origin (e.g., Chrome and Edge cannot share local storage).

### 3. IndexedDB
* The most powerful local storage mechanism available.
* Operates as a full transactional, object-oriented database system.
* Ideal for applications needing to store significant amounts of structured data locally.

---

## Session Storage vs. Local Storage

* **Session Storage:**
  * A separate storage area per origin available only for the duration of the page session.
  * Flushed and destroyed the moment the tab or browser window is closed.
* **Local Storage:**
  * Persists across browser restarts and computer reboots.
  * Remains accessible until explicitly cleared via code or developer tools.
* Both localStorage and sessionStorage can store data in key-value pair format
```JavaScript
// Data store karna (Dono mein tarika bilkul same hota hai)
localStorage.setItem('username', 'Zeeshan');
sessionStorage.setItem('theme', 'dark');

// Data retrieve karna
let user = localStorage.getItem('username'); // Output: 'Zeeshan'
let theme = sessionStorage.getItem('theme'); // Output: 'dark'
```

---

## Vue.js Implementation Examples

### Example A: Automatic Persistence via Watchers
* **Concept:** Automatically saves form inputs to local storage whenever a value changes, and restores it when the component loads.
* **Code Logic:**
  * **Mounted Hook:** Checks if local storage has an existing entry (`localStorage.name`) and loads it into the Vue instance data (`this.name`) on page load.
  * **Watcher:** Monitors changes to the variable and updates the local storage automatically in real-time.

<img width="685" height="523" alt="Automatic Persistence via Watchers" src="https://github.com/user-attachments/assets/f15122f3-07fc-44e9-a0f6-8f61c85e5bd6" />

### Example B: Manual Persistence via Action Buttons
* **Concept:** Holds changes in temporary component state until the user explicitly triggers a save action.
* **Code Logic:**
  * Uses input fields for multiple properties (e.g., name and age).
  * Data is loaded on the mounted hook.
  * Changes are only written to local storage when a specific button (e.g., a "Save" / persist function) is clicked. If the page is reloaded without clicking save, unsaved changes are lost.

<img width="717" height="660" alt="Manual Persistence via Action Buttons" src="https://github.com/user-attachments/assets/d2591a8e-b5cf-4b78-bae3-b39abcd12e3b" />

---

## Key Methods Summary for `localStorage`
* **Save/Update data:** `localStorage.setItem('key', value)`
* **Read data:** `localStorage.getItem('key')`
* **Remove specific item:** `localStorage.removeItem('key')`
* **Clear all local storage:** `localStorage.clear()`

---

## Key Takeaways & Limitations
* **Local means Local:** Storage is restricted to the specific browser instance on that specific machine. If a browser is reinstalled, uninstalled, or switched, the local storage data is lost or inaccessible.
* **Performance:** Accessing local storage is significantly faster than executing network calls to a remote server.
* **Master Copy Rule:** For critical data (like emails), server-side storage remains the master copy; local storage should be used wisely for caching, preferences, or offline support.
