# 数据管理

* 数据卷（Data Volumes）
* 数据卷容器（Data Volume Containers）

## 数据卷

* 数据卷可以在容器之间共享和重用
* 对数据卷的修改会立马生效
* 对数据卷的更新，不会影响镜像
* 卷会一直存在，直到没有容器使用

### 在容器内创建一个数据卷

docker run命令的时候，使用-v标记可以在容器内创建一个数据卷。多次使用-v标记可以创建多个数据卷。
```
docker run -d -P --name web -v /webapp training/webapp python app.py
```

### 挂载一个主机目录作为数据卷

```
// 默认权限是读写（rw）
docker run -d -P --name web -v /src/webapp:/opt/webapp trainging/webapp python app.py
// ro权限指定为只读
docker run -d -P --name web -v /src/webapp:/opt/webapp:ro trainging/webapp python app.py
```

### 挂载一个本地主机文件作为数据卷

```
docker run --rm -it -v ~/.bash_history:/.bash_histruy ubuntu /bin/bash
```

这样可以记录在容器输入过的历史命令了。

推荐的方式是直接挂载文件所在的目录。

## 数据卷容器

容器之间共享一些持续更新的数据，专门用数据卷容器提供数据供其他容器挂载使用。

创建一个数据卷容器dbdata，并在其中创建一个数据卷挂载到/dbdata：
```
docker run -it -v /dbdata --name dbdata ubuntu
```

然后可以在其他容器中使用--volumes-from来挂载dbdata容器中的数据卷
```
docker run -it --volumes-from dbdata --name db1 ubuntu
docker run -it --volumes-from dbdata --name db2 ubuntu
```
三个容器任何一方在该目录下的写入，其他容器都可以看到。

可以多次使用--volumes-from参数来从多个容器挂载多个数据卷。还可以从其他已经挂载了容器卷的容器来挂载数据卷
```
docker run -d --name db3 --volumes-from db1 training/postgres
```

## 利用数据卷容器迁移数据

### 备份

```
docker run --volumes-from dbdata -v ${pwd}:/backup --name worker ubuntu tar cvf /backup/backup.tar /dbdata
```

首先利用ubuntu镜像创建了一个容器worker。使用--volumes-from dbdata参数来让worker容器挂载dbdata容器的数据卷（即dbdata数据卷）；使用-v ${pwd}:/backup参数来挂载本地的当前目录到worker容器的/backup目录。

worker容器使用后，使用了tar cvf /backup/backup.tar /dbdata命令来将/dbdata下内容备份为容器内的/backup/backup.tar，即宿主主机当前目录下的backup.tar

### 恢复

创建一个带有数据卷的容器dbdata2
```
docker run -v /dbdata --name dbdata2 ubuntu /bin/bash
```

创建另一个新的容器，挂载dbdata2的容器，并使用untar解压本分文件到所挂载的容器卷中
```
docker run --volumes-from dbdata2 -v ${pwd}:/backup busybox tar xvg /backup/backup.tar
```
