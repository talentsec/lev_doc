### levrt.Cr

**构造方法**

> `levrt.Cr(image, schema, host, entry, cmd, env, files, links, ports, services)`

**功能介绍**

  定义一个 docker image 对象并且返回，后续的所有任务执行都在此镜像中。

**参数列表**

  | 参数名   | 类型              | 是否必传 | 说明                                                         | default | 示例                                                         |
  | -------- | ----------------- | -------- | ------------------------------------------------------------ | ------- | ------------------------------------------------------------ |
  | image    | str               | 是       | 镜像名                                                       | 无      | levkit/nmap_python:v1.0                                      |
  | schema   | str               | 否       | 所要储存数据的定义名,格式为`<namespace>/<name>@<major>.<minor>` | None    | `talentsec/nmap@1.0`                                         |
  | host     | boolean           | 否       | 是否使用 host网络模式                                        | False   |                                                              |
  | entry    | method            | 否       | 入口函数调用                                                 | None    |                                                              |
  | cmd      | list[str]         | 否       | 命令行参数                                                   | []      | ["--option", "-f"]                                           |
  | env      | list[str]         | 否       | 环境变量                                                     | []      | ["PATH=/bin", "DB_URI=..."]                                  |
  | files    | dict[ str, File ] | 否       | 挂载文件, key 为挂载的__目标绝对路径__, value 为文件内容或__本地文件路径__, | {}      | {"/etc/config.json": json.dumps(...).encode(),"/data/blob": pathlib.Path("./local_path...")} |
  | links    | dict[ str, str ]  | 否       | 主机链接别名                                                 | {}      | {"proxy": other_task.host}                                   |
  | ports    | list[int]         | 否       | 等待tcp端口可访问                                            | []      | [8000]                                                       |
  | services | dict[str, Cr]     | 否       | 所依赖的服务, 在主容器运行前启动, 并在主容器结束后销毁       | {}      | {"redis", Cr("redis:latest")}                                |

**示例**

* files 参数示例

```python
  @levrt.remote
  def entry():
      with open("/data/blob") as f:
          ...

  Cr("...", entry=entry, files={
          "/etc/config.json": json.dumps(...).encode(),
          "/data/blob": pathlib.Path("./local_path...")
      })
```

* ports, service 参数示例

```python
@levrt.remote
def entry():
    import redis
    import os

    r = redis.Redis.from_url(os.getenv("REDIS_URI"))
    # 在容器中可以获取环境变量 REDIS_URI, 且 tcp://redis:9001 处于可用状态
    ...


Cr("...",
   entry=entry,
   env=["REDIS_URI=redis://redis:9001"],
   services={
       "redis": Cr("redis:latest", cmd=["--port", "9001"], ports=[9001])
   })
```

---

**方法**

> Cr.start()

**功能**

开始执行 Cr 对象中注入的任务函数，也就是  `@levrt.remote`  下定义的 entry 方法

在定义了 Cr 对象之后，任务不会立即执行，必须要在 `await` 关键字之后才会执行，await 关键字会自动调用 start() ，然后等待结果

**参数**

无

**返回值**

levrt.Task 对象

**示例**

```python
async def background(domain:str):
    task = await nmap.port_os(ip).start()
    # 假设本次扫描需要在容器中执行较长时间，可以将其置于后台执行，signal() 方法会将代码阻塞，等待任务执行成功之后才会返回
    # 启动任务之后后台执行，然后可以继续启动其他的任务，等到需要的时候就阻塞住，等待 task 返回执行结果

    await task.signal()
    # 当指定后台执行后，支持在工具执行中传入信号（.signal()），如立刻中断工具在容器内的执行等
    result = await task.done()
    data = await result.get()
    # 从数据库获取数据集
    print(data)
```

---

**方法**

> Cr.done()

**功能**

等待任务结束, 执行效果等同于 `await start()`，等待任务结束并返回结果

**参数**

无

**返回值**

levrt.Document 对象

**示例**

```python
async def port_os_nmap_python(ip:str):
    result = await nmap.port_os(ip).done()
    data = await result.get()
    print(data)
```
