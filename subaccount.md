# 子账户对接

1. 后端：运行一个 app 之后，对应 task 会生成一个`jwt key`，并放到环境变量`LEV_JWT`中
2. 前端：app 可以通过 `GET` [https://lev.zone/api/account/children](https://lev.zone/api/account/children) 查询当前企业子账户的分页数据(页数`page`, 每页`size`)，不包括根账户
3. 前端：调用 `GET` [https://lev.zone/api/task/:task_id/jwt_token](https://lev.zone/api/task/:task_id/jwt_token) 可生成 task 的`jwt`(过期时间`1h`)，jwt 中包含`用户名`，`用户ID`，`用户角色`等基本账户信息，应当用该 token 去访问 api
4. 后端：使用环境变量中的`jwt key`去 verify jwt，使用`HS256`算法( `HMAC SHA256` )
