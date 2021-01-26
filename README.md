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