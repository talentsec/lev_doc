### annot.meta

**Construction method**

> annot.meta(exports, desc, params,cats,tags,ext)

**Effect**

It is used to describe mode or workflow functions, or export modules

**Parameter illustration**

  | Parameter name | Type         | Is it mandatory | illustration                                                                 | default | Example                                                         |
  | ------         | ------------ | --------        | ---------------------------------------                                      | ------- | ------------------------------------------------------------ |
  | export         | list         | No              | Current module exports                                                       | None    | [raw, alive, port_os]                                        |
  | desc           | str          | No              | description                                                                  | ‘’      | "Detect live hosts on LAN"                                   |
  | params         | list         | No              | Parameter list                                                               | []      | [annot.Param("ip", "target ip to scan", holder="192.168.1.1/24")] |
  | cats           | list \| dict | No              | Module or workflow category (refer to 14. FAQ appendix for details)| None    | {<br/>  Attck: [Attck.Reconnaissance],<br/>  BlackArch: [BlackArch.Scanner]<br/>} |
  | tags           | list[str]    | No              | Tool mode or workflow tags for searching                                     | []      | [ 'nmap', 'scan' ]                                           |

**Example**

Refer to [5.Hello World](https://lev.zone/docs/#/zh-cn/5.Hello_World)
