---
layout: post
title: "在ubuntu20.04上打包紫光华宇输入法"
---

因为各种各样的原因，尝试打华宇紫光输入法linux版的deb包
这次打包使用的代码是<http://bbs.unispim.com/forum.php?mod=viewthread&tid=70049&page=1#pid167067>这个帖子里的地址提到的

[ubuntu的文档]<https://packaging.ubuntu.com/html/index.html>

[debian的]<https://www.debian.org/doc/manuals/maint-guide>

[还是debian的]<https://www.debian.org/doc/manuals/debmake-doc/index.en.html>

ubuntu的文档用的是bzr的版本管理，不是很熟悉，考虑到紫光代码在github上，就没继续了

debian的git build看了下，主要是使用gbp 大概就是首先要导入包，然后 gbp buildpackage
gbp导入包需要配置git的用户名和邮件，不然会收到错误提示，配置方法也一起告诉你了
```
git config --global user.name="你的用户名"
git config --global user.email="你的邮件地址"
```
不过，这次没有用gbp打包

#准备工作  
首先安装需要的包

>>打包需要的

```
sudo apt install gnupg pbuilder ubuntu-dev-tools apt-file
```  

>>liblunispim需要的

```
sudo apt-get install cmake libboost-dev libboost-filesystem-dev libunistring-dev
```   

>>ibus-lunispim需要的

```
sudo apt-get install libibus-1.0-dev libnotify-dev
```

#开始打包  

下载源码

```
$ git clone https://github.com/linhuman/liblunispim.git
$ git clone https://github.com/linhuman/ibus-lunispim.git
```

之后是给文件夹改名，按照dh_make的要求，文件夹改名成 `包名-版本号` 这样的形式
 
然后切换到对应的代码目录中

`dh_make --createorig`

不加--createorig参数会提示没orig包
然后会提示包的类型，这里选的s 就是单个deb包
然后会弹出一些信息，回答y就好，好像按n也不给修改的样子
之后就可以用debuild来打包了

#构建中遇到的问题

* gbp import 源代码不能有git metadata

* 第一次编译liblunispim的时候379行会有个

```
Error format not a string literal and no format arguments [-Werror=format-security]
```
的警告

看了下github，有个pr，但是没合并，pr的内容是379行修改成这样

```c
result = fprintf("%s %s", stream, u8_buf);
```

或者选择在CMake脚本文件CMakeLists.txt里面添加一行add_definitions (-Wno-format-security)跳过
但是……后来好像不知道为什么那个报错没了
* 编译完liblunispim之后编译ibus-lunispim的时候，会遇到找不到liblunispim的错误，这里需要声明下环境变量
手动编译可以选择

```
export PKG_CONFIG_PATH=/usr/lib64/pkgconfig
```

因为要打包，所以修改CMake文件
添加

```c
set(ENV{PKG_CONFIG_PATH}"/usr/lib64/pkgconfig")
```

* 之后会在make的时候遇到错误

```
/usr/bin/ld: /lib64/liblunispim.so: undefined reference to `shm_open'
/usr/bin/ld: /lib64/liblunispim.so: undefined reference to `shm_unlink'
```

修改cmake 文件在38行target link librarys最后面加rt 就不会报错了

* 在构建ibus-lunispim的时候，如果你没有用deb包安装，会遇到dpkg-shlibdeps: error: no dependency information found
的错误，手动编译安装会遇到这种问题，解决方法就是直接把先前打包的liblunispim装上，或者点下面

<https://stackoverflow.com/questions/11238134/dpkg-shlibdeps-error-no-dependency-information-found-for>

简单说下
使用
```
override_dh_shlibdeps:
    dh_shlibdeps --dpkg-shlibdeps-params=--ignore-missing-info
```
替代debian/rules文件里面的
```
%:
    dh $@
```
或者忽略错误
把debian/rules里面 `dh_shlibdeps` 改成 `dh_shlibdeps --dpkg-shlibdeps-params=--ignore-missing-info`

或者指定本地lib
debian/shlibs.local里面写lib的路径


最后的最后，虽然打包没问题安装设置也没问题，然而在ubuntu20.04上，不能用……是的，可以正常显示华宇输入法，但是就是不能输入中文，怀疑是默认英文状态，然后在ibus-setup里面，首选项也是灰色的
添加拼音之后，切换到拼音，再切换到华宇，依旧是拼音输入法，倒是之前在旧版本上直接编译安装的时候貌似是可以用的

