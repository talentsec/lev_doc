### annot.meta

**构造方法**

> annot.meta(exports, desc, params,cats,tags,ext)

**作用**

用于模式或者编排函数的描述，或者导出模块

**参数说明**

  | 参数名 | 类型         | 是否必传 | 说明                                    | default | 示例                                                         |
  | ------ | ------------ | -------- | --------------------------------------- | ------- | ------------------------------------------------------------ |
  | export | list         | 否       | 当前模块导出项                          | None    | [raw, alive, port_os]                                        |
  | desc   | str          | 否       | 描述                                    | ‘’      | ”检测局域网中存活的主机“                                     |
  | params | list         | 否       | 参数列表                                | []      | [annot.Param("ip", "进行扫描的目标ip", holder="192.168.1.1/24")] |
  | cats   | list \| dict | 否       | 模块或者编排分类(详情参考 14. FAQ 附录) | None    | {<br/>  Attck: [Attck.Reconnaissance],<br/>  BlackArch: [BlackArch.Scanner]<br/>} |
  | tags   | list[str]    | 否       | 工具模式或者编排的标签，用于搜索        | []      | [ 'nmap', 'scan' ]                                           |

**示例**

参考 [5.Hello World](https://lev.zone/docs/#/zh-cn/5.Hello_World)
