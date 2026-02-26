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

```mermaid
graph TD
    %% Main Event Loop Container
    subgraph Event_Loop ["Event Loop (The Engine)"]
        direction TB
        Ready["Ready Queue<br/>(Tasks ready to run)"]
        Scheduled["Scheduled Queue<br/>(Waiting for IO/Timers)"]
    end

    %% The Execution Unit
    Running((Running Coroutine))

    %% Flow logic
    Ready -- "Selected by Selector" --> Running
    Running -- "await (yield control)" --> Scheduled
    Scheduled -. "IO Ready / Timer Done" .-> Ready

    %% Global Entry
    New[New Task] --> Ready

    %% Styling
    style Event_Loop fill:#fdfdfd,stroke:#333,stroke-width:2px
    style Running fill:#fff9c4,stroke:#fbc02d,stroke-width:3px
    style Ready fill:#e8f5e9,stroke:#2e7d32
    style Scheduled fill:#ffebee,stroke:#c62828
```

There are 2 main queues:
- Ready Queue: Tasks that can run immediately.
- Scheduled Queue: Tasks waiting for an event (timer, IO) to complete.

Generally What Happens is:
1. Multiple coroutines are wrapped into Tasks and placed into the Ready Queue.
2. The Event Loop uses a Selector to pull the next available task from the Ready Queue and executes it.
3. The task run until it hits an `await` (yield point), at which it yields control back to the event loop and is moved to the Scheduled Queue if it's waiting for an IO event or timer.
4. While the IO is pending, the Event Loop remains unblocked, selecting and running other tasks from the Ready Queue.
5. When the scheduled event completes, the task is moved back to the ready queue and eventually runs again.
6. This cycle continues until all tasks are complete.




### 1.3.2 Blocking vs Non-blocking Execution

**Blocking example (synchronous):**

```python
import time

def fetch_data():
    time.sleep(3)  # blocking wait
    return "data"
```

Blocking Execution means that the program halts at the time.sleep(3) line and cannot do anything else until the sleep is over. This is inefficient for IO-bound tasks.

**Non-blocking example (asynchronous):**

```python
import asyncio

async def fetch_data():
    await asyncio.sleep(3)  # non-blocking wait
    return "data"
```
Non-blocking Execution allows the event loop to run other tasks while waiting for the sleep to complete. This is efficient and allows for concurrency.



# Chapter 2 — Coroutines and Awaitables

## 2.1 What Are Coroutines?

A coroutine is a specialized Python function defined with async def. Unlike standard functions that run to completion once called, a coroutine can pause its execution at await points, yielding control back to the event loop so other tasks can progress.

Calling an async def function does not execute it immediately; instead, it returns a coroutine object that must be scheduled on the event loop to run.

**Key differences from normal functions:**

| Feature | Normal Function (`def`) | Coroutine (`async def`) |
|---|---|---|
| Returns | Direct value | Coroutine object (awaitable) |
| Execution | Runs immediately | Suspended until awaited |
| Can pause | No | Yes, via `await` |
| Control | Linear | Cooperative multitasking |

**Lifecycle of a Coroutine:**
**Created** :The coroutine is defined and called, but hasn't started (it's just an object).When you call an async function, it returns a coroutine object. This object represents the coroutine but does not execute it yet.

**Ready** :The coroutine is scheduled to run (e.g., via `asyncio.create_task()`) and is in the ready queue, waiting for the event loop to pick it for execution.

**Running** :The event loop starts executing the coroutine. It runs until it hits an `await` expression, at which point it yields control back to the event loop.

**Suspended** :When the coroutine hits an `await`, it is suspended and moved to the scheduled queue, waiting for the awaited operation (like IO or a timer) to complete.

```mermaid
stateDiagram-v2
    [*] --> Created : async_func() called
    
    Created --> Ready : asyncio.create_task()
    note right of Created
        Coroutine object exists 
        but is not executing yet.
    end note

    Ready --> Running : Loop selects task
    note left of Ready
        Task sits in the 
        Ready Queue.
    end note

    Running --> Suspended : Encounters 'await'
    note right of Running
        Actively executing 
        on the CPU.
    end note

    Suspended --> Ready : IO/Timer completes
    note left of Suspended
        Task sits in the 
        Scheduled Queue.
    end note

    Running --> Done : Final return
    Done --> [*]

    style Running fill:#fff9c4,stroke:#fbc02d
    style Ready fill:#e8f5e9,stroke:#2e7d32
    style Suspended fill:#ffebee,stroke:#c62828
```


## 2.2 Awaitables

An **awaitable** is any object that can be used with `await`. There are three main types:

1. **Coroutines** : Functions defined with async 
2. **Tasks** : High-level wrappers used to schedule coroutines concurrently.
3. **Futures**: : Low-level objects representing an eventual result of an asynchronous operation.(Wont be covered in this blog)


## 2.3 Chaining Coroutines

Coroutines can call and wait for other coroutines, creating a chain of execution. Control is only returned to the event loop when the innermost await hits a non-blocking operation (like a timer or network socket).

```python
import asyncio

async def fetch_data():
    await asyncio.sleep(2)  # Yields control to loop
    return "Data Received"

async def process_data():
    print("Fetching...")
    # Chains to fetch_data; process_data suspends here
    data = await fetch_data() 
    print(f"Result: {data}")

asyncio.run(process_data())
```

## 2.4 Conclusion
In this chapter's examples, we are not using asyncio.create_task(). Because we are awaiting one by one, the Ready Queue usually only has one task in it at a time which is asyncio.run(coro) .This mean when the coroutine hits an await, it yields control back to the event loop and is moved to the Scheduled Queue. Since there are no other tasks in the Ready Queue, the event loop simply waits for the awaited operation to complete before resuming the same coroutine. This results in sequential execution, where each coroutine runs to completion before the next one starts.

**Note** : To run coroutine it must be awaited or scheduled as a task. If you just call an async function without awaiting it or scheduling it, it will not execute and will simply return a coroutine object. This is a common mistake when starting with asyncio, but it's important to remember that coroutines need to be properly scheduled to run.

# Chapter 3 — Event Loop Essentials

## 3.1 What Is the Event Loop?
The Event Loop is the core of asyncio. Think of it as a central manager or a "Conductor" of an orchestra. While Python usually runs one line of code at a time (single-threaded), the Event Loop allows it to handle many tasks by switching between them whenever one hits a "waiting" period.

***Key Responsibilities of the Event Loop:***
- **Scheduling**: Manages when courotines among the ready queue get to run.
- **I/O Handling**: Communicates with the Operating System to check if network data has arrived.
- **Timer Management**: Keeps track of timeouts and scheduled callbacks.

**Components:**

| Component | Role |
|---|---|
| Ready Queue | Coroutines ready to execute immediately |
| Scheduled Queue | Coroutines waiting for timers/IO events |
| Selector/Poller | Monitors OS-level events (epoll/kqueue/IOCP) |

## 3.2 Event Loop Flow Visualization

```mermaid
graph TD
    %% Main Engine Container
    subgraph Event_Loop ["The Event Loop Engine (Single Threaded)"]
        direction TB
        
        %% Step 1: Selection Logic
        Selector["<b>1. OS Selector / Poller</b><br/>(epoll / kqueue / IOCP)<br/>Checks for finished IO & Timers"]
        
        %% The Queues
        subgraph Internal_Queues ["Queue Management"]
            direction LR
            Ready["<b>Ready Queue</b><br/>Tasks waiting for CPU"]
            Scheduled["<b>Scheduled Queue</b><br/>Tasks waiting for IO/Sleep"]
        end
        
        %% Step 2: Execution
        Running((<b>2. Execution</b><br/>Running Coroutine))
        
        %% Connections inside the loop
        Selector -->|Move finished tasks| Ready
        Ready -->|Loop picks next task| Running
        Running -->|Hits 'await'| Scheduled
        Scheduled -.->|Wait for signal| Selector
    end

    %% External Entry/Exit
    New[New Task / create_task] --> Ready
    Running -->|Task complete| Done((Done))

    %% Styling
    style Event_Loop fill:#fdfdfd,stroke:#333,stroke-width:2px
    style Running fill:#fff9c4,stroke:#fbc02d,stroke-width:3px
    style Ready fill:#e8f5e9,stroke:#2e7d32
    style Scheduled fill:#ffebee,stroke:#c62828
    style Selector fill:#e1f5fe,stroke:#01579b
    
```

**Execution Flow:**

1. **Task Insertion**: When you create a new task (e.g., via `asyncio.create_task()`), it is placed into the Ready Queue.
2. **Selector Step**: The loop asks the OS (using epoll, kqueue, or IOCP): "Has any IO finished or have any timers (like asyncio.sleep) expired?". if yes, those tasks are moved from the Scheduled Queue to the Ready Queue.
3. **The Execution Step**: The loop looks at the Ready Queue. It picks the first task in line and moves it to the Running state. This is where your actual Python code executes on the CPU.
4. **The Yield Point (await)**:The code runs until it hits an await. At this point, the task yields control. It saves its local variables and moves to the Scheduled Queue.
6.  **Repeat**: Repeat steps 2-4 until all tasks are complete.



## 3.3 Creating and Running the Event Loop
In most cases, you don't need to manually create or manage the event loop. The `asyncio.run()` function acts as a high-level entry point that handles the entire lifecycle: it creates a new event loop, runs your main coroutine until completion, and closes the loop once finished.

```python
import asyncio
async def main():
    print("Hello, Asyncio!")
asyncio.run(main())
```

> Note: Event loop run until all tasks are complete. If you create tasks that never finish (e.g., infinite loops or un-awaited coroutines), the event loop will run indefinitely, which can lead to resource leaks or unresponsive programs. Always ensure that your tasks have a clear exit condition or are properly cancelled when no longer needed.



---

# Chapter 4 — Tasks 

## 4.1 Overview
In `asyncio`, a Task is a wrapper for a coroutine that schedules it to run on the event loop independently. While a coroutine is just a "blueprint," a Task is the "active instance" that the event loop manages and executes. Tasks allow you to run multiple coroutines concurrently without blocking each other.




## 4.2 Coroutine vs Task

| Concept | Description |
|---|---|
| Coroutine | Async function (`async def`) that yields control with `await` |
| Task | Coroutine scheduled for execution in the event loop |

### 4.2.1 Visualization

```mermaid
stateDiagram-v2
    direction LR

    state "Ready" as Ready
    state "Running" as Running
    state "Done" as Done
    state "Cancelled" as Cancelled

    [*] --> Ready : asyncio.create_task()
    Ready --> Running : Loop selects task
    Running --> Ready : await (Yield)
    Running --> Done : Task finishes
    Running --> Cancelled : task.cancel()
    Ready --> Cancelled : task.cancel()
    Done --> [*]
    Cancelled --> [*]

    style Running fill:#fff9c4,stroke:#fbc02d
    style Ready fill:#e8f5e9,stroke:#2e7d32
    style Cancelled fill:#ffebee,stroke:#c62828
```

Here 
- **Ready** means the task is waiting to be picked by the event loop for execution. 
- **Running** means the task is currently executing on the CPU.
- **Done** means the task has completed successfully or with an exception. 
- **Cancelled** means the task was cancelled before it could complete.


## 4.3 Creating Tasks
To run multiple things at once, you use asyncio.create_task(). This is the key to concurrency.While we call `asyncio.create_task()`, the coroutine is wrapped in a Task and immediately placed into the ready queue. This means the event loop can start running it right away, even before you await anything.

Use `asyncio.create_task(coro())` to schedule a coroutine immediately.

**Example:**

```python
import asyncio

async def say_hello():
    await asyncio.sleep(1)
    return "Hello"

async def main():
    task = asyncio.create_task(say_hello())
    task1 = asyncio.create_task(say_hello())
    tasks2 = asyncio.create_task(say_hello())
    print("Tasks created, doing other work...")
    # Do other stuff here while tasks run concurrently
    print("Doing other work...")
    # Now we can await the tasks to get their results
    result1 = await task
    result2 = await task1
    result3 = await tasks2
    print(result1, result2, result3)
asyncio.run(main())
```


```mermaid
graph LR
    %% Step 1: Initialization
    subgraph Step_1 [Step 1: Tasks Created]
        direction TB
        MAIN_TASK[Task: main]
        CT1[Task: T1]
        CT2[Task: T2]
        CT3[Task: T3]
    end

    %% Step 2: The Queue Container
    subgraph Ready_Queue [Step 2: Ready Queue]
        direction LR
        Q_MAIN((main))
        Q1((T1))
        Q2((T2))
        Q3((T3))
    end

    %% Step 3: The Engine
    subgraph Execution [Step 3: Event Loop Engine]
        CPU{Selector}
        Running[Running State]
    end

    %% Step 4: The Waiting Area
    subgraph Waiting_Room [Step 4: Scheduled Room]
        Sleep[asyncio.sleep]
    end

    %% Flow Arrows
    MAIN_TASK -->|1. asyncio.run| Q_MAIN
    CT1 -->|2. create_task| Q1
    CT2 -->|3. create_task| Q2
    CT3 -->|4. create_task| Q3

    %% Selection Logic
    Q_MAIN -->|5. Loop Pulls| CPU
    Q1 -.->|7. Loop Pulls| CPU
    Q2 -.->|8. Loop Pulls| CPU
    Q3 -.->|9. Loop Pulls| CPU

    CPU --> Running
    Running -->|6. Hits await| Sleep
    
    %% Return Logic
    Sleep -.->|1s later| Q1
    Sleep -.->|1s later| Q2
    Sleep -.->|1s later| Q3
    Q1 -.->|T1 Finishes| Q_MAIN
```

Whats happen here is:
1. We create three tasks using `asyncio.create_task()`, which immediately places them into the Ready Queue.
2. The main coroutine hits an `await` and yields control, allowing the event loop to start executing tasks from the Ready Queue.
3. The event loop picks the first task (Task 1) and runs it until it hits `await asyncio.sleep(1)`, at which point it yields control again and moves Task 1 to the Scheduled Queue.
4. The event loop then picks the next task (Task 2) and runs it until it also hits `await asyncio.sleep(1)`, yielding control and moving Task 2 to the Scheduled Queue.
5. The event loop continues to pick tasks from the Ready Queue (Task 3) and runs it until it hits `await asyncio.sleep(1)`, yielding control and moving Task 3 to the Scheduled Queue.
6. After 1 second, the timers for Task 1, Task 2, and Task 3 "ding," and they are moved back to the Ready Queue.
7. The event loop picks each task in turn, runs it to completion, and the results are printed.


  
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


## 4.9 Awaiting Task Results

You can retrieve results in two ways.

**1. Preferred — `await` task:**

```python
result = await task
```
This waits for the task to complete and returns the result.if the task is not done yet, it will suspend the current coroutine until the task finishes. If the task raises an exception, it will be propagated here.


**2. Direct access (only when done):**

```python
if task.done():
    result = task.result()
```

> **Note:** Calling `task.result()` before the task is done raises `InvalidStateError`.


## 4.10 Some Thing You SHould Know
When we create asyncio.run(coro()) it create one task and put it in ready queue,and create_task() also create one task and put it in ready queue such that when the asyncio.run() halts at the first await, the event loop can pick the task created by create_task() and run it concurrently. This is how we achieve concurrency in asyncio. If we didn't use create_task() and just awaited the coroutine directly, we would not have any other tasks in the ready queue to run while waiting, resulting in sequential execution.


---



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