# 将licheeRV打造为一个可用的云盘

## 概述

本项目在前期的主线是想要将owncloud服务搭载到licheeRVDock上，但是发现的主要问题是，软件源自带的php版本（8.1）并不适配owncloud（其需要7.4版本）。因此，尝试编译安装php7.4，几乎成功（花了一下午+一晚上的时间编译，这个核的性能确实不能直接编译安装），最后owncloud（或者说apache2）并不能识别到php7.4（可能是软件源的apache2只支持和php8进行适配），最后，debian系统死机，强行关机导致系统文件损坏，重装系统。

介于其自带的debian系统过于容易死机和崩溃，且加上的图形化界面（没有太大必要，占用一定的系统资源，且操作起来比较卡顿）。因此，在经历了上述的那次死机之后，开始尝试安装armbian系统（armbian支持Nezha板（D1芯片），根据官方文档，也适配了licheeRV），实际效果比较好。目前，系统运行稳定，没有再出现卡死后系统损坏的现象。

在更换系统之后，放弃了owncloud方案（今后可尝试交叉编译，在电脑上编译出PHP7.4的安装包，再去板卡上进行安装和适配）。改成尝试seafile方案，官方并没有riscv的支持，但是有一个docker镜像（seafile-arm:riscv-experimental），因此，尝试安装docker和此镜像。但是，在运行的时候，出现了在初始化阶段一直waiting for db卡住的情况，尝试过重装mysql、mariadb等多种操作，始终无法解决此问题，可能是系统环境mysql的配置问题。随后又尝试在orangpi上运行seafile，但是出现了初始化时，无法登录mysql的情况。因此，seafile的方案的主要难点在于解决mysql相关的问题，目前没有找到合理的方案。

因此，最后决定试用一下已经有riscv安装包的syncthing以及miniserver项目，目前实测syncthing和minisever能够使用正常（中间有一个小bug，可以通过STFW解决）。

## 安装armbian系统

介于尝试过自带的系统时不时宕机，并且在突然断电的情况下，非常容易出现系统损坏，第二次无法正常启动的现象。因此，使用社区维护的armbian系统。

armbian目前有riscv的版本，([大佬](https://forum.armbian.com/topic/21465-armbian-image-and-build-support-for-risc-v/)提供，可以直接食用）。目前没有出现宕机和异常掉电无法启动的情况，稳定性好。由于文件下载需要访问外网，可以使用科学上网方法，或者使用我镜像到百度网盘的版本（链接: https://pan.baidu.com/s/1PVgEpbCsxN9aIaIUmg3KMw 提取码: 5ezv）（后期可能不会再更新镜像，请关注原作者，如有问题也可以提issue，如有时间可帮助解决）（**百度网盘加速**可以使用**软件小妹的油猴插件**，自行百度）。

下载好镜像文件后，使用烧录工具[balenaEtcher - Flash OS images to SD cards & USB drives](https://www.balena.io/etcher?ref=etcher_footer)可以将镜像系统文件直接烧录到SD卡上。

## 开机启动和初始设置

正常烧录完成系统后，将sd卡插入LicheeRV，上电启动。（这里需要给Lichee RV Docker外接一个显示器和USB键盘进行操作）。

系统在进行第一次启动的时候，会根据SD卡的大小对分区进行自动调整，充分利用SD卡的容量。

初始的账号是：root，密码：1234

在引导下完成wifi、用户名、密码等设置。

在进入系统后，使用armbian-config命令进行设置：

1. 首先，设置wifi。在连上wifi后，可以通过笔记本使用ssh无线连接（注意要在同一个局域网内，这里建议使用tabby终端，能够后期方便ftp传文件）。

2. 然后，换源和进行软件更新。

   - 备份原来的软件源list：`sudo mv /etc/apt/source.list /etc/apt/source.list.bak`
   - 新建使用的软件源list：`sudo nano /etc/apt/source.list`
   - 将以下内容复制到新建的list中：和miniserve

   ```bash
   deb http://cn.ports.ubuntu.com/ jammy main restricted universe multiverse
   #deb-src http://ports.ubuntu.com/ jammy main restricted universe multiverse
   
   deb http://cn.ports.ubuntu.com/ jammy-security main restricted universe multiverse
   #deb-src http://ports.ubuntu.com/ jammy-security main restricted universe multiverse
   
   deb http://cn.ports.ubuntu.com/ jammy-updates main restricted universe multiverse
   #deb-src http://ports.ubuntu.com/ jammy-updates main restricted universe multiverse
   
   deb http://cn.ports.ubuntu.com/ jammy-backports main restricted universe multiverse
   #deb-src http://ports.ubuntu.com/ jammy-backports main restricted universe multiverse
   ```

   - 更新软件：

   ```bash
   sudo apt upgrade
   sudo apt update
   ```

3. 部署syncthing软件。

   synchthing软件从github的release中下载，注意这里要下载riscv版本，[Releases · syncthing/syncthing](https://github.com/syncthing/syncthing/releases)。

   ![版本选择](https://s2.loli.net/2023/01/29/gPCWOY6wGmIEtja.png)

   在下载完成正确的版本后，使用tabby终端ssh连接到开发板上，然后将文件通过sftp传送过去即可。

   ![sftp](https://s2.loli.net/2023/01/29/ETnFdlWM9J4P8qA.png)

   将文件解压在`/opt/syncthing`文件夹下，使用命令的格式为：

   ```bash
   tar xf {{source.tar[.gz|.bz2|.xz]}} --directory={{path/to/directory}}
   ```

   解压完毕后，进入到syncthing所在的文件夹，增加其运行权限：

   ```bash
   chmod +x ./syncthing
   ```

   运行syncthing（初次运行可能会有一个bug，网上有解决方案，可以直接搜索报错信息）：

   ```bash
   ./syncthing
   ```

   第一次运行时会创建一个默认设置，ctrl + c 结束当前运行的syncthing，再通过文本编辑器更改其中的设置：

   ```bashs
   nano ~/.config/syncthing/config.xml
   ```

   找到其中有`127.0.0.1`的一行，改为`0.0.0.0`。

   再次重新打开。

   然后，其会开放一个端口，默认是8384，访问地址：`{licheepi's ip} : 8384`即可进入到syncthing界面进行管理。

   ![进入界面](https://s2.loli.net/2023/01/29/ULKd4xgXYkSHOqm.png)

   在左侧的文件夹下设置当前 lichee pi 的共享文件夹，然后在需要同步的设备上（例如，我是一台笔记本，就直接在笔记本上下载对应版本的syncthing并运行，并添加对应的共享文件夹，即可让两个文件夹之间实时同步）。

4. 部署minisever软件。

   minisever的作用是方便进行文件展示和上传，补充syncthing 的不足之处（syncthing是自动进行后台的备份，不能直接文件操作）。

   找到miniserver的官网release，同样选择riscv版本。[Release v0.22.0 · svenstaro/miniserve](https://github.com/svenstaro/miniserve/releases/tag/v0.22.0)

   通过ftp传输文件到lichee pi上，设置minisever的运行权限，然后使用参数启动miniserver：

   ```bash
   chmod +x miniserve-0.22.0-riscv64gc-unknown-linux-gnu 
   # -U 使能创建文件夹，-u uploads 使能在uploads文件夹下上传文件的功能，-D 排序文件夹优先 -- ~/Sync server的文件夹路径
   ./miniserve-0.22.0-riscv64gc-unknown-linux-gnu -U -u uploads -D -- ~/Sync
   ```

   展示的目录如下：

   ![](https://s2.loli.net/2023/01/30/JGOw9Bm81FQldaq.png)

至此，一个简易的云盘lichee cloud搭建完成，具有文件上传备份和同步盘的功能。

## 参考

1. [Risc-v 开源软件安装与自行编译 - 昉·星光 （中文论坛） - RVspace Forum](https://forum.rvspace.org/t/risc-v/330)





