# api

## 1. 获取 token

> 获取 jwt 凭证，用以业务 api 的认证

`POST` https://lev.zone/query/token

> 参数

| 名称        | 类型      | 解释                     | 位置   |
| ----------- | --------- | ------------------------ | ------ |
| `username`  | `string`  | `用户名`                 | `body` |
| `password`  | `string`  | `密码`                   | `body` |
| `container` | `string`  | `容器名(可向开发者咨询)` | `body` |
| `port`      | `integer` | `端口`                   | `body` |

> 返回

| 名称    | 类型     | 解释   | 位置   |
| ------- | -------- | ------ | ------ |
| `token` | `string` | `凭证` | `body` |

> 示例

```sh
curl -X POST https://lev.zone/query/token -d '{"username": "$username", "password": "$password", "container": "$container", "port": $port}'
```

## 2. 发起请求

> 发起业务 api 请求

https://lev.zone/query/biz_path

其中`biz_path`需要替换为实际的业务路径，请求方法根据具体的应用实现有所不同，比如某个接口为 `POST /sign_in`，那么实际的请求应该是`POST https://lev.zone/query/sign_in`

> 参数

| 名称            | 类型     | 解释                | 位置     |
| --------------- | -------- | ------------------- | -------- |
| `Authorization` | `string` | `之前获取到的token` | `header` |
| ...             | ...      | ...                 | ...      |

取决于具体的应用实现

> 返回

取决于具体的应用实现

> 示例

```sh
curl 'https://lev.zone/query/antifraud/api/v1/results/?skip=0&limi=10&only_upload=false' -H 'Authorization: $token'
```
