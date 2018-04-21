---
title: dlib安装总结 
tags: [Python,dlib,opencv]
categories:
- Python
- Opencv
comments: true
sitemap: false
grammar_cjkRuby: true
---
2017-8-10



### 1.dlib安装步骤
ubuntu下最好别用anaconda，会出现很多问题，宁可自己安装各种包！


>sudo apt-get install build-essential cmake
sudo apt-get install libgtk-3-dev
sudo apt-get install libboost-all-dev
sudo apt-get install libopenblas-dev liblapack-dev  # 提高cpu处理速度
#pip install scikit-image
sudo apt-get install python-skimage
pip install dlib


如果pip安装失败，可以直接从pypi上下载安装包，离线安装！
如果dlib指令安装失败还可以编译源码，源码里使用`sudo python setup.py install`，就可以安装了，仅python的API，前提是setuptools已经安装完成。

### 2.ubuntu16.06安装opencv3.3

1.安装各种依赖库
>[compiler] `sudo apt-get install build-essential`
[required] `sudo apt-get install cmake git libgtk2.0-dev pkg-config libavcodec-dev libavformat-dev libswscale-dev`
[optional] `sudo apt-get install python-dev python-numpy libtbb2 libtbb-dev libjpeg-dev libpng-dev libtiff-dev libjasper-dev libdc1394-22-dev`

2.下载源码后生成编译文件
2.1创建一个文件夹用来放cmake后的编译文件
>cd ~/opencv
mkdir build
cd build

2.2cmake后创建make所需的文件
> `cmake -D CMAKE_BUILD_TYPE=Release -D CMAKE_INSTALL_PREFIX=/usr/local ..`

build文件夹下会产生makefile文件用于编译，cmake的编译选项还有其他的，但目前用上面的就够了，python的接口也包含进去了

3.编译源码产生对象文件(**.o**文件)
>`make -j4` # j4表示开启4个线程
>`sudo make install` # 安装opencv库到系统中

4. 配置opencv.conf file ，加入环境变量

![enter description here][1]


> ` sudo gedit /etc/ld.so.conf.d/opencv.conf`
> 
如果没有该文件，则上述的命令会创建该文件，在opencv.conf里面加入 `/usr/local/lib`

> `sudo ldconfig` #更新库目录
----------------------------------------------------------------------------
[Ubuntu 安装OpenCV3.0.0][3] blog里还有下述步骤：
打开文件`bash.bashrc`
>`sudo gedit /etc/bash.bashrc`
>
加入下面两行
>`PKG_CONFIG_PATH=$PKG_CONFIG_PATH:/usr/local/lib/pkgconfig`
`export PKG_CONFIG_PATH`

我这里进行测试，没有该步骤也行！

-------------------------------

5.测试opencv
5.1 C++版本

``` cpp
#include "opencv2/opencv.hpp"
using namespace cv;  
int main() {    
Mat src = imread("test3.jpg",1); 
imshow("src",src);   
waitKey(0);  
return 0; }
```
编译方式：
1 命令行
```shell
g++  opencvtest.cpp -o opencvtest `pkg-config  --cflags --libs opencv` 
```

cflags前面是两个横线

2 借助cmake，写CMakeLists.txt

```cmake
cmake_minimum_required(VERSION 2.8)
project(opencvtest)
find_package( OpenCV REQUIRED )
add_executable( opencvtest opencvtest.cpp )
target_link_libraries( opencvtest ${OpenCV_LIBS} )
```
之后 执行
>`cmake .`
`Make`   # 生成可执行文件
`./ test` # 运行程序进行测试

5.2python版本
```python
import cv2
img = cv2.imread('test3.jpg',1)
cv2.imshow('img',img)
cv2.waitKey(0)
```
>`python test.py` #运行该出现，会显示一张图片

至此opencv的配置完成！

  ### 3.virtualbox虚拟机的ubuntu系统下打开笔记本自带的摄像头
  >`cheese`  #在ubuntu里可以打开摄像头
  
  如果打不开，则需要设置virtualbox。
  1.首先去官网下载virtualbox的扩展包进行安装，如下图：
  
  ![enter description here][4]
  

 2. 然后设置虚拟机的usb选项，并勾选设备选项里的摄像头选项

![enter description here][5]
 
 ![enter description here][6]

  
  设置好后再使用`cheese`指令打开摄像头，或运行与摄像头相关的程序，由于是虚拟机的原因摄像头比较卡。
  
 ### 4.whl文件安装中的问题处理
 原文件： `opencv_python-3.1.0-cp34-cp34m-win_amd64.whl`，
改后的文件：`opencv_python-3.1.0-cp34-none-win_amd64.whl`
 **把原来文件名中间的cp34m变为none(其实不改也能安装成功)，并且cp34要和python的版本对应，如cp36表示python版本3.6，如果不改的话不然会出现下面的问题：(但只是更改版本号可能会带来兼容性问题)**
 
opencv_python-3.1.0-cp34-cp34m-win_amd64.whl is not a supported wheel on this platform.



> pip3 install 路径名\opencv_python-3.1.0-cp34-none-win_amd64.whl
> 安装该模块 

### 5. **pip换源提升安装库的速度和稳定性**
网上有很多可用的源，
豆瓣：http://pypi.douban.com/simple/
 清华：https://pypi.tuna.tsinghua.edu.cn/simple
 
 `pip install -i https://pypi.tuna.tsinghua.edu.cn/simple 库名` 这样就会从清华这边的镜像去安装库。
 
 更改配置文件：
1.Linux系统
修改` ~/.pip/pip.conf` (没有就创建一个)， 修改 index-url至tuna，内容如下：
 ```
 [global]
 index-url = https://pypi.tuna.tsinghua.edu.cn/simple
 ```

2.windows
直接在user目录中创建一个pip目录，如：C:\Users\xx\pip，新建文件**pip.ini**，内容如下
 ```
 [global]
 index-url = https://pypi.tuna.tsinghua.edu.cn/simple
 ```
 
   <font color=red face="宋体">进过不断的试错，总结如下：
   1.首先为pip换源，这样大部分的库可以直接指令安装
   2.换源后安装失败的，可以在[source1][7]和[source2][8]下载whl文件离线安装
   3.大部分依赖库安装失败都是下载失败导致，所以上述两种方法可以解决大部分问题</font>
   
 [更改pip源至国内镜像，显著提升下载速度][9]


  [1]: http://p7jji9nvf.bkt.clouddn.com/%E5%B0%8F%E4%B9%A6%E5%8C%A0/error1.jpg "不进行此步骤出现的错误"
  [3]: http://jingpin.jikexueyuan.com/article/36054.html
  [4]: http://p7jji9nvf.bkt.clouddn.com/%E5%B0%8F%E4%B9%A6%E5%8C%A0/virtualbox0.jpg "3.1 安装扩展包"
  [5]: http://p7jji9nvf.bkt.clouddn.com/%E5%B0%8F%E4%B9%A6%E5%8C%A0/virtualbox.jpg "3.2 设置usb控制器为usb2.0"
  [6]: http://p7jji9nvf.bkt.clouddn.com/%E5%B0%8F%E4%B9%A6%E5%8C%A0/virtualbox2_1.jpg "3.3 勾选设备选项里的摄像头"
  [7]: http://www.lfd.uci.edu/~gohlke/pythonlibs/#opencv
  [8]: https://pypi.python.org/pypi
  [9]: http://blog.csdn.net/qw_xingzhe/article/details/52675158