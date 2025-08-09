# H510-Sequoia-EFI

只是一个黑苹果EFI

## 配置和版本

|种类|型号|参数|
|---|---|---|
|CPU|Intel Core i3-10105F||
|GPU|AMD Radeon RX 6600||
|主板|AsRock H510M-HDV/M.2||
|存储|Samsung 870 EVO SATA SSD|1TB|
|网卡|Intel Wi-Fi 6E AX210||

适用于系统：`macOS Sequoia 15.6`，所用到的Kext均为现在（2025年8月9日）能找到的最新版本。

## 缺陷

* 蓝牙无法使用
* 部分USB接口无法使用
* WiFi需要通过 [HeliPort](https://github.com/OpenIntelWireless/HeliPort) 才能用

由于时间和经验有限，没时间微调配置和进行更详细的测试，难免会有更多目前没被发现的缺陷。

## 使用方法

有些内容不会写得特别详细，使用之前可以先简单阅读以下教程：
* [OpenCore Install Guide](https://dortania.github.io/OpenCore-Install-Guide/)
* [国光的黑苹果安装教程：手把手教你配置 OpenCore](https://apple.sqlsec.com/)

### 替换序列号

参照[Desktop Comet Lake | OpenCore Install Guide](https://dortania.github.io/OpenCore-Install-Guide/config.plist/comet-lake.html#platforminfo)

使用工具 [GenSMBIOS](https://github.com/corpnewt/GenSMBIOS) 生成序列号，替换掉占位的`PlatformInfo -> Generic -> [SystemSerialNumber, MLB, SystemUUID, ROM]`

### 组装EFI

解压Github Release的EFIMG.zip压缩包，将保存后的[config.plist](./config.plist)放入`/EFI/OC/`目录下。

### 准备硬件环境

#### 网络

由于使用的是itlwm而不是AirportItlwm，安装时需要连接以太网（接上网线）

https://openintelwireless.github.io/itlwm/Installation.html

~~搞不定`SecureBootModel`，不禁用进不了系统。官方教程说可以强行启动但是OC install教程说不推荐，还是放弃了~~

#### BIOS

进入BIOS，进行以下设置：

![](/img/BIOS_CFG.png)

注意：**还需要启用Resizable Bar**（启用`C.A.M. (Clever Access Memory)`和`Above 4G Decode`）

#### PE

1. 进入PE环境，使用以下工具制作启动盘：

* [Ventory](https://github.com/ventoy/Ventoy)
* [微PE工具箱](https://www.wepe.com.cn/)

2. 使用微PE里自带的DiskGenius，删除磁盘所有分区之后给磁盘重新分区：

![](/img/PARTITION_1.png)

注意：**plist配置中打开了日志写入磁盘的flag**，所以这里的ESP分区划分得比较大。如果想获得更大的可用空间，可以修改配置关闭日志写入并只划分至1000MB。

然后将剩余空间格式化为一整个分区，卷标：ToBeFormatted：

![](/img/PARTITION_2.png)

3. 将之前处理好的EFI内容（包括`com.apple.recovery.boot`目录和放入了config.plist的`EFI`目录）通过DiskGenius放入ESP分区。

### 安装

1. 在OpenCore启动菜单**先按下空格键**，然后上下方向键选择`NO NAME (dmg)`选项并回车：

![](/img/BOOT_MENU.png)

2. 进入macOS Recovery之后，使用磁盘工具将之前的ToBeFormatted分区格式化成APFS格式，然后进入`重新安装 macOS Sequoia`选项完成安装。

## 其他记录

### 关于[diff.patch](./diff.patch)

`diff -u /path/to/Sample.plist /path/to/config.plist > /path/to/diff.patch`，看下自己改了多少东西。

### 感想

装黑苹果还是太麻烦了。
下载并移动一个个Kext，用ProperTree一项项对着改。也许在我还能过暑假的时候对我来说还是小事一桩，但是现在，这点“小事”都能严重损耗我的心力。

这次是我第二次装黑苹果。
上次装的时候是在前年夏天，也是在Sonoma推出预览但是还没正式发布的时候，装了Ventura。一如这次Tahoe正式发布前装了Sequoia。

当时也是用的大体相同的配置（那张RX6600是在当时临时买来玩这个的），但是总感觉当时OpenCore相关组件对Ventura的兼容性比如今对Sequoia的好很多。
至少我记得那次是不需要禁用SecureBootModel的。

https://elitemacx86.com/threads/how-to-fix-reboot-loop-when-installing-or-updating-to-macos-sonoma-14-4-and-later.1720/

我那次装的时候，我还没有Mac电脑，装上了挣扎地用了一个星期之后就抹盘了。
但是后来，我一直对macOS的字体渲染效果念念不忘，以至于我甚至装了 [MacType](https://github.com/snowie2000/MacType) 用了很长一段时间。
然后这股欲望驱使了我买了m2的mac mini，然后在不久前的国补我又买了一台m4的mac mini。

白果总是更好折腾的，尤其是不需要移动的mac mini。虽然我买的都是丐版，但是只需要外接硬盘，用起来也很舒心。
但是，我怎么也不想遗忘，当时在用着黑苹果时，那由于和Windows严重不符的操作习惯带来的厌恶感里夹杂着的带有奇怪气味的悸动。
听说Tahoe是最后一版支持Intel的macOS时，这股悸动在加强，甚至没让我撑到Tahoe正式版发布，不过折腾的过程又一次成功磨灭了它。