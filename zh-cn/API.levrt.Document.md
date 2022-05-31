### levrt.Document

**构造方法**

无

**属性**

* coll:

  mongodb collection

* __filters：

   dict，所储存数据在 mongodb 中的 _id 字段

---

**方法**

> Document.get(self, query=None, projection=None)

**功能**

获取任务执行结束之后存放在 mongo 中的数据，基本等价于 mongodb 的查询语句 db.collection.findOne(query, projection)

**参数**

* query

  mongodb query DSL, 默认取出所有字段

* projection

  选择你想获取的字段，假设结果字段很多，可以只获取部分字段

**返回值**

dict

**示例**

```python
async def background(domain:str):
    result = await nmap.port_os(ip)
    data = await result.get()
    print(data)
```
