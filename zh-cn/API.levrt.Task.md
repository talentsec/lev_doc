### levrt.Task

**构造方法**

> 无

---

**方法**

> Task.signal(self, signal="SIGINT")

**功能**

传入信号量，默认是终止信号("SIGINT")

**参数**

signal：字符串，参考 Linux 系统信号量，详情百度

**返回值**

无

**示例**

```python
async def background(domain:str):
    task = await nmap.port_os(ip).start()
    # 假设本次扫描需要在容器中执行较长时间，可以将其置于后台执行，signal() 方法会将代码阻塞，等待任务执行成功之后才会返回
    # 启动任务之后后台执行，然后可以继续启动其他的任务，等到需要的时候就阻塞住，等待 task 返回执行结果


    await task.signal()
    # 传递信号量 手动结束任务

    result = await task.done()
    data = await result.get()
    # 从数据库获取数据集
    print(data)
```

---

**方法**

> Task.done()

**功能**

等待任务结束，返回结果

**参数**

无

**返回值**

levrt.Document

**示例**

```python
async def background(domain: str):
    task = await nmap.port_os(ip).start()
    # 假设本次扫描需要在容器中执行较长时间，可以将其置于后台执行，signal() 方法会将代码阻塞，等待任务执行成功之后才会返回
    # 启动任务之后后台执行，然后可以继续启动其他的任务，等到需要的时候就阻塞住，等待 task 返回执行结果


    await task.signal()
    # 传递信号量 手动结束任务

    result = await task.done()
    data = await result.get()
    print(data)
```
