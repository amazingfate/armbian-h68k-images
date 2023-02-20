- [Hinlink H68K Armbian安装指南](#hinlink-h68k-armbian安装指南)
  - [1. 将Armbian安装到eMMC](#1-将armbian安装到emmc)
    - [1.1. 准备工作](#11-准备工作)
    - [1.2. 安装瑞芯微刷机工具](#12-安装瑞芯微刷机工具)
      - [1.2.1. Windows系统](#121-windows系统)
      - [1.2.2. Linux系统](#122-linux系统)
      - [1.2.3. Mac系统](#123-mac系统)
    - [1.3. 进入Maskrom模式](#13-进入maskrom模式)
    - [1.4. 开始刷机](#14-开始刷机)
      - [1.4.1. Windows系统](#141-windows系统)
      - [1.4.2. Linux/Mac系统](#142-linuxmac系统)
# Hinlink H68K Armbian安装指南

## 1. 将Armbian安装到eMMC

### 1.1. 准备工作

* H68K主板
* USB TYPEC数据线
* 有USB接口的Windows/Linux/Mac设备
* 卡针或牙签

### 1.2. 安装瑞芯微刷机工具

#### 1.2.1. Windows系统

1. 第一步: 安装RKDevTool

   下载[RKDevTool_Release_v2.86.zip](https://dl.radxa.com/tools/windows/RKDevTool_Release_v2.86.zip)并解压即可

2. 第二步: 安装USB驱动

   下载[DriverAssitant_v5.0.zip](https://dl.radxa.com/tools/windows/DriverAssitant_v5.0.zip), 双击`DriverInstall.exe`, 在弹出的窗口中先点击`驱动卸载`, 再点击`驱动安装`。

#### 1.2.2. Linux系统
1. 第一步: 准备一个基于Debian/Ubuntu的系统,运行以下命令安装编译依赖:

   `sudo apt-get install libudev-dev libusb-1.0-0-dev dh-autoreconf pkg-config`

2. 第二步: 下载源码并构建

    ```
    git clone https://github.com/radxa/rkdeveloptool
    cd rkdeveloptool
    autoreconf -i
    ./configure
    make
    ```

3. 第三步: 安装rkdeveloptool

   ```
   sudo cp rkdeveloptool /usr/local/bin/
   sudo ldconfig
   ```

4. 第四步: 查看rkdeveloptool版本

    `rkdeveloptool -v`

#### 1.2.3. Mac系统
1. 第一步: 准备[homebrew](https://brew.sh/)包管理器, 安装编译依赖:

   `brew install automake autoconf libusb pkg-config`

2. 第二步: 下载源码并构建

    ```
    git clone https://github.com/radxa/rkdeveloptool
    cd rkdeveloptool
    autoreconf -i
    ./configure
    make
    ```

3. 第三步: 安装rkdeveloptool

   `sudo cp rkdeveloptool /opt/homebrew/bin/`

4. 第四步: 查看rkdeveloptool版本

   `rkdeveloptool -v`


### 1.3. 进入Maskrom模式
1. 第一步: 拔掉电源, 取出TF卡
2. 第二步: 用卡针或牙签插入H68K外壳上有扳手图标的孔, 并按住
3. 使用USB线连接电脑和H68K的typec接口
4. 查看maskrom设备:

   Linux/Mac系统使用命令`lsusb`可以看到如下输出:
   `Bus 001 Device 112: ID 2207:350a Fuzhou Rockchip Electronics Company`

   Windows系统打开`RKDevTool`, 可以看到一个Maskrom设备

### 1.4. 开始刷机

#### 1.4.1. Windows系统
1. 第一步: 打开RKDevTool,如下图,确保如图中最下面的红圈显示有发现Maskrom设备;

   勾选如图中左上红圈所示的前两项，确保这两项的`Address`均为`0x00000000`

   下载[RK3568 Loader文件](https://github.com/radxa/rkbin/raw/master/bin/rk35/rk356x_spl_loader_ddr1056_v1.10.111.bin), 点击图中右上红圈的上半部分,设置为下载的Loader文件的地址

   下载[Armbian镜像](https://github.com/amazingfate/armbian-h68k-images/releases),将xz文件解压为img格式, 点击图中右上红圈的下半部分, 设置为img文件的地址

   ![Alt text](https://wiki.radxa.com/mw/images/5/58/Select-loader-bin.jpeg)

2. 第二步(可选): 擦除emmc
   当你的emmc已有系统, 不擦除可能会导致无法启动, 可以在烧写前擦除emmc
   步骤如下, 在`Advanced Function`选择Loader文件, 点击`Download`下载Loader, 然后点击`EraseAll`

   ![Alt text](https://wiki.radxa.com/mw/images/4/43/Rkdevtool-erase-device-1.png)

3. 第三步: 开始烧写, 如下图, 点击`Run`

   ![Alt text](https://wiki.radxa.com/mw/images/a/aa/3a-run-image.jpeg)

#### 1.4.2. Linux/Mac系统
1. 第一步: 运行命令`rkdeveloptool ld`, 可以看到如下输出, 确认识别到Maskrom设备:

   `DevNo=1	Vid=0x2207,Pid=0x350a,LocationID=104	Maskrom`

2. 第二步: 下载[RK3568 Loader文件](https://github.com/radxa/rkbin/raw/master/bin/rk35/rk356x_spl_loader_ddr1056_v1.10.111.bin), 运行如下命令加载loader:

   `sudo rkdeveloptool db rk356x_spl_loader_ddr1056_v1.10.111.bin`

3. 第三步(可选): 擦除emmc
   当你的emmc已有系统, 不擦除可能会导致无法启动, 可以在烧写前擦除emmc
   `sudo rkdeveloptool ef`
4. 第四步: 下载[Armbian镜像](https://github.com/amazingfate/armbian-h68k-images/releases),将xz文件解压为img格式, 运行如下命令进行烧写:

   `sudo rkdeveloptool wl 0 /path/to/Armbian-xxx-.img`