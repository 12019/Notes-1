[yacto编译打包](:/126d7e412b1244819e0bf2b2b7df5574)

1\. 全编image
`MACHINE=udx710-module DISTRO=unisoc-initgc source setup-environment`
`bitbake unisoc-console-image`

2.  单独编译
    
    ```
    MACHINE=udx710-module DISTRO=unisoc-initgc source setup-environment
    bitbake -C compile linux-unisoc-4.14
    bitbake -C compile u-boot15
    bitbake -C compile chipram
    ```
3.  编译后打包
    `cd prebuilts/scripts/`
    `./pac.sh udx710_module+initgc+console+sec userdebug`
    
4.  编译后签名
    
    ```shell
    cp build-unisoc-initgc/tmp-unisoc_initgc-glibc/deploy/images/udx710-module/boot.img out/target/product/udx710-module/
    ./sign.sh udx710-module boot
    ```
5.  编译并打包
    `./build.sh udx710_module+initgc+console+sec userdebug`
    注意，这个会将以前的编译全删除，时间会非常久