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
8. config wifi through CLI
  * `sudo su`
  * `ifconfig -a` (here should be a `wlan0`, just confirm it)
  * `ifconfig wlan0 up`
  * `wpa_passphrase {SSID} {PASSWORD} > /etc/wpa_supplicant/{SSID}.conf` (Use your wifi id and passwd)
    * for example: `wpa_passphrase "704" "12345678" > /etc/wpa_supplicant/704.conf`(Stuck sometimes)
  * `wpa_supplicant -i wlan0 -c /etc/wpa_supplicant/{SSID}.conf -B` (Connect wifi)
  * `dhclient wlan0` (allocate ip)
  * make it auto connection at startup
  * `sudo vim /etc/network/interfaces`
  * you should be ok now.
9. get ownclowd
  * `sudo apt install wget`
  * `wget https://download.owncloud.com/server/stable/owncloud-complete-latest.tar.bz2`
  * this take time, you can download 
10. unzip owncloud and move it to right direction
  * `tar -xvf owncloud-complete-latest.tar`
  * `sudo mv owncloud /var/www/html`
11. install dependency
  * `sudo apt install apache2`



## Reference

* [Sipeed荔枝派Lichee RV：安装debian系统、配置环境与外壳设计简易教程 - 知乎](https://zhuanlan.zhihu.com/p/468897470)
* [LicheeRV 入门开发一帖通](https://bbs.sipeed.com/thread/1300)
* [Debian10开启SSH服务\_51CTO博客\_centos开启ssh服务](https://blog.51cto.com/u_437549/4331925)
* [linux - How to disable messages from syslogd for my own user? - Unix & Linux Stack Exchange](https://unix.stackexchange.com/questions/517417/how-to-disable-messages-from-syslogd-for-my-own-user)
* [树莓派安装owncloud - 放码过来](https://blog.huangyuanlove.com/2019/08/03/%E6%A0%91%E8%8E%93%E6%B4%BE%E5%AE%89%E8%A3%85owncloud/)
* [debian无需登录自动连接wifi | 【谢金豆的官网】](https://xiejindou.com/2019/04/28/debian%E6%97%A0%E9%9C%80%E7%99%BB%E5%BD%95%E8%87%AA%E5%8A%A8%E8%BF%9E%E6%8E%A5wifi/)
* [networking - How do I configure WiFi to log in to WPA at boot time, regardless of user being logged in? - Ask Ubuntu](https://askubuntu.com/questions/1277/how-do-i-configure-wifi-to-log-in-to-wpa-at-boot-time-regardless-of-user-being)
* [Linux 下通过命令行连接 WiFi - 腾讯云开发者社区-腾讯云](https://cloud.tencent.com/developer/article/1494772)
* [linux server设置开机自动连接WIFI - Hello\_wshuo - 博客园](https://www.cnblogs.com/Hellowshuo/p/16930106.html)
* [Server Preparation for Ubuntu 22.04 :: ownCloud Documentation](https://doc.owncloud.com/server/next/admin_manual/installation/manual_installation/server_prep_ubuntu_22.04.html#multiple-concurrent-php-versions)
* [Release Alpha 187 · Eugeny/tabby](https://github.com/Eugeny/tabby/releases/tag/v1.0.187)