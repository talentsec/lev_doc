# Leviathan Usage Documentation

Welcome to Leviathan, where you can easily invoke all the integrated security tools and workflows on the platform to quickly detect assets, or become a developer/contributor, contributing tools and workflows to other security personnel.

In this document, we mainly teach you how to use tools and workflows on Leviathan. If you are interested in uploading tools or workflows, please move to the [development document](https://lev.zone/docs/).

Different from the traditional online security tool integration platform, Leviathan achieves dual-end separation of task distribution and task operation in terms of structure. **①The invocation and run of tools；②the production and storage of data, all rely on the user's private local environment. It does not go through Leviathan cloud server, thus ensuring the data security and privacy security of every security personnel**. In addition, Leviathan supports one-click startup of tools and detection tasks, and detection task can be completed without downloading/deploying/updating tools or any code!

Next, let's take a look at how to use Leviathan!



## Overview

First of all, in order to run the tools and workflows on Leviathan, you need to complete the configuration and setup of the local environment. There are 3 steps in the local environment configuration, namely: creation of the preliminary environment, acquisition and upload of credentials, and startup of the environment. The specific usage of different operating system environments is different. This manual is divided into the installation and configuration of three operating system environments: Windows, Linux and Mac. Each environment has the following three steps:

- Prerequisite environment installation (Docker installation)
- Credential acquisition and upload
- Environment startup




## 1. Local environment configuration

### Windows

#### 	1.1  Preliminary environment (installing Docker)

​			Preliminary knowledge: [What is docker](https://www.redhat.com/zh/topics/containers/what-is-docker)

​			If you have already installed Docker on your device, please check its version number, the version number supported by Leviathan is not lower than **20.10.10**, otherwise please update, and you can skip this step.

​			It is troublesome to install docker on Windows system. You need to enable the built-in hyper-v function of Windows system first. It is recommended to use Linux system or WSL, which is convenient and fast. The following tutorials take Win10 as an example and are divided according to the Win10 system version (If you do not know the version of your Win10, please search it).


##### 			1.1.1 Enable CPU virtualization support

​			Enter the system BIOS settings and check whether the BIOS has enabled CPU virtualization support, as shown below:			

​			![Enable virtualization support.jpg](https://levimg.s3.cn-northwest-1.amazonaws.com.cn/x/2e12d9cd-cf65-4c6c-88a2-1b6d42110f22.JPEG)




##### 			1.1.2 Win10 Home Edition Preliminary Steps

​			It is strongly recommended to upgrade Win10 professional version or use Linux, Mac. **If it is Win10 professional version, enterprise version or education version, please skip this step and enter 1.1.3**.

​			First, you need to enable the hyper-v module. The Windows Home Edition cannot query the hyper-v function module. It needs to be enabled in a special way, as follows:



​				Update the operating system to the latest version: Windows Settings -> Updates -> Check for Updates

​				Enable the display of file extensions. For details, refer to [File Extension Display](https://jingyan.baidu.com/article/f7ff0bfcc9c0e12e26bb13a0.html)

​			

​				Copy the following code into the new document:

```vbscript
pushd "%~dp0"
dir /b %SystemRoot%\servicing\Packages\*Hyper-V*.mum >hyper-v.txt
for /f %%i in ('findstr /i . hyper-v.txt 2^>nul') do dism /online /norestart /add-package:"%SystemRoot%\servicing\Packages\%%i"
del hyper-v.txt
Dism /online /enable-feature /featurename:Microsoft-Hyper-V-All /LimitAccess /ALL 
```

​				Save the document as install-hyper-v.cmd, right click and run as administrator.

![安装hyper-v.png](https://levimg.s3.cn-northwest-1.amazonaws.com.cn/x/61620bb6-2a0d-4974-a27c-39471f7c0020.png)

​			

​			After the installation is complete, enter Y and restart. **(Note: If the system is updated after the installation is complete, it may cause the installation to be invalid, you need to check, and if necessary, you need to reinstall hyper-v)**
​			

​			Repeat the steps above, save the following code as change.cmd and run as administrator:

​			``` REG ADD "HKEY_LOCAL_MACHINE\software\Microsoft\Windows NT\CurrentVersion" /v EditionId /T REG_EXPAND_SZ /d Professional /F```

​			So far, Win10 home version hyper-v is enabled.



##### 			1.1.3  Win10 Professional Edition, Enterprise Edition, Education Edition

​			If it is the Professional Edition, Education Edition or Enterprise Edition, it doesn't need to be as troublesome as the Home Edition, just go to Control Panel -> Programs -> Enable or Disable Windows Features -> Check hyper-v, then follow the prompts to restart.

​			The following steps are common and must be performed on all Windows systems:

​			

​			Enable the display of file extensions. For details, refer to [Display File Extensions](https://jingyan.baidu.com/article/f7ff0bfcc9c0e12e26bb13a0.html)

​			Download the docker installation file [docker installer download](https://desktop.docker.com/win/main/amd64/Docker%20Desktop%20Installer.exe)

​			Click the file to start installing dockerc (The Home version needs to uncheck the Windows container WSL2 option during installation, other versions do not need it). 

​			Wait for the successful installation and then restart. After restarting, click the docker client icon on the desktop. Docker has a graphical interface under **Windows**:

​			If the Docker engine logo in the lower left corner of the graphical interface is green, it has been successfully started![image.png](https://levimg.s3.cn-northwest-1.amazonaws.com.cn/x/87fe783c-f999-4b50-85f9-99a0080d6561.png)

​			If it shows that docker fails to launch and reports the error **"Because a Hyper-V component is not running"**, hardware virtualization has not been launched, please perform the first step: **1.1.1 Enable CPU virtualization support**



#### 1.2 Credential upload and acquisition

​	1.	Open PowerShell and run as administrator:

​		![打开 powershell.png](https://levimg.s3.cn-northwest-1.amazonaws.com.cn/x/3d52434b-96e6-445c-9d0f-2d786f09cd4b.png)



​	Enter ssh, line feed, and check whether the system has the ssh command installed:			

![image.png](https://levimg.s3.cn-northwest-1.amazonaws.com.cn/x/3e4dd10e-3021-4647-a9e4-09877140b6f6.png)

​	If the content shown above does not appear, please refer to the following document to install the ssh command: Install ssh.
​	[安装ssh](https://docs.microsoft.com/zh-cn/windows-server/administration/openssh/openssh_install_firstuse)

​	Then enter the command:

​		``` cd ~```

​	2.	Enter the current user's home directory with the command above, and then enter the ``` ls .ssh```command to check whether the ssh key exists, namely the two files  "id_rsa、id_rsa.pub" :

![查看 ssh密钥.png](https://levimg.s3.cn-northwest-1.amazonaws.com.cn/x/2997d374-f873-439b-ba77-d567adae155e.png)

As shown in the figure above, the prompt that the path cannot be found in red means that the file does not exist. At this time, it needs to be created (**if it exists, skip the generation step directly**), enter the following command, and execute it in a new line:

​		```ssh-keygen.exe```

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

![密钥.jpg](https://levimg.s3.cn-northwest-1.amazonaws.com.cn/x/38c66c66-6a58-4609-800a-032a13f0c3c8.JPEG)


3. Enter the command `cat ~/.ssh/id_rsa.pub`, view the contents of the file, and copy the contents of the output. (PowerShell cannot be copied directly by right-clicking, it requires special operations, please search for the details) 
   If you really can't copy, you can also open the id_rsa.pub file through the file browser(the file path is `C Disk\User\(Current User’s Name)\.ssh\)`, copy the content.
  

4. Log in to Leviathan, in `ACCOUNT SETTINGS`-`PUBLIC KEY MANAGEMENT`, select Upload SSH public key, enter the name at will, and add the content of the file copied in the steps above to the public key. 

5. Under the path of the third step, the file path is `C Disk\User\(Current User’s Name)\.ssh\`, create `New text document.txt`, open it, then copy and add the following content, save it:

   ```yaml
   Host lev
       HostName service.lev.zone
       Port 2222
       User username # Leviathan username
   ```

   After saving, ** rename the file as `config` **, note that there is no txt suffix here, the type is file, not text document.

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
   ```ssh username@service.lev.zone -p 2222 （here the username is your Leviathan username）``` 

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

   If the download speed of the container is too slow due to network reasons, it can be accelerated by changing the Docker image source:

   ```Bash
   Open Docker, click Settings
   Click Docker Engine, the below will show
   ```

   ![image.png](https://levimg.s3.cn-northwest-1.amazonaws.com.cn/x/e46b4413-d432-4817-89c3-6abd73b645e8.png)
   
   Paste the following code:

   ```Bash
     "registry-mirrors": [
       "https://docker.mirrors.ustc.edu.cn/"
     ]
   ```

   The location for the paste is as shown in the figure below (Tips for beginners:`"buildkit": true` A half width comma needs to be added after the curly braces in the following line, and the code should be pasted in front of the curly braces in the bottom line, otherwise an error will occur).

   ![image.png](https://levimg.s3.cn-northwest-1.amazonaws.com.cn/x/575d3a79-7772-4318-a566-7293f809a4c5.png)
   
   Click Apply & Restart!

   After completing the acceleration operations above, execute the following command again to launch the local environment:


   ```bash
   $ ssh lev agent | docker run -v /var/run/docker.sock:/docker --rm -i talentsec/lev
   # Are you sure you want to continue connecting (yes/no/[fingerprint])? If the command shows,please enter Yes immediately.
   INFO:root:Agent is ready
   # Successful
   ```

   Finally, in [Account Settings - My Device], you can see that the device is online! 
   Go to the [next step](https://lev.zone/portal/get-started#2.-%E6%BD%AE%E6%B1%90%E5%AE%89%E5%85%A8%E5%B9%B3%E5%8F%B0)
​	

### Linux

#### 1.  Install docker

​		 Please use domestic sources, otherwise the speed will be very slow.

​		Ubuntu:	[Documentation](https://docs.docker.com/engine/install/ubuntu/)

​		CentOS:	[Documentation](https://docs.docker.com/engine/install/centos/)

​		Debian:	[Documentation](https://docs.docker.com/engine/install/debian/)
​		  

#### 2. Generate ssh key

Input the command: `ls -la ~/.ssh` to detect whether there is a ssh key in the system, if not, follow the tutorial to generate a key pair:	

```
$ ssh-keygen -t ecdsa -C "youremail@example.com"
# Replace the email with Leviathan's registered email
# When an input option appears, hit Enter
# It is not recommended to use the rsa encryption algorithm to generate a key pair because ecdsa is more secure, and the rsa key is disabled by default in the higher version of openssh
```

#### 3. Upload

Log in to Leviathan, in `Account settings` - `Public key management`, choose to upload the ssh¥ public key, input the name at will, and copy the content of the id_rsa.pub or id_ecdsa.pub public key file in the .ssh folder generated by the steps above to the public key. 

#### 4.Configure the ssh config file

`touch ~/.ssh/config`
 Write the following configuration:

```bash
Host lev
	HostName service.lev.zone
	Port 2222
	User username # Leviathan username
```

Then input the command ssh lev to verify if the configuration was successful:

```Bash
$ ssh lev
Connection to service.lev.zone closed. # Succeessful
```

If the following message is returned, there is a problem with the configuration file, please check the previous configuration steps:

```Bash
$ ssh lev
Permission denied (publickey). # If this message displays, the ssh public key is incorrectly configured. Please check whether the ssh public key and username are correct.
```

If the process above shows an error, you can also directly input the following command: 
```ssh username@service.lev.zone -p 2222 （Leviathan username）``` 

The following statement appears: 
```Are you sure you want to continue connecting (yes/no/[fingerprint])? ``` input yes immediately and press Enter. Do not wait too long, otherwise the startup may fail.

#### 5. Launch

First, add a device on the [Account Settings - My Device] page, enter a name, and click Submit.

Next, execute the following command to launch the local environment (if the pull speed is slow, it is recommended to modify the image source of docker to the University of Science and Technology of China):


```bash
$ ssh lev agent | docker run -v /var/run/docker.sock:/docker --rm -i talentsec/lev
# Are you sure you want to continue connecting (yes/no/[fingerprint])?  input yes immediately and press Enter.
INFO:root:Agent is ready
# Successful
```

After the command is executed, the image container will be automatically pulled and the container will be launched, and the whole process may last for a few minutes. When the containers are all launched, everything is ready!

Finally, in [Account Settings - My Device], you can see that the device is online!  
![image.png](https://levimg.s3.cn-northwest-1.amazonaws.com.cn/x/64c575b9-3ea3-494c-b25c-191835f2ba78.png)

Next, let's take a look at how to use Leviathan to run tools/workflows for asset security detection! Go to the [next step](https://lev.zone/portal/get-started#2.-%E6%BD%AE%E6%B1%90%E5%AE%89%E5%85%A8%E5%B9%B3%E5%8F%B0).



### Mac

#### Install docker

If you have already installed Docker on your device, please check its version, the version supported by Leviathan is not lower than **20.10.10** , otherwise you need to update it. Go to the download page of Docker's official website [https://docs.docker.com/get-docker/](https://docs.docker.com/get-docker/), and find the Docker that matches your computer's operating system to download and install. 
**Mac** has a graphical interface for Docker:

If the Docker engine logo in the lower left corner of the graphical interface is green, it has been successfully launched.
![image.png](https://levimg.s3.cn-northwest-1.amazonaws.com.cn/x/87fe783c-f999-4b50-85f9-99a0080d6561.png)

#### Generate ssh public key

Open the folder (Finder) under Mac, press the combination of `shift`+`command`+`G`, the following pop-up box will appear, input `~/.ssh` . Enter the .ssh directory, and check whether there are "id_rsa、id_rsa.pub" files.

![image.png](https://levimg.s3.cn-northwest-1.amazonaws.com.cn/x/e109ae0f-9bc1-4ea7-912f-b9641720f9fb.JPEG)

If not, enter the following command to generate:

```
Mac:
$ ssh-keygen -t ecdsa -C "youremail@example.com"
# Replace the email with Leviathan's registered email
# When an input option appears, hit Enter
# It is not recommended to use the rsa encryption algorithm to generate a key pair because ecdsa is more secure，and the rsa key is disabled by default in the higher version of openssh
```

#### Upload ssh public key

Log in to Leviathan, in `Account settings` - `Public key management`, choose to upload the ssh public key, input the name at will, and copy the content of the id_rsa.pub or id_ecdsa.pub public key file generated in the steps above to the public key.

```sh
# Mac: View .pub file，copy and upload to Leviathan(recommended)
$ cat ~/.ssh/id_ecdsa.pub 
or
$ cat ~/.ssh/id_rsa.pub 
```

#### Configure ssh config file

Firstly, you need to find the file directory where the ssh public key is stored:
Run the command:`touch ~/.ssh/config` to create a config file and write the following configuration:

```bash
Host lev
    HostName service.lev.zone
    Port 2222
    User username # Leviathan username
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

If the process above shows an error, you can also directly input the following command: 
 
```ssh username@service.lev.zone -p 2222 （Leviathan username）``` 

The following statement appears:
```Are you sure you want to continue connecting (yes/no/[fingerprint])? ``` input yes and press Enter immediately, do not wait too long, otherwise the startup may fail.


#### Launch

First, add a device on the [Account Settings - My Device] page, input a name, and click Submit.

Next, execute the following command to launch the local environment: (If the pull speed is slow, it is recommended to modify the image source of docker to the University of Science and Technology of China)


```bash
$ ssh lev agent | docker run -v /var/run/docker.sock:/docker --rm -i talentsec/lev
# The following statement appears: Are you sure you want to continue connecting (yes/no/[fingerprint])? input yes and press Enter immediately, do not wait too long, otherwise the startup may fail.

INFO:root:Agent is ready
# Successful
```

After the command is executed, the image container will be automatically pulled and the container will be launched, and the whole process may last for a few minutes. When the containers are all launched, everything is ready! Then go to the [next step](https://lev.zone/portal/get-started#2.-%E6%BD%AE%E6%B1%90%E5%AE%89%E5%85%A8%E5%B9%B3%E5%8F%B0)











## 2. Leviathan Cyber Security Platform

After logging in to Leviathan, you can see a total of 6 items in the directory bar.

![image.png](https://levimg.s3.cn-northwest-1.amazonaws.com.cn/x/18e05be5-ed18-4903-9b20-4c48d5c28b30.png)


- Tasks: Detection tasks I have run
- Favorites: My favorite tools/workflows/authors
- Repository: Tools/workflows I uploaded
- Marketplace: Aggregation of platform-wide tools/workflows
- Forum: A gathering place for tutorials and a place to communicate
- Document: Platform usage documents and development documents


### 2.1 Choose a tutorial

If this is your first time to Leviathan, you may not know where to start, or what tools should be experienced firstly, you can go to the forum page. According to the official penetration tutorial 1-4, you can easily complete a web penetration on Leviathan! 

![image_1.png](https://levimg.s3.cn-northwest-1.amazonaws.com.cn/x/0c5d1e0b-6024-41d6-a742-c6b4aa87e502.png)

### 2.2 Select tools

If you have a specific tool to use, you can skip the tutorial to find the relevant tools or workflows in the marketplace , and run the task directly.

![image_2.png](https://levimg.s3.cn-northwest-1.amazonaws.com.cn/x/dd7c358b-2b01-440e-852d-d49fdab60c27.png)

Find the tool you want to use and go to the tool page.

![image_3.png](https://levimg.s3.cn-northwest-1.amazonaws.com.cn/x/098fd6cb-5afb-4348-bffb-c951577b372f.png)

The tool page is divided into the following four modules:

![image_4.png](https://levimg.s3.cn-northwest-1.amazonaws.com.cn/x/7d1add24-d3d1-4f65-808f-a28fe95b9163.png)

- Basic Information:
  This module briefly describes the function and version of the tool. It mainly describes the use of the tool interface. For example, the direct mode of the tool only needs you to input the url parameter, of which the type is str string.

- Source Code File:
  Here you can directly view the invocation source code of the tool for learning and researching.

- Communication Panel:
  This is the part where you can share your tips and thoughts on using the tool.

- Running Log:
  You can view the invocation log of the tool.


---

You can create a task after you find the tool you want to run!

#### 2.1.1 Method 1: Create a new task on the tool page

Click directly on the upper right of the tool `Create new task` to quickly enter the invocation interface of the tool.

![image_5.png](https://levimg.s3.cn-northwest-1.amazonaws.com.cn/x/b57a17db-c6af-4095-8799-0d1f922bf5bf.png)

#### 2.1.2 Method 2: Collect tools/workflows and create tasks

On the top right of the tool page, select Collect tool, add tool to `Favorites`. After that, you can view favorite tools and create new tasks in `My Favorites`.

![image_6.png](https://levimg.s3.cn-northwest-1.amazonaws.com.cn/x/37a60e4f-ba39-490d-aa75-25ca6511019b.png)


### 2.3 Create the first task

Let's take a look at what information is needed to be filled in to complete the creation of the task!

Here we take the wafw00f as an example to complete a target firewall deployment identification task.

When creating a task, the first step is to select the run mode:


- Quick mode: No code knowledge is required, and the detection can be enabled with one click by directly entering parameters on the web page.
- Customized mode: 100% of the tool capabilities can be exerted, with a high degree of freedom, which is convenient for debugging and use for advanced users.


#### 2.3.1 Quick Mode 

Firstly, select the Quick mode, make the invocation method remain default, and then fill in the parameters required by the task and submit it.

In this mode, one detection demands only some basic taget information.

![image_7.png](https://levimg.s3.cn-northwest-1.amazonaws.com.cn/x/2d6c5bff-8d80-4b93-a18f-dc107b05080d.png)

You can see then that the task is being executed, and you can view the results after the task has been done.

![image_8.png](https://levimg.s3.cn-northwest-1.amazonaws.com.cn/x/103afcb5-1282-4e78-93ac-91a87e82ab9e.png)

#### 2.3.2 Customized Mode 

Now we have learned the Quick Mode Invocation, but the actual situation of penetration testing changes rapidly. How can we realize the full performance of the tool?

In this chapter，we will learn how to conduct an advanced invocation of tools.

Here we choose xsstrike to detect the xss vulnerability of the website. Because some websites often need to carry cookies for access, it is necessary to use the raw mode to add the cookie field.

At first, enter the `Add task` interface.

In this interface, choose Raw Mode, and make it as default advanced invocationthe.


![image_9.png](https://levimg.s3.cn-northwest-1.amazonaws.com.cn/x/4ebf3832-7b5c-45c4-af0e-e783b9a98a9e.png)

The use of the tool command and of the command line is exactly the same, the only difference is that the cli parameter is a list. The only thing we have to do is to upload the input command in the form of a list into the cli parameter.

If you want to learn about the parameters and commands of one specific tool, you can move to the official document of this tool.

For example, we want to conduct xss detection on a URL, but this URL requires cookie access. Xsstrike specifies the flag of the url as `-u` and the flag of the http request header as`—-headers`. We need xsstrike to run without confirmation and in the interactive mode with option —-`—-skip`


```Bash
xsstrike -u "http://xxxx.app.mituan.zone/vulnerabilities/xss_r/?name=1#" --headers "Cookie: PHPSESSID=kpq9sje3nufjree77pcmosgia3; security=low" --skip
```


As element interval, spaces can be converted to a list as shown below:

```Bash
cli=["-u", "http://2325af11816b4bccb9caf11baac437ab.app.mituan.zone/vulnerabilities/xss_r/?name=1#", "--headers","Cookie: PHPSESSID=kpq9sje3nufjree77pcmosgia3; security=low","--skip"]

```


![image_10.png](https://levimg.s3.cn-northwest-1.amazonaws.com.cn/x/cd49c205-6e13-4b50-8c5f-39bbbe3f54ab.png)

Then submit it.

### 2.4 View Results

After the execution of the task, click `Operation result` to view the running result of the task, then a simple tool invocation has been completed.

![image_11.png](https://levimg.s3.cn-northwest-1.amazonaws.com.cn/x/4d2d5b55-132a-4520-966f-40710c32e975.png)

### 2.5 Debug

Unexpected errors may occur during the task execution, most of which are caused by input parameters, such as the target being inaccessible, or simply typing the wrong URL. So how do we debug it?

![image_12.png](https://levimg.s3.cn-northwest-1.amazonaws.com.cn/x/c036186f-3f36-4808-8d48-c2fd68ace522.png)

Here you can see that the task is running. Firstly, you can view the task log by clicking the log button to view the running status of the task.

![image_13.png](https://levimg.s3.cn-northwest-1.amazonaws.com.cn/x/54f69ed2-27a6-4a23-96bf-1f18aa04a97b.png)

If the log cannot be used to directly identify or solve the problem, select the Debug button.

![image_14.png](https://levimg.s3.cn-northwest-1.amazonaws.com.cn/x/e5d315ac-9a61-48a4-8bca-cf4320e44af2.png)

The platform will give a debug command. We copy this command into the command line of the environment for execution, and we can enter the debug command line inside the container.

Ipdb is a python code command-line debugging tool that integrates ipython. For specific use, you can move to the Documentation of ipdb.

![image_15.png](https://levimg.s3.cn-northwest-1.amazonaws.com.cn/x/4d65446f-dfc3-4e73-860b-c3eeb323dcfc.png)
