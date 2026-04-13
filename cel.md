I’ll first load the files in qu49 to see the full code, then I’ll walk through how all parts (Flask, Celery worker, tasks) work together.

Read [](file:///e%3A/Final-assesment-meet/qu49/main.py)

Read [](file:///e%3A/Final-assesment-meet/qu49/tasks.py)

Read [](file:///e%3A/Final-assesment-meet/qu49/celery_worker.py)

Read [](file:///e%3A/Final-assesment-meet/qu49/requirements.txt)

Here’s how the qu49 project works, piece by piece, and how they fit together.

**1. Big Picture**

- This is a background email system using Celery + Redis.
- celery_worker.py configures the Celery app (broker, backend, queues).
- tasks.py defines Celery tasks to send one email and to send many emails.
- main.py is a simple script that triggers the bulk email task asynchronously.
- Redis must be running, and a Celery worker must be started to process tasks.

---

**2. Celery configuration – celery_worker.py**

- Imports and creates the Celery app:

  ```python
  from celery import Celery

  celery_app = Celery(
      "email_service",
      broker="redis://localhost:6379/0",
      backend="redis://localhost:6379/1",
      include=['tasks']
  )
  ```

  - `broker`: Redis DB 0 holds queued tasks.
  - `backend`: Redis DB 1 stores task results/status.
  - `include=['tasks']`: Celery will import the `tasks` module so it knows about task functions.

- Extra config:

  ```python
  celery_app.conf.update(
      task_serializer='json',
      task_routes={
          "tasks.send_email": {"queue": "emails"},
      },
      task_default_retry_delay=5,
      task_annotations={
          "tasks.send_email": {"max_retries": 3}
      }
  )
  ```

  - `task_serializer='json'`: tasks args/results encoded as JSON.
  - `task_routes`: `tasks.send_email` is sent to the `emails` queue.
  - Default retry delay 5 seconds.
  - `max_retries` for `send_email` is 3.

So `celery_app` is the central object used by all tasks.

---

**3. Tasks – tasks.py**

Imports:

- `celery_app` from `celery_worker`.
- `time`, `random`, `logging` for simulation and logs.

Logging is configured:

```python
logging.basicConfig(filename='celery-logs.log', level=logging.INFO)
logger = logging.getLogger("Tasks")
```

So all task logs go to `celery-logs.log`.

---

**3.1 send_email task**

Decorator:

```python
@celery_app.task(bind=True, max_retries=3, default_retry_delay=5)
def send_email(self, to, subject, body):
```

- `bind=True` gives the task `self`, letting it call `self.retry`.
- `max_retries=3`, `default_retry_delay=5` mirror the requirement.

Flow:

1. Logs start:

   ```python
   logger.info(f"Sending email to {to}")
   ```

2. Simulates sending:

   ```python
   time.sleep(1)
   ```

3. Randomly succeeds or fails:

   ```python
   if random.choice([True, False]):
       raise Exception("Simulated email failure")
   ```

4. On success:

   ```python
   logger.info(f"Email successfully sent to {to}")
   return True
   ```

5. On exception:

   ```python
   except Exception as exc:
       logger.error(f"Failed to send email to {to}: {str(exc)}")

       try:
           self.retry(exc=exc)
       except self.MaxRetriesExceededError:
           logger.error(f"Max retries exceeded for {to}")
           return False
   ```

- `self.retry` schedules the same task again after 5 seconds (up to 3 times).
- If even after retries it still fails, it logs “Max retries exceeded” and returns `False`.
- This matches: retry on failure, max 3, 5 seconds delay, log failures.

---

**3.2 send_bulk_emails task**

Decorator:

```python
@celery_app.task
def send_bulk_emails(users):
```

- `users` is a list like:
  ```python
  [{"email": "a@test.com"}, {"email": "b@test.com"}]
  ```

Flow:

1. Initializes a `results` list.

2. Loops all users:

   ```python
   for user in users:
       email = user.get("email")

       result = send_email.delay(
           to=email,
           subject="Hello!",
           body="This is a test email by Meet.!"
       )

       results.append({
           "email": email,
           "task_id": result.id
       })
   ```

   - For each user, it calls `send_email.delay(...)`.
   - `.delay(...)` does NOT run the function immediately; it sends a task message to Redis.
   - The Celery worker process picks these up and runs them in parallel/async.
   - `result.id` is the Celery task ID; it’s recorded along with the email.

3. Returns the list of task IDs:

   ```python
   return results
   ```

So `send_bulk_emails` itself is also a Celery task, and inside it, it fires many `send_email` tasks asynchronously – satisfying “run tasks in parallel” and “if one fails, others still continue” (because each email is a separate task).

---

**4. Trigger script – main.py**

```python
from tasks import send_bulk_emails

users = [
    {"email": "a@test.com"},
    {"email": "b@test.com"},
    {"email": "c@test.com"},
]

result = send_bulk_emails.delay(users)

print("Bulk email task triggered:", result.id)
```

- Imports the `send_bulk_emails` task.
- Defines a list of 3 fake users.
- Calls `send_bulk_emails.delay(users)`:
  - Queues a single Celery task that will then enqueue multiple `send_email` tasks inside the worker context.
  - Immediately returns an AsyncResult object; `result.id` is the task ID for the bulk job.
- Prints out that ID so you can track it later (`celery -A celery_worker.celery_app inspect/query` or by reading the backend).

This script does not itself send any emails; it just enqueues the work for Celery workers.

---

**5. requirements.txt**

- Contains:

  ```text
  celery
  redis
  ```

These are the two Python dependencies needed.

---

**6. How to run everything (conceptually)**

1. Start Redis server (must be installed).
2. Start Celery worker in the qu49 folder, for example:

   ```bash
   celery -A celery_worker.celery_app worker -Q emails -l info
   ```

   (exact command may vary, but the important part is `-A celery_worker.celery_app` and queue `emails`).

3. Run the trigger script:

   ```bash
   python main.py
   ```

4. Celery worker sees:
   - 1 `tasks.send_bulk_emails` job, which then spawns multiple `tasks.send_email` jobs.
   - Logs go into `celery-logs.log`.

If you want, I can next help you with the exact Windows commands to start Redis, the Celery worker, and test the whole flow step by step.
