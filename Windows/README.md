# Microsoft Windows 系列镜像

云固件目前提供了Windows 10 和Windows 11 两个大版本的镜像，其他版本比如7、8、8.1后续会提供。

## Windows 10

- [Windows 10 22H2](10/22H2/README.txt)  
  - ISO [Consumer Editions](10/22H2/ISO/9a093d20/README.txt) [Bussiness Editions](10/22H2/ISO/9a792b10/README.txt)
  - 全部发行版本 [L0](10/22H2/L0/a0b28d74/README.txt)
  - Home [L1](10/22H2/Home/a1c562f3/README.txt)
  - Professional [L1](10/22H2/Pro/a1fe3133/README.txt)

## Windows 11

- [Windows 11 22H2](11/22H2/README.txt)  
  - ISO [Consumer Editions](11/22H2/ISO/9b82c4b2/README.txt) [Bussiness Editions](11/22H2/ISO/9bee62ca/README.txt)
  - 全部发行版本 [L0](11/22H2/L0/b084f7dd/README.txt)
  - Home [L1](11/22H2/Home/b1978722/README.txt)
  - Professional [L1](11/22H2/Pro/b1882792/README.txt)

## Windows 镜像制作说明

云固件镜像制作的方法和微软提供的VHD(x)镜像格式制作过程有很大的不同。具体差异主要表现在微软要求的本地启动VHD镜像使用位于VHD(x)文件之外的引导管理器（Boot Loader），而云固件使用VHD(x)镜像文件内ESP分区内的引导管理器。因此，云固件使用的VHD(x)镜像文件是可以作为虚拟机的磁盘启动的，但微软要求的本地启动VHD(x)镜像是无法启动，原因是缺少ESP分区。

按照《云固件镜像分层规范》，通常Windows镜像分成L0-L5共计六层设计。其中L0、L1制作方法有些特殊，L2-L5及以上制作方法类似。

### L0镜像制作

云固件的镜像包含ESP分区，制作L0镜像时需要手动创建ESP分区。

第一种方式：  
使用DiskPart命令初始化虚拟磁盘，分区及格式化。使用bcdboot命令安装Windows Boot Manager。展开WIM时，可以通过DISM命令来完成。

在L0镜像中间提供了Windows1x-xxxx-Just-ESP.vhdx镜像，已经安装好对应版本系统的ESP分区和启动文件；
Windows的各个版本也提供了展开好的镜像文件，方便用户自行部署使用。

第二种方式：  
就是直接在Hyper-V内使用ISO光盘启动，正常安装Windows，待重新启动时关闭虚拟机，保存镜像文件，ESP分区和WIM展开一次做完。

### L1镜像制作

正常情况下，通过DISM释放的镜像可以直接通过Windows Boot Manager启动进入安装过程，但云固件的镜像分区可能和微软要求的方式有差异，所以使用云固件启动后会出现错误导致无法继续安装，解决方式是使用Hyper-V虚拟化平台进行安装。

在Hyper-V内创建一台虚拟机，将L0镜像作为虚拟磁盘，不启用虚拟机网络，设置完成后即可启动进行安装。安装完成后，关闭虚拟机，保存镜像文件为L1镜像。

### L2~L5及以上镜像制作

在Hyper-V中安装完成的L1镜像，可以使用云固件演练场在物理机上进行后续驱动安装、更新补丁、KMS激活、安装应用软件。演练场使用可参考演练场的README。