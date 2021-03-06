## 配置

`i5-6300HQ` `16GB` `PM961 512G` `1080p` 

### 当前运行情况

- 10.14 正式版，型号为 MacBook Pro (15-inch, 2016)
- CPU 变频正常（最低 800 Mhz）
- HDMI 和 Typec 热插拔外接显示器正常
- Sleep 正常，Hiberation 禁用（可以有，但没必要）
- 电量显示正常
- 触摸板使用 `VoodooI2C`
- 外放和耳机正常
- 外接 ExFat 移动硬盘正常
- 蓝牙鼠标正常，其他未测试

### 未测试

- 雷电
- SD卡（没这个需求，在 BIOS 里禁用了）

## 说明

配置主要来自 [jardenliu/XPS15-9560-Mojave](https://github.com/jardenliu/XPS15-9560-Mojave) 

本 Repo 会尽量精简配置（如果精简后能日常使用一段时间，我就默认精简掉的东西是无用的），所以难免会出现一些问题，欢迎提 issue。

本 Repo 只是简单介绍一些安装情况和问题，具体教程请参考 Github 上其他 repo（ kext 放在 clover 里就可以了 ）。

另外，使用这个 Clover 在登录 Apple 账户前，请先使用 `Clover Configurator` 随机下 `SMBIOS/System/Serial Number`。

## 问题

### 先安装 WIN10 再安装 macOS

安装WIN10后，默认的EFI分区只有 100MB，而 macOS 要求的 EFI 分区大小至少是 **200MB**，因此会出现无法为macOS新建分区的情况。

解决方法就是**扩大EFI分区到 200MB 以上**就可以了（如果WIN10有恢复分区，可以直接把恢复分区删了扩展到EFI分区，WIN10在更新后会自动重新在分区最后增加恢复分区。

### 开机启动 macOS

如果想要开机直接启动 macOS，可以在 `Clover Configurator-Boot` 中设置 `Default Boot Volume` 为你的macOS 卷的名字，并且设置 Timeout 为 0（设置为 -1 表示不自动启动）。

另一种方式是勾选 `fast` 选项，勾选 `fast` 无法按任意键进入 Clover 界面

### 原生NTFS读写

原生NTFS读写需要**关闭WIN10的Hiberation**：在 powershell 里运行 `powercfg -h off` 后重启一下。

回到macOS，使用 `Disk Utility` 查看 WIN10 分区的 UUID，之后在终端执行

```
sudo echo "UUID=xxx none ntfs rw,auto,nobrowse" >> /etc/fstab
```

重启后，应该会在 finder 中看到 WIN10分区，如果没有可以在finder中 `command+shift+G` 进入 `/Volumes`，将其放入个人收藏中即可。

### 驱动耳机

默认下外放应该是正常的。耳机则需要安装 `PostInstall/ALC298PluginFix` ，直接运行 `install双击自动安装.command` 即可。

### 睡眠问题

```
sudo pmset -a hibernatemode 0
sudo pmset -a disksleep 0
sudo pmset -b tcpkeepalive 0
sudo pmset -b standbydelay 3600
```

### CPU 变频

我的 CPU 是 `i5-6300hq`， `CPUFriendDataProvider.kext` 中的数据是针对这个 CPU 的。

所以其他型号的 CPU 请自行寻找合适的 `CPUFriendDataProvider.kext` 或者使用 [CPUFriend的教程](https://github.com/acidanthera/CPUFriend/blob/master/Instructions.md) 。需要注意的是，你需要提供的是一个针对你的 CPU 型号**已经修改过的**一个 plist 文件。

修改教程可以参考 [开启完整HWP(SpeedShift)电源管理特性](http://bbs.pcbeta.com/viewthread-1737021-1-1.html) 。

### Framebuffer, USB 和 Audio

请看 [Intel FB-Patcher v1.4.3](https://www.tonymacx86.com/threads/release-intel-fb-patcher-v1-4-3.254559/)

### 单指和双指的单击延迟

参考 [is-it-possible-to-get-rid-of-the-delay-between-right-clicking-and-seeing-the-con](https://apple.stackexchange.com/questions/218179/is-it-possible-to-get-rid-of-the-delay-between-right-clicking-and-seeing-the-con)

macOS 有一个检测手势的延时。单指单击的延迟是 `双击拖移` 造成的，双指单击的延迟是 `智能缩放` 造成的。因此想要降低单击的延迟，前者可以改用 `三指拖移`，后者取消即可。