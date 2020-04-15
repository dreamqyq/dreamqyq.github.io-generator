---
title: "Deepin与Windows8.1双系统启动项恢复"
date: 2018-05-14
draft: false
tags: ["Linux"]
categories: ["Linux"] 
---

## 前言：
这两天在折腾双系统，打算体验一下传说中的Linux的美妙之处，由于是第一次使用Linux，并且主旨不在折腾系统，只是想尝试Linux的环境以及更优雅的写代码，所以选择了相对不折腾的Deepin，但是还是遇到了各种奇葩的问题。比如这篇文章要写的启动项。

## 问题起因
安装完deepin以后，启动的时候自动进入的是的deepin的引导程序，然后发现第一项是deepin，如果五秒不操作默认进入deepin的系统，我想实现让我的Windows系统放在第一位，于是Google了一些解决办法。

- 首先说是在控制中心的启动菜单中，可以通过拖拽高亮项进行改变启动顺序，发现拖拽不了
- 然后尝试改一下grub.cfg的配置（在boot/grub目录下），把Windows的启动设置移动到deepin的上面
```sh
### BEGIN /etc/grub.d/30_os-prober ###
menuentry 'Windows Boot Manager (on /dev/sda2)' --class windows --class os $menuentry_id_option 'osprober-efi-7C8F-9031' {
	insmod part_gpt
	insmod fat
	set root='hd0,gpt2'
	if [ x$feature_platform_search_hint = xy ]; then
	  search --no-floppy --fs-uuid --set=root --hint-bios=hd0,gpt2 --hint-efi=hd0,gpt2 --hint-baremetal=ahci0,gpt2  7C8F-9031
	else
	  search --no-floppy --fs-uuid --set=root 7C8F-9031
	fi
	chainloader /EFI/Microsoft/Boot/bootmgfw.efi
}
### END /etc/grub.d/30_os-prober ###
```

最后发现有一些问题，不但没有把启动顺序改掉，还莫名多了一项`advanced options for deepin 15.5 gnu/linux`的选项；而且每次关机后再开机，这份配置文件会重置（但是多的那么一项没有消失），而且在启动菜单里选择第一项（即deepin启动），实际会默认进入advanced，但是启动菜单里没有advanced options，grub.cfg文件改了因为备份文件被删掉来了导致恢复不了，只好退而求其次，只是想恢复到刚开始安装的那个样子就好，于是寻找了一些**重置grub.cfg文件**的方法，如下所示。

## 不那么成功的解决方案

虽然查到了很多方法，但是因为Windows是EFI模式启动，所以常规方法会有问题，双系统情况下，deepin官网也有两个[解决方案](https://wiki.deepin.org/index.php?title=%E4%BF%AE%E5%A4%8D%E5%90%AF%E5%8A%A8#EFI.2BGPT.E6.A8.A1.E5.BC.8F.E4.B8.8B.E4.BF.AE.E5.A4.8DGRUB2.E5.8F.8C.E7.B3.BB.E7.BB.9F.E5.BC.95.E5.AF.BC)(附带链接)，第一个尝试失败，下面是我使用的第二种方法，有一些问题，但至少让启动菜单和实际启动相互间有了正确的联系（即不会出现启动菜单选择的默认启动项是deepin，实际启动过程中默认启动项是advanced options的情况）

- 首先进入系统前通过启动盘启动（本人是联想笔记本，按<kbd>F12</kbd>）
- 进入系统待安装界面，按<kbd>Ctrl</kbd>+<kbd>Alt</kbd>+<kbd>F1</kbd>（因为可能没反应，可以试着再按<kbd>Ctrl</kbd>+<kbd>Alt</kbd>+<kbd>F2</kbd>）到达可以输入命令的界面
- 输入以下命令
```sh
$ sudo service lightdm stop  //关闭图形界面。不过由于目前的界面本来就不是图形界面，亲测可以这一句可以省略
$ startx
```
- 进入Live CD系统后打开终端，输入以下命令
```sh
$ sudo su
# mount /dev/sda4 /mnt（注意先确认自己的 / 分区即deepin的系统分区是 sdaX）
# mount /dev/sda1 /mnt/boot/efi //注意你的efi是不是sda1，如果不是要改成你自己电脑中Windows的EFI所在磁盘
# mount -t proc proc /mnt/proc
# mount -t sysfs sys /mnt/sys
# mount -o bind /dev /mnt/dev
# mount -t devpts pts /mnt/dev/pts/
# chroot /mnt
# grub-install /dev/sda1
# update-grub2
```

然后正常情况下就修复完成了，重启即可生效

## 关于开机启动页面的时长

我觉得开机启动页面的各个选项还是很有用的（包括可以用来恢复系统等），所以本文就不介绍如何关闭这个页面啦。下面说一下开机启动页面的等待时长，因为它默认只有5s，稍微没反应过来就结束了这个页面进入了系统，我想将其的等待时间变为30s，该如何操作呢？

1. 进入终端
2. 输入 `sudo vi /etc/default/grub`
3. 看到该文件最后一行：` GRUB TIMEOUT="X" `，其中X表示的就是你想让它在启动页面停留多久，把它改成你想改成的数字就好啦。改好之后保存并退出。
4. 在终端执行：`sudo update-grub` 用来更新grub设置 。
5. 重启后就会发现你设置的启动延时已经生效啦。
