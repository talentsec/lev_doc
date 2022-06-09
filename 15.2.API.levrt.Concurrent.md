### levrt.Concurrent

**Construction method**

> `__init__(self,limit=None)`

**Parameter**

* limit

  Maximum number of concurrently running tasks

* Effect

  Multi-threaded execution of tasks

---

**Method**

> Concurrent.start(self, coroutine)

**Function**

Start a new task asynchronously

**Parameter**

* coroutine

  Task functions that need to be executed asynchronously

**Example**

```python
async def current():
    urls = [...]

    # Multiple scan tasks will run at the same time, but at most 5 tasks will run at the same time
    async with Concurrent(limit=5) as c:
        for url in urls:
            c.start(scan(url))
```
