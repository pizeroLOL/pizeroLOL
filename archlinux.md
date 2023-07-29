# Archlinux

> 当你在思考是 Linux 还是 Windows 的时候，就已经证明你不需要 Linux。

下次想换成 nixos 玩玩。

## 安装

[ventoy]: https://www.ventoy.net/cn/index.html

因为要上课摸鱼顺便避开学习的睿智系统，
所以得有办法启动或者用虚拟机的方式启动一个 Windows 系统，
所以选择了使用 Ventoy。
然而我已经很久没更新这玩意了，请结合实际情况来进行配置。

> **Warnning**
>
> 您的资料将会丢失，请谨慎操作。

首先，去 [官网][ventoy] 根据说明自己安装在移动硬盘上，设置显示硬盘，gpt 格式，格式化为 ntfs 分区，在磁盘后面预留一定容量，然后安装。

> **Tips**
>
> 这里有一点坑，非 ntfs 使用 vhd 启动 Windows 会开不起来。
> 除非你想折腾，还是直接用 ntfs 得了。

往你刚刚安装好的 Ventoy 硬盘中扔个 archlinux 镜像，重启。
进入启动选项，选择 Ventoy，选择刚刚你扔的镜像。

## 安装 Archlinux 流程

这时候你应该用另一台设备打开 Archlinux 官网，找到 Installation Guide。

如果你是新手，请不要去找别的奇奇怪怪的 csdn 或者 blog，请你回到 Windows，
你会觉得窗口如此的美好。

根据配置引导配置好该配置的，比如说分区，时区，一些必要的软件包什么的。

> 小白请老老实实用 ext4 之类的，不容易出事故。

这里我留了 50G 给根目录，用的 btrfs，8G swap，还剩的 6G 当临时空间使（没挂载为 tmp）。

记住你格式化的UUID，一会要用。

如果你用 Ventoy，先暂时不用安装引导。

## 引导

在 `<你安装了 Ventoy 挂载位置，就是那个 ntfs 分区的>/ventoy/ventoy_grub.cfg` 新增以下内容。

```config
menuentry "Arch Linux" --class=custom {
    set gfxpayload=keep
    insmod gzio
    insmod part_gpt
    #insmod btrfs # btrfs 专用选项，不是 btrfs 不用选
    echo "setting boot root path"
    set root=($vtoydev,gpt<根目录第几个分区，从1开始>)
    echo "setting root path"
    linux /boot/vmlinuz-<你安装的内核包名称> root=UUID=<刚刚让你记住的UUID> 
    echo "initrd ucode"
    #initrd /boot/<CPU 微码，根据你安装的包名来> # 没有不用选 
    echo "initrd system"
    initrd /boot/initramfs-<你安装的内核包名称>.img
    echo "start boot"
    boot
}
```

重启，完事。

### 使用的软件包

[清华源]: https://mirrors.tuna.tsinghua.edu.cn/help/archlinux/

`pacstamp` 记得终止去看一眼 `/etc/pacman.d/mirrorlist` 里面的东西，
如果感觉太慢就去 [清华源] 把源换了。

> 以下内容默认你已经安装好了。

进系统换源，在 [清华源] 里面找到 archlinuxcn，加进 `/etc/pacman.conf`。

安装 aur助手。

```bash
pacman -Syyu && pacman -S base-devel paru
```

#### 系统

- `base`：别忘了这鬼东西，之前忘了一次还得重新把盘清空重下。
- `linux-zen`：没啥区别，单纯是当时想玩 waydroid 和 anbox 下的。

#### 文件系统

`btrfs-progs`：记得在重启的时候前看一眼你选的文件系统要不要装一些稀奇古怪的东西。

#### 网络

`networkmanager`： 记得启动服务

#### 编辑器

- `helix`：拿来玩。
- `neovim`：命令行底下干正事用的。
- `visual-studio-code-bin`：aur，得安完 aur 助手再说。我最常用的编辑器。

#### 桌面环境

- `wayland`：让你能跑桌面的协议。
- `xwayland`：让 x.org 应用跑在 wayland 上。
- `xorg-server`：桌面的协议，以备不时之需。
- `plasma-desktop`：安桌面。
- `kde-applications`：常用软件包，俗称 kde 全家桶。konsole，kate，yakuake什么的都在，记得把用不到的手动删一下。
- `sddm`：记得命令行能跑通了再启用这鬼东西，懒得切 tty。

#### 浏览器

- `firefox`
- `chromium`

没什么好说的，很常规。

#### 输入法

- `fcitx5`：记得看一眼 Archlinux 的文档，要配置的东西很多。
- `fcitx5-gtk`：fcitx5 gtk 兼容。
- `fcitx5-qt`：fcitx5 qt 兼容。
- `fcitx5-configtoo`：配置工具。
- `fcitx5-chinese-addons`：中文相关的东西，如果你安的是 kde，记得去语言和输入设置改一下东西。

> 别忘了安装中文字体。

#### 一些稀奇古怪的东西

- `linuxqq-appimage`：QQ，aur 包，安完了 aur 助手再说。
- `icalingua`：第三方QQ客户端。aur，archlinuxcn。
- `spectacle`：`kde-applications` 里面用来截屏的，记得自己改一下快捷键。
- `watt-toolkit`：github 加速器，避免卡死的情况。
- `dingtalk`：睿智钉钉，没法交作业。
- `qbittorrent`：种子下载器，可以找一下 XIU 的 tracker 列表，巨好用。
- `vlc`：播放器，理论上直接用浏览器也行。
- `obs-studio`：录屏。
- `pandoc`：万物转 Markdown，Markdown 转万物。
- `texlive-bin`：记得去看一眼 Archlinux 说明，有些坑。

完整见 [Archlinux pkgs](./archlinux-pkgs.txt)
