# Lecture Notes: Webhooks & Real-Time Data Exchange

## 1. What is a Webhook?

**Definition:** A webhook is a way for an application to provide other applications with real-time information [[00:30](https://www.youtube.com/watch?v=vVXiXZhn7hM&t=30)].
### The Core Idea: 
As soon as an event happens on **Server A**, it automatically pushes a notification/message to **Server B** [[01:29](https://www.youtube.com/watch?v=vVXiXZhn7hM&t=89)].
### Alternative Names:
* **Web Callback:** You register a callback URL so that when an event occurs, the system "calls you back" on that specific endpoint [[02:45](https://www.youtube.com/watch?v=vVXiXZhn7hM&t=165)].
* **HTTP Push API:** It acts like an API interface, but instead of you pulling data, it pushes data using the HTTP protocol [[01:47](https://www.youtube.com/watch?v=vVXiXZhn7hM&t=107)].
* **Reverse API:** Traditional APIs pull data *from* a server, whereas webhooks push messages *out* to a receiver [[04:07](https://www.youtube.com/watch?v=vVXiXZhn7hM&t=247)].



---

## 2. Technical Characteristics of Webhooks

* **Protocol:** Uses standard HTTP requests [[03:07](https://www.youtube.com/watch?v=vVXiXZhn7hM&t=187)].
* **HTTP Method:** **`POST`** requests are predominantly used [[03:15](https://www.youtube.com/watch?v=vVXiXZhn7hM&t=195)].
* *Why `POST`?* It allows you to package payload data neatly in the request body [[03:16](https://www.youtube.com/watch?v=vVXiXZhn7hM&t=196)].
* *Why not `GET`?* `GET` forces you to encode parameters into the URL string, limiting data size and causing issues with caching [[03:36](https://www.youtube.com/watch?v=vVXiXZhn7hM&t=216)].


* **Synchronous Nature:** Webhooks are fundamentally **synchronous** [[05:09](https://www.youtube.com/watch?v=vVXiXZhn7hM&t=309)].
* When a webhook fires, it makes a direct connection, sends the message, and expects an immediate response code (like an HTTP `200 OK`) [[07:03](https://www.youtube.com/watch?v=vVXiXZhn7hM&t=423)].
* **Important Caveat:** Webhooks do **not** natively provide store-and-forward or retry mechanisms (unlike Message Queues such as Celery or RabbitMQ) [[05:28](https://www.youtube.com/watch?v=vVXiXZhn7hM&t=328)]. If the receiving server is offline, the sender typically drops or ignores the failure [[05:35](https://www.youtube.com/watch?v=vVXiXZhn7hM&t=335)].
* **Best Practice:** The receiver should quickly accept the data, return a response immediately, and handle heavy backend processing asynchronously [[06:06](https://www.youtube.com/watch?v=vVXiXZhn7hM&t=366)]. Hanging a connection too long can cause platforms (like GitHub/GitLab) to disable your webhook endpoint [[06:36](https://www.youtube.com/watch?v=vVXiXZhn7hM&t=396)].



---

## 3. Practical Example: Setting Up Webhooks in GitLab

To understand how webhooks function in practice, we can look at Git hosting tools like GitLab or GitHub.

### Step 1: Setting up a Receiver for Debugging (RequestBin / Pipedream)

* Before writing a custom backend script, you can use temporary testing receivers like **RequestBin** (via Pipedream) [[08:14](https://www.youtube.com/watch?v=vVXiXZhn7hM&t=494)].
* RequestBin generates a unique URL (e.g., `[https://xyz.pipedream.net](https://xyz.pipedream.net)`) that logs all incoming HTTP requests, headers, and payloads for debugging [[08:24](https://www.youtube.com/watch?v=vVXiXZhn7hM&t=504)].

### Step 2: Registering a Webhook in GitLab

1. Navigate to your project repository in GitLab [[12:11](https://www.youtube.com/watch?v=vVXiXZhn7hM&t=731)].
2. Go to **Settings > Webhooks** [[12:15](https://www.youtube.com/watch?v=vVXiXZhn7hM&t=735)].
3. Paste your receiver URL (e.g., your RequestBin endpoint) [[14:13](https://www.youtube.com/watch?v=vVXiXZhn7hM&t=853)].
4. **Configure Secret Tokens (Optional but Recommended):**
* You can provide a secret token string [[12:32](https://www.youtube.com/watch?v=vVXiXZhn7hM&t=752)].
* GitLab passes this token inside an HTTP header named `X-Gitlab-Token` with every request [[12:41](https://www.youtube.com/watch?v=vVXiXZhn7hM&t=761)].
* **Security Benefit:** Your receiver script can verify this token to ensure requests genuinely originate from GitLab rather than malicious attackers spamming your endpoint [[12:58](https://www.youtube.com/watch?v=vVXiXZhn7hM&t=778)].


5. **Select Trigger Events:** Choose which actions trigger the webhook (e.g., *Push events*, *Tag push events*, *Comments on issues*) [[14:54](https://www.youtube.com/watch?v=vVXiXZhn7hM&t=894)].

---

## 4. Anatomy of a Webhook Payload

When a push event happens, GitLab sends a `POST` request containing a JSON payload with extensive metadata about the repository state.

### Key Headers Sent:

* `X-Gitlab-Event`: Identifies the exact type of event (e.g., `Push Hook`) [[17:52](https://www.youtube.com/watch?v=vVXiXZhn7hM&t=1072)].
* `X-Gitlab-Token`: Contains your secret verification token [[17:41](https://www.youtube.com/watch?v=vVXiXZhn7hM&t=1061)].

### Example JSON Payload Structure (Conceptual):

```json
{
  "object_kind": "push",
  "before": "9581f18541e2e92fb985a9ac424b8260d1c896f5",
  "after": "da1560886d4f094c3e6c9ef40349f7d38b5d27d7",
  "ref": "refs/heads/main",
  "user_username": "zeeshan",
  "project": {
    "id": 15,
    "name": "my-webhook-repo",
    "web_url": "https://gitlab.com/zeeshan/my-webhook-repo"
  },
  "commits": [
    {
      "id": "da1560886d4f094c3e6c9ef40349f7d38b5d27d7",
      "message": "Update README.md",
      "author": {
        "name": "Zeeshan Ahmed",
        "email": "zeeshan@example.com"
      }
    }
  ]
}

```

---

## 5. Implementing a Custom Webhook Receiver (Python / Flask Example)

Instead of relying on RequestBin, you can write a lightweight backend script using **Flask** to catch and process incoming webhook payloads.

```python
from flask import Flask, request, jsonify

app = Flask(__name__)

# Define a secret token for security validation
WEBHOOK_SECRET = "testing_gitlab_secret_123"

@app.route('/webhook', methods=['POST'])
def handle_webhook():
    # 1. Verify the secret token header from GitLab
    token = request.headers.get('X-Gitlab-Token')
    if token != WEBHOOK_SECRET:
        return jsonify({"error": "Unauthorized"}), 401

    # 2. Check the event type header
    event_type = request.headers.get('X-Gitlab-Event')
    print(f"Received event type: {event_type}")

    # 3. Parse the JSON payload sent by GitLab
    data = request.json
    if not data:
        return jsonify({"error": "Invalid JSON payload"}), 400

    # Extract useful information from the payload
    if event_type == "Push Hook":
        project_name = data.get("project", {}).get("name")
        user = data.get("user_username")
        total_commits = len(data.get("commits", []))
        
        print(f"User {user} pushed {total_commits} commit(s) to project {project_name}")

    # 4. Return an immediate HTTP 200 response to acknowledge receipt
    return jsonify({"status": "success"}), 200

if __name__ == '__main__':
    app.run(port=5000, debug=True)

```

---

*Reference Material:* [W10_L2: Webhooks | real time data exchange explained](http://www.youtube.com/watch?v=vVXiXZhn7hM)
