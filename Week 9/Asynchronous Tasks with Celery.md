# Lecture Notes: Asynchronous Tasks with Celery

**Course:** Modern Application Development II (IIT Madras)

**Topic:** Task Queues, Push vs. Pull Architecture, and Celery in Python

---

## 1. What is a Task Queue?

When a web server handles high user traffic, certain long-running operations (like sending bulk emails, processing images, or running heavy reports) shouldn't block the main request/response thread [[00:30](https://www.youtube.com/watch?v=xLbVR5_ygFk&t=30)].

* **User Request Handler:** Receives incoming HTTP traffic and quickly pushes background jobs onto a queue [[00:43](https://www.youtube.com/watch?v=xLbVR5_ygFk&t=43)].
* **Separation of Concerns:** The main application stays responsive to users while background worker processes pull tasks from the queue and execute them asynchronously [[01:54](https://www.youtube.com/watch?v=xLbVR5_ygFk&t=114)].

### Core Rules for Task Queues:

1. **Fast Pushing:** Pushing a task onto a queue must be extremely fast—faster than executing the task directly [[04:18](https://www.youtube.com/watch?v=xLbVR5_ygFk&t=258)].
* *Bad use case:* Pushing simple arithmetic like `3 + 4` to a queue overheads execution [[04:28](https://www.youtube.com/watch?v=xLbVR5_ygFk&t=268)].
* *Good use case:* Creating a new record in a database, then dispatching background jobs to send alert emails to hundreds of subscribers [[05:44](https://www.youtube.com/watch?v=xLbVR5_ygFk&t=344)].


2. **Preventing Backlogs:** Workers must actively drain the queue faster than producers fill it, otherwise buffers overflow and tasks get dropped [[06:47](https://www.youtube.com/watch?v=xLbVR5_ygFk&t=407)].

---

## 2. Push Queues vs. Pull Queues

| Feature | Push Queue | Pull Queue |
| --- | --- | --- |
| **Execution Trigger** | The server/broker tries to start executing the task **immediately** upon receipt [[10:20](https://www.youtube.com/watch?v=xLbVR5_ygFk&t=620)]. | Workers **poll** or look up tasks periodically at regular time intervals [[11:52](https://www.youtube.com/watch?v=xLbVR5_ygFk&t=712)]. |
| **Ideal Use Case** | Real-time immediate events (e.g., instant social media notifications, sending a single password reset email) [[10:49](https://www.youtube.com/watch?v=xLbVR5_ygFk&t=649)]. | Batch processing and dashboards (e.g., updating leaderboards or compiling game scores every 5 seconds) [[12:10](https://www.youtube.com/watch?v=xLbVR5_ygFk&t=730)]. |

### Polling Mechanisms:

* **Standard Polling:** Worker repeatedly asks the server *“Is there data for me?”* every few milliseconds, consuming unnecessary CPU power [[13:58](https://www.youtube.com/watch?v=xLbVR5_ygFk&t=838)].
* **Long Polling:** The client opens a connection and holds a **blocking read**. The server leaves the thread open and sends data back only when it's actually ready, making it look instantaneous without continuous active polling [[15:20](https://www.youtube.com/watch?v=xLbVR5_ygFk&t=920)].

---

## 3. Distributed System Risks & Pitfalls

* **Deadlocks:** A state where Server A waits for output from Server B, but Server B is blocked waiting for Server A or a resource to clear, halting the system entirely [[07:42](https://www.youtube.com/watch?v=xLbVR5_ygFk&t=462)].
* **Buffer Overflow:** If the task queue buffer fills up because workers are too slow or traffic spikes uncontrollably, incoming requests will be dropped [[09:37](https://www.youtube.com/watch?v=xLbVR5_ygFk&t=577)].

---

## 4. Introduction to Celery

**Celery** is a popular, open-source asynchronous task queue/job queue written in Python. It allows developers to handle background work seamlessly by abstracting away the underlying message broker [[18:02](https://www.youtube.com/watch?v=xLbVR5_ygFk&t=1082)].

### Architecture of a Celery Setup:

1. **Your Flask Application:** Dispatches asynchronous tasks.
2. **Message Broker (e.g., RabbitMQ or Redis):** Stores and routes tasks safely across networks.
3. **Celery Worker Processes:** Separate processes running in the background that continuously pull tasks and execute them [[20:00](https://www.youtube.com/watch?v=xLbVR5_ygFk&t=1200)].
4. **Result Backend (Optional):** Stores task states and return values so your app can check if a task succeeded or failed.

---

## 5. Practical Notebook Example: Writing and Running a Celery Task

Imagine you are building a Flask app and want to send a welcome email in the background so the user doesn't have to wait. Here is how a basic Celery setup looks:

### Step 1: Initialize Celery and Define a Task (`tasks.py`)

```python
from celery import Celery
import time

# Initialize Celery and link it to a Redis broker running locally
app = Celery(
    'tasks',
    broker='redis://localhost:6379/0',
    backend='redis://localhost:6379/0'
)

# Define an asynchronous task using the @app.task decorator
@app.task
def send_welcome_email(user_email):
    print(f"[Worker] Preparing to send email to {user_email}...")
    time.sleep(5)  # Simulate a heavy network delay (e.g., talking to SMTP server)
    print(f"[Worker] Email successfully sent to {user_email}!")
    return "Success"

```

### Step 2: Triggering the Task from your Flask App (`app.py`)

When a user registers, instead of blocking the request for 5 seconds to send the email, we trigger the background task using `.delay()`:

```python
from flask import Flask, jsonify
from tasks import send_welcome_email

app = Flask(__name__)

@app.route('/register', methods=['POST'])
def register_user():
    # 1. Quickly save user to database (mocked here)
    user_email = "student@example.com"
    
    # 2. Fire the background task asynchronously (returns instantly!)
    # Notice we use .delay() instead of calling the function normally
    send_welcome_email.delay(user_email)
    
    print("[Flask] Response returned immediately to the client.")
    return jsonify({"message": "Registration successful! Welcome email is being sent."}), 200

if __name__ == '__main__':
    app.run(debug=True)

```

### Step 3: Running the Worker via Terminal

To make sure those tasks actually get executed, you have to spin up a Celery worker from your terminal alongside your Flask app:

```bash
celery -A tasks.py worker --loglevel=info

```
