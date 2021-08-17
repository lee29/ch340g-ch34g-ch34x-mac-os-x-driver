

1直接安装pkg驱动  

2 brew install flashrom按照提示安装

3CH341A 编程器十几块钱包邮白菜价，用于刷路由器25系列闪存很给力。
大多数人都在 Windows 下用 CH341A 编程器软件，还需要注册、破解，麻烦，而且有些版本对32M、64M闪存支持不给力。

今天给大家介绍一款 Linux 下的刷机软件，flashrom，相当强大。
flashrom v1.0 以及1.0以上版本已经支持 CH341A 编程器。
实测可以支持读写 2M、4M、8M、16M、32M、64M 的闪存。
简单说一下 flashrom 这个命令行刷机软件的用法。

列出已插入的闪存芯片类型：
flashrom -p ch341a_spi
复制代码

擦除闪存芯片：
flashrom -p ch341a_spi -E
复制代码

读取闪存芯片内容并保存为 backup.bin 文件（编程器固件）：
flashrom -p ch341a_spi -r backup.bin
复制代码

把编程器固件 abc.bin 刷入闪存并校验是否刷入成功：
flashrom -p ch341a_spi -w abc.bin
复制代码

手动校验编程器内容与 abc.bin 是否一致：
flashrom -p ch341a_spi -v abc.bin
复制代码

只把 cfe.bin 这个引导软件刷入空白闪存，可分多步来（单刷breed引导程序也一样）：
1、查看 cfe.bin 有多少字节：
$ ls -l cfe.bin
-rw------- 1 me me 131072 1月 27 22:08 cfe.bin
复制代码

2、擦除闪存：
$flashrom -p ch341a_spi -E
复制代码

3、读取空白闪存内容保存为 blank.bin：
$flashrom -p ch341a_spi -r blank.bin
复制代码

4、查看 blank.bin 有多少字节：
ls -l blank.bin
-rw-r--r-- 1 me me 16777216  1月 27 21:45 blank.bin
复制代码

5、把 blank.bin 头部去掉 cfe.bin 大小内容保存为临时文件 temp.bin:
$dd if=blank.bin of=temp.bin bs=1 skip=131072
复制代码

6、把 cfe.bin 和 temp.bin 组装成仅含 cfe 引导程序的空白固件 cfenorom.bin：
$cat cfe.bin temp.bin > cfenorom.bin
复制代码

7、确认 cfenorom.bin 大小是否跟 blank.bin 一模一样：
$ls -l cfenorom.bin
-rw-r--r-- 1 me me 16777216  1月 27 21:46 cfenorom.bin
复制代码

8、把 cfenorom.bin 这个仅仅只有 cfe 引导程序没有具体固件的“空白固件”刷入闪存：
$flashrom -p ch341a_spi -w cfenorom.bin
复制代码


总之这个开源免费的 flashrom 相当强，完全可以代替那个CH341A商业编程器软件。
















# ch340g-ch34g-ch34x-mac-os-x-driver
Latest **macOS Sierra/High Sierra**-compatible driver for devices using the CH340G, CH34G or CH34X chipset. This chipset is used in several Arduino-compatible clones and serial-to-USB cables.

**Do not install if you have the current macOS Mojave 10.14 or later.** macOS Mojave 10.14 (released in October 2018) includes a CH34x driver by Apple. If both Apple's and the OEM driver are installed, they will create conflicting non-functional serial ports. Steps 1 to 3 below can be useful to remove the conflicting OEM drivers.

If you use Apple's driver and have problems with the serial communication, ensure the data rate is 460,800 bps or lower.

## Introduction
Version 1.5 (2018-07-05) of the [OEM driver](http://www.wch.cn/download/CH341SER_MAC_ZIP.html) for the CH34x chipset.

Version 1.4 (2017-01-11) of the [OEM driver](http://www.wch.cn/download/CH341SER_MAC_ZIP.html) for the CH34x chipset.

Version 1.3 (2016-09-27) of the OEM driver causes a kernel panic (a.k.a. *crash*) when installed on **macOS Sierra**.

## Installation

1.  Unplug any CH34* devices.
1.  Unload the old drivers if running:
    * `sudo kextunload /Library/Extensions/usbserial.kext`
    * `sudo kextunload /System/Library/Extensions/usb.kext`
1.  Remove the old driver by issuing one of the following commands (depending on your installation):
    * `sudo rm -rf /System/Library/Extensions/usb.kext`
    * `sudo rm -rf /Library/Extensions/usbserial.kext`
1.  Double-click on the `CH34x_Install_V1.5.pkg` file and install (no need to reboot)
1.  Instead of rebooting, you can force quit Installer after it completes.
1.  Load the new driver:
    * `sudo kextload /Library/Extensions/usbserial.kext`
1.  Plug in your device. It should now be listed under the `/dev` directory. Examples:
    * `/dev/cu.wchusbserial1410`
    * `/dev/tty.wchusbserial14210` (driver version 1.5)

## Installation with Homebrew-Cask

* Install the driver by the following commands:
  * `brew tap adrianmihalko/ch340g-ch34g-ch34x-mac-os-x-driver https://github.com/adrianmihalko/ch340g-ch34g-ch34x-mac-os-x-driver`
  * `brew cask install wch-ch34x-usb-serial-driver`
## Troubleshooting

If the device doesn't show up under `/dev`, check the section "Security and Privacy" in System Preferences for any notices about blocked software.

Note: **disabling System Integrity Protection is no longer necessary**, as the current drivers are properly signed by the OEM. If you're having problems, they're almost certainly not related to SIP. The instructions below are left only for reference purposes. If you had previously disabled it (*especially* if you did so completely), [**you are strongly encouraged to fully re-enable SIP.**](https://developer.apple.com/library/content/documentation/Security/Conceptual/System_Integrity_Protection_Guide/ConfiguringSystemIntegrityProtection/ConfiguringSystemIntegrityProtection.html)

If, and only if, the device is not recognized after the installation (or you cannot install the driver), please disable *System Integrity Protection*:

*  Reboot your Mac into *Recovery Mode* by restarting your computer and holding down `Command+R` until the Apple logo appears on screen.
*  Open the Terminal (Applications > Utilities > Terminal).
*  In the Terminal window, type in `csrutil enable --without kext` (or to fully disable: `csrutil disable`) and press `Enter`.
*  Restart your Mac.

Please share this page!

Regards,  
Adrian Mihalko  
www.mihalko.eu

p.s:
I **LOVE** coffee! Buy me a coffee at:   
[![Donate](https://img.shields.io/badge/Donate-PayPal-green.svg)](https://www.paypal.com/cgi-bin/webscr?cmd=_donations&business=89T88ER75BJ9L&currency_code=EUR&source=url)




