# 			[在VMWare上安装Arch Linux](https://www.cnblogs.com/freerqy/p/8502838.html)		

## 1、为什么选择Arch Linux

Arch Linux 是通用 x86-64 GNU/Linux 发行版。Arch采用滚动升级模式，尽全力提供最新的稳定版软件。初始安装的Arch只是一个基本系统，随后用户可以根据自己的喜好安装需要的软件并配置成符合自己理想的系统。　　—— 引自[Arch Linux Wiki](https://wiki.archlinux.org/index.php/Arch_Linux_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87))

　　我就为了上面的最后一句话：”用户可以根据自己的喜好安装需要的软件并配置成符合自己理想的系统“。然后自己就根据Arch Linux 的WiKi上的安装教程，再配合网上其他人的安装教程安装并配置自己的Arch Linux；另外说一句，Arch Linux的WiKi真的是非常全面，基本上可以解决日常使用中的大部分问题，而且大部分都有对应的中文翻译（这篇教程很多也直接引用了Arch Linux WiKi上安装教程的内容）。然而，就算有如此多的资源可用，但在实际安装过程中还是出现了不少问题。

　　废话少说，下面就开始安装我们的Arch Linux吧！

## 2、安装准备

### 2.1 准备

　　由于我是使用VMWare workstation虚拟机（VMWare的安装和使用就不介绍了，网上一搜一大片）来安装的Arch Linux。新建一个虚拟机，然后我为Arch Linux在WMWare上分配了1G的内存和8G的硬盘，你也可以使用更大的硬盘空间。要注意的是VMWare在新建虚拟机选择系统时是没有Arch Linux的选项的，这里我选择的是”其他Linux 3.x内核64位“，因为我使用的是x86_64的[ISO映像文件](https://www.archlinux.org/download/)；然后在虚拟机中设置下载好的ISO映像文件的路径即可，再在“虚拟机->设置->选项->高级”中勾选”通过EFI而非BIOS引导(B)“选项（其实这一步不是必须的，最要是看你使用什么方式来引导系统），最后直接启动虚拟机就可以开始正式安装Arch Linux了。

　　先给出几个有用的Arch Linux的WiKi链接：

　　1、[Installation guide（简体中文）](https://wiki.archlinux.org/index.php/Installation_guide_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87))

　　2、[VMware/Installing Arch as a guest (简体中文)](https://wiki.archlinux.org/index.php/VMware/Installing_Arch_as_a_guest_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)#.E9.80.9A.E8.BF.87_vmhgfs-fuse_.E5.85.B1.E4.BA.AB.E7.9B.AE.E5.BD.95)

上面两个链接对安装基本的Arch Linux其实就足够了，但是由于Arch Linux是“高度自治”的Linux发行版，所以在安装后需要根据自己的喜好来配置系统（例如，对于图形桌面的选择，当然前提是你需要使用图形桌面），因此你还需要搜索你需要的其他信息。

### 2.2 启动安装archlinux

　　好了，现在开始安装Arch Linux吧，使用EFI和BIOS引导时的启动界面分别如下图：

![img](https://images2018.cnblogs.com/blog/804179/201803/804179-20180303204838970-449162221.jpg)

 使用EFI引导时的启动界面

 

![img](https://images2018.cnblogs.com/blog/804179/201803/804179-20180303205022561-1978536481.jpg)

使用BIOS引导时的启动界面

无论使用哪种引导方式，均选择第一项启动就可以了，启动成功后就会进入命令行模式，此时如果在真机上安装的话，可以在此处列出efivars目录以验证启动模式来判断主板是以何种方式引导系统的（这对之后对硬盘的分区有用）：

```
ls /sys/firmware/efi/efivars
```

如果该目录不存在，系统就可能以BIOS模式启动。

### 2.3 连接到网络

　　由于Arch Linux在启动后，守护进程dhcpcd已被默认启用以探测有线设备，因此，如果是在真机上安装的话，请确保使用的是有线网络。

　　Arch Linux的安装必须使用网络才能完成，使用下面命令以验证网络是否正常：

```
ping -c 3 www.baidu.com
```

　　如果网络不正常，可能是由于dhcp服务没有开启，可以使用以下命令来开启此服务：

```
systemctl enable dhcpcd.service
```

### 2.4 更新系统时间

　　首先还是验证一下系统的时间是否正常：

```
timedatectl status
```

如果时间和当前时间对不上的话，使用下面命令来更新系统时间：

```
timedatectl set-ntp true
```

ntp服务会每隔一段时间进行一次网络对时，更新系统时间后，可以再次验证一下时间是否正常。

### 2.5 建立硬盘分区

　　硬盘如果被系统识别到，就会被分配为一个块设备，如/dev/sda；因此先查看一下硬盘的状态：

```
lsblk
```

 输出如下图：

![img](https://images2018.cnblogs.com/blog/804179/201803/804179-20180303212829628-761295067.jpg)

这里sda即是我分配给虚拟机的8GB硬盘，因为sda节点下无任何显示，所以此硬盘还没有分区；loop0和sr0可以忽略。如果硬盘已经有分区，sda节点下应当会显示如下图：

![img](https://images2018.cnblogs.com/blog/804179/201803/804179-20180303213648895-1695877903.png)

具有3个分区的硬盘

 也可以使用命令：

```
fdisk -l
```

来查看硬盘的分区情况。

接下来我们要对这8GB的硬盘进行分区，能够创建分区的命令很多，如fdisk，parted，cfdisk等，这里使用有GUI的cfdisk命令（在真机上分区时，请认真检查你的硬盘是否选择正确，如果你有多个硬盘，可能你要用来安装Linux的硬盘并不是如下所写的/dev/sda，而是/dev/sdb也说不定；或者你是想安装双系统）：

```
cfdisk /dev/sda
```

 对于一个选定的硬盘，有一些分区是必须要有的：

- 一个根分区（挂载在根目录）  /  ，
- 如果 [UEFI ](https://wiki.archlinux.org/index.php/Unified_Extensible_Firmware_Interface_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87))模式被启用，你还需要一个 [EFI 系统分区](https://wiki.archlinux.org/index.php/EFI_System_Partition_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87))，
- [Swap](https://wiki.archlinux.org/index.php/Swap_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)) 可以在一个独立的分区上设置，也可以直接建立 [交换文件](https://wiki.archlinux.org/index.php/Swap_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87))。

因为我前面设置的是EFI引导，因此需要在此处需分一个EFI分区（EFI分区推荐大小为512MB）。使用cfdisk分区命令后进入分区界面，如下图：

![img](https://images2018.cnblogs.com/blog/804179/201803/804179-20180303221300947-803896250.jpg)

分区表类型选择

 这里我们选择gpt分区表，进入之后，我就分了上面所讲的3个必要分区，分别为根分区，EFI系统分区，Swap分区：

![img](https://images2018.cnblogs.com/blog/804179/201803/804179-20180303221953373-1500287132.jpg)

使用cfdisk分好区

 分好区后确认写入分区到硬盘，然后退出分区工具，再次使用lsblk查看一下，显示如下图：

![img](https://images2018.cnblogs.com/blog/804179/201803/804179-20180303222508728-132046664.jpg)

　已分好区后的硬盘

###  2.6 格式化分区

　　分区完成后，需要对分区做格式化处理，由于这里使用了EFI分区，因为EFI分区需要FAT32文件格式（如果是在真机上已安装有Windows的情况下安装Linux成双系统，且以EFI引导系统，则EFI分区不需要再次格式化），所以需要将其格式化为FAT32格式；根分区格式化为ext4格式；设置并开启Swap分区：

```
mkfs.fat -F32 /dev/sda1
mkfs.ext4 /dev/sda2
mkswap /dev/sda3 -L Swap
swapon /dev/sda3
```

###  2.7 挂载分区

　　格式话完成后，需要将分区挂载到 /mnt ，先挂载根分区（这里是/dev/sda2）；再挂载EFI分区（这里是/dev/sda1），挂载EFI分区时，需要在/mnt上先创建 boot/EFI  目录，然后将EFI分区挂载到/mnt/boot/EFI上；Sawp分区不需要挂载：

```
mount /dev/sda2 /mnt
mkdir -p /mnt/boot/EFI
mount /dev/sda1 /mnt/boot/EFI
```

## 3、安装基本系统

### 3.1 选择软件镜像源

　　在安装基本系统之前，需要修改一下软件镜像源，不然安装基本系统时会安装不上。镜像源列表在 /etc/pacman.d/mirrorlist  文件中。

我们选择软件镜像源时，最好选择国内的镜像源，因为国内网络环境的关系，选择其他国家或地区的镜像源，安装时可能很慢或失败也不一定。

下面首先添加了阿里巴巴镜像源到一个新文件（此处为mrlist），然后从mirrolist文件中选出所有国内镜像源追加到mrlist中，然后将mirrorlist文件的内容追加在mrlist的最后面，最后将mrlist重命名为mirrorlsit：

```
echo '## China\nServer = http://mirrors.aliyun.com/archlinux/$repo/os/$arch' > mrlist
grep -A 1 'China' /etc/pacman.d/mirrorlist|grep -v '\-\-' >> mrlist
cat /etc/pacman.d/mirrorlist >> mrlist
mv mrlist /etc/pacman.d/mirrorlist
```

执行完以上命令后，可以使用以下命令来查看mirrorlist文件是否修改成功：

```
nano /etc/pacman.d/mirrorlist
```

若修改成功，会看到mirrorlist文件中的开头的内容全是国内的镜像源。最后，建议将163、清华（tuna）放在最前面。

### 3.2 开始安装系统

　　修改完软件镜像源后，然后就可以开始安装系统了：

```
pacstrap -i /mnt base base-devel vim
```

 使用-i选项会在实际安装前进行确认；安装 [base-devel](https://www.archlinux.org/groups/x86_64/base-devel/)组，可以让我们通过 [AUR (简体中文)](https://wiki.archlinux.org/index.php/AUR_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)) 或者 [ABS (简体中文)](https://wiki.archlinux.org/index.php/Arch_Build_System_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)) 编译安装软件包，如果不需要通过AUR或ABS安装软件包，则只需要安装base组就可以了 。

## 4、配置系统

### 4.1 Fstab

　　等待基本系统安装完成后，用以下命令生成 [fstab](https://wiki.archlinux.org/index.php/Fstab_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)) 文件 (用 `-U` 或 `-L` 选项设置UUID 或卷标)：

```
genfstab -U /mnt >> /mnt/etc/fstab
```

 然后使用以下命令检查一下生成的fstab文件是否正确：

```
nano /mnt/etc/fstab
```

如果生成的fstab文件正确，会看到之前分的3个分区的信息。

### 4.2 Chroot

　　切换到新安装的系统：

```
arch-chroot /mnt
```

chroot之后，当前目录就变成为 /  。此步会自动进行创建初始的ramdisk环境，但是如果以后更改了内核配置了的话，最好使用一下命令再重新生成ramdisk环境：

```
mkinitcpio -p linux
```

### 4.3 设置时区

　　然后将系统时区设为东八区：

```
ln -sf /usr/share/zoneinfo/Asia/Chongqing /etc/localtime
```

设置时间标准为UTC，并调整时间漂移：

```
hwclock --systohc --utc
```

### 4.4 配置Locale

　　这一步对使用地区和语言等进行配置。在/etc/locale.gen文件中进行配置，locale.gen是一个仅包含注释文档的文本文件。指定需要的本地化类型，只需移除对应行前面的注释符号（`＃`）即可，使用下面命令打开locale.gen文件：

```
nano /etc/locale.gen
```

然后找到下面3项，去掉每项前面的#即可：

```
en_US.UTF-8 UTF-8
zh_CN.UTF-8 UTF-8
zh_TW.UTF-8 UTF-8
```

locale-gen生成Locale信息，并列出所有启用的Locale：

```
locale-gen
locale -a
```

最后创建locale.conf文件，并提交所要使用的本地化选项，然后使用locale命令显示当前正在使用的Locale和相关的环境变量：

```
echo LANG=en_US.UTF-8 > /etc/locale.conf
locale
```

/etc/locale.conf用来配置整个系统所使用的Loacle，而这也可以由用户通过用户自己的 ~/.config/locale.conf （~表示当前用户的Home目录）来覆盖整个系统的Locale配置。

提示：建立 /etc/skel/.config/locale.conf 文件，可以在新用户的建立（新用户的建立见下文）且同时创建用户主目录（useradd -m）时，自动应用其中的Locale（会将此文件复制到新建用户的 ~/.config/locale.conf 中）。

注意：不推荐此时设置任何中文locale，因为这样做可能会导致tty显示乱码。

### 4.5 设置主机名

　　要设置主机名，创建 /etc/hostname 文件并将主机名写入该文件即可。我的主机名为freeLinux：

```
ehco freeLinux > /etc/hostname
```

然后配置主机名对应的IP到 /etc/hosts  中：

```
nano /etc/hosts
```

将其中的主机名改为你自己的主机名（我这里是freeLinux）：

```
127.0.0.1    localhost.localdomain    localhost
::1          localhost.localdomain    localhost
127.0.1.1    freeLinux.localdomain    freeLinux
```

### 4.6 网络配置

　　若使用有线网络的话，启动dhcp服务：

```
systemctl enable dhcpcd.service
```

若使用无线网络的话，则安装以下几个软件包（因为使用的时虚拟机，并未验证过）：

```
pacman -S iw wpa_supplicant dialog
```

### 4.7 设置Root用户密码

　　设置root密码：

```
passwd
```

然后输入两次密码即可。

### 4.8 创建新用户

　　因为使用root用户登陆后，root用户拥有系统的所有操作权限，这样对系统的操作非常不安全（如一不小心将系统文件删除了，怎么办？），所以需要新建一个普通用户，让其对系统的操作受到一定限制，使用下面命令新建用户free：

```
useradd -m -G wheel -s /bin/bash free
```

- `-m：创建用户主目录（/home/[用户名]）`
- `-G：用户要加入的附加组列表；此处`将用户加到`wheel`组中，之后可以给这个组执行`sudo`命令的权限
- `-s：`指定了用户默认登录shell的路径，此处设置为bash的路径``

更多创建新用户的使用请查看Arch Linux WiKi：[Users and groups（简体中文）](https://wiki.archlinux.org/index.php/Users_and_groups_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87))。

然后修改新创建用户的用户密码，和修改Root用户密码所使用的命令一样（只是需要指定要修改密码的用户名）：

```
passwd free
```

然后输入两次密码即可。

以后大部分时间我们都将使用此普通用户来工作，但由于此用户的操作权限有限，有时会对很多操作带来不便，因此需要给该用户在某些情况下提权，这就需要允许该用户所在的wheel组有执行sudo命令的权限，此时需要修改 /etc/sudoers文件 ，但请不要直接修改此文件，而是用下面的命令修改：

```
visudo
```

使用上面命令打开sudoers文件后，删除wheel组前面的注释（#）即可：

```
## Uncomment to allow members of group wheel to execute any command
%wheel ALL=(ALL) ALL
```

若执行visudo时，提示找不到vim，则请先安装vim后在执行上面的操作，执行下面指令安装vim：

```
pacman -S vim
```

### 4.9 安装grub

　　grub是一个启动引导器，同时支持EFI和BIOS方式的启动。若使用的UEFI方式引导系统，则还需要安装efibootmgr，如果是双系统的话，还需要安装os-prober，且如果使用Intel CPU的话，则需要安装 intel-ucode 并启用[因特尔微码更新](https://wiki.archlinux.org/index.php/Microcode_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87))。

因为我们使用的是虚拟机和UEFI引导方式，因此只需要安装grub和efibootmgr：

```
pacman -S grub efibootmgr
```

然后，还需要将其安装到EFI分区当中：

```
grub-install --recheck /dev/sda
```

注意：此处的 /dev/sda 后没有数字。

若提示 error:cannot find EFI directory，则说明EFI文件夹的路径不正确，找不到EFI文件夹的位置，此时就需要在上面命令中加入 efi-directory 参数指定安装路径：

```
grub-install --recheck /dev/sda --efi-directory=/boot
```

最后还需要生成一个grub的配置文件：

```
grub-mkconfig -o /boot/grub/grub.cfg
```

提示：如是在已经有Windows系统的PC上安装Linux成双系统，那么由于在安装介质环境中，此时可能检测不到Windows系统。在之后重启后进入Arch Linux后，再重新执行一遍此命令，这样就能检查到所有的系统了。

### 4.10 重启系统

　　到此，Arch Linux的基本系统的安装就完成了。现在需要重启以进入新系统：

```
exit
umount -R /mnt
reboot
```

下图显示为使用exit命令退出chroot环境前后的命令提示符：

![img](https://images2018.cnblogs.com/blog/804179/201803/804179-20180304142135753-1258547425.jpg)

退出chroot环境之前

![img](https://images2018.cnblogs.com/blog/804179/201803/804179-20180304142208840-809770027.jpg)

退出chroot环境之后

若使用exit无法退出chroot环境，请多输入几次或则请使用Crtl+D组合键多试几次。退出chroot环境后再执行之后的操作即可。

重启系统之后，会出现下图启动界面：

![img](https://images2018.cnblogs.com/blog/804179/201803/804179-20180304161903252-492066628.jpg)

 

重启之后的启动界面 

 选择第一个就进入到我们新安装的Arch Linux了，如下图：

 ![img](https://images2018.cnblogs.com/blog/804179/201803/804179-20180304162236361-511501660.jpg)

进入新系统之后的界面

 然后我们直接使用前面创建的free用户登陆到系统，如下图：

![img](https://images2018.cnblogs.com/blog/804179/201803/804179-20180304162419146-1474607689.jpg)

使用free登陆到系统

这样，Arch Linux的基础系统就算安装完成了。

由于还没有安装桌面，所以登陆后还处在命令行模式。之所以使用free用户登陆而不直接使用root用户登陆（实际上这也是安全的方式，除非你确定知道你想以root用户登陆做些什么），是因为我之后只想默认在free用户下安装和启动桌面；而如果使用root登陆时，默认还是继续运行在命令行模式，而如果想要在root用户下使用桌面，则还需要相关的配置。

桌面及其基本软件的安装等配置，之后再写吧！