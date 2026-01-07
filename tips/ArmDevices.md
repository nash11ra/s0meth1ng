# Arm 设备折腾相关

# 1. N1盒子刷iStoreOS备忘录
## 更新时间 2026.01.07
## 准备工作

  找一个大一点的U盘，4G以上的
  
  下载N1[固件](https://fw.koolcenter.com/iStoreOS/alpha/n1/)
  
  使用刷写工具例如balenaEtcher刷入U盘

## 刷写系统

  注意需要刷写两次
  
  U盘插入离HDMI较近的USB口，网线连接主路由，开机，这时iStore应该从USB启动，默认用户root，默认密码password，终端中运行install-to-emmc.sh命令，等待一会提示All Done则表明安装完成，安装完成后终端中运行poweroff关机
  
  通电开机，再次进入终端。重复上述步骤再刷写一次，然后就可以拔掉U盘，从内置的emmc启动了

## 旁路由设置

  网络向导中使用旁路由模式，设置为静态IP，网关地址为上级路由器地址，DNS可以自己写，或者直接填写上级路由器地址，IPV6按个人喜好开启，默认我是关闭的

## 软件包配置与系统设置

  相关UP主推荐：[酷友社](https://space.bilibili.com/1492058311)，[大鹅体验](https://space.bilibili.com/517246386)，[好用斋](https://space.bilibili.com/3546380987533935)，[悟空的日常](https://space.bilibili.com/250915741)
  
  可以更新一下iStore，左侧iStore点击进入，选择维护，更新即可

  扩展一下默认的空间，将emmc剩余的部分用作docker的空间，主页系统根目录磁盘信息三个小点，前两个分区不可扩展，对最后的5GB的分区进行操作，首先格式化那个分区，格式化之后剩余4.5GB左右，应该叫mmcblk1p4，首先去系统-挂载点处，点击下面的 挂载点 为/mnt/mmcblk1p4的那一行，后面有个编辑，弹出窗口中挂载点处下拉，输入挂载点名称，比如我这里改成/mnt/myemmc，保存。再去上面的已挂载的文件系统把刚刚的/mnt/mmcblk1p4的那一行点击卸载分区。最后点最底下的保存并应用。同样的可以挂载移动硬盘，我挂了个64GB的不用的固态，格式化为了exfat，一样的原理，先更改挂载点名称，我这里更改为/mnt/mydisk，然后卸载分区，保存并应用即可。
  
  上面的操作完成了之后可迁移docker的位置，docker中点击快速配置迁移到/mnt/myemmc/docker，等待一会提示迁移成功即可完成，这样原本的2G空间就变成了4.5GB，注册表镜像可以填写[1](https://docker.1panel.live)和[2](https://hub.rat.dev)两个国内加速源

  Samba服务开启：先去 网络存储-统一文件管理-用户 添加要使用的用户和密码（不要使用 root 用户，因为 Samba 默认禁止 root 用户登录），再去 服务-网络共享 添加共享目录，名称随意比如我填写N1，路径填写挂载点，比如我这里填写外接硬盘的挂载点/mnt/mydisk，勾选 可浏览，强制root。允许用户处填写上面设置的用户，多个用户使用空格分开。勾选允许匿名用户和继承所有者，创建权限掩码填写0666，目录权限掩码填写0777。VFS对象可填写recycle启用回收站，不用回收站可以不填。
  
  好用的软件包：AirConnect，Aria2下载器，KMS，OpenC1ash，Openlist

  七八年了，最终我这N1盒子寿终正寝了，开不了机。将它的外部数据迁移到群晖虚拟机里面，还好上面的docker数据和文件没啥重要的，有用的几乎都在那个外置的固态上。

  OpenC1ash新版本可能Tun内核不能启动，尝试的办法有，直接上传YAML文件。`/etc/openclash`目录下只有一行的运行配置文件，直接替换成刚才的文件（也就是下载两遍）。运行时的网络栈设置成System。`/etc/openclash/core/clash_meta`目录下的版本换成[MetaCubeX](https://github.com/MetaCubeX/mihomo/releases)的`linux-amd64`版本（这里由于是X86群晖小主机，所以我选的V1）。确保`版本更新`的`检查更新`能正常下载。以及重启。

# 2. E20C刷机备忘录
## 更新时间 2026.01.07
## 准备工作

  找一个5V 2A或者2A以上的TYPE-C线和电源适配器

  找一个TF卡，默认系统从TF卡启动
  
  下载E20C的[iStoreOS固件](https://fw.koolcenter.com/iStoreOS/e20c)
  
  使用刷写工具例如balenaEtcher刷入TF卡，通电启动

  打算等飞牛OS的arm版本公测后，采用[dd写盘的方式](https://wkdaily.cpolar.cn/archives/e20c)把系统写进去

  当然也能采用[传统方式](https://docs.radxa.com/e/e20c/getting-started/install-os)进行刷写
