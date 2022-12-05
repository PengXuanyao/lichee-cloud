# 教程：如何在LicheepiRVDock上实现一个局域网云盘

## 前期的工作

前面第一次踩坑的教程可见：00_Tutorial。由于踩的坑太多了，最后一个大坑直接把系统给干崩了。因此，重装了一次系统，希望这次能够速通。

> 好了，第二次系统也崩了，这次是因为没有拓展分区就apt upgrade导致外存用完，系统崩溃。
> 因此，一定要先拓展分区啊


这希望是一个速通版本，并且也希望中间不时总结了遇到的一些问题。

## 硬件准备

* HDMI接口的显示屏
  * 由于板子没有自带网口，第一次配置还是建议使用图形化界面登录上去配置
  * 当配置好ssh和wifi之后，可以不需要再使用此显示屏
* 鼠标、键盘和拓展坞
  * 也是在第一次登录系统的时候，需要使用外部输入设备进行设置
  * 同样，当配置好ssh和wifi之后，可以不需要再使用额外的输入外设
* 32/64G的SD卡
  * 测试过8G的SD卡不能使用，下载的镜像文件img大于8G（上古时期遗留的产物，现在应该也见不到了）
* 闲置的硬盘加硬盘盒组成的移动硬盘（壕可以选择直接买移动硬盘）
  * 闲置一块128G的SSD，凑合着用

## 软件准备

* 操作系统：
  * Ubuntu22.04LTS
  * Windows（有相关的工具软件只能在windows上运行）
* 工具软件
  * Tabby（Windows / Linux）(终端软件，带有ssh和sftp，外观时尚)
  * GPart（Linux）（分区软件，windows上可以使用DiskGenius等）
  * Angry IP Scanner（Linux / Windows）
  * PhoenixCard（Windows）（镜像烧录工具，别的好像不行）