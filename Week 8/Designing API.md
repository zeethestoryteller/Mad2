# Designing API


**Remote Procedure Call (RPC)** is a foundational client-server communication concept used for several decades.

* **Definition:** RPC allows a client program (e.g., Client A) to connect to a remote server (e.g., Server B) and execute a function or procedure located on that server.
* **Functionality:** It handles how the client connects to the server, passes arguments/parameters into the function, and receives the resulting response back from the server.
* **Evolution into Web APIs:** Modern web APIs are an evolution of RPC. Instead of traditional custom networking protocols, web APIs typically leverage HTTP as a simple, text-based communication layer to invoke these remote functions, pass parameters, and receive responses.

---

## 1. HTTP Methods and Verbs

When designing an API, URLs should focus on **entities (nouns)**, while actions are handled by standard **HTTP verbs**.

* **`GET` (Read Data):**
* **Purpose:** Used to retrieve data (either a single item or a list) [[00:28](https://www.youtube.com/watch?v=VM-2WK2KG4I&t=28)].
* **Caching:** Highly **cacheable**. Since `GET` requests should not modify the database, repeated requests to the same URL return identical data (unless the server-side data changes) [[00:48](https://www.youtube.com/watch?v=VM-2WK2KG4I&t=48)].


* **`POST` (Create Data):**
* **Purpose:** Used to create new data or resources [[01:58](https://www.youtube.com/watch?v=VM-2WK2KG4I&t=118)].
* **Payload:** The data is passed in the **request body**, not in the URL [[01:53](https://www.youtube.com/watch?v=VM-2WK2KG4I&t=113)].
* **Caching:** **Not cacheable** because URLs stay identical while the underlying states and bodies change [[02:22](https://www.youtube.com/watch?v=VM-2WK2KG4I&t=142)].


* **Other Verbs:** `PUT`, `PATCH`, and `DELETE` carry standard intuitive definitions for updating and removing resources [[02:51](https://www.youtube.com/watch?v=VM-2WK2KG4I&t=171)].
* **Golden Rule:** **Never break conventions!** Unexpected behavior frustrates developers using your API [[03:15](https://www.youtube.com/watch?v=VM-2WK2KG4I&t=195)].

---

## 2. API Output Formats

When building an API, you return structured data rather than rendered HTML templates so the front end can render it flexibly [[04:14](https://www.youtube.com/watch?v=VM-2WK2KG4I&t=254)].

* **XML:** Excellent for hierarchical types and validation, but **too verbose** and complex for modern needs [[04:43](https://www.youtube.com/watch?v=VM-2WK2KG4I&t=283)].
* **JSON (JavaScript Object Notation):**
* **Why it's preferred:** Lightweight, highly human-readable, and easy to parse across most programming languages [[05:26](https://www.youtube.com/watch?v=VM-2WK2KG4I&t=326)].
* **Limitation:** Lacks strict native support for complex data types (like class objects), requiring system-level parsing intelligence [[05:08](https://www.youtube.com/watch?v=VM-2WK2KG4I&t=308)].



---

## 3. Self-Linking and API Discoverability

Good APIs often include links within their JSON responses to aid debugging, auditing, and exploration [[07:04](https://www.youtube.com/watch?v=VM-2WK2KG4I&t=424)].

* **Self-Links:** Point back to the exact URL used to fetch the resource [[07:38](https://www.youtube.com/watch?v=VM-2WK2KG4I&t=458)].
* **Related Links:** Provide endpoints to nested resources (e.g., a user's followers or repository links), allowing developers to "crawl" the API schema dynamically [[08:18](https://www.youtube.com/watch?v=VM-2WK2KG4I&t=498)].

---

## 4. API Authentication

* Avoid custom, home-baked security protocols since they easily introduce vulnerabilities [[12:15](https://www.youtube.com/watch?v=VM-2WK2KG4I&t=735)].
* **Best Practices:** Use established, heavily studied industry standards like **OAuth 2.0** or **JSON Web Tokens (JWT)** to securely validate requests via authentication tokens [[10:56](https://www.youtube.com/watch?v=VM-2WK2KG4I&t=656)].

---

## 5. Limitations of REST Architecture

While REST is a great architectural style based on the design principles of the web, it has a few practical friction points:

* **Chatty Protocols:** Sometimes requires multiple round-trip requests to fetch deeply nested or aggregated data (e.g., getting a student's profile, then their courses, then their individual grades step-by-step) [[15:34](https://www.youtube.com/watch?v=VM-2WK2KG4I&t=934)].
* **Rigid Query Structures:** Standard REST endpoints return full resource objects rather than letting clients request specific fields dynamically (e.g., fetching an entire user record when you only need a name and roll number) [[16:23](https://www.youtube.com/watch?v=VM-2WK2KG4I&t=983)].
* **Design Takeaway:** Focus on building a practical, user-friendly **web API**. If you need to bend a strict REST rule to make your system easier to use, it is acceptable as long as you understand why you are doing it [[15:09](https://www.youtube.com/watch?v=VM-2WK2KG4I&t=909)].

---

## Quick Code & Conceptual Summary Examples

### Concept A: RESTful Endpoint Route Mapping (Flask Style)

```python
from flask import Flask, jsonify, request

app = Flask(__name__)

# GET: Retrieve resource list or a specific item
@app.route('/api/students', methods=['GET'])
def get_students():
    # Returns a list of student JSON data (Cacheable)
    return jsonify([{"id": 1, "name": "Zeeshan"}, {"id": 2, "name": "Alex"}])

# POST: Create a new resource using request body data
@app.route('/api/students', methods=['POST'])
def create_student():
    incoming_data = request.json  # Data extracted from request body
    # Logic to save to database goes here...
    return jsonify({"status": "success", "created": incoming_data}), 201

if __name__ == '__main__':
    app.run(debug=True)

```

### Concept B: JSON Response Format with Hypermedia Links Example

```json
{
  "id": 42,
  "login": "z_ahmed",
  "name": "Zeeshan Ahmed",
  "links": {
    "self": "https://api.example.com/users/42",
    "followers": "https://api.example.com/users/42/followers",
    "repositories": "https://api.example.com/users/42/repos"
  }
}

```
