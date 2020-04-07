# 容器

## 创建容器

### 新建容器

docker create
```
docker create -it ubuntu:lastest
```

docker create命令新建的容器处于停止状态，可以使用docker start命令来启动

### 新建并启动容器

docker run

等价于先执行docker create命令，再执行docker start命令。
```
docker run ubuntu /bin/echo 'hello world'
```

利用docker run来创建并启动容器时，Docker在后台运行的标准操作包括：
* 检查本地是否存在指定的镜像，不存在就从共有仓库下载
* 利用镜像创建并启动一个容器
* 分配一个文件系统，并在只读的镜像层外面挂载一层可读写层
* 从宿主主机配置的网桥接口中桥接一个虚拟接口到容器中去
* 从地址池配置一个IP地址给容器
* 执行用户指定的应用程序
* 执行完毕后容器被终止

-t      让docker分配一个伪终端并绑定到容器的标注你输入上
-i      让容器的标准输入保持打开

### 守护态运行

更多的时候。需要让docker容器在后台以守护态（Daemonized）形式运行。用户可以通过-d参数来实现。
```
docker run -d ubuntu /bin/sh -c "while true; do echo hello world; sleep 1; done"
```

容器启动后会返回一个唯一的ID，也可以通过docker ps命令来查看容器信息

要获取容器的输出信息，可以通过docker logs命令
```
docker logs [CONTAINER ID]
```

## 终止容器



