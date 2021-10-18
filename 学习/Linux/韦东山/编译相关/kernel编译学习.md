1、 打补丁

2、设置配置项

3种方式

​ 2.1 使用make menuconfig命令手动配置

​ 2.2 使用kernel中芯片方提供的参考配置

配置项位于‘arch/arm/configs/’ 目录，在其中找到相似的配置文件，如：s3c2410_defconfig，然后执行'make s3c2410_defconfig'

​ 2.3 使用主板厂家提供的配置文件（这里是jz2440）

```shell
cp config_ok .config
make menuconfig
```

3、 编译

​ make -jx

4、生成的镜像位于‘arch/arm/boot/’

5、生成适用uboot引导的镜像

需要使用mkimage 工具，使用以下命令安装

```shell
sudo apt-get install u-boot-tools
```

然后

```
 make uImage
```

生成‘arch/arm/boot/uImage’

6、烧写uImage