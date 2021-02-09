# DockerTest

## 简单说明

​           本测试是基于《深入浅出学习Docker》,所以但看这片文档是不完整的，主要还是看书，本文档主要用于记录在学习过程中出现的问题以及解决过程。

## 简单的配置docker

1. 默认安装docker以后还得先把它计入开机自启动`systemctl enable docker`
2. 重启docker服务`sudo service docker restart`
3. 查看docker服务连接情况`docker version`
4. 拉取ubuntu镜像`docker image pull ubuntu:latest`

  >附一下常用的docker命令
  >
  >```docker
  >启动        systemctl start docker
  >守护进程重启   sudo systemctl daemon-reload
  >重启docker服务   systemctl restart  docker
  >重启docker服务  sudo service docker restart
  >关闭docker service docker stop
  >关闭docker systemctl stop docker
  >```

## docker配置国内镜像

```Linux
$ cd /etc/docker
$ cat daemon.json 
//前两步先创建一个docker配置文件，然后把配置信息写入
{"graph": "/mnt/docker-data","storage-driver": "overlay",
"registry-mirrors": [
"https://kfwkfulq.mirror.aliyuncs.com",
"https://2lqq34jg.mirror.aliyuncs.com",
"https://pee6w651.mirror.aliyuncs.com",
"https://registry.docker-cn.com",
"http://hub-mirror.c.163.com"
],
"dns": ["8.8.8.8","8.8.4.4"]
}
```

## 启动第一个docker程序

1. 我们需要使用的是资源.rar中的`psweb-master`

   > ![image-20210127000918521](C:\Users\Norxus\AppData\Roaming\Typora\typora-user-images\image-20210127000918521.png)
   >
   > 这是解压后的内部结构，可以看到有一个Dockerfile，这个文件就是一个纯文本文件，其中主要描述了如何将应用构建到Docker镜像中：
   >
   > ![image-20210127001108722](C:\Users\Norxus\AppData\Roaming\Typora\typora-user-images\image-20210127001108722.png)
   >
   > 可以看到主要是一些配置的步骤。

   2. 启动之前，千万要注意这个npm install,命令，这个命令是真的要人命了，因为下载得太慢了，我在这卡了两个小时。所以我们需要配置淘宝镜像

      > ![image-20210127001502714](C:\Users\Norxus\AppData\Roaming\Typora\typora-user-images\image-20210127001502714.png)
      >
      > 接下来配置淘宝镜像
      >
      > ```shell
      > npm install -g cnpm --registry=https://registry.npm.taobao.org
      > ```

   3. 接下来构建该应用

     ```shell
   sudo docker image build -t test:latest .
     ```

   4. 最后启动该应用

   ```shell
   docker container run -d --name web1 --publish 8080:8080 test:latest
   ```

   5. 访问该端口

   ```txt
   Docker主机的DNS名称/Docker主机的IP地址 + 8080
   ```

   ​                                                                          

   ​                                                                            **大功告成！！！**

   

![image-20210127002139383](C:\Users\Norxus\AppData\Roaming\Typora\typora-user-images\image-20210127002139383.png)

## 记docker-compose测试时遇到的一个小问题

### 起因

没有安装最新的docker，导致和docker-compose不匹配，docker-compose在执行时会使用`--iidfile`这个命令参数，而老版本的`docker build`里面是没有这个参数的，这就导致docker-compose无法执行，因为我在一开始安装docker的时候是直接通过Linux里的默认仓库安装的，导致安装的远古版本的docker。

### 解决办法

1. Docker 要求 CentOS 系统的内核版本高于 3.10 ,查看CentOS的内核版本。 

   ```shell
   uname -a
   ```

2. 删除旧版本

   ```shell
   yum erase 你的docker安装包
   ```

3. 安装需要的安装包

   1. `yum-util` 提供`yum-config-manager`功能，另外两个是`devicemapper`驱动依赖的

      ```shell
      sudo yum install -y yum-utils device-mapper-persistent-data lvm2
      ```

   2. 设置yum源

      ```shell
      sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
      ```

   3. 查看仓库中的版本

      ```shell
      yum list docker-ce --showduplicates | sort -r
      ```

   4. 安装你需要的版本

      ```shell
      sudo yum install docker-ce
      #上面是安装最新的版本，也可以安装执行的版本
      sudo yum install docker-ce-18.06.1.ce  
      ```

      