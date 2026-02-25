---
title: "Asyncio Day 2: Coroutines, Tasks & Synchronization"
slug: "python-asyncio-day-2-coroutines-tasks-locks"
date: 2024-11-22
description: "Learn to write concurrent Python code with Coroutines, Tasks, gather(), and control access with Locks and Semaphores."
showToc: true
weight: 2
series: ["Asyncio"]
categories: ["Asyncio", "Python"]
tags: ["Asyncio", "Coroutines", "Tasks", "Locks", "Semaphores"]
summary: "Day 2 of Asyncio series. Practical guide to async/await syntax, creating Tasks, handling concurrency with gather, and using Synchronization Primitives."
images: ["/images/python.png"]
---


# Chapter 1 — What Is Asynchronous Programming?

## 1.1 Overview

Asynchronous programming allows your program to handle multiple tasks concurrently without waiting for each task to finish. Unlike synchronous/blocking execution, async code can pause at certain points (`await` points) while the event loop continues executing other tasks.

**Key distinctions:**

| Concept | Definition |
|---|---|
| Concurrency | Multiple tasks make progress together, but not necessarily at the same instant. |
| Parallelism | Multiple tasks execute simultaneously on separate CPU cores. |
| Blocking IO | The program halts until the operation completes. |
| Non-blocking IO | The program continues executing other tasks while waiting for IO operations to finish. |

> Modern async in Python relies on `asyncio` (introduced in Python 3.4, stabilized in 3.7+). Recommended Python version: **3.11+** for performance improvements (task groups, fine-grained cancellation).

## 1.2 Why Async Matters

- **Efficient IO-bound task handling:** Asyncio avoids wasting CPU cycles while waiting for network, disk, or timer operations.
- **Scalability:** Can handle thousands of connections efficiently (e.g., web servers, network clients).
- **Non-blocking operations:** Reduces latency in real-time applications.

**When to use asyncio:**

- Networking: HTTP requests, WebSockets, TCP/UDP connections.
- File and database operations that support async.
- Long-running tasks that involve waiting, e.g., polling or timers.

## 1.3 Visualization of Async Flow

### 1.3.1 Event Loop Concept

```bash
+----------------------+
|      Event Loop      |
|----------------------|
|  Ready Queue         | <- Tasks ready to run
|  Scheduled Queue     | <- Tasks waiting for timers/IO
+----------------------+
        |  ^
        |  | schedule / complete
        v  |
  +-------------+
  |  Coroutines | <- Tasks yield control with await
  +-------------+
```

- The event loop continuously checks the ready queue and scheduled queue.
- When a coroutine `await`s an IO operation, it yields control to the loop.
- The loop can run other ready tasks while waiting.

### 1.3.2 Blocking vs Non-blocking Execution

**Blocking example (synchronous):**

```python
import time

def fetch_data():
    time.sleep(3)  # blocking wait
    return "data"

def main():
    print("Start")
    result = fetch_data()
    print(result)
    print("End")

main()
``` 

Output takes ~3 seconds:

```bash
Start
data
End
```

**Non-blocking example (asynchronous):**

```python
import asyncio

async def fetch_data():
    await asyncio.sleep(3)  # non-blocking wait
    return "data"

async def main():
    print("Start")
    result = await fetch_data()
    print(result)
    print("End")

asyncio.run(main())
```

**Flow:**

1. `asyncio.run()` starts the event loop.
2. `fetch_data()` awaits `asyncio.sleep(3)`.
3. Event loop can run other tasks while waiting (no CPU wasted).

## 1.4 Key Considerations

- **Async is not parallelism:** Async handles concurrency for IO-bound tasks; CPU-heavy tasks still block unless offloaded to threads/processes.
- **Do not mix blocking calls inside async:** Using `time.sleep()` blocks the event loop; always use `asyncio.sleep()`.
- **Use Python 3.11+ features:** `asyncio.TaskGroup` for structured concurrency, precise cancellation, and error aggregation.
- **Debugging tools:** `asyncio.run(main(), debug=True)` to detect slow tasks, forgotten awaits, or deadlocks.

## 1.5 Example Exercise

**Task:** Print messages asynchronously with different delays.

```python
import asyncio

async def greet(name, delay):
    await asyncio.sleep(delay)
    print(f"Hello {name} after {delay}s")

async def main():
    await asyncio.gather(
        greet("Alice", 1),
        greet("Bob", 2),
        greet("Charlie", 1.5)
    )

asyncio.run(main())
```

**Expected Output (approximate order):**

```bash
Hello Alice after 1s
Hello Charlie after 1.5s
Hello Bob after 2s
```

**Learning Points:**

- `async def` defines a coroutine.
- `await` pauses execution and lets the loop handle other tasks.
- `asyncio.gather()` runs multiple coroutines concurrently.

---

# Chapter 2 — Coroutines and Awaitables

## 2.1 What Are Coroutines?

A coroutine is a special Python function defined with `async def` that can pause its execution at `await` points and let the event loop run other tasks. Coroutines are awaitable objects, meaning they can be used with `await` to retrieve results once complete.

**Key differences from normal functions:**

| Feature | Normal Function (`def`) | Coroutine (`async def`) |
|---|---|---|
| Returns | Direct value | Coroutine object (awaitable) |
| Execution | Runs immediately | Suspended until awaited |
| Can pause | No | Yes, via `await` |
| Control | Linear | Cooperative multitasking |

**Lifecycle of a Coroutine:**

```bash
Created → Scheduled → Running → Done
```

- **Created:** Defined by `async def` but not yet awaited.
- **Scheduled:** Placed on the event loop (e.g., via `asyncio.create_task()`).
- **Running:** Actively executing until next `await` or completion.
- **Done:** Finished execution or raised exception.

## 2.2 Awaitables

An **awaitable** is any object that can be used with `await`. There are three main types:

1. **Coroutines** (`async def` functions)
2. **Tasks** (created from coroutines via `asyncio.create_task()` or `asyncio.ensure_future()`)
3. **Futures** (`asyncio.Future`) — low-level placeholders for results that will be available later

**Flow Visualization:**

```bash
+------------------+
| async def foo()  |  <-- Coroutine (awaitable)
+------------------+
        |
        v
+------------------+
| await foo()      |  <-- yields control to event loop
+------------------+
        |
        v
+------------------+
| Event Loop       |  <-- schedules other ready tasks
+------------------+
```

## 2.3 Nested Coroutines and Chaining Awaits

Coroutines can call other coroutines. Control returns to the event loop whenever `await` is hit.

```python
import asyncio

async def fetch_data():
    await asyncio.sleep(2)
    return "Data"

async def process_data():
    data = await fetch_data()  # await another coroutine
    print(f"Processed {data}")

async def main():
    await process_data()

asyncio.run(main())
```

**Flow:**

1. `main()` awaits `process_data()`.
2. `process_data()` awaits `fetch_data()`.
3. `fetch_data()` hits `asyncio.sleep(2)` — yields control.
4. Event loop executes other tasks (if any) during sleep.
5. After 2s, resumes execution and prints `Processed Data`.

## 2.4 Difference Between Generators and Coroutines

| Feature | Generator (`yield`) | Coroutine (`async def`, `await`) |
|---|---|---|
| Purpose | Produce values lazily | Pause and resume execution |
| Return Type | Iterator | Coroutine object (awaitable) |
| Scheduling | Manual iteration | Event loop manages execution |
| Control Yielded | Value | Control to event loop |

> **Key:** Generators produce data, coroutines yield control.

## 2.5 Practical Example

**Task:** Print multiple messages with delays using nested coroutines.

```python
import asyncio

async def greet(name, delay):
    await asyncio.sleep(delay)
    print(f"Hello {name} after {delay}s")

async def welcome_team():
    await greet("Alice", 1)
    await greet("Bob", 2)

async def main():
    # run concurrently
    task1 = asyncio.create_task(welcome_team())
    task2 = asyncio.create_task(greet("Charlie", 1.5))

    await task1
    await task2

asyncio.run(main())
```

**Expected Output (timed order):**

```bash
Hello Alice after 1s
Hello Charlie after 1.5s
Hello Bob after 2s
```

**Observations:**

- Tasks run concurrently with the event loop handling the scheduling.
- Nested coroutines (`welcome_team`) allow sequential chaining inside concurrent execution.

## 2.6 Important Considerations

- **Always `await` coroutines:** Forgetting `await` just creates a coroutine object; it won't execute.
- **Do not block inside coroutines:** Avoid `time.sleep()`, `file.read()`, etc. Use `asyncio` equivalents.
- **Tasks vs coroutines:** Wrapping coroutines with `asyncio.create_task()` schedules them independently; just awaiting runs them sequentially.
- **Exceptions propagate:** Unhandled exceptions in awaited coroutines will bubble up unless caught with `try/except`.
- **Python 3.11+ improvements:** `asyncio.TaskGroup` can run multiple coroutines safely, cancel together, and collect exceptions in a structured way.

---

# Chapter 3 — Event Loop Essentials

## 3.1 What Is the Event Loop?

The event loop is the core of `asyncio`. It:

- Schedules and runs coroutines
- Handles IO events, timers, and callbacks
- Operates in a single thread, but can efficiently manage thousands of concurrent tasks

**Components:**

| Component | Role |
|---|---|
| Ready Queue | Coroutines ready to execute immediately |
| Scheduled Queue | Coroutines waiting for timers/IO events |
| Callbacks | Functions scheduled with `call_soon`, `call_later`, or `call_at` |
| Selector/Poller | Monitors OS-level events (epoll/kqueue/IOCP) |

## 3.2 Event Loop Flow Visualization

```bash
          +-----------------------+
          |       Event Loop      |
          +-----------------------+
                  |
      +-----------+------------+
      |                        |
  Ready Queue               Scheduled Queue
      |                        |
      v                        v
 +-----------+          +----------------+
 | Task/Coro |          | IO / Timer Exp |
 +-----------+          +----------------+
      |                        |
      +-----------+------------+
                  |
               Execute
                  |
         Check for new ready tasks
                  |
               Repeat
```

**Execution Flow:**

1. Poll ready tasks or IO events
2. Run tasks until `await`
3. Move waiting tasks to scheduled queue
4. Execute callbacks (`call_soon`, `call_later`)
5. Repeat until all tasks complete

## 3.3 Modern Loop APIs (Python 3.11+)

- Always use `asyncio.run()`.
- Schedule coroutines as tasks: `asyncio.create_task(coro)`
- Schedule callbacks inside a coroutine: `loop = asyncio.get_running_loop()` then `loop.call_soon()` / `loop.call_later()`
- **Deprecated:** `get_event_loop()`, `run_until_complete()`, `run_forever()` should not be used.

## 3.4 Selector Internals (OS-level IO Multiplexing)

| OS | Mechanism |
|---|---|
| Linux | `epoll` |
| macOS | `kqueue` |
| Windows | `IOCP` |

The selector monitors multiple file descriptors (sockets, pipes) without blocking. When an event is ready, the corresponding coroutine is moved to the ready queue.

```bash
      OS Selector
+-------------------------+
| Monitors sockets/FDs    |
| Notifies loop on events |
+-------------------------+
        |
        v
  Ready Queue in Event Loop
```

## 3.5 Task Execution

**Step-by-step Execution in Modern Python:**

1. Ready tasks run until next `await`
2. Waiting tasks move to scheduled queue
3. Execute callbacks (`call_soon`, `call_later`)
4. Poll OS-level IO events
5. Repeat until all tasks complete

## 3.6 Example: Scheduling Coroutines and Callbacks

```python
import asyncio

async def task(name, delay):
    print(f"{name} started")
    await asyncio.sleep(delay)
    print(f"{name} finished after {delay}s")

async def main():
    # Access the currently running loop
    loop = asyncio.get_running_loop()

    # Schedule callbacks
    loop.call_soon(lambda: print("Immediate callback"))
    loop.call_later(1, lambda: print("Delayed callback (1s)"))

    # Run multiple coroutines concurrently
    await asyncio.gather(
        task("Task A", 2),
        task("Task B", 1),
    )

asyncio.run(main())
```

**Expected Output:**

```bash
Immediate callback
Task A started
Task B started
Delayed callback (1s)
Task B finished after 1s
Task A finished after 2s
```

## 3.7 Important Considerations

- **Single-threaded:** CPU-heavy tasks block other coroutines.
- **Non-blocking only:** Avoid blocking calls like `time.sleep()`.
- **Callbacks vs coroutines:** `call_soon` / `call_later` run plain functions, not coroutines.
- **Debugging:** Use `loop.set_debug(True)` inside `asyncio.run()` for slow tasks or scheduling issues.
- **Task management:** Use `asyncio.create_task()` for concurrency, `asyncio.gather()` to wait for multiple tasks.


---

# Chapter 4 — Tasks and Futures

## 4.1 Overview

In asyncio, coroutines alone are awaitable objects but do not run independently unless awaited. To allow a coroutine to execute concurrently within the event loop, it must be wrapped into a Task.

- A **Task** represents a coroutine scheduled for execution by the event loop.
- A **Future** is a lower-level abstraction that represents a result that will be available later.

**Hierarchy:**

```bash
Coroutine → Task → Future (result holder)
```

## 4.2 Coroutine vs Task vs Future

| Concept | Description |
|---|---|
| Coroutine | Async function (`async def`) that yields control with `await` |
| Task | Coroutine scheduled for execution in the event loop |
| Future | Placeholder object for a result not yet available |
| Awaitable | Any object usable with `await` (coroutines, tasks, futures) |

### 4.2.1 Visualization

```bash
+---------------------+
| async def foo()     |
|  Coroutine object   |
+----------+----------+
           |
           | asyncio.create_task()
           v
+---------------------+
|       Task          |
| Scheduled coroutine |
+----------+----------+
           |
           v
+---------------------+
|       Future        |
| Holds result/exception |
+---------------------+
```

> **Key Insight:** Every Task is a Future, but not every Future is a Task.

## 4.3 Task Lifecycle

A Task transitions through several states during execution.

```bash
Created → Scheduled → Running → Done / Cancelled
```

**State Breakdown:**

| State | Meaning |
|---|---|
| Pending | Created but not yet executed |
| Running | Currently executing |
| Done | Completed successfully or raised exception |
| Cancelled | Explicitly cancelled |

## 4.4 Task Scheduling and Queues

When you call `asyncio.create_task(coro())`, the coroutine is:

1. Wrapped into a Task
2. Added to the ready queue
3. Picked by the event loop for execution

**Full Scheduling Visualization:**

```bash
Coroutine Created
        |
        v
asyncio.create_task()
        |
        v
+-------------------+
|   Ready Queue     |  <- Ready to execute immediately
+---------+---------+
          |
          v
     Event Loop Executes
          |
     Runs until await
          |
          v
+-------------------+
| Scheduled Queue   |  <- Waiting for IO / sleep / await
+---------+---------+
          |
          | Await completes
          v
+-------------------+
|   Ready Queue     |  <- Re-scheduled automatically
+-------------------+
          |
          v
       Running
          |
     +----+----+
     |         |
   Done    Cancelled
```

> When an awaited operation completes, the transition from Scheduled queue to Ready queue happens automatically — this is handled internally by the event loop.

## 4.5 Creating Tasks

### 4.5.1 Modern API (Python 3.7+)

Use `asyncio.create_task(coro())` to schedule a coroutine immediately.

**Example:**

```python
import asyncio

async def say_hello():
    await asyncio.sleep(1)
    return "Hello"

async def main():
    task = asyncio.create_task(say_hello())
    print("Task scheduled")
    result = await task
    print(result)

asyncio.run(main())
```

**Output:**

```bash
Task scheduled
Hello
```

## 4.6 Running Concurrent Tasks

Tasks enable true async concurrency in a single-threaded event loop.

```python
import asyncio

async def worker(name, delay):
    print(f"{name} started")
    await asyncio.sleep(delay)
    print(f"{name} finished")

async def main():
    t1 = asyncio.create_task(worker("Task A", 2))
    t2 = asyncio.create_task(worker("Task B", 1))

    await t1
    await t2

asyncio.run(main())
```

**Execution Order:**

```bash
Task A started
Task B started
Task B finished
Task A finished
```

Both tasks run concurrently despite sequential awaits.

## 4.7 Futures Explained

A Future represents a result that will be available later. Internally:

- Tasks store results in a Future-like container.
- Futures are heavily used in low-level asyncio APIs.

**Future Visualization:**

```bash
+--------------------+
|     Future         |
|--------------------|
| result()           |
| exception()        |
| done()             |
+--------------------+
```

**Common Future Methods:**

| Method | Description |
|---|---|
| `done()` | Check completion |
| `result()` | Get result (if finished) |
| `exception()` | Get raised exception |

## 4.8 Cancellation

Tasks can be cancelled explicitly using `task.cancel()`. Cancellation raises `asyncio.CancelledError` at the next `await` point.

**Example:**

```python
import asyncio

async def long_task():
    try:
        print("Task started")
        await asyncio.sleep(5)
        print("Task finished")
    except asyncio.CancelledError:
        print("Task cancelled")

async def main():
    task = asyncio.create_task(long_task())
    await asyncio.sleep(1)
    task.cancel()
    await task

asyncio.run(main())
```

**Output:**

```bash
Task started
Task cancelled
```

**Cancellation Flow:**

```bash
Running Task
     |
task.cancel()
     |
     v
CancelledError injected at next await
     |
Coroutine handles cleanup
     |
Task marked Cancelled
```

## 4.9 Awaiting Task Results

You can retrieve results in two ways.

**1. Preferred — `await` task:**

```python
result = await task
```

**2. Direct access (only when done):**

```python
if task.done():
    result = task.result()
```

> **Note:** Calling `task.result()` before the task is done raises `InvalidStateError`.

## 4.10 Example — Multiple Tasks with Cancellation

```python
import asyncio

async def worker(name, delay):
    try:
        print(f"{name} started")
        await asyncio.sleep(delay)
        print(f"{name} finished")
    except asyncio.CancelledError:
        print(f"{name} cancelled")

async def main():
    task1 = asyncio.create_task(worker("Task 1", 3))
    task2 = asyncio.create_task(worker("Task 2", 5))

    await asyncio.sleep(1)
    task2.cancel()

    await asyncio.gather(task1, task2, return_exceptions=True)

asyncio.run(main())
```

**Output:**

```bash
Task 1 started
Task 2 started
Task 1 finished
Task 2 cancelled
```

## 4.11 Important Considerations

- **Tasks enable concurrency:** Without `create_task`, coroutines execute sequentially.
- **Always handle cancellation:** Use `try/except asyncio.CancelledError` for cleanup.
- **Tasks reschedule automatically:** When awaited events complete, the transition `Scheduled → Ready → Running` happens internally — you never manually move tasks between queues.
- **Avoid orphan tasks:** Un-awaited background tasks may leak exceptions or produce unpredictable behavior. Use structured concurrency (`TaskGroup` in Python 3.11+).
- **Single-threaded scheduling:** Tasks are concurrent, not parallel. CPU-heavy work still blocks the event loop.
> Note:Note: When you use async and await without asyncio.create_task(), you are not creating concurrent tasks. In this case, there is only one coroutine in the event loop’s ready queue. The loop will run this coroutine until it hits an await, at which point it pauses execution until the awaited operation completes. Since there are no other tasks to run, the event loop simply waits for the operation to finish before resuming the same coroutine.

> Note: In contrast, if you use asyncio.create_task() to schedule multiple coroutines, all of them are placed in the ready queue together. The event loop can then switch between coroutines whenever they reach an await, enabling true concurrency.


---

# Chapter 5 — Scheduling and Timing

## 5.1 What Is Scheduling in Asyncio?

Scheduling determines when and in what order coroutines or callbacks execute within the event loop. Asyncio allows fine-grained control for delays, timeouts, and periodic execution without blocking the event loop.

> **Key concept:** Tasks can be **ready** (run immediately) or **scheduled** (run after a delay or IO event).

## 5.2 Non-Blocking Delays

Use `asyncio.sleep(seconds)` instead of `time.sleep()` to pause without blocking the event loop.

```python
import asyncio

async def task(name, delay):
    print(f"{name} started")
    await asyncio.sleep(delay)
    print(f"{name} finished after {delay}s")

async def main():
    await asyncio.gather(
        task("A", 1),
        task("B", 2),
    )

asyncio.run(main())
```

**Output (timed order):**

```bash
A started
B started
A finished after 1s
B finished after 2s
```

> **Observation:** The event loop executes other tasks while waiting, enabling concurrency.

## 5.3 Timeout Control

`asyncio.wait_for(coro, timeout)` limits the maximum time a coroutine can run. Raises `asyncio.TimeoutError` if the timeout is exceeded.

```python
import asyncio

async def slow_task():
    await asyncio.sleep(5)
    return "Done"

async def main():
    try:
        result = await asyncio.wait_for(slow_task(), timeout=2)
        print(result)
    except asyncio.TimeoutError:
        print("Task timed out")

asyncio.run(main())
```

**Output:**

```bash
Task timed out
```

> **Key Consideration:** Use `wait_for` for long-running network requests or polling to avoid hanging tasks.

## 5.4 Callback Scheduling

Asyncio allows scheduling plain callbacks in addition to coroutines:

| Method | Description |
|---|---|
| `loop.call_soon(callback)` | Execute callback as soon as the current task yields |
| `loop.call_later(delay, callback)` | Execute callback after a fixed delay |
| `loop.call_at(when, callback)` | Execute callback at a specific loop time (`loop.time() + delay`) |

**Example:**

```python
import asyncio

def immediate():
    print("Immediate callback executed")

def delayed():
    print("Delayed callback executed")

async def main():
    loop = asyncio.get_running_loop()
    loop.call_soon(immediate)
    loop.call_later(1, delayed)

    await asyncio.sleep(2)  # let callbacks execute

asyncio.run(main())
```

**Expected Output:**

```bash 
Immediate callback executed
Delayed callback executed
```

---

# Chapter 6 — Running Multiple Coroutines

## 6.1 Overview

`asyncio.gather()` allows running multiple coroutines concurrently and collecting their results. It internally schedules coroutines as tasks and lets the event loop interleave their execution whenever they hit `await`.

| Tool | Purpose |
|---|---|
| `asyncio.gather()` | Run coroutines concurrently and collect all results |

## 6.2 Sequential vs Concurrent Execution

### 6.2.1 Sequential Execution (Await One by One)

When `await` is used on each coroutine in sequence, the next coroutine starts only after the previous one finishes.

```python
import asyncio

async def task(name, delay):
    print(f"{name} started")
    await asyncio.sleep(delay)
    print(f"{name} finished")

async def main():
    await task("A", 2)
    await task("B", 1)

asyncio.run(main())
```

**Task lifecycle (sequential):**

```bash
Coroutine Created: task("A")
        |
        v
asyncio.await() called
        |
        v
+-------------------+
|   Ready Queue     |  <- Only task("A") ready
+---------+---------+
          |
          v
     Event Loop Executes
          |
     Runs until await asyncio.sleep(2)
          |
          v
+-------------------+
| Scheduled Queue   |  <- Waiting 2s
+---------+---------+
          |
          | Sleep completes
          v
+-------------------+
|   Ready Queue     |  <- Resumed task("A")
+---------+---------+
          |
          v
       Running
          |
     +----+----+
     |         |
   Done    Cancelled

Next, task("B") created and follows the same lifecycle.
```

**Output:**

```bash
A started
A finished
B started
B finished
```

> **Total time: ~3 seconds** (tasks run sequentially). Sequential execution wastes concurrency; the ready queue is empty while waiting.

### 6.2.2 Concurrent Execution with `asyncio.gather()`

`asyncio.gather()` schedules all coroutines immediately as tasks. Each can run until it hits `await`, then yields control, letting other tasks execute.

```python
import asyncio

async def task(name, delay):
    print(f"{name} started")
    await asyncio.sleep(delay)
    print(f"{name} finished")

async def main():
    await asyncio.gather(
        task("A", 2),
        task("B", 1),
    )

asyncio.run(main())
```

**Task lifecycle (concurrent):**

```bash
Coroutine Created: task("A"), task("B")
        |
        v
asyncio.create_task() for each
        |
        v
+-------------------+
|   Ready Queue     |  <- Task-A, Task-B ready
+---------+---------+
          |
          v
     Event Loop Executes
          |
Task-A runs → hits await asyncio.sleep(2)
Task-A moves to scheduled queue
Task-B runs → hits await asyncio.sleep(1)
Task-B moves to scheduled queue
          |
          v
+-------------------+
| Scheduled Queue   |  <- Task-A (2s), Task-B (1s)
+---------+---------+
          |
          | Sleep completes for Task-B
          v
+-------------------+
|   Ready Queue     |  <- Task-B rescheduled
+---------+---------+
          |
          v
       Running Task-B → Done
          |
          v
+-------------------+
| Scheduled Queue   |  <- Task-A remaining
+---------+---------+
          |
          | Sleep completes for Task-A
          v
+-------------------+
|   Ready Queue     |  <- Task-A rescheduled
+---------+---------+
          |
          v
       Running Task-A → Done
```

**Output:**

```bash
A started
B started
B finished
A finished
```

> **Total time: ~2 seconds** (tasks overlap — only the longest delay matters). The ready queue never sits idle while there are scheduled tasks; this is true asyncio concurrency.

## 6.3 `asyncio.gather()` — Result Collection

Results are returned in **input order**, even if tasks finish out of order. Use this when tasks are independent but you want all results together.

```python
import asyncio

async def fetch_data(source, delay):
    print(f"Fetching from {source}...")
    await asyncio.sleep(delay)
    return f"Data from {source}"

async def main():
    results = await asyncio.gather(
        fetch_data("API-1", 2),
        fetch_data("API-2", 1),
        fetch_data("API-3", 3),
    )
    for r in results:
        print(r)

asyncio.run(main())
```

**Output:**

```bash
Fetching from API-1...
Fetching from API-2...
Fetching from API-3...
Data from API-1
Data from API-2
Data from API-3
```

> **Total time: ~3 seconds** (tasks overlap; longest sleep dominates).

**Comparison Table:**

| Approach | Total Time | Use When |
|---|---|---|
| Sequential (`await` each) | Sum of all delays | Tasks depend on each other |
| Concurrent (`gather`) | Max of all delays | Tasks are independent |


> Note:When you use asyncio.create_task(), the coroutine is wrapped in a Task and immediately placed into the ready queue. This means the event loop can start running it right away, even before you await anything.In contrast, when you pass coroutines to asyncio.gather() without awaiting it, they are not yet scheduled. They remain inactive and are not in the ready queue.Only when you await gather() does the event loop schedule all the coroutines passed to gather(). At this point, they are placed into the ready queue simultaneously, allowing the event loop to execute them concurrently.

# Chapter 7- Lock,Semaphores

## 3.1 Locks

Locks are a synchronization primitive that allows us to limit access to a shared resource to only one coroutine at a time. This is useful when we have a resource that can only be accessed by one coroutine at a time, like a file or a database connection. Locks are created using the asyncio.Lock class and can be acquired using the acquire method and released using the release method.

#basic example of lock
``` python
import asyncio

async def locking(lock):
    print('Waiting for the lock')
    async with lock:
        print('Acquired the lock')
        await asyncio.sleep(2)
    print('Released the lock')

async def main():
    lock = asyncio.Lock()
    await asyncio.gather(
        locking(lock),
        locking(lock),
        locking(lock)
    )

asyncio.run(main())
```

Output:
```bash
Waiting for the lock
Acquired the lock
Waiting for the lock
Waiting for the lock
Released the lock
Acquired the lock
Released the lock
Acquired the lock
Released the lock
```

In this example, we create a lock using asyncio.Lock and pass it to the locking coroutine. We then use the async with statement to acquire the lock and release it when we are done. When we run the program, we can see that only one coroutine can acquire the lock at a time, and the other coroutines have to wait until the lock is released.


## 3.2 Semaphores

Semaphores are a synchronization primitive that allows us to limit access to a shared resource to a fixed number of coroutines at a time. This is useful when we have a resource that can be accessed by a limited number of coroutines, like a connection pool or a web API. Semaphores are created using the asyncio.Semaphore class and can be acquired using the acquire method and released using the release method.

#basic example of semaphore
``` python
import asyncio

async def semaphoring(semaphore):
    async with semaphore:
        print('Acquired the semaphore')
        await asyncio.sleep(2)
    print('Released the semaphore')

async def main():
    semaphore = asyncio.Semaphore(2)
    await asyncio.gather(
        semaphoring(semaphore),
        semaphoring(semaphore),
        semaphoring(semaphore),
        semaphoring(semaphore)
    )

asyncio.run(main())
```

Output:
```bash
Acquired the semaphore
Acquired the semaphore
Acquired the semaphore
Released the semaphore
Released the semaphore
Released the semaphore
Acquired the semaphore
Released the semaphore
```

In this example, we create a semaphore with a limit of 2 using asyncio.Semaphore and pass it to the semaphoring coroutine. We then use the async with statement to acquire the semaphore and release it when we are done. When we run the program, we can see that only two coroutines can acquire the semaphore at a time, and the other coroutines have to wait until the semaphore is released.


# Chapter 8 : Context Manager and Aiohttp

## 8.1 Context Managers
Context managers are a powerful tool in Python that allow us to manage resources and ensure that they are properly cleaned up after use. What it does is that it allows us to define a block of code that will be executed when we enter and exit a context. This is useful for managing resources like files, network connections, and database connections.

Example of Context Manager For Database Connection:
``` python
import asyncio
import asyncpg
class DatabaseConnection:
    def __init__(self, dsn):
        self.dsn = dsn
        self.connection = None

    async def __aenter__(self):
        self.connection = await asyncpg.connect(self.dsn)
        return self.connection

    async def __aexit__(self, exc_type, exc, tb):
        await self.connection.close()

async def main():
    dsn = 'postgresql://user:password@localhost:5432/mydatabase'
    async with DatabaseConnection(dsn) as connection:
        result = await connection.fetch('SELECT * FROM mytable')
        print(result)
asyncio.run(main())
```
What we are doing here is that we are defining a context manager for a database connection using the async with statement. When we enter the context, we establish a connection to the database and return it using the __aenter__ method. When we exit the context, we close the connection using the __aexit__ method. This ensures that the connection is properly cleaned up after use, even if an exception occurs.


# 8.2 Aiohttp
Aiohttp is a popular asynchronous HTTP client and server library for Python. It allows us to make HTTP requests and build web servers using asyncio. Aiohttp provides a simple and intuitive API for making HTTP requests and handling responses, as well as for building web servers that can handle multiple requests concurrently.
Example of Aiohttp Client:
``` python
import asyncio
import aiohttp
async def fetch(url):
    async with aiohttp.ClientSession() as session:
        async with session.get(url) as response:
            return await response.text()