1、打补丁

2、配置

make 100ask24x0_config

3、 编译

make -j8

uboot常用命令操作

print : 打印环境变量

set  ： 设置变量值

save ： 保存变量值

reset ： 重启

4、环境变量

![03fb4f947fed4f9dae390ac7dc896697](https://user-images.githubusercontent.com/31771253/137620986-d2a2f0ff-17b8-4e51-b27b-d3d1e67e4535.png)


设置环境变量

```
set $() x
save
reset
```
