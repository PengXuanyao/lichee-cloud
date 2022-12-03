# Tutorial

## Preparation

**Hardware**

1. sd card (recommended 32G, and 16G is also ok)
2. usb keyboard and mouse
3. screen
4. usb docking station (Only one usb on board is not enough, or you an use ssh to connect the board)

**Software**

1. os img ([下载站 - Sipeed](https://dl.sipeed.com/shareURL/LICHEE/D1/Lichee_RV/SDK/image))
  * debian os is on baidu cloud disk (Debian 系统镜像请到百度网盘上下载：链接：https://pan.baidu.com/s/1QJTaDw6kkTM4c_GAlmG0hg 提取码：wbef)
  * if you are not in the Chinese Mainland, use this [link](https://mega.nz/folder/lx4CyZBA#PiFhY7oSVQ3gp2ZZ_AnwYA)
  * and make sure use this debian version.
2. disk imager ([下载站 - Sipeed](https://dl.sipeed.com/shareURL/LICHEE/D1/Lichee_RV/tool))
3. Gparted (sudo apt install gparted (on ubuntu))

## Initialize your board

1. Follow this [LicheeRV 入门开发一帖通](https://bbs.sipeed.com/thread/1300) to download the image on the sd card.
2. After correct download, you can see the UI. User name: sipeed, passwd: licheepi.
3. Set the wifi
4. Set the software source and set pub-key:
  * `sudo vim /etc/apt/sources.list`
  * use this to replace the original content:
  * deb https://mirrors.aliyun.com/debian-ports/ sid main 
  * deb https://mirrors.aliyun.com/debian-ports/ unreleased main 
  * set pub-key:
  * `sudo apt-key add archive_2022.key` ([archive\_2022.key\_免费高速下载|百度网盘-分享无限制](https://pan.baidu.com/link/zhihu/7VhmzbuNhpijdvFUZjc3AXBVR3as52QQUDFD==))
5. Update and upgrade
  * `sudo apt update`
  * `sudo apt upgrade`(This takes time)
6. Set and open ssh
  * `vim /etc/ssh/sshd_config` and change below
  * PermitRootLogin yes
  * PubkeyAuthentication no
  * PasswordAuthentication yes
  * `/etc/init.d/ssh start`
  * `sudo update-rc.d ssh enable` (startup launch setting)
7. close rsyslog
  * `vim /etc/rsyslog.conf`
  * comment this line(use #):
  * `*.emerg                    :omusrmsg:*`
  * -->
  * `# *.emerg                    :omusrmsg:*`

## Reference

* [Sipeed荔枝派Lichee RV：安装debian系统、配置环境与外壳设计简易教程 - 知乎](https://zhuanlan.zhihu.com/p/468897470)
* [LicheeRV 入门开发一帖通](https://bbs.sipeed.com/thread/1300)
* [Debian10开启SSH服务\_51CTO博客\_centos开启ssh服务](https://blog.51cto.com/u_437549/4331925)
* [linux - How to disable messages from syslogd for my own user? - Unix & Linux Stack Exchange](https://unix.stackexchange.com/questions/517417/how-to-disable-messages-from-syslogd-for-my-own-user)
