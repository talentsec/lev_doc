### levrt.Task

**Construction method**

> none

---

**Method**

> Task.signal(self, signal="SIGINT")

**Function**

The incoming semaphore, the default is the termination signal ("SIGINT")

**Parameter**

signal: string, refer to Linux system semaphore, please google it for details

**Return value**

none

**Example**

```python
async def background(domain:str):
    task = await nmap.port_os(ip).start()
    # Supposing that this scan needs to be executed in the container for a long time, it can be executed in the background. The signal() method will block the code and wait for the task to execute successfully before returning.
    # After starting the task, it is executed in the background, and then you can continue to start other tasks, block them when needed, and wait for the task to return the execution result


    await task.signal()
    # Pass the semaphore Manually end the task

    result = await task.done()
    data = await result.get()
    # Get dataset from database
    print(data)
```

---

**Method**

> Task.done()

**Function**

Wait for the task to end and return the result

**Parameter**

none

**Return value**

levrt.Document

**Example**

```python
async def background(domain: str):
    task = await nmap.port_os(ip).start()
    # Supposing that this scan needs to be executed in the container for a long time, it can be executed in the background. The signal() method will block the code and wait for the task to execute successfully before returning.
    # After starting the task, it is executed in the background, and then you can continue to start other tasks, block them when needed, and wait for the task to return the execution result


    await task.signal()
    # Pass the semaphore Manually end the task

    result = await task.done()
    data = await result.get()
    print(data)
```
