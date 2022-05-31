### levrt.Concurrent

**构造方法**

> `__init__(self,limit=None)`

**参数**

* limit

  最多同时运行的任务数量

* 作用

  多线程执行 task

---

**方法**

> Concurrent.start(self, coroutine)

**功能**

异步启动新的任务

**参数**

* coroutine

  需要异步执行的任务函数

**示例**

```python
async def current():
    urls = [...]

    # 多个 scan 任务会同时运行, 但某一时刻最多只有5个任务同时运行
    async with Concurrent(limit=5) as c:
        for url in urls:
            c.start(scan(url))
```
