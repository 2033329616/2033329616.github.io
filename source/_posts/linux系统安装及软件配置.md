---
title: linux系统安装及软件配置 
tags: [ubuntu系统, sublime配置]
categories:
- ubuntu系统
comments: true
sitemap: false
grammar_cjkRuby: true
---
2017-11-29

###  **1.安装ubunut系统时一直卡在图标的位置无法进入安装界面**
将系统bios里security boot设置为disable，重启后再重新进入安装状态；
 如果仍然无法进入安装状态：在选择安装和试用的界面，将光标移动到ubunt上，按 ‘e’，系统进入一个设置界面，在`quiet splash`后面加 `nomodeset`(别忘了空格)，然后按F10后系统以不设置显示模式的状态可以进入安装界面，但此时分辨率较低，尤其在选择分区后无法直接点击“安装”按钮，而且无法点击菜单栏拖动，此时使用**alt + 鼠标左键**可以拖动窗口，然后按步骤安装系统即可。
 
 ### **2.电脑安装ubuntu16.04后重启一直黑屏无法进入系统**
 可能和电脑的intel核显卡和nvidia集成显卡的驱动有关，此时再以上面的nomodeset模式进入系统，使用指令`sudo apt-get install nvidia-current`，然后重启可以成功进入系统。
       如果没有进入系统又卡在黑屏界面，则使用另一种方法：
      1使用nomodeset模式进入系统，先安装Intel的核心显卡驱动，去官网下载update-tool工具，这是我下载的版本`intel-graphics-update-tool_2.0.2_amd64.deb`，然后安装即可。
    ` sudo dpkg -i intel-graphics-update-tool_2.0.2_amd64.deb `
    ` sudo apt-get -f install`
    ` sudo apt-get update`
安装完成后使用`glxinfo | grep rendering`可以查看安装的结果，显示yes表示安装成功
      2安装nvidia的显卡驱动
     ` lspci | grep -i nvidia`查看电脑是否支持英伟达显卡，安装官网安装驱动
      a关闭图像界面和禁用nouveau
         进入字符界面`ctrl + alt + f1`后登录，`sudo serivce lightdm stop`关闭图像界面服务，禁用nouveau第三方驱动(步骤略)，`lsmod | nouveau`无输出则禁用成功
      b 安装nvidia的deb文件即可
     安装完成后使用`cat /proc/driver/nvidia/version`查看驱动版本，使用`nvidia-smi`也可以查看驱动及显卡的硬件信息
 所有的驱动安装完成后，重启进入系统，如果还没有进入系统，则按上述的办法重安nvidia驱动，使用`sudo apt-get remove nvidia`和`sudo /usr/bin/nvidia-uninstall`将驱动卸载干净再重新安装。
 ### **3.配置sublime + python3的运行环境**
 #### 3.1 首先在ubuntu16.04下安装sublime text3
 > sudo add-apt-repository ppa:webupd8team/sublime-text-3 添加apt的安装包来源
 > sudo apt-get update 更新软件源
 > sudo apt-get install sublime-text-installer 安装sublime text
 
 安装完成后输入`subl 文件名`，如果该⽂件存在就会以sublime text打开， 否则就创建该⽂件， 默认的有代码
提⽰和⾼亮功能， 选择运⾏的环境后， Ctrl + B 可以编译运⾏编写的代码

PPA 全称为 Personal Package Archives（个⼈软件包档案） ,通常 PPA 源⾥的软件是官⽅源⾥没有的， 或
者是最新版本的软件。 相对于通过 Deb 包安装来说， 使⽤ PPA 的好处是， ⼀旦软件有更新， 通过` sudo
apt-get upgrade` 这样命令就可以直接升级到新版本。
在 Ubuntu Karmic (9.10) 我们可以使⽤ `add-apt-repository` 脚本添加 ppa 到当前的库中并且⾃动导⼊
公钥。 在终端下使⽤语法： `add-apt-repository ppa:<ppa_name>`
 
 #### 3.2 配置python3环境
 A   新建运行环境使用Tools==>Build System==>New Build Systems生成一个配置文件，我们可以改   名字为python3.sublime-build，python3就成为了build system中的一个选项了，在该文件中写入：
 ```bash
{
"shell_cmd": "python3 -u \"$file\"",

"file_regex": "^[ ]*File \"(...*?)\", line ([0-9]*)",

"selector": "source.python",
}
```
保存后在build system中选择python3，使用`ctrl + B就`可以运行代码了
```
import sys
print(sys.version)
print(sys.version_info)
```
上面的代码可以输出使用的python版本，python2版本可以直接选择Tools==>build system中的python就行，应为在系统中默认的是python2，所以这里要构建python3的运行环境。
B  在sublime中加入anaconda的插件，特别好使
先安装package control，然后安装anaconda的插件，点击Preferences==>Package Settings==>Anaconda==>Settings User生成文件Anaconda.sublime-settings，里面写入下面代码：
```
{

"anaconda_linting": false,

//保存文件后自动pep8格式化

"auto_formatting": true,

"enable_signatures_tooltip": false,
// close the document in the function or packages

"python_interpreter": "/usr/bin/python3"

}
```
配置后，函数可以补全，可以查看定义等

#### 3.3 解决sublime无法输入中文问题
`sudo apt-get update && sudo apt-get upgrade`  克隆项目到本地 

`git clone https://github.com/lyfeyaj/sublime-text-imfix.git `  运行脚本 
`cd sublime-text-imfix && ./sublime-imfix`
 
最后重启即可
如果上述方法不可行，则：
 1将sublime-test-imfix/lib路径下的libsublime-imfix.so拷贝到sublime的文件夹下
 2 将sublime-test-imfix/src路径下的subl文件拷贝到/usr/bin/路径下并作修改如下：
 ```
#!/bin/sh
export LD_PRELOAD=/you_install_path/sublime_text/libsublime-imfix.so
exec /you_install_path/sublime_text/sublime_text "$@"
```
使用subl就可以打开sublime并输入中文了

### **4.ubuntu安装vnc服务器**
> sudo apt-get install tightvncserver 安装VNC服务器
> tightvncserver 启动vnc服务器， 之后会要求输⼊密码， 任意输⼊密码（⼤于6个数）

打开vnc viewer软件， 输⼊IP:n ,IP为vnc服务器的ip地址， n代表打开的窗⼝个数， 连接vnc服务器， 输⼊刚
才在服务器上的密码进⼊系统

### **5.ubuntu安装ftp服务器**

 1. ⾸先服务器要安装ftp软件,查看是否已经安装ftp软件下：
`sudo apt-get install vsftpd` 安装ftp软件
`which vsftpd` 查看软件是否安装成功
如果看到有vsftpd的⽬录说明服务器已经安装了f tp软件

2. 查看ftp 服务器状态
`service vsftpd status` ,输出ftp服务器的状态

3. 启动ftp服务器
`service vsftpd start` 启动ftp服务器

4. 重启ftp服务器
`service vsftpd restart` 重启f tp服务器

参考:[http://blog.csdn.net/f t1512975/article/details/6620227](http://blog.csdn.net/ft1512975/article/details/6620227)

### **6.ubuntu使用screen在ssh下查看历史历史窗口**
`sudo apt-get install screen` 安装该软件
`screen` 开启一个新窗口，该窗口和普通的窗口功能一样
当我们在该窗⼝执⾏程序时， 当前screen窗⼝中键⼊`C-a d`，即Ctrl键+a键， 之后再按下d键,会退回到
screen前的窗⼝， 该命令的好处是虽然我们退出了ssh连接， 但是我们的程序任然在执⾏中， 我们可以⽤
ssh再次连接后， 使⽤ `top` 指令可以查看程序的运⾏状态， 但⽆法恢复之前的运⾏界⾯， ⽽screen可以做
到，**监控窗口已关闭但依然在运行的程序，并调出该窗口**。
> 使⽤ screen -ls 列出screen窗⼝运⾏的数量， 每个窗⼝都有⾃⼰的编号，
使⽤ screen -r 窗⼝的编号 连接之前退出的screen窗⼝(该窗⼝的退出⽅式必须是 C-a d )
exit 可以退出当前的窗⼝， 并返回之前的窗⼝
C-a ? 显⽰所有键绑定信息
C-a w 显⽰所有窗⼝列表
C-a C-a 切换到之前显⽰的窗⼝
C-a c 创建⼀个新的运⾏shell的窗⼝并切换到该窗⼝
C-a n 切换到下⼀个窗⼝ C-a p 切换到前⼀个窗⼝(与C-a n相对) C-a 0..9 切换到窗⼝0..9 C-a a 发送
C-a到当前窗⼝ C-a d 暂时断开screen会话 C-a k 杀掉当前窗⼝ C-a进⼊拷贝/回滚模式

参考：[在ssh、telnet断开之后继续执行程序](https://blog.csdn.net/wind19/article/details/4986458)

### **7.ubuntu以root身份打开文件管理器(GNOME)**
> sudo nautilus 以root用户打开文件管理器，可以在根目录的文件夹下进行操作





  [1]: http://blog.csdn.net/u013989576/article/details/61618454