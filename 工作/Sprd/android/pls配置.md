## pls中的紫外线Gsensor配置

### 驱动

底层驱动路径

`kernel/drivers/input/misc/ltr390/ltr390_uv.c`

配置对应的I2C控制器

`kernel/drivers/input/misc/kxtj9/kionix_accel.c`

设置加载的驱动文件

`vendor/sprd/modules/sensors/libsensorclassic/Android.mk`

`device/sprd/sharkle/sp9820e_2h10/BoardConfig.mk`

### 中间层控制

`vendor/sprd/modules/sensors/libsensorclassic/acc/Acc_XR.cpp`

`vendor/sprd/modules/sensors/libsensorclassic/pls/Pls_LTR558ALS.cpp`

中间层上报光感和紫外线2种数据，需修改为只上报紫外线数据。得到数据后，要通过转换公式和校准数据计算出最终的值，然后上报给App。校准数据由于每台数据会有误差，在工厂中将每台设备与标准设备在同一环境中测试得到数据，根据误差计算出校准数据，再把校准数据写入到epprom储存，软件中从文件系统读取数据并计算。