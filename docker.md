# docker（更新中……）

[docker官网](http://www.docker.com)

[docker hub官网(需要魔法)](https://hub.docker.com)                  [国内仓库](https://hub.daocloud.io/)

## 一、linux安装docker

___(使用root权限，如果不是，每个命令前加sudo)___

[官网安装教程](https://docs.docker.com/engine/install/)

### 1. ubuntu安装docker

1. 卸载ubuntu原装docker：`apt-get remove docker docker-engine docker.io containerd runc`

2. 更新软件包和版本数据：

   * `apt-get update`
   * `apt upgrade`

3. 安装docker所需的依赖文件：`apt-get install ca-certificates curl gnupg lsb-release`

4. 添加docker官方GPG密钥：

   `curl -fsSL http://mirrors.aliyun.com/docker-ce/linux/ubuntu/gpg | sudo apt-key add -`

5. 添加docker国内镜像源（此处选用阿里云）：

   `sudo add-apt-repository "deb [arch=amd64] http://mirrors.aliyun.com/docker-ce/linux/ubuntu $(lsb_release -cs) stable"`

6. 安装docker：`apt-get install docker-ce docker-ce-cli containerd.io`

7. docker 启动：`systemctl start docker`

8. 安装工具：

   `apt-get -y install apt-transport-https ca-certificates curl software-properties-common`

9. 重启：`service docker restart`

10. 设置开机自启：`systemctl enable docker.service`

### 2. centos安装docker

1. 清除旧版本：

   ```
   yum remove docker \
   docker-client \
   docker-client-latest \
   docker-latest-logrotate \
   docker-logrotate \
   docker-engine
   ```

2. 安装gcc相关：

   ```
   yum -y install gcc
   yum -y install gcc-c++
   ```

3. 设置仓库：

   ```shell
   # 先安装yum-utils
   yum install -y yum-utils
   # 设置（阿里云）仓库地址
   yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
   ```

4. 更新yum软件包索引：`yum makecache fast`

5. 安装docker引擎：`yum -y install docker-ce docker-ce-cli containerd.io`

6. 启动：`systemctl start docker`

### 3. 配置镜像加速器

ubuntu或centos都可用以下方式配置镜像加速器

1. 登录阿里云官网，搜索容器镜像服务

   ![image-20230928103643167](https://cdn.jsdelivr.net/gh/fosss666/notebook/img/202309281036686.png)

2. 进入后在镜像工具-镜像加速器下复制自己的加速器地址，在linux中配置镜像加速器地址

   ```shell
   sudo mkdir -p /etc/docker
   sudo tee /etc/docker/daemon.json <<-'EOF'
   {
     "registry-mirrors": ["【填写个人地址】"]
   }
   EOF
   sudo systemctl daemon-reload
   sudo systemctl restart docker
   ```

   

## 二、docker基本命令

### 1. 帮助启动类命令

* 防火墙状态：`ufw status`
* 关闭或开启防火墙：`sudo ufw enable/disable`
* 开放端口：`ufw allow 【端口号】`
* 启动docker：`systemctl start docker`
* 停止docker：`systemctl stop docker`
* 重启docker：`systemctl restart docker`
* 查看docker状态：`systemctl status docker`
* 是否开机自启：`systemctl enable/disable docker`
* 查看版本：`docker version`
* 查看概要信息：`docker info`
* 查看总体帮助文档：`docker --help`
* 查看docker命令帮助文档：`docker 具体命令 --help`

### 2. 镜像命令

* 展示镜像：`docker images`
* 删除镜像： `docker rmi 【镜像名称:镜像版本】`

### 3. 容器命令

* 显示已启动的容器：`docker ps`

* 显示所有容器：`docker ps -a`

* 删除容器：`docker rm 【容器名称/容器id(可只写一部分)】`

* 设置容器开机自启：`docker update --restart=always 【容器名称或id】`

​      关闭开机自启：`docker update --restart=no 【容器名称或id】`

* 创建容器：`docker run [-d -v -p --name --restart……] 镜像名:版本`

​		-v：数据挂载

​		-d：后台启动

​		-p：端口映射

​		--name：自定义容器名

​		--restart：开机是否自启

* 启动容器：`docker start 容器名/容器id(可部分)`

​	  重启容器：`docker restart 容器名/容器id(可部分)`

​	  关闭容器：`docker start 容器名/容器id(可部分)`

* 复制容器文件到本地系统文件：`docker cp 【容器id(可只写一部分):文件位置】 新位置`

## 三、docker安装镜像及启动容器

### 1. docker 安装mysql

1. 拉取镜像：

   ```
   docker pull mysql   默认拉取最新版，可在后边加上':5.7'指定5.7版本
   ```

2. 启动容器，做好端口映射、数据挂载（放置容器删除后数据也被删除）

   ```
   docker run -d -p 3306:3306 --privileged=true \
   -v /mydata/mysql/log:/var/log/mysql \
   -v /mydata/mysql/data:/var/lib/mysql \
   -v /mydata/mysql/conf:/etc/mysql/conf.d \
   -e MYSQL_ROOT_PASSWORD=123456 \
   --name mysql \
   mysql:latest
   ```

3. 设置字符集为utf8

   ```
   cd /mydata/mysql/conf
   vim my.cnf
   ```

   编辑内容为：

   ```
   [client]
   default_character_set=utf8
   [mysqld]
   collation_server = utf8_general_ci
   character_set_server = utf8
   ```

4. 重启mysql

   ```
   docker restart mysql
   ```

   

5. 设置开机自启

   ```
   docker update --restart=always mysql
   ```

### 2. docker安装emqx

1. 拉取

   ```
   docker pull emqx/emqx
   ```

   

2. 运行容器

   ```
   docker run -p 18083:18083 -p 8883:8883 -p 8083:8083 -p 8084:8084 -p 1883:1883 --name=emqx -d emqx/emqx
   ```

### 3. docker安装nginx+rtmp

1. 拉取(nginx和rtmp的整合包，rtmp是流媒体协议，用于直播等)

   ```
   docker pull alqutami/rtmp-hls
   ```

2. 运行

   ```
   docker run -p 1935:1935 -p 8080:8080 --name=nginx_rtmp -d alqutami/rtmp-hls
   ```

3. 拷贝文件

   ```
   docker cp 容器id:/etc.nginx /mydata
   ```

4. 将mydata下的nginx文件修改名称为nginx_rtmp：直接在图形界面上编辑

5. 运行

   ```
   docker run -p 1935:1935 -p 8080:8080 \
   -v /mydata/nginx_rtmp:/etc/nginx \
   --name nginx_rtmp \
   -d alqutami/rtmp-hls
   ```

### 4. docker安装nginx

1. 拉取

   ```
   docker pull nginx
   ```

2. 运行

   ```
   docker run  -p 80:80 --name nginx --restart=always \
   -v /mydata/nginx/conf/nginx.conf:/etc/nginx/nginx.conf \
   -v /mydata/nginx/html:/usr/share/nginx/html \
   -v /mydata/nginx/logs:/var/log/nginx \
   -d  nginx:latest
   ```

## 四、远程连接

### 使用idea远程连接docker

1. 配置docker远程连接端口

   * 编辑docker 配置文件

     `vim /lib/systemd/system/docker.service`

   * 修改配置

     ```shell
     # 将[Service]下的ExecStart改成下面这样
     ExecStart=/usr/bin/dockerd   -H tcp://0.0.0.0:2375 -H unix://var/run/docker.sock
     ```

2. 加载配置：`systemctl daemon-reload`

3. 重启docker：`systemctl restart docker` 

4. 开启防火墙或开放2375端口：操作☞上文 [docker基本命令](##二、docker基本命令)

5. 测试远程访问，访问 http://[虚拟机ip]:2375/version，出现展示docker版本的一段json即说明设置成功

6. idea配置docker连接

   * 下载插件docker
   * 在设置中找到docker并添加连接
     - 点击加号
     - 选择TCP socket,添加Engine API URL 
     - url格式为tcp://【主机地址】:2375，如下图所示
   
   ![image-20230927190648105](https://cdn.jsdelivr.net/gh/fosss666/notebook/img/202309271907214.png)
   
   