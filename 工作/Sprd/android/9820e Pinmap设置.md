## IO属性

- Function 功能选择(Function0~3)
- 上下拉设置(WPD,WPU,X)
- 驱动能力设置(DS0~X)
- sleep时的上下拉设置(WPD,WPU,X)
- sleep时的输入输出设置(Input,Output,Hiz)
- 强上拉的设置(WPUS,X)
- AP或CP的sleep控制—AP+CP架构(AP,CP0~CP2)
- 其他(PIN_ctrl0~3)

## 配置文件

根据不同芯片选择

`idh.code/u-boot15/board/spreadtrum/sp9820e_2h10/pinmap-sp9820e_2h10.c`

## 驱动文档核心

SC9832 Android 6.0 客户驱动配置文档 V1.0 重要

## 基本步骤总结

1.  在kernel/drivers/...的对应目录下添加驱动程序
2.  在Makefile文件添加要编译的文件
3.  在Kconfig添加配置项
4.  在板级defconfig文件中开启编译项
5.  修改板级dts文件的硬件注册信息
6.  添加HAL层文件
7.  Android.mk和BoardConfig.mk中添加编译HAL层文件
8.  设置设备文件的权限和上下文