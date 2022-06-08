### levrt.Document

**Construction method**

none

**Attribute**

* coll:

  mongodb collection

* __filters：

   dict, the _id field of the stored data in mongodb

---

**Method**

> Document.get(self, query=None, projection=None)

**Function**

Get the data stored in mongo after the task is executed, which is basically equivalent to the mongodb query statement db.collection.findOne(query, projection)

**Parameter**

* query

  mongodb query DSL, fetches all fields by default

* projection

  Select the fields you want to get, if there are many result fields, you can get only some of the fields

**Return value**

dict

**Example**

```python
async def background(domain:str):
    result = await nmap.port_os(ip)
    data = await result.get()
    print(data)
```
