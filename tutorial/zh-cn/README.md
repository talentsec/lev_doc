# 阅前说明

1、 **目标对象**：**所有人（包含开发者在内）**

2、 **文档功能**：
- 完成使用文档配置后，用户即可以在线使用所有潮汐上的工具，无需进行任何的针对单一工具的部署和安装，大大提高安全人员工作效率。

3、 **使用指导**：
- 针对于平台的使用者，只需根据「使用说明」进行平台配置即可使用平台上所有的工具；
- 针对于平台的开发者，「使用说明」是开发工具的前置步骤，完成「使用说明」配置之后需要根据「开发文档」的说明完成工具的开发。

# 潮汐安全平台使用说明

欢迎来到潮汐安全平台，在这里，你可以方便地调用一切平台上已集成的安全工具和编排，快速对资产进行检测，也可以成为一名开发者/贡献者，为其他安全人员贡献工具和编排。

在这个文档里，我们主要教大家如何通过潮汐平台来使用工具和编排，如果你对上传工具/编排有兴趣，请移步[开发文档](https://lev.zone/docs/#/zh-cn/)。

不同于传统的在线安全工具集成平台，潮汐从架构上做到任务分发和任务运行双端分离，**①工具的调用和运行、②数据的生产和存储展示，都完全依赖于用户私有的本地环境，不经过潮汐云端服务器，从而保障每一位安全人员的的数据安全和隐私安全**。

此外，潮汐支持一键启动工具和检测任务，无需进行工具的下载/部署/更新，也无需任何代码，即可完成任务检测！

接下来，让我们一起来看一下，如何使用潮汐平台吧！



## 概览

首先，为了可以正常地运行潮汐上的工具和编排，你需要完成本地环境的配置和搭建。

本地环境配置一共有3个步骤，分别是：前置环境安装，凭证上传获取，环境启动。

不同的操作系统环境的具体使用方式不同，本手册分为Windows、Linux、Mac三个操作系统环境的安装配置，每个环境都是如下三个步骤：

- 前置环境安装（docker安装）
- 凭证上传获取
- 环境启动



## 1. 本地环境配置

### Windows

#### 	1.1 前置环境安装（安装docker）

​			前置知识：[什么是docker](https://www.redhat.com/zh/topics/containers/what-is-docker)

​			如果你的设备上已经安装过docker，请检查其版本号，潮汐支持的版本号不低于**20.10.10**，如果满足条件，可以跳过此步骤，否则请进行更新。

​			Windows系统安装docker比较麻烦，需要先开启Windows系统内置的hyper-v功能，建议使用Linux系统或者WSL，方便快捷，以下教程以Win10为示例，且按照Win10系统版本划分（如果不知道自己Win10系统版本，请百度）。

##### 			1.1.1 开启CPU支持虚拟化

​			进入系统BIOS设置，查看BIOS是否开启CPU的虚拟化支持，如下图：			

​			![开启虚拟化支持.jpg](https://levimg.s3.cn-northwest-1.amazonaws.com.cn/x/2e12d9cd-cf65-4c6c-88a2-1b6d42110f22.JPEG)

##### 			1.1.2 Win10家庭版 前置步骤

​			强烈建议升级Win10专业版或者使用Linux、Mac，**如果是Win10专业版或者企业版、教育版，请跳过此步骤，进入1.1.3。**

​			首先需要启用hyper-v模块，Windows家庭版无法查询到hyper-v功能模块，需要特殊方式启用，如下：

​				更新操作系统至最新版本，Windows 设置 -> 更新 -> 检查更新

​				开启显示文件扩展名，详情参考[显示文件扩展名](https://jingyan.baidu.com/article/f7ff0bfcc9c0e12e26bb13a0.html)		

​				将下面代码拷入新建文档：			

```vbscript
pushd "%~dp0"
dir /b %SystemRoot%\servicing\Packages\*Hyper-V*.mum >hyper-v.txt
for /f %%i in ('findstr /i . hyper-v.txt 2^>nul') do dism /online /norestart /add-package:"%SystemRoot%\servicing\Packages\%%i"
del hyper-v.txt
Dism /online /enable-feature /featurename:Microsoft-Hyper-V-All /LimitAccess /ALL
```

​				将文档另存为install-hyper-v.cmd，右键点击 管理员运行：

![安装hyper-v.png](https://levimg.s3.cn-northwest-1.amazonaws.com.cn/x/61620bb6-2a0d-4974-a27c-39471f7c0020.png)		

​			安装完毕后输入Y，重启（注意：**如果安装完毕之后更新系统，有可能造成安装无效，需要检查，有必要的话需要重新安装hyper-v**）。			

​			重复上述步骤，将如下代码另存为change.cmd并以管理员身份运行：

​			``` REG ADD "HKEY_LOCAL_MACHINE\software\Microsoft\Windows NT\CurrentVersion" /v EditionId /T REG_EXPAND_SZ /d Professional /F```

​			至此，Win10家庭版hyper-v启用完毕。

##### 			1.1.3 Win10专业版、企业版、教育版

​			如果是专业版、教育版或者企业版，那么不需要像家庭版那么麻烦，直接在 控制面板 -> 程序 -> 启用或关闭Windows功能 -> 勾选hyper-v，然后按照提示重启即可。

​			以下步骤通用，所有Windows系统都必须执行：​			

- 开启显示文件扩展名，详情参考[显示文件扩展名](https://jingyan.baidu.com/article/f7ff0bfcc9c0e12e26bb13a0.html)

- [下载docker安装文件](https://desktop.docker.com/win/main/amd64/Docker%20Desktop%20Installer.exe)

- 点击文件开始安装docker（家庭版需要在安装时取消勾选Windows容器WSL2那个选项，其他版本不需要）

- 等待安装成功重启

- 重启之后，点击docker桌面客户端图标，**Windows**下docker有图形化界面：

​			图形化界面左下角docker引擎标识呈绿色则成功启动。

![image.png](https://levimg.s3.cn-northwest-1.amazonaws.com.cn/x/87fe783c-f999-4b50-85f9-99a0080d6561.png)

​			如果显示docker启动失败，报错“**因为一个Hyper-V组件未运行**”，那么就是没有启动硬件虚拟化，请执行第一步：**1.1.1 开启CPU支持虚拟化**。

#### 1.2 凭证上传与获取

​	1. 打开powershell，以管理员身份运行：

​		![打开powershell.png](https://levimg.s3.cn-northwest-1.amazonaws.com.cn/x/3d52434b-96e6-445c-9d0f-2d786f09cd4b.png)

​	输入ssh，换行，检测系统是否安装了ssh命令：			

![image.png](https://levimg.s3.cn-northwest-1.amazonaws.com.cn/x/3e4dd10e-3021-4647-a9e4-09877140b6f6.png)

​	如果没有出现上图所示内容，请参照以下文档安装ssh命令：
​	[安装ssh](https://docs.microsoft.com/zh-cn/windows-server/administration/openssh/openssh_install_firstuse)

​	然后输入命令：``` cd ~```

​	2. 上述命令进入当前用户家目录，接着输入```ls .ssh```命令，查看是否存在ssh密钥，即“id_ed25519”、“id_ed25519.pub”这两个文件：

![查看ssh密钥.png](https://levimg.s3.cn-northwest-1.amazonaws.com.cn/x/2997d374-f873-439b-ba77-d567adae155e.png)

如上图所示，显示红色字符、找不到路径的提示，意味着文件不存在，这时候就需要创建（**如果存在，那么直接跳过生成步骤**），输入下面命令，换行执行：

​		```ssh-keygen.exe -t ed25519```

会提示输入，不用管，按Enter键一路到底就好，直到出现如下提示即为完成（注：不建议使用默认的rsa加密方法生成密钥，高版本的openssh中会默认禁用rsa密钥）：

```vbscript
+---[ed25519 256]----+
|        .o       |
|        o        |
|      oo .       |
|.     ++o  .     |
|o.  ...*S.o      |
|.o+.o.o+=. .     |
|.o.+. o=.o+o     |
|. .. *oo.oB.o    |
| ...oE*.o=o+     |
+----[SHA256]-----+
```

此时再次输入`ls ~/.ssh`，就会显示文件上面提到的两个文件：

![密钥.jpg](https://levimg.s3.cn-northwest-1.amazonaws.com.cn/x/38c66c66-6a58-4609-800a-032a13f0c3c8.JPEG)

3. 输入`cat ~/.ssh/id_ed25519.pub`命令，查看文件内容，复制输出的内容（powershell不可以直接右键复制，需要特殊操作，详情百度）。

   如果实在不会复制，那么也可以通过文件浏览器打开id_ed25519.pub文件，该文件路径为`C盘\用户\（当前用户名）\.ssh\`，复制内容。

4. 登录潮汐安全平台，在`账户设置`—`公钥管理`中，选择上传ssh公钥，名称随意输入，并将上述步骤复制的文件内容添加入至公钥中即可。

5. 在第三步的路径下，该文件路径为`C盘\用户\（当前用户名）\.ssh\`，创建`新建文本文档.txt`，打开并复制添加以下内容保存：

   ```yaml
   Host lev
       HostName service.lev.zone
       Port 2222
       User username # 这里将username换成你在潮汐安全平台的用户名，非邮箱名！
   ```

   保存完毕，将此文件名修改为`config`，注意，此处没有txt的后缀名，类型为文件，而不是文本文档。

6. 在powershell中输入测试命令`ssh lev`（$符号无意义，请勿输入）：

   ```vbscript
   $ ssh lev
   Connection to service.lev.zone closed. # 显示此消息即配置成功。
   ```

   如返回以下消息，则说明配置文件有问题，请检查前述配置步骤：

   ```Bash
   $ ssh lev
   Permission denied (publickey). # 显示此消息说明ssh公钥配置有误，请检查ssh公钥和用户名是否正确。
   ```

   如果上述流程显示错误，也可以直接输入下列命令行  ：

   ```ssh username@service.lev.zone -p 2222```（username替换成潮汐平台用户名，非邮箱账号！）

   出现如下语句：
   ```Are you sure you want to continue connecting (yes/no/[fingerprint])?``` 后立即输入yes并按回车，请勿等待过长时间，否则可能启动失败。

7. 首先，在潮汐平台【**账户设置-我的设备**】页面添加设备，输入一个名称，再点击提交即可。

8. 接下来，执行以下命令启动本地环境：

   ```bash
   $ ssh lev agent | docker run -v /var/run/docker.sock:/docker --rm -i talentsec/lev
   # 出现如下语句：Are you sure you want to continue connecting (yes/no/[fingerprint])? 后立即输入yes并按回车，请勿等待过长时间，否则可能启动失败。
   INFO:root:Agent is ready
   # 出现此语句则表示启动成功。
   ```

   执行命令后将自动拉取镜像容器并启动容器，整个过程可能持续几分钟的时间。待容器全部启动完毕则一切准备就绪！

   如果由于网络原因，下载容器的速度过慢，那么可以通过更换docker镜像源的方式来进行加速：

   ```Bash
   打开docker，点击Settings进入设置
   点击Docker Engine，将看到如下页面
   ```

   ![image.png](https://levimg.s3.cn-northwest-1.amazonaws.com.cn/x/e46b4413-d432-4817-89c3-6abd73b645e8.png)

   粘贴以下代码：

   ```Bash
     "registry-mirrors": [
       "https://docker.mirrors.ustc.edu.cn/"
     ]
   ```

   粘贴的位置如下图所示（新手Tips：`"buildkit": true`下面一行的花括号后面需要加上一个半角的逗号，代码粘贴位置为最下面一行花括号的前面，否则会出现异常）：

   ![image.png](https://levimg.s3.cn-northwest-1.amazonaws.com.cn/x/575d3a79-7772-4318-a566-7293f809a4c5.png)

   点击Apply&Restart即可！

   完成了上述加速操作后，再次执行以下命令启动本地环境：

   ```bash
   $ ssh lev agent | docker run -v /var/run/docker.sock:/docker --rm -i talentsec/lev
   # 出现如下语句：Are you sure you want to continue connecting (yes/no/[fingerprint])? 后立即输入yes并按回车，请勿等待过长时间，否则可能启动失败。
   INFO:root:Agent is ready
   # 出现此语句则表示启动成功。
   ```

   最终，在【账户设置-我的设备】中，可以看到该设备已经上线！



### Linux

#### 1.  安装 docker

​		Ubuntu：[参考文档](https://docs.docker.com/engine/install/ubuntu/)

​		CentOS：[参考文档](https://docs.docker.com/engine/install/centos/)

​		Debian：[参考文档](https://docs.docker.com/engine/install/debian/)

#### 2. 生成 ssh密钥

输入命令`ls -la ~/.ssh`，检测系统是否存在ssh密钥，如果不存在请按照教程生成密钥对：

```
$ ssh-keygen -t ed25519 -C "youremail@example.com"
# 将邮箱替换为潮汐的注册邮箱
# 出现输入选项全部回车即可
# 不建议使用默认的rsa加密算法生成密钥对，ed25519安全性更高，且高版本的openssh中会默认禁用rsa密钥
```

#### 3. 上传

登录潮汐平台，在 `账户头像` - `账户设置` — `公钥管理` 中，选择 `创建SSH公钥` ，名称可以随意输入，这里我们输入 `main` ，并将上述步骤生成的.ssh文件夹下的id_ed25519.pub公钥文件内容复制加入至公钥中即可。

#### 4. 配置ssh config 文件

`touch ~/.ssh/config`

写入以下配置：

```bash
Host lev
	HostName service.lev.zone
	Port 2222
	User username # 这里将username换成你在潮汐安全平台的用户名。
```

然后输入`ssh lev`命令，验证是否配置成功：

```Bash
$ ssh lev
Connection to service.lev.zone closed. # 显示此消息即配置成功。
```

如返回以下消息，则说明配置文件有问题，请检查前述配置步骤：

```Bash
$ ssh lev
Permission denied (publickey). # 显示此消息说明ssh公钥配置有误，请检查ssh公钥和用户名是否正确。
```

如果上述流程显示错误，也可以直接输入下列命令行：

```bash
ssh username@service.lev.zone -p 2222
# （username替换成潮汐平台用户名，非邮箱账号！）
```

#### 5. 启动

在 `账户头像` - `账户设置` - `我的设备` 页面，选择 `添加设备`，随意输入一个名称，点击提交即可。

接下来，执行以下命令启动本地环境：

```bash
ssh lev agent | docker run -v /var/run/docker.sock:/docker --rm -i talentsec/lev
```

该命令将自动拉取镜像、启动容器，整个过程可能持续几分钟的时间。

命令执行完毕后，在 `账户头像` - `账户设置` - `我的设备` 页面，可以看到该设备已经上线。

![image.png](https://levimg.s3.cn-northwest-1.amazonaws.com.cn/x/%E6%88%AA%E5%B1%8F2022-05-29+14.05.37.png)

### Mac

#### 安装docker

如果你的设备上已经安装过docker，请检查其版本号，潮汐支持的版本号不低于**20.10.10**，否则请进行更新。

进入[docker官方网站的下载页面](https://docs.docker.com/get-docker/)，并找到符合自己电脑操作系统的docker进行下载并安装；

**Mac**下docker有图形化界面：

图形化界面左下角docker引擎标识呈绿色则成功启动。

![image.png](https://levimg.s3.cn-northwest-1.amazonaws.com.cn/x/87fe783c-f999-4b50-85f9-99a0080d6561.png)

#### 生成ssh公钥

在Mac下打开文件夹（访达/Finder），按下`shift`+`command`+`G`组合键，将出现如下弹框，并在其中输入`~/.ssh`回车，进入.ssh目录后，查看是否有"id_ed25519"、"id_ed25519.pub"文件。

![image.png](https://levimg.s3.cn-northwest-1.amazonaws.com.cn/x/e109ae0f-9bc1-4ea7-912f-b9641720f9fb.JPEG)

如没有，则在命令行输入下列命令来生成：

```
Mac：
$ ssh-keygen -t ed25519 -C "youremail@example.com"
# 将邮箱替换为潮汐的注册邮箱
# 出现输入选项全部回车即可
# 不建议使用默认的rsa加密算法生成密钥对，ed25519安全性更高，且高版本的openssh中会默认禁用rsa密钥
```

#### 上传ssh公钥

登录潮汐安全平台，在`账户设置`—`公钥管理`中，选择上传ssh公钥，名称随意输入，并将上述步骤生成的id_ed25519.pub或id_ed25519.pub公钥文件内容复制加入至公钥中即可。

```sh
# Mac：查看.pub文件，然后复制上传到平台，建议使用此方式。
$ cat ~/.ssh/id_ed25519.pub
```

#### 配置ssh config文件

首先，第一步需要你找到存放ssh公钥的文件目录：

在命令行运行`touch ~/.ssh/config`创建config文件，并写入以下配置：

```bash
Host lev
    HostName service.lev.zone
    Port 2222
    User username # 这里将username换成你在潮汐安全平台的用户名，非邮箱名！
```

使用如下命令验证是否配置成功。

```Bash
$ ssh lev
Connection to service.lev.zone closed. # 显示此消息即配置成功。
```

如返回以下消息，则说明配置文件有问题，请检查前述配置步骤：

```Bash
$ ssh lev
Permission denied (publickey). # 显示此消息说明ssh公钥配置有误，请检查ssh公钥和用户名是否正确。
```

如果上述流程显示错误，也可以直接输入下列命令行：
```ssh username@service.lev.zone -p 2222```（username替换成潮汐平台用户名，非邮箱账号！）

出现如下语句：
```Are you sure you want to continue connecting (yes/no/[fingerprint])? ```后立即输入yes并按回车，请勿等待过长时间，否则可能启动失败。

#### 启动

首先，在【账户设置-我的设备】页面添加设备，输入一个名称，再点击提交即可。

接下来，执行以下命令启动本地环境（如果拉取速度慢的话建议修改docker的镜像源为中科大，详情百度）：

```bash
$ ssh lev agent | docker run -v /var/run/docker.sock:/docker --rm -i talentsec/lev
# 出现如下语句：Are you sure you want to continue connecting (yes/no/[fingerprint])? 后立即输入yes并按回车，请勿等待过长时间，否则可能启动失败。
INFO:root:Agent is ready
# 出现此语句则表示启动成功。
```

执行命令后将自动拉取镜像容器、启动容器，整个过程可能持续几分钟的时间。待容器全部启动完毕则一切准备就绪！



## 2. 潮汐平台使用

### 2.1 平台功能介绍

登录潮汐安全平台后，可以看到主页如下图所示，在目录栏一共有6个选项。

![image.png](https://levimg.s3.cn-northwest-1.amazonaws.com.cn/x/%E6%88%AA%E5%B1%8F2022-05-29+14.21.40.png)

>任务：用于显示任务清单，按照任务状态分别展示
>
>收藏：用于展示收藏的编排、工具和作者
>
>仓库：用于管理和展示你开发的工具、镜像，并且进行授权管理
>
>市场：潮汐平台开源安全工具、编排市场
>
>论坛：用于展示潮汐平台最新消息、官方团队消息以及用户讨论
>
>文档：潮汐平台的使用文档和开发文档

### 2.2 使用工具

### 2.2.1 选择官方工具

进入 `市场` 选项卡，选择左侧的分类，
> 将 `类型` 选择为 `工具`
>
> 将 `过滤` 选择为 `官方`
>
> 并在 `搜索输入框` 输入 `nmap`

可以看到官方集成的工具 `nmap`：

![](https://levimg.s3.cn-northwest-1.amazonaws.com.cn/x/%E6%88%AA%E5%B1%8F2022-05-29+14.43.36.png)

### 2.2.2 了解工具信息

进入工具主页，可以看到页面分为4个选项卡:

> 基本信息：展示工具的介绍、多种模式以及版本等核心信息
>
> 源码文件：展示工具的源码（以包为核心）
>
> 交流面板：展示社区用户对该工具的反馈及交流信息
>
> 运行记录：展示当前账户对该工具的使用记录

![](https://levimg.s3.cn-northwest-1.amazonaws.com.cn/x/%E6%88%AA%E5%B1%8F2022-05-29+18.08.55.png)

### 2.2.3 运行工具

可以看到，`nmap` 提供了 `raw`、`typical`、`alive`、`traceroute`、`port_os` 5种模式供调用，值得一提的是，大部分官方工具都提供了 `raw` 模式在内的多种模式。

> raw 模式：可以理解为原生模式，未经过场景化
>
> 其他模式：工具的不同场景化模式

这里我们准备使用 `traceroute` 模式，可以看到 `使用说明` 部分已经介绍了该模式的工作原理和使用方式：

![](https://levimg.s3.cn-northwest-1.amazonaws.com.cn/x/%E6%88%AA%E5%B1%8F2022-05-29+18.59.17.png)

继续选择 `新建任务` 选项卡，进入 `添加任务` 页面，选择 `nmap 路由追踪...` 模式后，可以看到页面弹出了需要配置的参数组，填好待配置的内容，点击 `提交` 即可运行：

![](https://levimg.s3.cn-northwest-1.amazonaws.com.cn/x/%E6%88%AA%E5%B1%8F2022-05-29+19.03.38.png)

### 2.3 查看结果

任务执行完毕之后，点击 `运行结果` 按钮，可以查看任务的运行结果。通过刚刚的任务，我们可以很容易地从结果中判断出从任务执行器到 `lev.zone` 的路由节点。

> 注：由于 Mac/Windows 平台的 Docker Desktop 实现尚不完善，因此 nmap 的 traceroute 功能可能会结果异常，在高可靠性场景下，建议使用 Linux 平台作为潮汐平台的执行环境。

![](https://levimg.s3.cn-northwest-1.amazonaws.com.cn/x/%E6%88%AA%E5%B1%8F2022-05-29+19.17.21.png)
