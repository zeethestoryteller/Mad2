# Lecture Notes: Frontend Security (XSS, CSRF, CORS & Best Practices)

## 1. Introduction: The Frontend Security Landscape

As web applications grow more interactive—shifting from simple static HTML pages to complex client-side frameworks like Vue.js or React—the browser becomes a crucial battleground [[02:26](https://www.youtube.com/watch?v=M2Cm_Q_i8PI&t=146)]. Security cannot be managed by the server alone; developers must understand browser-side mechanisms, vulnerabilities, and defense strategies [[23:34](https://www.youtube.com/watch?v=M2Cm_Q_i8PI&t=1414)].

---

## 2. Cookies: Session vs. Permanent, First-Party vs. Third-Party

### **Types of Cookies**

* **Session Cookies:** Temporary cookies used to track active logins [[04:21](https://www.youtube.com/watch?v=M2Cm_Q_i8PI&t=261)]. Once you close the browser session, they expire. They allow servers to recognize you without asking for your password on every request [[04:31](https://www.youtube.com/watch?v=M2Cm_Q_i8PI&t=271)].
* **Permanent Cookies ("Remember Me"):** Stored on disk and automatically sent by the browser on future visits for convenience, but carry a higher risk profile if mishandled [[04:51](https://www.youtube.com/watch?v=M2Cm_Q_i8PI&t=291)].

### **First-Party vs. Third-Party Cookies**

* **First-Party Cookies:** Set by the domain you are actively visiting (useful for authentication and site state) [[05:58](https://www.youtube.com/watch?v=M2Cm_Q_i8PI&t=358)].
* **Third-Party Cookies:** Set by external domains (typically advertisers tracking your movement across unrelated websites) [[06:21](https://www.youtube.com/watch?v=M2Cm_Q_i8PI&t=381)]. Modern browsers are increasingly blocking or restricting third-party cookies for privacy protection [[06:53](https://www.youtube.com/watch?v=M2Cm_Q_i8PI&t=413)].

---

## 3. Cross-Site Scripting (XSS)

### **What is XSS?**

Cross-Site Scripting occurs when an application takes untrusted user input and includes it in a web page without proper validation or escaping, allowing attackers to inject malicious JavaScript into the browser [[08:45](https://www.youtube.com/watch?v=M2Cm_Q_i8PI&t=525)].

### **How it Happens**

1. **Reflected XSS:** An attacker tricks a user into clicking a malicious link containing a script tag inside a query parameter (e.g., `?q=<script>...</script>`). If the server echoes this parameter back into the HTML response unescaped, the browser executes the script [[08:37](https://www.youtube.com/watch?v=M2Cm_Q_i8PI&t=517)].
2. **Stored XSS:** The malicious script is permanently saved on the server database (e.g., via a compromised forum post or comment section) and loaded whenever other users visit that page [[09:12](https://www.youtube.com/watch?v=M2Cm_Q_i8PI&t=552)].

### **Prevention**

* Always validate and sanitize user inputs on the backend [[09:34](https://www.youtube.com/watch?v=M2Cm_Q_i8PI&t=574)].
* Escape output data before rendering it in HTML templates.

---

## 4. Cross-Site Request Forgery (CSRF)

### **What is CSRF?**

CSRF forces an authenticated user to execute unwanted actions on a web application in which they are currently logged in [[10:10](https://www.youtube.com/watch?v=M2Cm_Q_i8PI&t=610)].

### **The Mechanism**

1. Imagine you are logged into your bank (`bank.example.com`) in one tab [[10:16](https://www.youtube.com/watch?v=M2Cm_Q_i8PI&t=616)]. Your browser automatically attaches your session cookies to every request.
2. In another tab, you visit a malicious site containing a hidden link or auto-submitting form targeting the bank's API:
```html
<img src="https://bank.example.com/transfer?source=abc&target=attacker&amount=100000">

```


3. Because HTTP is stateless and the browser automatically sends your authentication cookies along with the request, the bank treats the request as legitimate [[12:04](https://www.youtube.com/watch?v=M2Cm_Q_i8PI&t=724)].

### **Prevention**

* **CSRF Tokens:** The application generates a secret, unique token tied to the user's current session [[12:32](https://www.youtube.com/watch?v=M2Cm_Q_i8PI&t=752)]. State-changing requests (like POST, PUT, DELETE) must include this token. If an external site tries to trigger the request, it won't know the token value, and the server will reject it.

---

## 5. Cross-Origin Resource Sharing (CORS)

### **What is CORS?**

CORS is a security mechanism implemented by web browsers to control how resources on one domain (e.g., `api.example.com`) can be requested from another domain (e.g., `[www.example.com](https://www.example.com)`) [[14:04](https://www.youtube.com/watch?v=M2Cm_Q_i8PI&t=844)].

### **How it Works**

* By default, browsers restrict cross-origin HTTP requests initiated from scripts due to the **Same-Origin Policy**.
* API servers use special response headers—most notably `Access-Control-Allow-Origin`—to explicitly specify which frontend origins are permitted to access their endpoints [[14:59](https://www.youtube.com/watch?v=M2Cm_Q_i8PI&t=899)].

---

## 6. Content Security Policy (CSP)

### **What is CSP?**

Content Security Policy is an HTTP header (`Content-Security-Policy`) that allows site administrators to declare approved sources of content that the browser is allowed to load and execute (e.g., restricting scripts, images, and stylesheets) [[15:36](https://www.youtube.com/watch?v=M2Cm_Q_i8PI&t=936)].

### **Example Configuration**

```http
Content-Security-Policy: default-src 'self'; img-src https://trusted-cdn.com; script-src 'self' https://apis.google.com

```

* **`default-src 'self'`**: Only allow content from the site's own origin by default [[16:16](https://www.youtube.com/watch?v=M2Cm_Q_i8PI&t=976)].
* **`script-src`**: Restricts JavaScript execution to trusted sources, mitigating XSS risks [[16:37](https://www.youtube.com/watch?v=M2Cm_Q_i8PI&t=997)].

---

## 7. Secure Contexts and Sandboxing

* **Secure Contexts:** Certain sensitive browser APIs (like access to the camera, microphone, or geolocation) require a **Secure Context**—meaning the page must be served over **HTTPS** rather than plain HTTP to prevent tampering and data interception [[17:19](https://www.youtube.com/watch?v=M2Cm_Q_i8PI&t=1039)].
* **Sandboxing:** Browsers use sandboxing to isolate web content and scripts [[20:36](https://www.youtube.com/watch?v=M2Cm_Q_i8PI&t=1236)]. A sandbox restricts code running inside it from accessing operating system files, local storage, or hardware resources unless explicitly permitted, preventing malicious web pages from harming the host machine [[21:07](https://www.youtube.com/watch?v=M2Cm_Q_i8PI&t=1267)].

---

## 8. Summary Checklist for Frontend Security

* $\square$ **Enforce HTTPS:** Ensure all resources are served securely to establish a trusted connection [[19:01](https://www.youtube.com/watch?v=M2Cm_Q_i8PI&t=1141)].
* $\square$ **Implement CSP Headers:** Restrict untrusted script execution and data injection vectors [[15:36](https://www.youtube.com/watch?v=M2Cm_Q_i8PI&t=936)].
* $\square$ **Protect Against CSRF:** Use anti-CSRF tokens for all state-changing API requests [[12:32](https://www.youtube.com/watch?v=M2Cm_Q_i8PI&t=752)].
* $\square$ **Sanitize and Escape Input:** Prevent XSS vulnerabilities by validating user inputs before rendering [[09:34](https://www.youtube.com/watch?v=M2Cm_Q_i8PI&t=574)].
