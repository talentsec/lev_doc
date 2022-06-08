### levrt.Cr

**Construction method**

> `levrt.Cr(image, schema, host, entry, cmd, env, files, links, ports, services)`

**Functional introduction**

  Define a docker image object and return, all subsequent tasks are executed in this image.

**Parameter list**

  | Parameter name   | Type              | Is it mandatory | illustration                                                         | default | Example                                                         |
  | -------- | ----------------- | -------- | ------------------------------------------------------------ | ------- | ------------------------------------------------------------ |
  | image    | str               | yes       | image name                                                       | none      | levkit/nmap_python:v1.0                                      |
  | schema   | str               | no       | The definition name of the data to be stored, the format is `<namespace>/<name>@<major>.<minor>` | None    | `talentsec/nmap@1.0`                                         |
  | host     | boolean           | no       | Whether to use host network mode                                        | False   |                                                              |
  | entry    | method            | no       | Entry function invocation                                                 | None    |                                                              |
  | cmd      | list[str]         | no       | command line parameters                                                   | []      | ["--option", "-f"]                                           |
  | env      | list[str]         | no       | environment variable                                                     | []      | ["PATH=/bin", "DB_URI=..."]                                  |
  | files    | dict[ str, File ] | no       | Mount file, the key is the __absolute path__ of the target to mount, the value is the file content or the __local file path__, | {}      | {"/etc/config.json": json.dumps(...).encode(),"/data/blob": pathlib.Path("./local_path...")} |
  | links    | dict[ str, str ]  | no       | host link alias                                                 | {}      | {"proxy": other_task.host}                                   |
  | ports    | list[int]         | no       | Wait for the tcp port to be accessible                                           | []      | [8000]                                                       |
  | services | dict[str, Cr]     | no       | Dependent services, which are started before the main container runs and destroyed when the main container ends       | {}      | {"redis", Cr("redis:latest")}                                |

**Example**

* Examples of the files parameter

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

* Examples of ports, service parameters

```python
@levrt.remote
def entry():
    import redis
    import os

    r = redis.Redis.from_url(os.getenv("REDIS_URI"))
    # The environment variable REDIS_URI can be obtained in the container, and tcp://redis:9001 is available
    ...


Cr("...",
   entry=entry,
   env=["REDIS_URI=redis://redis:9001"],
   services={
       "redis": Cr("redis:latest", cmd=["--port", "9001"], ports=[9001])
   })
```

---

**Method**

> Cr.start()

**Function**

Start executing the task function injected in the Cr object, which is the entry method defined under `@levrt.remote`

After defining the Cr object, the task will not be executed immediately, it must be executed after the `await` keyword, the await keyword will automatically invoke start() and then wait for the result"

**Parameter**

none

**Return value**

levrt.Task object

**Example**

```python
async def background(domain:str):
    task = await nmap.port_os(ip).start()
    # Supposing that this scan needs to be executed in the container for a long time, it can be executed in the background. The signal() method will block the code and wait for the task to execute successfully before returning.
    # After starting the task, it is executed in the background, and then you can continue to start other tasks, block them when needed, and wait for the task to return the execution result

    await task.signal()
    # When the background execution is specified, the signal (.signal()) can be passed in the tool execution, such as immediately interrupting the execution of the tool in the container, etc.
    result = await task.done()
    data = await result.get()
    # Get dataset from database
    print(data)
```

---

**Method**

> Cr.done()

**Function**

Wait for the end of the task, the execution effect is equivalent to `await start()`, wait for the end of the task and return the result

**Parameter**

none

**Return value**

levrt.Document object

**Example**

```python
async def port_os_nmap_python(ip:str):
    result = await nmap.port_os(ip).done()
    data = await result.get()
    print(data)
```
