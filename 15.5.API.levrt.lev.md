### levrt.lev / levrt.ctx

**Construction method**

None

**Effect**

store data

---

**Method**

> lev.set()

**Function**

Create a new namespace to store data

**Parameter**

* data

  Namespace

* **kwargs

  Stored data, in any format

**Example**

```python
import levrt
from levrt import lev


def scan(target: str):
    @levrt.remote
    def entry():
        issues=["Some issues"]
        # store data
        lev.set(target=target, issues=issues)

    return Cr("...", "ts/issues@1.0", entry=entry())


async def main():
    doc = await scan("example.com")
    assert {"target": "example.com", "issues": ["Some issues"]} == await doc.get()
    assert doc.coll.collaction_name == "ts/issues@1.0"
    # Saved at ts/issues@1.0

    with lev / "example":
        example = await scan("example.com")
        # Saved at example|ts/issues@1.0

    with lev / "httpbin" / "org":
        httpbin = await scan("httpbin.org")
        # Saved at httpbin|org|ts/issues@1.0
```
