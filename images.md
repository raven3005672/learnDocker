# 镜像

## 获取镜像

docker pull NAME[:TAG]

```shell
docker pull ubuntu
docker pull ubuntu:lastest
docker pull ubuntu:14.04
```

```shell
<!-- 默认注册服务器 -->
docker pull registry.hub.docker.com/ubuntu:lastest
<!-- dockerPool社区镜像 -->
docker pull dl.dockerpool.com:5000/ubuntu
```

利用该镜像创建一个容器，其中运行bash引用

```shell
sudo docker run -t -i ubuntu /bin/bash
```

## 查看镜像信息

docker images列出本地主机上已有的镜像

docker tag命令为本地镜像添加新的标签
```
sudo docker tag dl.dockerpool.com:5000/ubuntu:latest ubuntu:lastest
```

docker inspect获取镜像的详细信息
```
docker inspect [IMAGE ID]
docker inspect -f {{".Architecture"}} [IMAGE ID]
```

## 搜寻镜像

docker search TERM

* --automated=false   仅显示自动创建的镜像
* --no-trunc=false    输出信息不截断显示
* -s, --starts=0      指定仅显示评价为指定星级以上的镜像

## 删除镜像

docker rmi IMAGE

强行删除镜像，可以使用-f参数

docker rmi -f ubuntu

当镜像创建的容器存在时，镜像文件默认是无法被删除的。

先利用ubuntu镜像创建一个简单的容器
```
docker run ubuntu echo 'hello'
```

使用docker ps -a可以看到本机上的所有容器
```
docker ps -a
```

删除容器
```
docker rm [CONTAINER ID]
```

## 创建镜像

* 基于已有镜像的容器创建
* 基于本地模板导入
* 基于Dockerfile创建

### 基于已有镜像的容器创建

docker commit [OPTIONS] CONTAINER [REPOSITORY[:TAG]]
* -a, --author=""   作者信息
* -m, --message=""  提交信息
* -p, --pause=true  提交时暂停容器运行

#### 创建一个新镜像

启动一个镜像，并在其中进行修改操作：
```
docker run -ti ubuntu /bin/bash
root@44d21c43d1f6:/# touch test
root@44d21c43d1f6:/# exit
```

commit提交为一个新的镜像，提交时可以使用ID或名称来指定容器
```
docker commit -m "Added a new file" -a "Docker Test" 44d21c43d1f6
<!-- 返回 -->
sha256:484b27248ee1a23c8130a4ad3e801ae61da42dc1c08fcce1e465c95e53145b5b
```

#### 基于本地模板导入

```
sudo cat ubuntu-14.04-x86_64-minimal.tar.gz | docker import - ubuntu:14.04
```

### 存储和载入镜像

* docker save
* docker load

存储
```
docker images
docker save -o ubuntu_14.04.tar unbuntu:14.04
```

载入
```
docker load --input ubuntu_14.04.tar
docker load < ubuntu_14.04.tar
```

### 上传镜像

docker push NAME[:TAG]
```
docker tag test:lastest user/test:lastet
docker push user/test:lastest
```
