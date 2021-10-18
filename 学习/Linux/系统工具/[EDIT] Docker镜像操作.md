# Docker镜像操作/Docker容器操作/Docker将容器保存为镜像/Docker镜像备份与迁移

https://blog.csdn.net/qq_35531549/article/details/88958715

##### [Docker安装](https://blog.csdn.net/qq_35531549/article/details/88958715)

##### [docker.io/ubunto:12.04](http://docker.io/ubunto:12.04) /bin/bash，表明从镜像 Ubuntu:12.04 启动一个容器，而这个镜像的操 作系统就是 Ubuntu:12.04。在构建容器时指定仓库的标签也是一个好习惯。

- 拉取镜像
    Docker维护了镜像仓库，分为共有和私有两种，共有的官方仓库Docker Hub(https://hub.docker.com/ )是最重要最常用的镜像仓库。私有仓库（Private Registry）是开发者或者企业自建的镜像存储库，通常用来保存企业 内部的 Docker 镜像，用于内部开发流程和产品的发布、版本控制。
    
- 要想获取某个镜像，我们可以使用pull命令，从仓库中拉取镜像到本地，如
    

```
 docker image pull library/hello-world
```

- 1

上面代码中，docker image pull是抓取 image 文件的命令。library/hello-world是 image 文件在仓库里面的位置，其中library是 image 文件所在的组，hello-world是 image 文件的名字。

由于 Docker 官方提供的 image 文件，都放在library组里面，所以它的是默认组，可以省略。

- 因此，上面的命令可以写成下面这样。

```
 docker image pull hello-world
```

- 1

- 删除镜像

```
docker image 
rm 镜像名或镜像id 
```

- 1

- 如

```
docker image 
rm hello-world 
```
