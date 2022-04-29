# 潮汐安全平台使用文档

欢迎来到潮汐安全平台，在这里，你可以方便地调用一切平台上已集成的安全工具和编排，快速对资产进行检测，也可以成为一名开发者/贡献者，为其他安全人员贡献工具和编排。

在这个文档里，我们主要教大家如何通过潮汐平台来使用工具和编排，如果你对上传工具/编排有兴趣，请移步[开发文档](https://lev.zone/portal/developer)。

不同于传统的在线安全工具集成平台，潮汐从架构上做到任务分发和任务运行双端分离，**①工具的调用和运行、②数据的生产和存储展示，都完全依赖于用户私有的本地环境，不经过潮汐云端服务器，从而保障每一位安全人员的的数据安全和隐私安全**。此外，潮汐支持一键启动工具和检测任务，无需进行工具的下载/部署/更新，也无需任何代码，即可完成任务检测！

接下来，让我们一起来看一下，如何使用潮汐平台吧！



## 概览

首先，为了可以正常地运行潮汐上的工具和编排，你需要完成本地环境的配置和搭建。本地环境配置一共有3个步骤，分别是：前置环境安装，凭证上传获取，环境启动。不同的操作系统环境的具体使用方式不同，本手册分为Windows、Linux、Mac三个操作系统环境的安装配置，每个环境都是如下三个步骤：

- 前置环境安装（docker安装）
- 凭证上传获取
- 环境启动





## 1. 本地环境配置

### Windows

#### 	1.1 前置环境安装（安装docker）

​			前置知识：[什么是docker](https://www.redhat.com/zh/topics/containers/what-is-docker)

​			如果你的设备上已经安装过docker，请检查其版本号，潮汐支持的版本号不低于**20.10.10**，否则请进行更新，且可以跳过此步骤。

​			Windows系统安装docker比较麻烦，需要先开启Windows系统内置的hyper-v功能，建议使用Linux系统或者WSL，方便快捷，以下教程以Win10为示例，且按照Win10系统版本划分(如果不知道自己Win10系统版本，请百度)。



##### 			1.1.1 开启CPU支持虚拟化

​			进入系统BIOS设置，查看BIOS是否开启CPU的虚拟化支持，如下图：			

​			![开启虚拟化支持.jpg](https://levimg.s3.cn-northwest-1.amazonaws.com.cn/x/2e12d9cd-cf65-4c6c-88a2-1b6d42110f22.JPEG)





##### 			1.1.2 Win10家庭版 前置步骤

​			强烈建议升级Win10专业版或者使用Linux、Mac，**如果是Win10专业版或者企业版、教育版，请跳过此步骤，进入1.1.3。**

​			首先需要启用hyper-v模块，Windows家庭版无法查询到hyper-v功能模块，需要特殊方式启用，如下：



​				更新操作系统至最新版本，Windows 设置 -> 更新 -> 检查更新

​				开启显示文件扩展名，详情参考[显示文件扩展名](https://jingyan.baidu.com/article/f7ff0bfcc9c0e12e26bb13a0.html)

​			

​				将下面代码拷入新建文档：			

```vbscript
pushd "%~dp0"
dir /b %SystemRoot%\servicing\Packages\*Hyper-V*.mum >hyper-v.txt
for /f %%i in ('findstr /i . hyper-v.txt 2^>nul') do dism /online /norestart /add-package:"%SystemRoot%\servicing\Packages\%%i"
del hyper-v.txt
Dism /online /enable-feature /featurename:Microsoft-Hyper-V-All /LimitAccess /ALL 
```

​				将文档另存为install-hyper-v.cmd，右键点击 管理员运行

![安装hyper-v.png](https://levimg.s3.cn-northwest-1.amazonaws.com.cn/x/61620bb6-2a0d-4974-a27c-39471f7c0020.png)

​			

​			安装完毕后输入Y，重启（注意：**如果安装完毕之后更新系统，有可能造成安装无效，需要检查，有必要的话需要重新安装hyper-v**）。

​			

​			重复上述步骤，将如下代码另存为change.cmd并以管理员身份运行：

​			``` REG ADD "HKEY_LOCAL_MACHINE\software\Microsoft\Windows NT\CurrentVersion" /v EditionId /T REG_EXPAND_SZ /d Professional /F```

​			至此，Win10家庭版hyper-v启用完毕。



##### 			1.1.3 Win10专业版、企业版、教育版

​			如果是专业版、教育版或者企业版，那么不需要像家庭版那么麻烦，直接在 控制面板 -> 程序 -> 启用或关闭Windows功能 -> 勾选hyper-v，然后按照提示重启即可。

​			以下步骤通用，所有Windows系统都必须执行：

​			

​			开启显示文件扩展名，详情参考[显示文件扩展名](https://jingyan.baidu.com/article/f7ff0bfcc9c0e12e26bb13a0.html)

​			下载docker安装文件[docker安装程序下载](https://desktop.docker.com/win/main/amd64/Docker%20Desktop%20Installer.exe)

​			点击文件开始安装docker（家庭版需要在安装时取消勾选Windows容器WSL2那个选项，其他版本不需要）

​			等待安装成功重启。重启之后，打开docker桌面客户端图标，**Windows**下docker有图形化界面：

​			图形化界面左下角docker引擎标识呈绿色则成功启动![image.png](https://levimg.s3.cn-northwest-1.amazonaws.com.cn/x/87fe783c-f999-4b50-85f9-99a0080d6561.png)

​			如果显示docker启动失败，报错“**因为一个Hyper-V组件未运行**”，那么就是没有启动硬件虚拟化，请执行第一步：**1.1.1 开启CPU支持虚拟化**



#### 1.2 凭证上传与获取

​	1. 打开powershell，以管理员身份运行：

​		![打开powershell.png](https://levimg.s3.cn-northwest-1.amazonaws.com.cn/x/3d52434b-96e6-445c-9d0f-2d786f09cd4b.png)"



​	输入ssh，换行，检测系统是否安装了ssh命令：			

![image.png](https://levimg.s3.cn-northwest-1.amazonaws.com.cn/x/3e4dd10e-3021-4647-a9e4-09877140b6f6.png)

​	如果没有出现上图所示内容，请参照以下文档安装ssh命令：
​	[安装ssh](https://docs.microsoft.com/zh-cn/windows-server/administration/openssh/openssh_install_firstuse)

​	然后输入命令：

​		``` cd ~```

​	2. 上述命令进入当前用户主目录，接着输入```ls .ssh```命令，查看是否存在ssh密钥，即“id_ed25519”、“id_ed25519.pub”这两个文件：

![查看ssh密钥.png](https://levimg.s3.cn-northwest-1.amazonaws.com.cn/x/2997d374-f873-439b-ba77-d567adae155e.png)

如上图所示，显示红色字符、找不到路径的提示，意味着文件不存在，这时候就需要创建(**如果存在，那么直接跳过生成步骤**)，输入下面命令，换行执行：

​		```ssh-keygen.exe -t ed25519```

会提示输入，不用管，按Enter键一路到底就好，直到出现如下提示：

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

即为完成(注：不建议使用默认的rsa加密方法生成密钥，高版本的openssh中会默认禁用rsa密钥)。
此时再次输入`ls ~/.ssh`，就会显示文件上面提到的两个文件：

![密钥.jpg](https://levimg.s3.cn-northwest-1.amazonaws.com.cn/x/38c66c66-6a58-4609-800a-032a13f0c3c8.JPEG)

3. 输入`cat ~/.ssh/id_ed25519.pub`命令，查看文件内容，复制输出的内容(powershell不可以直接右键复制，需要特殊操作，详情百度)。
   如果实在不会复制，那么也可以通过文件浏览器打开id_ed25519.pub文件，该文件路径为`C盘\用户\(当前用户名)\.ssh\`，复制内容。

4. 登录潮汐安全平台，在`账户设置`—`公钥管理`中，选择上传ssh公钥，名称随意输入，并将上述步骤复制的文件内容添加入至公钥中即可。

5. 在第三步的路径下，该文件路径为`C盘\用户\(当前用户名)\.ssh\`，创建`新建文本文档.txt`，打开并复制添加以下内容保存：

   ```yaml
   Host lev
       HostName service.lev.zone
       Port 2222
       User username # 这里将username换成你在潮汐安全平台的用户名，非邮箱名！
   ```

   保存完毕，**将此文件名修改为`config`**，注意，此处没有txt的后缀名，类型为文件，而不是文本文档。

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

   如果上述流程显示错误，也可以直接输入下列命令行  
   ```ssh username@service.lev.zone -p 2222 （username替换成潮汐平台用户名，非邮箱账号！）``` 

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
   粘贴以下代码

   ```Bash
     "registry-mirrors": [
       "https://docker.mirrors.ustc.edu.cn/"
     ]
   ```

   粘贴的位置如下图所示(新手Tips：`"buildkit": true`下面一行的花括号后面需要加上一个半角的逗号，代码粘贴位置为最下面一行花括号的前面，否则会出现异常)
   
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
   进入[下一步](https://lev.zone/portal/get-started#2.-%E6%BD%AE%E6%B1%90%E5%AE%89%E5%85%A8%E5%B9%B3%E5%8F%B0)
​	

### Linux

#### 1.  安装 docker

​		请使用国内源，否则速度会很慢：

​		Ubuntu：[参考文档](https://docs.docker.com/engine/install/ubuntu/)
​		CentOS：[参考文档](https://docs.docker.com/engine/install/centos/)
​		Debian：[参考文档](https://docs.docker.com/engine/install/debian/)
​		  

#### 2. 生成 ssh密钥

输入命令`ls -la ~/.ssh`，检测系统是否存在ssh密钥，如果不存在请按照教程生成密钥对：	

```
$ ssh-keygen -t ed25519 -C "youremail@example.com"
# 将邮箱替换为潮汐的注册邮箱
# 出现输入选项全部回车即可
# 不建议使用默认的rsa加密算法生成密钥对，ed25519安全性更高，且高版本的openssh中会默认禁用rsa密钥
```

#### 3. 上传

登录潮汐安全平台，在`账户设置`—`公钥管理`中，选择上传ssh公钥，名称随意输入，并将上述步骤生成的.ssh文件夹下的id_ed25519.pub公钥文件内容复制加入至公钥中即可。

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
```ssh username@service.lev.zone -p 2222（username替换成潮汐平台用户名）```

出现如下语句：
```Are you sure you want to continue connecting (yes/no/[fingerprint])?``` 后立即输入yes并按回车，请勿等待过长时间，否则可能启动失败。

#### 5. 启动

首先，在【账户设置-我的设备】页面添加设备，输入一个名称，再点击提交即可。

接下来，执行以下命令启动本地环境(如果拉取速度慢的话，建议修改docker的镜像源为中科大)：

```bash
$ ssh lev agent | docker run -v /var/run/docker.sock:/docker --rm -i talentsec/lev
# 出现如下语句：Are you sure you want to continue connecting (yes/no/[fingerprint])? 后立即输入yes并按回车，请勿等待过长时间，否则可能启动失败。
INFO:root:Agent is ready
# 出现此语句则表示启动成功。
```

执行命令后将自动拉取镜像容器、启动容器，整个过程可能持续几分钟的时间。待容器全部启动完毕，则一切准备就绪！

最终，在【账户设置-我的设备】中，可以看到该设备已经上线！
![image.png](https://levimg.s3.cn-northwest-1.amazonaws.com.cn/x/64c575b9-3ea3-494c-b25c-191835f2ba78.png)

接下来，让我们来看看怎么使用潮汐来运行工具/编排，进行资产的安全检测吧！进入[下一步](https://lev.zone/portal/get-started#2.-%E6%BD%AE%E6%B1%90%E5%AE%89%E5%85%A8%E5%B9%B3%E5%8F%B0)



### Mac

#### 安装docker

如果你的设备上已经安装过docker，请检查其版本号，潮汐支持的版本号不低于**20.10.10**，否则请进行更新。
进入docker官方网站的下载页面[https://docs.docker.com/get-docker/](https://docs.docker.com/get-docker/)，并找到符合自己电脑操作系统的docker进行下载并安装；
**Mac**下docker有图形化界面：

图形化界面左下角docker引擎标识呈绿色则成功启动。
![image.png](https://levimg.s3.cn-northwest-1.amazonaws.com.cn/x/87fe783c-f999-4b50-85f9-99a0080d6561.png)

#### 生成ssh公钥

在Mac下打开文件夹（访达/Finder），按下`shift`+`command`+`G`组合键，将出现如下弹框，并在其中输入`~/.ssh`回车，进入.ssh目录后，查看是否有"id_ed25519、id_ed25519.pub"文件![image.png](https://levimg.s3.cn-northwest-1.amazonaws.com.cn/x/e109ae0f-9bc1-4ea7-912f-b9641720f9fb.JPEG)
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
```ssh username@service.lev.zone -p 2222（username替换成潮汐平台用户名）``` 

出现如下语句：
```Are you sure you want to continue connecting (yes/no/[fingerprint])? ```后立即输入yes并按回车，请勿等待过长时间，否则可能启动失败。

#### 启动

首先，在【账户设置-我的设备】页面添加设备，输入一个名称，再点击提交即可。

接下来，执行以下命令启动本地环境(如果拉取速度慢的话建议修改docker的镜像源为中科大，详情百度)：

```bash
$ ssh lev agent | docker run -v /var/run/docker.sock:/docker --rm -i talentsec/lev
# 出现如下语句：Are you sure you want to continue connecting (yes/no/[fingerprint])? 后立即输入yes并按回车，请勿等待过长时间，否则可能启动失败。
INFO:root:Agent is ready
# 出现此语句则表示启动成功。
```

执行命令后将自动拉取镜像容器、启动容器，整个过程可能持续几分钟的时间。待容器全部启动完毕则一切准备就绪！之后进入[下一步](https://lev.zone/portal/get-started#2.-%E6%BD%AE%E6%B1%90%E5%AE%89%E5%85%A8%E5%B9%B3%E5%8F%B0)











## 2. 潮汐安全平台

登录潮汐安全平台后，可以看到在目录栏一共有6项。

![image.png](https://levimg.s3.cn-northwest-1.amazonaws.com.cn/x/18e05be5-ed18-4903-9b20-4c48d5c28b30.png)

- 任务：我运行过的检测任务
- 收藏：我收藏的工具/编排/作者
- 仓库：我上传的工具/编排
- 市场：全平台工具/编排的汇总
- 论坛：教程汇集地以及交流处
- 文档：平台使用文档以及开发文档

### 2.1 选择教程

如果这是你第一次来到潮汐，你也许会不知道从何开始、应该先体验哪些工具，那么你可以先进入论坛页面，根据官方提供的渗透教程1-4篇，轻松在潮汐上完成一次Web渗透！

![image_1.png](https://levimg.s3.cn-northwest-1.amazonaws.com.cn/x/0c5d1e0b-6024-41d6-a742-c6b4aa87e502.png)

### 2.2 选择工具

如果你有明确想要使用的工具，也可以先跳过教程，在市场找到相关的工具/编排，直接运行任务。

![image_2.png](https://levimg.s3.cn-northwest-1.amazonaws.com.cn/x/dd7c358b-2b01-440e-852d-d49fdab60c27.png)

找到想要使用的工具，进入工具页面。

![image_3.png](https://levimg.s3.cn-northwest-1.amazonaws.com.cn/x/098fd6cb-5afb-4348-bffb-c951577b372f.png)

工具页面分为如下四个模块：

![image_4.png](https://levimg.s3.cn-northwest-1.amazonaws.com.cn/x/7d1add24-d3d1-4f65-808f-a28fe95b9163.png)

- 基本信息
  此模块简单描述工具的作用和版本。主要描述工具的借口使用说明，例如此工具的direct模式只需要输入url参数，类型为str字符串。
- 源码文件
  这里可以直接查看工具的调用源码，用作学习和研究。
- 交流面板
  在这里可以分享你对此工具的使用技巧和想法。
- 运行记录
  可以查看对此工具的调用记录。

---

找到想运行的工具后，接下来就可以进行任务的创建啦！

#### 2.1.1 方式一 在工具页面新建任务

在工具右上方直接点击`新建任务`，可以快速进入此工具的调用界面。

![image_5.png](https://levimg.s3.cn-northwest-1.amazonaws.com.cn/x/b57a17db-c6af-4095-8799-0d1f922bf5bf.png)

#### 2.1.2 方式二 收藏工具/编排后创建任务

在工具页面的右上方选择`收藏工具`将工具添加入收藏，加入收藏之后，可以在`我的收藏`里面查看到收藏的工具，并且新建任务。

![image_6.png](https://levimg.s3.cn-northwest-1.amazonaws.com.cn/x/37a60e4f-ba39-490d-aa75-25ca6511019b.png)

### 2.3 新建第一个任务

新建好后，将进入下述流程，让我们来看一看需要填写哪些信息，以完成任务的创建吧！

这里我们用wafw00f这个工具来举例，完成一个目标防火墙部署识别任务。

在创建任务时，第一步就是选择运行模式：

- 快速模式：无需代码知识，直接在网页输入参数即可一键启动检测。
- 高级模式：可以发挥100%的工具能力，高自由度，方便调试和进阶用户使用。

#### 2.3.1 快速模式调用

模式选择simple快速使用模式，调用方式保持默认，然后填入任务所需要的参数，提交即可。

简单的调用仅仅只需要填入基础的目标信息，就可以完成一次检测。

![image_7.png](https://levimg.s3.cn-northwest-1.amazonaws.com.cn/x/2d6c5bff-8d80-4b93-a18f-dc107b05080d.png)

可以看到任务正在执行中，等待任务执行完成即可查看结果。

![image_8.png](https://levimg.s3.cn-northwest-1.amazonaws.com.cn/x/103afcb5-1282-4e78-93ac-91a87e82ab9e.png)

#### 2.3.2 原生模式使用

现在我们学会了简单的调用，但是渗透测试实战情况瞬息万变，如何才能发挥工具的最强性能呢？

本章教给大家如何使用原生模式对工具进行高级调用。

这边我选择xsstrike对网站进行xss漏洞检测，因为一些网站常常需要携带cookie访问，因此需要使用原生模式加入cookie字段。

简单模式一样的，首先进入添加任务界面。

在添加任务界面，选择模式为raw原生模式，raw原生模式默认为高级调用，保持不变。

![image_9.png](https://levimg.s3.cn-northwest-1.amazonaws.com.cn/x/4ebf3832-7b5c-45c4-af0e-e783b9a98a9e.png)

工具命令和命令行使用是完全一致的，唯一不同的是cli参数是一个列表，我们唯一要做的就是将输入的命令用列表的方式传入cli参数。

具体工具有哪些参数和命令，可以移步此工具的官方文档。

例如，我这边想对一个URL进行xss探测，但这个url需要cookie访问。xsstrike指定url的flag为`-u`，指定http请求头的flag为`—-headers`，然后我们需要xsstrike在无需确认和交互模式下运行选项为`—-skip`，于是我们的命令行请求为：

```Bash
xsstrike -u "http://xxxx.app.mituan.zone/vulnerabilities/xss_r/?name=1#" --headers "Cookie: PHPSESSID=kpq9sje3nufjree77pcmosgia3; security=low" --skip
```


空格为元素间隔，转换成列表为下面所示：

```Bash
cli=["-u", "http://2325af11816b4bccb9caf11baac437ab.app.mituan.zone/vulnerabilities/xss_r/?name=1#", "--headers","Cookie: PHPSESSID=kpq9sje3nufjree77pcmosgia3; security=low","--skip"]

```


![image_10.png](https://levimg.s3.cn-northwest-1.amazonaws.com.cn/x/cd49c205-6e13-4b50-8c5f-39bbbe3f54ab.png)

之后提交即可。

### 2.4 查看结果

任务执行完毕之后，我们点击`运行结果`按钮查看任务的运行结果，这样就完成了一次简单的工具调用。

![image_11.png](https://levimg.s3.cn-northwest-1.amazonaws.com.cn/x/4d2d5b55-132a-4520-966f-40710c32e975.png)

### 2.5 调试

任务运行过程中有可能会产生一些预期之外的错误，大多数是输入参数引起的，例如目标无法访问，或者只是单纯地打错URL。那我们如何如何对进行调试呢？

![image_12.png](https://levimg.s3.cn-northwest-1.amazonaws.com.cn/x/c036186f-3f36-4808-8d48-c2fd68ace522.png)

这里可以看到任务正在运行，首先可以在日志按钮里查看任务日志，以查看任务运行情况。

![image_13.png](https://levimg.s3.cn-northwest-1.amazonaws.com.cn/x/54f69ed2-27a6-4a23-96bf-1f18aa04a97b.png)

如果日志并不能直接确定或者解决问题，可以选择调试按钮。

![image_14.png](https://levimg.s3.cn-northwest-1.amazonaws.com.cn/x/e5d315ac-9a61-48a4-8bca-cf4320e44af2.png)

平台会给出一条调试命令，我们将这条命令复制入环境的命令行进行执行，可以进入容器内部的调试命令行。

ipdb是集成了ipython的python代码命令行调试工具，具体使用可以移步ipdb的说明文档。

![image_15.png](https://levimg.s3.cn-northwest-1.amazonaws.com.cn/x/4d65446f-dfc3-4e73-860b-c3eeb323dcfc.png)
