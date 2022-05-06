# Leviathan Usage Guide

Welcome to Leviathan, where you can easily call all the security tools and workflows published to quickly detect assets, or become a developer, contributing tools and workflows to other security personnel.

In this document, we mainly teach you how to use tools and workflows on Leviathan. If you are interested in uploading tools or workflows, please move to the [development document](https://lev.zone/docs/).

Different from the traditional online security tool integration platform, Leviathan achieves the separation of the distribution and the run of the task architecturally. **①The invocation and run of tools；②the production and storage of data, all rely on the user's private local environment. It does not go through Leviathan cloud server, thus ensuring the data security and privacy security of every security personnel**. In addition, Leviathan can enable the tools and detection tasks with one click, and without the download/deployment/update of the tools or any code!

Next, let's take a look at how to use Leviathan!



## Overview

First of all, in order to run the tools and workflows normally on Leviathan, you need to complete the configuration and setup of the local environment. There are 3 steps, namely: preliminary environment installation, credential uploading and acquisition, and startup of the environment. The specific usage of different operating system environments is different. This manual is divided into the installation and configuration of three operating system environments: Windows, Linux and Mac. And they all follow the following three steps:

- Preliminary environment installation (Install Docker)
- Credential uploading and acquisition
- Environment startup




## 1. Local environment configuration

### Windows

#### 	1.1  Preliminary Environment Installation (Install Docker)

Pre-knowledge: [what is docker](https://www.redhat.com/zh/topics/containers/what-is-docker)

 If Docker has been installed on your device, please check its version number. Leviathan supports 20.10.10 and above. If the condition is met, you can skip this step, otherwise, please install or update Docker as follows.
 
 Docker installation on Windows is complicated. Firstly, you need to enable the built-in 'hyper-v' function on Windows (therefore, it is recommended to use Linux system or WSL instead of Windows). The following tutorial takes Win10 as an example, and explains differences when using differnet Win10 system Edition (If you don't know your system Edition, feel free to google it).



##### 1.1.1 Enable CPU virtualization support

 Enter the BIOS settings in system and check whether the BIOS has enabled CPU virtualization support as below.

 ![Enable virtualization support.jpg](https://levimg.s3.cn-northwest-1.amazonaws.com.cn/x/2e12d9cd-cf65-4c6c-88a2-1b6d42110f22.JPEG)



##### 1.1.2 Win10 Home Edition Pre-step

It is strongly recommended to upgrade to Win10 Professional version or use Linux, Mac. If you use Win10 Professional Edition, Enterprise Edition or Education Edition, please skip this step and enter 1.1.3.

First, you need to enable the hyper-v module. The Windows Home Edition cannot query the 'hyper-v' function module. It needs to be enabled in a special way, as follows:

Update the operating system to the latest version, Windows Settings -> Update -> Check for updates

Enable the display of file extensions. For details, [refer to File Extension Display](https://jingyan.baidu.com/article/f7ff0bfcc9c0e12e26bb13a0.html)

Copy the following code into a new document:


```
pushd "%~dp0"
dir /b %SystemRoot%\servicing\Packages\*Hyper-V*.mum >hyper-v.txt
for /f %%i in ('findstr /i . hyper-v.txt 2^>nul') do dism /online /norestart /add-package:"%SystemRoot%\servicing\Packages\%%i"
del hyper-v.txt
Dism /online /enable-feature /featurename:Microsoft-Hyper-V-All /LimitAccess /ALL
```

 Save the document as install-hyper-v.cmd, right click and run as administrator.

![install hyper-v.png](https://levimg.s3.cn-northwest-1.amazonaws.com.cn/x/575279f5-e410-4348-963b-349bdeb858a6.JPEG)

 After the installation is completed, enter Y and restart. (Note: **If the system is updated after the installation has been completed, it may cause the installation to be invalid, you need to check, and if necessary, you need to reinstall hyper-v**)


 Repeat the above steps, save the following code as change.cmd and run as administrator.

 `REG ADD "HKEY_LOCAL_MACHINE\software\Microsoft\Windows NT\CurrentVersion" /v EditionId /T REG_EXPAND_SZ /d Professional /F`

 Now, the Win10 Home Ediiton hyper-v should be enabled.



##### 1.1.3 Win10 Professional Edition, Enterprise Edition, Education Edition

 If you use Win10 Professional Edition, Education Edition, or Enterprise Edition, it won't be as complicated as the Home Edition. Go to Control Panel -> Programs -> Enable or Disable Windows functions -> Check hyper-v, and then follow the prompts to restart the system.

 The following steps should and must be performed on all Windows systems.


 Enable the display of file extensions. For details, refer to [File Extension Display](https://jingyan.baidu.com/article/f7ff0bfcc9c0e12e26bb13a0.html)

 Download the docker installation file. [docker installer download](https://desktop.docker.com/win/main/amd64/Docker Desktop Installer.exe?utm_source=docker&utm_medium=webreferral&utm_campaign=dd-smartbutton&utm_location=header)

 Click the file to install docker (the Home Edition requires that Windows Container WSL2 should be unchecked during the installation; This step is not needed in other Win Editions).

 Wait for the installation to restart successfully. After it, open Docker by clicking the Desktop icon. Docker has a graphical interface under **Windows**.

 When the Docker Logo on the lower left corner is green, it means you have successfully launched Docker![image.png](https://levimg.s3.cn-northwest-1.amazonaws.com.cn/x/87fe783c-f999-4b50-85f9-99a0080d6561.png)

 If it displays that docker fails to launch and reports the error **"Hyper-V component is not running"**, it means that hardware virtualization has not been started. Please perform the first step: **1.1.1 Enable CPU virtualization support**.


#### 1.2 Credential uploading and acquisition

​	1.	Open PowerShell and run as administrator:

​		![open powershell.png](https://levimg.s3.cn-northwest-1.amazonaws.com.cn/x/ae0c173f-92ee-455f-88bf-87ece5ca95bb.JPEG)

​	Enter ssh, line feed, and check whether the system has the ssh command installed:			

![image.png](https://levimg.s3.cn-northwest-1.amazonaws.com.cn/x/3e4dd10e-3021-4647-a9e4-09877140b6f6.png)

​	If the content shown above does not appear, please refer to the following document to install the ssh command: Install ssh.
​	[Install ssh](https://docs.microsoft.com/zh-cn/windows-server/administration/openssh/openssh_install_firstuse)

​	Then enter the command:

​		``` cd ~```

​	2.	Enter the current user's home directory with the command above, and then enter the ``` ls .ssh```command to check whether the ssh key exists, namely the two files  "id_ed25519" 、"id_ed25519.pub" :

![Check ssh key.png](https://levimg.s3.cn-northwest-1.amazonaws.com.cn/x/2997d374-f873-439b-ba77-d567adae155e.png)

As shown in the picture above, the prompt that the path cannot be found in red means that the file does not exist. At this time, it needs to be created (**if it exists, skip the generation step directly**), enter the following command, and execute it in a new line:

​		```ssh-keygen.exe -t ed25519```

You will be prompted to input, please ignore it, just press the Enter key all the way to the end, until the following prompt appears(Notes: It is not recommended to use the default rsa encryption method to generate keys, and rsa keys are disabled by default in higher versions of openssh):

```vbscript
+---[RSA 2048]----+
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

​	After that, you enter  `ls .ssh` again, the two files mentioned above will be displayed:

![ssh key.jpg](https://levimg.s3.cn-northwest-1.amazonaws.com.cn/x/38c66c66-6a58-4609-800a-032a13f0c3c8.JPEG)


3. Enter the command`cat ~/.ssh/id_ed25519.pub` to view the contents of the file, and copy the it. (It cannot be copied directly by right-click in PowerShell, and the copy requires special operations, please feel free to google it.) 
   If you really can't copy, you can also open the id_ed25519.pub file through the file browser(the file path is `C Disk\User\(Current User’s Name)\.ssh\)`, then copy it.
  

4. Log in to Leviathan, in `Account settings` - `Public key management`, choose to upload the ssh public key, enter the name at will, and add the content of the file copied in the steps above to the public key. 

5. Under the path of the third step, the file path is `C Disk\User\(Current User’s Name)\.ssh\`, create `New text document.txt`, open it, then copy and add the following content, save it:

   ```yaml
   Host lev
       HostName service.lev.zone
       Port 2222
       User username # Leviathan username，not email!
   ```

   After that, **rename the file as `config`**, note that there is no txt suffix here, the type is file, not text document.

6. Enter the test command `ssh lev` in PowerShell (the $ sign is meaningless, don't enter it):

   ```vbscript
   $ ssh lev
   Connection to service.lev.zone closed. # successful
   ```

   If the following message is returned, there is a problem with the configuration file, please check the previous configuration steps:

   ```Bash
   $ ssh lev
   Permission denied (publickey). # This message indicates that the ssh public key is incorrectly configured, please check whether the ssh public key and username are correct.
   
   ```

   If the process above shows an error, you can also directly enter the following command line:  
   ```ssh username@service.lev.zone -p 2222 （the username is your Leviathan username，not email!）``` 

   The following statement appears:
   ```Are you sure you want to continue connecting (yes/no/[fingerprint])? ``` enter yes immediately and press Enter. Please do not wait too long, otherwise the startup may fail.

7. First, add a device on the **[Account Settings-My Device]** page of  Leviathan, enter a name, and click Submit.

8. Next, execute the following command to launch the local environment:

   ```bash
   $ ssh lev agent | docker run -v /var/run/docker.sock:/docker --rm -i talentsec/lev
   # The following statement appears: Are you sure you want to continue connecting (yes/no/[fingerprint])? enter yes immediately and press Enter. Please do not wait too long, otherwise the startup may fail.
   INFO:root:Agent is ready
   # Successful
   ```

   After the command is executed, the image container will be automatically pulled and launched, and the whole process may last for a few minutes. When the containers are all launched, everything is ready!

   If the download speed of the container is too slow due to network, it can be accelerated by changing the Docker image source:

   ```Bash
   Open Docker, click Settings
   Click Docker Engine, view the below
   ```

   ![image.png](https://levimg.s3.cn-northwest-1.amazonaws.com.cn/x/e46b4413-d432-4817-89c3-6abd73b645e8.png)
   
   Paste the following code:

   ```Bash
     "registry-mirrors": [
       "https://docker.mirrors.ustc.edu.cn/"
     ]
   ```

   The location for the paste is as shown in the picture below (Tips for beginners:`"buildkit": true` A half width comma needs to be added after the curly braces in the following line, and the code should be pasted in front of the curly braces in the bottom line, otherwise an error will occur).

   ![image.png](https://levimg.s3.cn-northwest-1.amazonaws.com.cn/x/575d3a79-7772-4318-a566-7293f809a4c5.png)
   
   Click Apply & Restart!

   After completing the acceleration operations above, execute the following command again to launch the local environment:


   ```bash
   $ ssh lev agent | docker run -v /var/run/docker.sock:/docker --rm -i talentsec/lev
   # The following statement appears: Are you sure you want to continue connecting (yes/no/[fingerprint])? enter yes immediately and press Enter. Please do not wait too long, otherwise the startup may fail.
   INFO:root:Agent is ready
   # Successful
   ```

   Finally, in [Account Settings - My Device], you can see that the device is online! 
   Go to the [next step](https://lev.zone/portal/get-started#2.-%E6%BD%AE%E6%B1%90%E5%AE%89%E5%85%A8%E5%B9%B3%E5%8F%B0)
​	

### Linux

#### 1.  Install docker

​		 Please use Chinese sources, otherwise the speed will be very slow.

​		Ubuntu:	[reference documentation](https://docs.docker.com/engine/install/ubuntu/)

​		CentOS:	[reference documentation](https://docs.docker.com/engine/install/centos/)

​		Debian:	[reference documentation](https://docs.docker.com/engine/install/debian/)
​		  

#### 2. Generate ssh key

Enter the command: `ls -la ~/.ssh` to detect whether there is a ssh key in the system, if not, follow the tutorial to generate a key pair:	

```
$ ssh-keygen -t ecdsa -C "youremail@example.com"
# Replace the email with Leviathan's registered email
# When an Input option appears, click Enter
# It is not recommended to use the rsa encryption algorithm to generate a key pair because ecdsa is more secure, and the rsa key is disabled by default in the higher version of openssh
```

#### 3. Upload

Log in to Leviathan, in `Account settings` - `Public key management`, choose to upload the ssh public key, enter the name at will, and copy the content of the id_ed25519.pub public key file in the .ssh folder generated in the steps above to the public key. 


#### 4.Configure the ssh config file

`touch ~/.ssh/config`
 Write as follows:

```bash
Host lev
	HostName service.lev.zone
	Port 2222
	User username # Leviathan username
```

Then enter the command `ssh lev` to verify if the configuration was successful:

```Bash
$ ssh lev
Connection to service.lev.zone closed. # Succeessful
```

If the following message is returned, there is a problem with the configuration file, please check the previous configuration steps:

```Bash
$ ssh lev
Permission denied (publickey). # If this message displays, the ssh public key is incorrectly configured. Please check whether the ssh public key and username are correct.
```

If the process above shows an error, you can also directly enter the following command: 
```ssh username@service.lev.zone -p 2222 （Leviathan username）``` 

The following statement appears: 
```Are you sure you want to continue connecting (yes/no/[fingerprint])? ``` enter yes immediately and press Enter. Do not wait too long, otherwise the startup may fail.

#### 5. Launch

First, add a device on the [Account Settings - My Device] page, enter a name, and click Submit.

Next, execute the following command to launch the local environment (if the pull speed is slow, it is recommended to modify the image source of docker to that of the University of Science and Technology of China):


```bash
$ ssh lev agent | docker run -v /var/run/docker.sock:/docker --rm -i talentsec/lev
# The following statement appears: Are you sure you want to continue connecting (yes/no/[fingerprint])? enter yes immediately and press Enter. Please do not wait too long, otherwise the startup may fail.
INFO:root:Agent is ready
# Successful
```

After the command is executed, the image container will be automatically pulled and launched, and the whole process may last for a few minutes. When the containers are all launched, everything is ready!

Finally, in [Account Settings - My Device], you can see that the device is online!  
![image.png](https://levimg.s3.cn-northwest-1.amazonaws.com.cn/x/64c575b9-3ea3-494c-b25c-191835f2ba78.png)

Next, let's take a look at how to use Leviathan to run tools/workflows for asset security detection! Go to the [next step](https://lev.zone/portal/get-started#2.-%E6%BD%AE%E6%B1%90%E5%AE%89%E5%85%A8%E5%B9%B3%E5%8F%B0).



### Mac

#### Install docker

If Docker has been installed on your device, please check its version number. Leviathan supports 20.10.10 and above. If the condition is met, you can skip this step, otherwise, please install or update Docker as follows. 

Download the [docker installation file](https://docs.docker.com/get-docker/)

Click the file to launch docker. Open Docker by clicking the Desktop icon. Docker has a graphical interface under **Mac**.
When the Docker Logo on the lower left corner is green, it means you have successfully launched Docker.![image.png](https://levimg.s3.cn-northwest-1.amazonaws.com.cn/x/87fe783c-f999-4b50-85f9-99a0080d6561.png)

#### Generate ssh public key

Open the folder (Finder) under Mac, press the combination of `shift`+`command`+`G`, the following pop-up Windows will appear, enter `~/.ssh` . Enter the .ssh directory, and check whether there are "id_ed25519"、"id_ed25519.pub" files.

![image.png](https://levimg.s3.cn-northwest-1.amazonaws.com.cn/x/e109ae0f-9bc1-4ea7-912f-b9641720f9fb.JPEG)

If not, enter the following command:

```
Mac:
$ ssh-keygen -t ed25519 -C "youremail@example.com"
# Replace the email with Leviathan's registered email
# When an Input option appears, click Enter
# It is not recommended to use the rsa encryption algorithm to generate a key pair because ed25519 is more secure，and the rsa key is disabled by default in the higher version of openssh
```

#### Upload ssh public key

Log in to Leviathan, in `Account settings` - `Public key management`, choose to upload the ssh public key, enter the name at will, and copy the content of the id_ed25519.pub or id_ed25519.pub public key file generated in the steps above to the public key.

```sh
# Mac: View .pub file，copy and upload to Leviathan(recommended)
$ cat ~/.ssh/id_ed25519.pub
```

#### Configure ssh config file

First, you need to find the file directory where the ssh public key is stored:
Run the command:`touch ~/.ssh/config` to create a config file and write as follows:

```bash
Host lev
    HostName service.lev.zone
    Port 2222
    User username # Leviathan username, not email!
```

Use the following command to verify whether the configuration is successful.

```Bash
$ ssh lev
Connection to service.lev.zone closed. # Successful
```


If the following message is returned, there is a problem with the configuration file, please check the previous configuration steps:

```Bash
$ ssh lev
Permission denied (publickey). # This message indicates that the ssh public key is incorrectly configured, please check if the ssh public key and username are correct.

```

If the process above shows an error, you can also directly enter the following command: 
 
```ssh username@service.lev.zone -p 2222 （Leviathan username）``` 

The following statement appears:
```Are you sure you want to continue connecting (yes/no/[fingerprint])? ``` enter yes and press Enter immediately, do not wait too long, otherwise the startup may fail.


#### Launch

First, add a device on the [Account Settings - My Device] page, enter a name, and click Submit.

Next, execute the following command to launch the local environment(If the pull speed is slow, it is recommended to modify the image source of docker to that of the University of Science and Technology of China，feel free to google it): 


```bash
$ ssh lev agent | docker run -v /var/run/docker.sock:/docker --rm -i talentsec/lev
# The following statement appears: Are you sure you want to continue connecting (yes/no/[fingerprint])? enter yes and press Enter immediately, do not wait too long, otherwise the startup may fail.
INFO:root:Agent is ready
# Successful
```

After the command is executed, the image container will be automatically pulled and launched, and the whole process may last for a few minutes. When the containers are all launched, everything is ready! Then go to the [next step](https://lev.zone/portal/get-started#2.-%E6%BD%AE%E6%B1%90%E5%AE%89%E5%85%A8%E5%B9%B3%E5%8F%B0)











## 2. Leviathan Cyber Security Platform

Log in to Leviathan, you can then see a total of 6 items in the directory bar.

![image.png](https://levimg.s3.cn-northwest-1.amazonaws.com.cn/x/18e05be5-ed18-4903-9b20-4c48d5c28b30.png)


- Task: Detection tasks I have run
- Collection: My favorite tools/workflows/authors
- Repository: Tools/workflows I have uploaded
- Market: Tools/workflows published publicly
- Forum: A place to communicate
- Document: Usage documents and development documents


### 2.1 Choose a tutorial

If this is your first time to Leviathan, you may not know where to start, or what tools should be experienced firstly, you can go to the Forum. According to the official penetration tutorial 1-4, you can easily complete a web penetration test on Leviathan! 

![image_1.png](https://levimg.s3.cn-northwest-1.amazonaws.com.cn/x/0c5d1e0b-6024-41d6-a742-c6b4aa87e502.png)

### 2.2 Select tools

If you have a specific tool to use, you can find the relevant tools or workflows in the Market, and run the task directly.

![image_2.png](https://levimg.s3.cn-northwest-1.amazonaws.com.cn/x/dd7c358b-2b01-440e-852d-d49fdab60c27.png)

Find the tool you want to use and go to the tool detail page.

![image_3.png](https://levimg.s3.cn-northwest-1.amazonaws.com.cn/x/098fd6cb-5afb-4348-bffb-c951577b372f.png)

The tool page is divided into the following four modules:

![image_4.png](https://levimg.s3.cn-northwest-1.amazonaws.com.cn/x/7d1add24-d3d1-4f65-808f-a28fe95b9163.png)

- Basic Information:
  It briefly describes the function and version of the tool. It mainly describes how to use the tool.For example, to run the tool in Quick Mode, you only need to enter the url parameter, which is a str string.

- Source Code File:
  Here you can directly view the invocation source code of the tool for learning and researching.

- Communication Panel:
  This is the part where you can share your tips and thoughts on using the tool.

- Running Log:
  You can view the invocation log of the tool.


---

You can create a task after you find the tool you want to run!

#### 2.2.1 Method 1: Create a new task on the tool page

Click directly `Create new task` on the upper right of the tool page to quickly enter the invocation interface of the tool.

![image_5.png](https://levimg.s3.cn-northwest-1.amazonaws.com.cn/x/b57a17db-c6af-4095-8799-0d1f922bf5bf.png)

#### 2.2.2 Method 2: Collect tools/workflows and create tasks

On the upper right of the tool page, select Collect, add tool to `Collection`. After that, you can view the tool and create new tasks in `Collection` page.

![image_6.png](https://levimg.s3.cn-northwest-1.amazonaws.com.cn/x/37a60e4f-ba39-490d-aa75-25ca6511019b.png)


### 2.3 Create the first task

Let's take a look at what information is needed to be filled in to complete the creation of the task!

When creating a task, the first step is to choose the run mode:

- Quick Mode: No code knowledge is required, and the detection can be enabled with one click by directly entering parameters on the web page.
- Customized Mode: 100% of the tool capabilities can be exerted, with a high degree of freedom, which is convenient for debugging and use for advanced users.


#### 2.3.1 Quick Mode 

First, choose the simple mode, make the Method of invocation remain default(that is, Quick Mode), then fill in the parameters required by the task and submit it.

In this mode, one detection demands only some basic taget information.

![image_7.png](https://levimg.s3.cn-northwest-1.amazonaws.com.cn/x/0bd2ea8b-9514-4b55-a7e6-5127ce9d3c45.JPEG)

You can see then that the task is being executed, and you can view the results after the task has been done.

![image_8.png](https://levimg.s3.cn-northwest-1.amazonaws.com.cn/x/0c5e9676-834b-4ced-a74c-acdd6599372a.JPEG)

#### 2.3.2 Customized Mode 

Now we have learned the Quick Mode Invocation, but the actual situation of penetration testing changes rapidly. How can we realize the full performance of the tool?

In this chapter，we will learn how to conduct a customized invocation of tools.

Here we choose xsstrike to detect the xss vulnerabilities of the website. Because some websites often need to carry cookies for access, it is necessary to use the raw mode to add the cookie field.

At first, enter the `New task` page.

On this page, choose Raw Mode, and make the Method of invocation remain default(that is, Customized Mode).


![image_9.png](https://levimg.s3.cn-northwest-1.amazonaws.com.cn/x/be280f8d-1a0c-4bf5-bf02-fe4ee5a725c9.JPEG)

The use of the tool command and of the command line is exactly the same, the only difference is that the cli parameter is a list. The only thing we have to do is to upload the input command in the form of a list into the cli parameter.

If you want to learn about the parameters and commands of one specific tool, you can move to the official detail document of this tool.

For example, we want to conduct xss detection on a URL, but this URL requires cookie access. Xsstrike specifies the flag of the url as `-u` and the flag of the http request header as`—-headers`. Then we need xsstrike to run in no confirmation and interactive mode as `--skip`, so our command line request is as follows:


```Bash
xsstrike -u "http://xxxx.app.mituan.zone/vulnerabilities/xss_r/?name=1#" --headers "Cookie: PHPSESSID=kpq9sje3nufjree77pcmosgia3; security=low" --skip
```


As element interval, spaces can be converted to a list as shown below:

```Bash
cli=["-u", "http://2325af11816b4bccb9caf11baac437ab.app.mituan.zone/vulnerabilities/xss_r/?name=1#", "--headers","Cookie: PHPSESSID=kpq9sje3nufjree77pcmosgia3; security=low","--skip"]

```

![image_10.png](https://levimg.s3.cn-northwest-1.amazonaws.com.cn/x/bdb7143f-2a95-4735-b867-89763e615798.JPEG)

Then submit it.

### 2.4 View Results

After the execution of the task, click `Run result` to view the running result of the task, then a simple tool invocation has been completed.

![image_11.png](https://levimg.s3.cn-northwest-1.amazonaws.com.cn/x/2eb75d26-4fd7-4073-b538-f161fdc7a3be.JPEG)

### 2.5 Debug

Unexpected errors may occur during the task execution, most of which are caused by input parameters, such as the target inaccessible, or simply the wrong URL. So how do we debug it?

![debug](https://levimg.s3.cn-northwest-1.amazonaws.com.cn/x/efc3a8d5-6d97-4b3a-b2b7-803e94136411.JPEG)

Here you can see that the task is running. First, you can view the task log by clicking the Log button to view the running status of the task.

If the log cannot be used to directly identify or solve the problem, select the Debug button.

![debug_en](https://levimg.s3.cn-northwest-1.amazonaws.com.cn/x/efc3a8d5-6d97-4b3a-b2b7-803e94136411.JPEG)

The platform will give a debug command. We copy it into the command line of the environment for execution, and we can enter the debug command line inside the container.

Ipdb is a python code command-line debugging tool that integrates ipython. For specific use, you can move to the Documentation of ipdb.

![image_15.png](https://levimg.s3.cn-northwest-1.amazonaws.com.cn/x/4d65446f-dfc3-4e73-860b-c3eeb323dcfc.png)
