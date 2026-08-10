 # modern Promises and async/await

Asynchronous JavaScript allows your program to perform long-running tasks (like fetching data from an API, reading files, or waiting for timers) in the background without freezing the main thread or blocking the user interface.
Here is a step-by-step guide to mastering asynchronous JavaScript, moving from traditional callbacks to modern Promises and async/await.

---
### 1. Why Do We Need Asynchronicity?
JavaScript is single-threaded, meaning it executes one command at a time on a single main thread. If a task takes 5 seconds to complete (e.g., downloading a large file), a synchronous program would freeze completely for 5 seconds.
Asynchronous programming solves this by sending off background tasks and continuing to run the rest of the code immediately. Once the background task finishes, its result is handled via callbacks, promises, or async/await.

---
### 2. Callbacks (The Old Way)
A callback is simply a function passed as an argument to another function, to be executed once an asynchronous operation is complete.
```JavaScript

function fetchData(callback) {
  setTimeout(() => {
    const data = { id: 1, name: "Alice" };
    callback(data);
  }, 2000); // Simulates a 2-second delay
}
fetchData((result) => {
  console.log("Data received:", result);
});
```
The Problem: Callback Hell
When you have multiple dependent asynchronous operations, callbacks must be nested inside one another, leading to deeply indented, unreadable code often called "Callback Hell" or the "Pyramid of Doom."

---
### 3. Enter Promises
A Promise is an object representing the eventual completion (or failure) of an asynchronous operation and its resulting value.
A Promise can be in one of three states:
1. Pending: Initial state; neither fulfilled nor rejected.
2. Fulfilled (Resolved): The operation completed successfully, and the promise now has a value.
3. Rejected: The operation failed, and the promise has a reason (error).
Creating a Promise
```JavaScript

const fetchUserData = new Promise((resolve, reject) => {
  let success = true; // Simulating success or failure
setTimeout(() => {
    if (success) {
      resolve({ id: 42, username: "Bob" });
    } else {
      reject("Failed to fetch user data.");
    }
  }, 1500);
});
```
# Consuming a Promise (.then, .catch, .finally)
You interact with promises using `.then()` for success, `.catch()` for errors, and `.finally()` for cleanup code that runs regardless of the outcome.

```JavaScript

fetchUserData
  .then((user) => {
    console.log("Success:", user.username);
    return user.id; // Passed to the next .then()
  })
  .then((userId) => {
    console.log("User ID:", userId);
  })
  .catch((error) => {
    console.error("Error:", error);
  })
  .finally(() => {
    console.log("Operation complete.");
  });
```
---
### 4. Modern Standard: Async / Await
Introduced in ES2017, async and await are syntactic sugar built on top of Promises. They let you write asynchronous code that looks and behaves like synchronous code, making it much easier to read and maintain.
Using async and await
* Mark a function with the async keyword to make it automatically return a promise.
* Use the await keyword inside an async function to pause execution until the promise settles (resolves or rejects).
```JavaScript

// A helper function returning a promise
const delay = (ms) => new Promise((resolve) => setTimeout(resolve, ms));
async function processUserFlow() {
  try {
    console.log("Fetching user...");
    await delay(1500); // Pauses execution for 1.5 seconds
    
    const user = { id: 101, name: "Charlie" };
    console.log("User fetched:", user.name);
console.log("Loading user posts...");
    await delay(1000);
    console.log("Posts loaded successfully!");
} catch (error) {
    console.error("An error occurred:", error);
  } finally {
    console.log("Process finished.");
  }
}

processUserFlow();
```
---
### Example of async function:

Ye JavaScript code ek async function ka use karke API se data fetch karne aur errors ko handle karne ke liye likha gaya hai. 

***`FetchFunct` Function Definition***

```javascript
async function FetchFunct(ApiUrl) {
    const response = await fetch(ApiUrl).catch(() => {
        throw new Error('Network Error')
    })

```

* **`async function`**: Yeh ek asynchronous function hai jo promises ke sath kaam karta hai (`await` keyword ka use karne ke liye).
* **`fetch(ApiUrl)`**: Yeh di gayi URL (`ApiUrl`) par HTTP request bhejta hai.
* **`.catch(...)`**: Agar network connection fail hojata hai (jaise internet nahi hai ya URL invalid hai), toh yeh catch block chalega aur `Network Error` throw kar dega.



***Response Ko Check karna aur Parse karna***

```javascript
    if (response) {
        if (response.ok) {
            const data = await response.json().catch(() => {
                throw new Error('Unexpected Error')
            })
            if (data) {
                return data
            }
        } else {
            throw new Error(response.statusText)
        }
    }
}

```

* **`response.ok`**: Yeh check karta hai ki HTTP response status successful hai ya nahi (status code 200–299 ke beech mein hai ya nahi).
* **`response.json()`**: Agar response ok hai, toh yeh response body ko JSON object mein parse karta hai.
* **Inner `.catch(...)**`: Agar server se data JSON format mein nahi aata ya parsing fail ho jati hai, toh yeh `Unexpected Error` throw karta hai.
* **`else` Block**: Agar response successful nahi hota (jaise 404 Not Found ya 500 Server Error), toh yeh server ka status text (`response.statusText`, jaise "Not Found") error ke roop mein throw kar deta hai.

***Function Call aur Execution***

```javascript
const url = 'dummyUrl'
FetchFunct(url)
    .then((data) => {
        console.log(data)
    })
    .catch((err) => {
        console.log(err.message)
    })

```

* **`const url = 'dummyUrl'`**: Ek dummy URL variable banaya gaya hai. (Note: `'dummyUrl'` ek valid URL nahi hai, isliye yeh network ya request fail kar dega).
* **`.then(...)`**: Agar function successfully data return karta hai, toh woh data console par print ho jata hai.
* **`.catch(...)`**: Agar function ke andar koi error throw hota hai (jaise question paper ke Question 4 mein diya gaya hai ki `dummyUrl` HTML payload return karta hai ya network/parsing fail hoti hai), toh error ka message console par print ho jata है.
---
### 5. Handling Multiple Promises Concurrently
If you have multiple asynchronous tasks that do not depend on each other, running them sequentially with await wastes time. Instead, run them concurrently.
*	Promise.all([p1, p2, p3]): Waits for all promises to resolve. If any promise rejects, the whole thing fails.
*	Promise.allSettled([p1, p2, p3]): Waits for all promises to finish, regardless of whether they succeed or fail, returning an array of their statuses.
*	Promise.race([p1, p2]): Returns the result of the first promise that settles (wins the race).
```JavaScript

const fetchUsers = Promise.resolve(["Alice", "Bob"]);
const fetchPosts = Promise.resolve(["Post 1", "Post 2"]);
async function loadDashboard() {
  try {
    // Both fetch requests run simultaneously
    const [users, posts] = await Promise.all([fetchUsers, fetchPosts]);
    
    console.log("Users:", users);
    console.log("Posts:", posts);
  } catch (error) {
    console.error("One of the requests failed:", error);
  }
}
loadDashboard();
```
---

### Note:

1. `reponse.ok`: Yeh check karta hai ki HTTP response status successful hai ya nahi (status code 200–299 ke beech mein hai ya nahi)
