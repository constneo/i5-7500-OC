# 黑苹果

## 台式机配置

```
System  台式机   PRIME B250M-PLUS

ASUSTeK   PRIME B250M-PLUS    Intel 200

CPU: Intel(R) Core(TM) i5-7500 CPU (x1) 4C/4T

内存: Team Group Inc. ChannelA-DIMM1 2400 8G
     Team Group Inc. ChannelA-DIMM2 2400  8G
显卡: NVIDIA GeForce GTX 1050 Ti VEN_10DE&DEV_1C82  不免驱但可以驱动
     Intel(R) HD Graphics 630 VEN_8086&DEV_5912 免驱

网卡: Intel(R) Wi-Fi 6E AX210 160MHz VEN_8086&DEV_2725 免驱到 11/12
     Intel(R) Ethernet Connection (2) 1219-V VEN_8086&DEV_15B8

声卡: High Definition Audio i1 VEN_10EC&DEV_0887 免驱

蓝牙: 英特尔(R)无线 Bluetooth(R) VID_8087&PID_0032 免驱到 11/12

确盘: Samsung SSD 980 1TB NVME
     Samsung SSD 860 EVO 250GB SATA
     WDC WDS500G2BOC-00PXHO NVME

```

如果核显安装黑苹果,显示器不能插在主板的 VGA 接口上!

绿色免驱;黄绿色:免驱到 11/12;黄色不免驱但可以驱动;红色无法驱动;其他不涉及不影响

## BIOS 设置

无论使用哪种方法进行安装，BIOS 设置的步骤是不能免去的。各个机型需要做的具体设置不太一样，但总的来说以下几项是基本操作：

- 关闭以下选项：Fast Boot、CSM、CFG Lock（解锁 CFG 写保护）、Secure Boot、VT-d，OS Type 选择 Other，清空 Secure Boot Key，主板自带 WiFi 和蓝牙
- 开启以下选项：VT-x（用于支持虚拟化，可不开）、Execute Disable Bit、iGPU/IGFX（如果用核显）、Above 4G Decoding、XHCI-Handoff

- CSM（Compatibility Support Module，兼容性支持模块）在 BIOS 中扮演着重要角色，它允许系统同时支持 UEFI（统一可扩展固件接口）启动和传统的 MBR（主引导记录）

如果有相同的配置完全可以按照本文下面的步骤操作，开机按下 F2 或 DEL 进入 BIOS，做以下修改：

- Exreme Tweaker -> X.M.P -> 开启（如果你的内存条支持 XMP）
- Advanced
  - CPU Configuration -> Execute Disable Bit -> 开启
  - CPU Configuration -> SW Guard Extensions -> 关闭
    - CPU Power Management Control -> CFG Lock -> 关闭
  - Advanced System Agent
  - VT-d -> 关闭
  - Graphics Configuration
    - Primary Display -> IGFX（要用核显需要先选这个，保存退出再进，不用核显设为 PEG 或 Auto）
    - iGPU Multi-Monitor -> 开启（不用核显不需要）
    - DVMT Pre-Allocated 128M（或更高，不用核显不需要）
  - Onboard Devices Configuration
    - Asmedia Back/Front USB 3.1 Controller -> 关闭（关闭 USB3.1 总线，笔者不需要所以关闭了）
    - Bluetooth Controller -> 关闭（自带蓝牙无法驱动，关闭）
    - Wi-Fi Controller -> 关闭（自带 WiFi 无法驱动，关闭）
- Boot
  - Fast Boot -> 关闭
  - Above 4G Decoding -> 开启
  - CSM -> 关闭
  - Secure Boot -> 关闭
    - 进入 Key Management 菜单，找到 Clear Secure Boot Keys，回车清空
    - OS Type 选择 Other（如果你的主板这里选择 Other 会导致 CSM 绑定启动，选择 Windows8.1 或 Windows UEFI）

## 将 macos 刻录到 U 盘

- `[HeiPG.cn]Install.macOS.Monterey.12.7.6(21H1320).dmg` 这个没下载,未尝试
- `macOS Monterey 12.7 DMG by techrechard.com.dmg` 这个刻录后,不显示 install macOS 选项
- `Hackintosh.club-macOS Sequoia 15.5.dmg` 最终用的这个,这个镜像存放在`天翼云盘`

其他镜像可以从这里找到: [macOS 自制镜像下载 - 黑果之家 - Hackintosh.Club](https://hackintosh.club/d/10000080)

### balenaEtcher(推荐)

- balenaEtcher 支持多语言
- 从文件烧录,选择下载的`Hackintosh.club-macOS Sequoia 15.5.dmg`
- 选择目标磁盘
- 现在烧录

### TransMac

- 下载 TransMac
- 推荐下载 .dmg 镜像?不确定,暂时下载的 iso 镜像
- 在左侧磁盘列表,右键 U 盘
- Format Disk for Mac
- 输入一个名字
- 再次右键 U 盘
- Restore with Disk Image
- 选择下载的 .dmg 或 .iso 镜像

刻录完成后,通过 `DiskGenius` 查看 U 盘分区

会有两个分区,能在 window 下看到的只有 EFI 分区

## 将目标磁盘分区

这里的目标磁盘就是将要安装 macos 的磁盘

- EFI
- macos

如果不在这里分区,就需要在安装过程中分区,增加 EFI 分区

如果已安装完成,忘记添加 EFI 分区,参考后续步骤.

目标磁盘增加 EFI 分区的目的

是在安装完成后,不使用 U 盘,引导 macos

## 给磁盘增加 EFI 分区

安装的时候,忘记添加 EFI 分区

1. 查看磁盘信息  
   打开终端，输入以下命令，确认磁盘标识符（如 `disk0`）和现有分区结构：

```bash
diskutil list
```

记录 macOS 所在磁盘（如 `disk0`）和 APFS 容器（如 `disk0s2`）。

2. 缩小 APFS 容器腾出空间

```bash
diskutil apfs resizeContainer /dev/disk0s2 450G
```

- 将 `disk0s2` 的容量从 500GB 缩小到 450GB（留出 50GB 空间，实际 EFI 仅需 300MB，多余空间可后续合并）。

3. 创建 EFI 分区  
   使用 `gdisk`（需安装，通过 `brew install gdisk`）：

```bash
sudo gdisk /dev/disk0
```

- 输入 `n` 新建分区：
  - 分区号：默认
  - 起始扇区：默认（紧接现有分区）
  - 大小：`+300M`（300MB）
  - 类型代码：`EF00`（EFI 分区）
- 输入 `w` 保存并退出。

4. 格式化 EFI 分区

```bash
sudo newfs_msdos -F 32 -v EFI /dev/disk0s3  # 假设新分区为 disk0s3
```

5. 挂载并复制 OpenCore/Clover 文件

```bash
sudo mkdir /Volumes/EFI
sudo mount -t msdos /dev/disk0s3 /Volumes/EFI
cp -R /path/to/your/EFI /Volumes/EFI/
```

#### 方法 2：使用磁盘工具（需备份数据）

1. 备份数据（此方法会清空磁盘）。
2. 打开 磁盘工具 → 选择磁盘 → 点击 抹掉：
   - 格式：`GUID 分区图`（确保创建 EFI 分区）。
3. 重新安装 macOS 或从 Time Machine 恢复。

```bash
diskutil list
mount /Volumes/EFI
```

## 制作 EFI

[OpenCore 模板](https://github.com/5T33Z0/OC-Little-Translated/tree/main/F_Desktop_EFIs/Config_Templates/plist)

## 使用 U 盘引导安装

- BIOS 设置 U 盘启动
- 进入 BIOS，切换到 Boot 选项卡，选择 U 盘分区进行引导。
- 强烈建议把 U 盘插在 USB2.0 接口。

如果 oc 没有 install macos 选项,更换 EFI 文件,具体原因还未研究

最终选用的 [ASUS-B250M-V-I5-7500-macOS-Sequoia](https://github.com/aa937/ASUS-B250M-V-I5-7500-macOS-Sequoia)

## 安装过程

- 使用西部数据 SN550 500GB 硬盘
- 将该硬盘格式化为 APFS 格式,将其命名为`macos`,这里应该增加一个 EFI 分区,双硬盘双系统,各自管理各自的 EFI
- 在 windwos 下无法识别该硬盘的格式
- 安装到该磁盘
- 安装期间会重启数次,每次都选择`macOS installer`,不要选错
- 可能会卡住,如果卡住,请按下`ctrl+alt+delete`强制重启
- 重启数次后,出现`macos`,这个名称是在格式化磁盘的时候定义的.
- 安装完成

## OC 配置

- ProperTree:python GUI
- OCAuxiliaryTools:跨平台
- OCC(Opencore Configurator):macos,第三方应用

- OCC 官网下载地址为：https://mackie100projects.altervista.org/download-opencore-configurator/
- [OCC 2.78.0.2.zip | 123 云盘](https://www.123912.com/s/flFSTd-6tzW)

## OC 主题

- [acidanthera/OcBinaryData 官方主题?](https://github.com/acidanthera/OcBinaryData/tree/master)
- [LuckyCrack/OpenCore-Themes: Minimal Themes For OpenCore v0.6.6](https://github.com/LuckyCrack/OpenCore-Themes)
