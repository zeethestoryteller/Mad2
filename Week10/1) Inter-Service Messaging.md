# Lecture Notes: Inter-Service Messaging & Webhooks

## 1. Introduction & Context Recap

**Where we left off:** In previous classes, we talked about **message queues** and how they help us build **task queues** for backend services (like handling background emails, SMS, or payments).
* **The Problem with Tight Coupling:** A typical web app has multiple servers (frontend, database, email service, payment gateway) working together. If one fails, the app breaks.
### The Message Broker Solution:
Instead of point-to-point connections, we put a message broker (like Redis or RabbitMQ) in the middle.
* Server A pushes a message to the queue.
* The message broker guarantees delivery, ordering, and automatic retries.
* Server B picks it up and processes it asynchronously.


**The Limitation:** Message queues are fantastic for internal services within the same data center. But what happens when we scale to **internet-distributed services** (e.g., using third-party services like Twilio, GitHub, or AWS)? Setting up a message broker across the public internet introduces heavy infrastructure and complex authentication overhead.

---
**Message Broker** and **Message Queue**, **Core Differences**

| Feature | Message Queue | Message Broker |
| --- | --- | --- |
| **Communication Pattern** | Point-to-Point (1-to-1) | Point-to-Point, Publish-Subscribe, Request-Reply (1-to-many or complex routing) |
| **Message Lifetime** | Usually deleted immediately after successful processing by a single consumer. | Can retain messages for multiple consumers, persist them to disk, or broadcast them via topics. |
| **Routing Logic** | Simple FIFO (First-In, First-Out) buffer. | Advanced routing using rules, exchanges, topic filters, and transformations. |
| **Primary Focus** | Task distribution, load leveling, and background job processing. | Enterprise service integration, decoupling complex distributed systems, and event broadcasting. |
| **Popular Examples** | Amazon SQS (standard queues), RabbitMQ (basic queue mode), Redis (Lists). | Apache Kafka, RabbitMQ (exchanges/topics), Apache Pulsar, AWS SNS/SQS combo. |
---

## 2. Moving to Internet-Distributed Services & Lightweight APIs

* When interacting with public APIs (like Wikipedia or GitHub), we usually send a **GET request** to *retrieve* information from the server.
* But for inter-service notifications (e.g., telling our system that an external event occurred), we need the reverse: **a server pushing information to another server**.
* **The Requirement:** We don't always need a heavy message broker with strict guarantees. Sometimes, we just want a **lightweight API call** where a server exposes an endpoint, and others can post simple messages to it.

---

## 3. What is a Webhook?

> **Definition:** A webhook is a lightweight, event-driven mechanism where one system uses standard web infrastructure (HTTP POST requests) to send real-time data or messages to another system's registered URL.

* **How it works:** Instead of polling a server repeatedly to ask *"Is there any update?"*, the server **notifies you automatically** the moment an event happens by making an HTTP request to a URL you provided.
* **Why use it?** It avoids the need to set up and maintain a full message queue infrastructure just to receive simple event notifications from external platforms.

---

## 4. Real-World Examples of Webhooks

### Example A: GitHub and Chat Integrations (e.g., Google Chat / Slack)

* **Scenario:** You want a notification in your team chatroom every time someone pushes a new code commit to a GitHub repository.
* **The Process:**
1. GitHub doesn't natively know how to talk to every custom chat app.
2. Instead, GitHub allows you to **register a webhook URL** in your repository settings.
3. Whenever a developer pushes code, GitHub automatically sends an **HTTP POST request** (carrying a JSON payload with commit details) to that registered URL.
4. Your custom backend server (listening at that URL) catches the request and forwards the message to your team chatroom.



### Example B: Bulk Messaging with Twilio (Callbacks)

* **Scenario:** You send a large batch of SMS messages through Twilio.
* **The Process:**
1. You dump your messages onto Twilio to send out asynchronously.
2. Once Twilio finishes processing the batch, it needs to tell you the final status.
3. Twilio makes an **HTTP callback** to a webhook URL you provided during registration, sending a payload detailing which messages were sent successfully and which failed.



---

## 5. Conceptual Comparison: Message Queues vs. Webhooks

| Feature | Message Queues (e.g., Celery/RabbitMQ) | Webhooks |
| --- | --- | --- |
| **Primary Use Case** | Internal inter-service & task processing | External, internet-distributed event notifications |
| **Infrastructure** | Requires a dedicated message broker | Uses standard HTTP servers & REST endpoints |
| **Communication Flow** | Pull/Broker-mediated | Direct push via HTTP POST to a URL |
| **Guarantees** | Heavy guarantees (retries, persistence, ordering) | Lightweight; relies on HTTP success/failure responses |

---

## 6. Code Example: Receiving a Webhook in Flask

Here is a simple Python Flask server acting as a webhook endpoint (similar to what you would set up to catch events from GitHub or Twilio):

```python
from flask import Flask, request, jsonify

app = Flask(__name__)

@app.route('/webhook/github-events', methods=['POST'])
def handle_github_webhook():
    # 1. Extract the JSON payload sent by the external service
    event_data = request.json
    
    if not event_data:
        return jsonify({"error": "No data received"}), 400

    # 2. Inspect the event type (e.g., push event)
    # GitHub often sends headers like 'X-GitHub-Event' to specify the action
    event_type = request.headers.get('X-GitHub-Event', 'unknown')
    
    print(f"Received event: {event_type}")
    
    if event_type == 'push':
        pusher = event_data.get('pusher', {}).get('name', 'Unknown')
        repo = event_data.get('repository', {}).get('name', 'Unknown')
        print(f"User {pusher} pushed code to repository {repo}!")
        
        # TODO: Add logic here to notify your chat room (e.g., Google Chat)

    # 3. Acknowledge receipt back to the sender with a 200 OK
    return jsonify({"status": "success", "message": "Webhook processed"}), 200

if __name__ == '__main__':
    app.run(port=5000, debug=True)

```

### Key Takeaway for Your Notebook:

* Webhooks use **HTTP POST** requests to reverse the traditional client-server dynamic: instead of you fetching data, the remote server pushes data *to you* whenever an event occurs.

---

Source video reference: [W10_L1: Inter-service messaging | webhooks vs message queues explained](http://www.youtube.com/watch?v=ffHIEp4smlQ)
