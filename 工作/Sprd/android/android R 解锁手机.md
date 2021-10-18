android R 解锁手机

1 adb reboot bootloader

2 make -j12 fastboot 生成fastboot工具  在./out/host/linux-x86/bin/fastboot下

3 使用 fastboot oem get\_identifier\_token 获取设置号

4 拷贝 cp ./vendor/sprd/proprietories-source/packimage\_scripts/signidentifier\_unlockbootloader.sh .  cp ./vendor/sprd/proprietories-source/packimage\_scripts/signimage/sprd/config/rsa4096\_vbmeta.pem .

5 ./signidentifier\_unlockbootloader.sh device\_sn rsa4096\_vbmeta.pem signature\_file  其中device-sn 为第二步获取到的

6 sudo ./fastboot flashing unlock\_bootloader signature\_file    根据提示解锁手机