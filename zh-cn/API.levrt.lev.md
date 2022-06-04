### levrt.lev / levrt.ctx

**构造方法**

无

**作用**

储存数据

---

**方法**

> lev.set()

**功能**

创建新的命名空间存储数据

**参数**

* data

  命名空间

* **kwargs

  存储的数据，任意格式

**示例**

```python
import levrt
from levrt import lev


def scan(target: str):
    @levrt.remote
    def entry():
        issues=["Some issues"]
        # 储存数据
        lev.set(target=target, issues=issues)

    return Cr("...", "ts/issues@1.0", entry=entry())


async def main():
    doc = await scan("example.com")
    assert {"target": "example.com", "issues": ["Some issues"]} == await doc.get()
    assert doc.coll.collaction_name == "ts/issues@1.0"
    # 保存在 ts/issues@1.0

    with lev / "example":
        example = await scan("example.com")
        # 保存在 example|ts/issues@1.0

    with lev / "httpbin" / "org":
        httpbin = await scan("httpbin.org")
        # 保存在 httpbin|org|ts/issues@1.0
```
