# deepin-wine

> deepin-wine环境与应用在Debian/Ubuntu上的移植仓库
>
> 使用deepin官方原版软件包
>
> 安装QQ/微信只需要两条命令

## 关于V2（老用户看这里）

**deepin-wine**移植仓库现（2010/05/03）已升级为**V2**版本，兼容更多发行版。

旧版现在依然可以使用，但将来会择期关闭，运行如下命令可以从**V1**升级到**V2**。

```sh
sudo rm /etc/apt/trusted.gpg.d/i-m.dev.gpg
wget -O- https://deepin-wine.i-m.dev/setup.sh | sh
```

## 快速开始（新用户看这里）

1. 添加仓库

   首次使用时，你需要运行如下一条命令将移植仓库添加到系统中。

   ```sh
   wget -O- https://deepin-wine.i-m.dev/setup.sh | sh
   ```

2. 应用安装

   自此以后，你可以像对待普通的软件包一样，使用`apt-get`系列命令进行各种应用安装、更新和卸载清理了。

   比如安装微信只需要运行下面的命令，

   ```sh
   sudo apt-get install deepin.com.wechat
   ```

   将`deepin.com.wechat`替换为下列包名，可以继续安装其他应用：

   |    应用    |          包名           |
   | :--------: | :---------------------: |
   |    TIM     |  deepin.com.qq.office   |
   |     QQ     |    deepin.com.qq.im     |
   |  QQ轻聊版  | deepin.com.qq.im.light  |
   |    微信    |    deepin.com.wechat    |
   |  百度网盘  |  deepin.com.baidu.pan   |
   | 迅雷极速版 | deepin.com.thunderspeed |
   |   WinRAR   |  deepin.cn.com.winrar   |

   当然还有一些其他的应用，不逐一列出。
3. fix中文乱码问题
修改如下2个文件：
/opt/deepinwine/tools/run.sh
/opt/deepinwine/tools/run_v2.sh

内容如下，替换WINE_CMD
-------------------
#WINE_CMD="deepin-wine"
WINE_CMD="LC_ALL=zh_CN.UTF-8 deepin-wine"
#added by user
export GTK_IM_MODULE="ibus"
export QT_IM_MODULE="ibus"
export XMODIFIERS="@im=ibus"



## 添加仓库过程详解

**不关心细节的同学不必了解这部分，完全不影响使用**

环境配置其实就是添加我自行构建的软件仓库为源，具体包括以下几步。

1. 添加i386架构

   因为deepin-wine相关的软件包都是i386的，而现在的系统基本是64位的，所以需要先添加i386架构支持。

   通过`dpkg --print-architecture`和`dpkg --print-foreign-architectures`命令查看系统原生和额外添加的架构支持，如果输出结果不含`i386`，则需要手动添加支持。

   ```sh
   sudo dpkg --add-architecture i386
   ```

3. 添加软件源

   创建`/etc/apt/sources.list.d/deepin-wine.i-m.dev.list`文件，编辑其内容如下，

   ```
   deb [trusted=yes] https://deepin-wine.i-m.dev /
   ```

3. 设置源优先级

   创建`/etc/apt/preferences.d/deepin-wine.i-m.dev.pref`文件，编辑其内容如下，

   ```
   Package: *
   Pin: release l=deepin-wine
   Pin-Priority: 200
   ```

4. 刷新软件源

   ```sh
   sudo apt-get update
   ```

## 卸载清理

卸载与清理按照层次从浅到深可以分为如下四个层级，

1. 清理应用运行时目录

   例如QQ/TIM会把帐号配置、聊天文件等保存`~/Documents/Tencent Files`目录下，而微信是`~/Documents/WeChat Files`，删除这些文件夹以移除帐号配置等数据。

2. 清理wine容器

   删除`~/.deepinwine/`目录下相应名称的文件夹即可。

3. 卸载软件包

   常规的`sudo apt-get purge xxx`和`sudo apt-get autoremove`操作。

4. 移除软件仓库

   ```sh
   sudo rm /etc/apt/preferences.d/deepin-wine.i-m.dev.pref /etc/apt/sources.list.d/deepin-wine.i-m.dev.list
   sudo apt-get update
   ```
   
   这会把一切恢复到最初始的状态。

## 移植原理

deepin把QQ/微信之类的deepin wine应用打包放在了deepin仓库中，因此先提取出这些应用及依赖的软件包，再减去Debian/Ubuntu等发行版官方仓库中固有的软件包，就可以打包成一个移植于对应发行版的“差量仓库”。

## 版权相关

这个git仓库中的代码只包括了移植版软件仓库的构建工具，最后仓库中软件包的下载地址会被301重定向到deepin的官方仓库（或者镜像）中去，其版权由 [deepin](https://www.deepin.com/) 所有。

## 感谢

本项目受 [wszqkzqk/deepin-wine-ubuntu](https://github.com/wszqkzqk/deepin-wine-ubuntu) 项目启发，改进了一下安装方式，因此兼容原项目，已经按照deepin-wine-ubuntu项目安装好后，依然可以再按此项目进行配置，可以更方便地进行后续更新。
