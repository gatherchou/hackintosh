# XPS 9360 CPU i7-7560u 黑苹果记录 

## 前言

通过此次黑苹果安装研究，总结安装流程及问题。

- 本流程安装为MacOS 单系统，顺利引导进入安装界面后，直接抹除整个磁盘安装系统，随后使用U盘PE系统恢复clover引导。

## 准备

  - 苹果镜像： https://mirrors.dtops.cc/iso/MacOS/daliansky_macos/
  - U盘制作工具：https://www.balena.io/etcher/
  - 分区管理工具：https://www.diskgenius.cn/
  - clover配置：https://github.com/hoanX/xps13-9360-i7-7560u
  
  黑果小兵的部落阁提供的镜像自带PE镜像，可在进不了mac系统时临时补救。

## 阶段一

1. 使用ethcher 制作U盘启动盘，将黑果小兵部落阁的镜像写入U盘

2. 制作完镜像后，复制 `hoanX` Clover 目录覆盖 EFI 文件夹中 Clover 的目录

3. 设置U盘MacOS分区 `EFI/Clover/config.plist` （此部操作防止安装过程中IGPU报错）

    - 显卡设置中 `ig-platform-id` 设置为 `0x12345678`
    - 设备设置中 `IntelGFX` 设置为 `0x12345678`

## 阶段二

1. 重启进入 `BIOS` 设定如下设置

    - SATA：AHCI
    - Disable Secure Boot
    - Enable VT
    - Disable VT-D
    - Disable SD card reader 
    - 设置两个启动引导
  
        - `Boot Sequence`  >> `Add Boot Option` >> `EFI/Clover/CLOVERX64.efi`
        - `Boot Sequence`  >> `Add Boot Option` >> `EFI/Clover/tools/DVMT.efi`

2. 保存 `BIOS`，重启进入 `DVMT.efi` 引导 

    a. 首先命令 setup_var 0x785 回车, 查看是否有设定参数，如果无参数切勿执行以下命令。

      - setup_var 0x4de 0x00

      - setup_var 0x785 0x02

      - setup_var 0x786 0x03

3. exit 退出 `grub` ，重启进入 `MacOS` 安装引导, 顺利的话跑码结束后，进入苹果读条，然后进入安装界面， 

    安装过程中多次重启可能需要手动选择进入引导

## 阶段三

1. 配置 U盘 `EFI/Clover/config.plist` 

    - 显卡设置中 `ig-platform-id` 设置为 `0x59260002`
    - 设备设置中 `IntelGFX` 设置为 `0x59268086`

2. 更新 `WhateverGreen`, `LiLu` 覆盖 EFI/Clover/kexts/下 `whatevergreen.kexts` 和 `lilu.kexts`

    - `WhateverGreen` : 1.4
    - `Lilu`: 1.4.5

3. 重启后，确认系统显存是否变为 `1536MB` 

4. 再次重启，引导进入 PE 系统，将U盘中引导 `MacOS` 的 `EFI/Clover` 复制到磁盘EFI分区中的 `EFI目录下`

5. 重启进入 `BIOS` ，删除之前的U盘引导，和 `DVMT` 引导，并设置磁盘 `EFI/Clover/CLOVERX64.efi` 引导, 保存并重启

## 参考

- 教程参考
  - https://www.bilibili.com/video/BV1x54y1X7tS
  - https://www.bilibili.com/video/BV1fE411W7ei

- EFI引导

  - https://github.com/hoanX/xps13-9360-i7-7560u
