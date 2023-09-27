# docker

### ubuntu安装docker

___(使用root权限，如果不是，每个命令前加sudo)___

1. 卸载ubuntu原装docker：`apt-get remove docker docker-engine docker.io containerd runc`

2. 更新软件包和版本数据：

   * `apt-get update`
   * `apt upgrade`

3. 安装docker所需的依赖文件：`apt-get install ca-certificates curl gnupg lsb-release`

4. 添加docker官方GPG密钥：

   `curl -fsSL http://mirrors.aliyun.com/docker-ce/linux/ubuntu/gpg | sudo apt-key add -`

5. 添加docker软件源：

   `sudo add-apt-repository "deb [arch=amd64] http://mirrors.aliyun.com/docker-ce/linux/ubuntu $(lsb_release -cs) stable"`

6. 安装docker：`apt-get install docker-ce docker-ce-cli containerd.io`

7. docker 启动：`systemctl start docker`

8. 安装工具：

   `apt-get -y install apt-transport-https ca-certificates curl software-properties-common`

9. 重启：`service docker restart`

10. 设置开机自启：`systemctl enable docker.service`

### docker基本命令

1. 查看版本：`docker version`

2. 删除镜像： `docker rmi 【镜像名称:镜像版本】`

3. 删除容器：`docker rm 【容器名称/容器id(可只写一部分)】`

4. 复制文件：`docker cp 【容器id(可只写一部分):文件位置】 新位置`

5. 设置容器开机自启：`docker update --restart=always 【容器名称或id】`

   关闭开机自启：`docker update --restart=no 【容器名称或id】`

6. 防火墙状态：`ufw status`

   关闭或开启防火墙：`sudo ufw enable/disable`

7. 开放端口：`ufw allow 【端口号】`

### docker 安装mysql

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

### docker安装emqx

1. 拉取

   ```
   docker pull emqx/emqx
   ```

   

2. 运行容器

   ```
   docker run -p 18083:18083 -p 8883:8883 -p 8083:8083 -p 8084:8084 -p 1883:1883 --name=emqx -d emqx/emqx
   ```

### docker安装nginx+rtmp

1. 拉取(nginx和rtmp的整合包，rtmp是流媒体协议，用于直播等)

   ```
   docker pull alqutami/rtmp-hls
   ```

2. 运行

   ```
   docker run -p 1935:1935 -p 8080:8080 -d alqutami/rtmp-hls
   ```

3. 拷贝文件

   ```
   docker cp 容器id:/etc.nginx /mydata
   ```

4. 将mydata下的nginx文件修改名称为nginx_rtmp

5. 运行

   ```
   docker run -p 1935:1935 -p 8080:8080 \
   -v /mydata/nginx_rtmp:/etc/nginx \
   --name nginx_rtmp \
   -d alqutami/rtmp-hls
   ```

### docker安装nginx

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

   