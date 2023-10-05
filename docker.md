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
* 关闭或开启防火墙：
  * ubuntu: `ufw enable/disable`
  * centos: `systemctl enable/disable firewalld`
* 开放及关闭端口：
  * ubuntu：`ufw [delete] allow 【端口号】`
  * centos：`firewall-cmd --zone=public --add/remove-port=【端口号】/tcp --permanent`
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
* 查询镜像：`docker search 【镜像名】`
* 拉取镜像：`docker pull 镜像名[:版本]  # 默认版本是latest`
* 删除镜像： `docker rmi [-f] 镜像名称或镜像id   # -f表示强制删除`

### 3. 容器命令

* 创建容器：`docker run [-d -v -p --name --restart……] 镜像名:版本`

  -v：数据挂载

  -d：后台启动

  -p：端口映射    `6379:6379`表示左边为暴露的端口，右边为容器端口

  --name：自定义容器名

  --restart：开机是否自启

  -it：启动交互式容器

* 显示已启动的容器：`docker ps`

* 显示所有容器：`docker ps -a`

* 进入容器：`docker exec -it 【容器名或id】 /bin/bash【或bash】`   

  退出容器：`exit`

* 删除容器：`docker rm 【容器名称/容器id(可只写一部分)】 # 可加-f参数强制删除`  

* 设置容器开机自启：`docker update --restart=always 【容器名称或id】`

  关闭开机自启：`docker update --restart=no 【容器名称或id】`

*  启动容器：`docker start 容器名/容器id(可部分)`

   重启容器：`docker restart 容器名/容器id(可部分)`

   关闭容器：`docker start 容器名/容器id(可部分)`

* 查看日志：`docker logs 【容器名或id】`

* 复制容器文件到本地系统文件，防止容器删除后数据也没了：`docker cp 【容器id(可只写一部分):文件位置】 本地位置`
* 导入导出容器：
  + 导出容器的内容留作为一个tar归档文件：`docker export 【容器id】 > abc.tar`
  + 从tar包中的内容创建一个新的文件系统再导入为镜像：`cat abc.tar | docker import - 镜像用户/镜像名:镜像版本号 `

## 三、docker安装镜像及启动容器

### 1. docker 安装mysql

1. 拉取镜像：

   ```
   docker pull mysql   默认拉取最新版，可在后边加上':5.7'指定5.7版本
   ```

2. 启动容器，做好端口映射、数据挂载（放置容器删除后数据也被删除）

   ```shell
   docker run -d -p 3306:3306 --privileged=true \
   -v /mydata/mysql/log:/var/log/mysql \
   -v /mydata/mysql/data:/var/lib/mysql \
   -v /mydata/mysql/conf:/etc/mysql/conf.d \
   -e MYSQL_ROOT_PASSWORD=123456 \
   --name mysql \
   --update always \
   mysql:latest
   ```

3. 设置字符集为utf8

   ```shell
   cd /mydata/mysql/conf
   vim my.cnf
   ```

   编辑内容为：

   ```shell
   [client]
   default_character_set=utf8
   [mysqld]
   collation_server = utf8_general_ci
   character_set_server = utf8
   ```

4. 重启mysql

   ```shell
   docker restart mysql
   ```

### 2. docker安装emqx

1. 拉取

   ```shell
   docker pull emqx/emqx
   ```

2. 运行容器

   ```shell
   docker run -p 18083:18083 -p 8883:8883 -p 8083:8083 -p 8084:8084 -p 1883:1883 --name=emqx -d emqx/emqx
   ```

### 3. docker安装rtmp

1. 拉取(nginx和rtmp的整合包，rtmp是流媒体协议，用于直播等)

   ```shell
   docker pull alqutami/rtmp-hls
   ```

2. 运行

   ```shell
   docker run -p 1935:1935 -p 8080:8080 --name=nginx_rtmp -d alqutami/rtmp-hls
   ```

3. 拷贝文件

   ```shell
   docker cp 容器id:/etc.nginx /mydata
   ```

4. 将mydata下的nginx文件修改名称为nginx_rtmp：直接在图形界面上编辑

5. 运行

   ```shell
   docker run -p 1935:1935 -p 8080:8080 \
   -v /mydata/nginx_rtmp:/etc/nginx \
   --name nginx_rtmp \
   -d alqutami/rtmp-hls
   ```

### 4. docker安装nginx

1. 拉取

   ```shell
   docker pull nginx
   ```

2. 运行

   ```shell
   docker run  -p 80:80 --name nginx --restart=always \
   -v /mydata/nginx/conf/nginx.conf:/etc/nginx/nginx.conf \
   -v /mydata/nginx/html:/usr/share/nginx/html \
   -v /mydata/nginx/logs:/var/log/nginx \
   -d  nginx:latest
   ```

### 5. docker安装kafka

1. 创建一个网络

   ```sh
   docker network create app-tier --driver bridge
   ```

2. 安装zookeeper(kafka依赖zookeeper)

   ```sh
   docker run -d --name zookeeper-server \
   --network app-tier \
   -e ALLOW_ANONYMOUS_LOGIN=yes \
   --restart always \
   bitnami/zookeeper:latest
   ```

3. 安装kafka

   ```sh
   docker run -d --name kafka-server \
   --network app-tier \
   -p 9092:9092 \
   -e ALLOW_PLAINTEXT_LISTENER=yes \
   -e KAFKA_CFG_ZOOKEEPER_CONNECT=zookeeper-server:2181 \
   -e KAFKA_CFG_ADVERTISED_LISTENERS=PLAINTEXT://【主机ip】:9092 \
   --restart always \
   bitnami/kafka:latest
   ```

4. kafka-map图形化管理工具

   ```sh
   docker run -d --name kafka-map \
   --network app-tier \
   -p 9001:8080 \
   -v /mydata/kafka-map/data:/usr/local/kafka-map/data \
   -e DEFAULT_USERNAME=admin \
   -e DEFAULT_PASSWORD=admin \
   --restart always dushixiang/kafka-map:latest
   ```

   * _访问地址：http://服务器IP:9001/_
   * 默认账号密码为：admin

### 6. docker安装Elasticsearch、Kibana、IK分词器

#### 1. 安装Elasticsearch

1. 创建网络：因为需要部署kibana容器，因此需要让es和kibana容器互联

   ```sh
   docker network create es-net
   ```

2. 拉取镜像 _jdk8不能使用6以上的版本_

   ```sh
   docker pull elasticsearch:7.12.1
   ```

3. 创建挂载点目录

   ```sh
   mkdir -p /mydata/es/data /mydata/es/config /mydata/es/plugins
   chmod 777  /mydata/es/data
   chmod 777  /mydata/es/config
   chmod 777  /mydata/es/plugins
   ```

4. 部署单点es，创建es容器

   ```sh
   docker run -d \
   --restart=always \
   --name es \
   --network es-net \
   -p 9200:9200 \
   -p 9300:9300 \
   --privileged \
   -v /mydata/es/data:/usr/share/elasticsearch/data \
   -v /mydata/es/plugins:/usr/share/elasticsearch/plugins \
   -e "discovery.type=single-node" \
   -e "ES_JAVA_OPTS=-Xms512m -Xmx512m" \
   elasticsearch:7.12.1
   ```

5. 验证是否安装成功

   访问http://ip:9200。显示版本等信息说明启动成功


#### 2. 安装Kibana

1. 拉取镜像

   ```sh
   docker pull kibana:7.12.1
   ```

2. 创建容器

   ```sh
   docker run -d \
   --restart=always \
   --name kibana \
   --network es-net \
   -p 5601:5601 \
   -e ELASTICSEARCH_HOSTS=http://es:9200 \
   kibana:7.12.1
   ```

3. 测试是否安装成功

   访问http://ip:5601

#### 3. 安装ik分词器

1. 进入es容器

   ```sh
   docker exec -it es /bin/bash
   ```

2. 在线安装 

   ___注意：安装IK分词器的版本，必须和Elasticsearch的版本一致___。安装其他版本的将版本号修改即可

   ```sh
   ./bin/elasticsearch-plugin install https://github.com/medcl/elasticsearch-analysis-ik/releases/download/v7.12.1/elasticsearch-analysis-ik-7.12.zip
   ```

   

## 四、远程连接

### 使用idea远程连接docker

1. 配置docker远程连接端口

   * 编辑docker 配置文件

     `vim /lib/systemd/system/docker.service`

   * 修改配置

     ```shell
     # 将[Service]下的ExecStart添加 -H tcp://0.0.0.0:2375,如：
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
   

## 五、本地镜像发布

### 1. commit命令

1. 拉取ubuntu镜像：`docker pull ubuntu`

2. 运行容器，ubuntu不能后台运行，所以直接进入：`docker run -it 【镜像id】 /bin/bash`

3. 默认没有vim命令,执行`vim a.txt`会保存vim没有找到

4. 下载vim：

   ```shell
   apt-get update
   apt-get install vim
   ```

5. 此时再进行测试，发现vim安装成功

6. 提交容器副本使之成为一个新的镜像：本ubuntu已经添加了vim命令，将其提交

   ```
   docker commit -m="提交的描述信息" -a="作者" 容器ID 要创建的目标镜像名:[标签名]
   ```

7. 提交之后，执行`docker images`会看到刚才提交的镜像，运行这个镜像，执行vim命令，发现该命令已存在

### 2.本地镜像发布到阿里云

1. 进入阿里云容器镜像服务，在实例列表中创建个人示例

2. 设置Registry登录密码：

   ![image-20230930133923420](https://cdn.jsdelivr.net/gh/fosss666/notebook/img/202309301339493.png)

3. 点击命名空间 -> 创建命名空间

   ![image-20230930135203126](https://cdn.jsdelivr.net/gh/fosss666/notebook/img/202309301352204.png)

4. 点击镜像仓库 ->创建命名空间

   * 填写仓库信息

     ![image-20230930135742334](https://cdn.jsdelivr.net/gh/fosss666/notebook/img/202309301359636.png)

   * 选择代码源

     ![image-20230930140002375](https://cdn.jsdelivr.net/gh/fosss666/notebook/img/202309301400448.png)

5. 参照阿里云给出的命令将本地镜像推送到阿里云：

   ![image-20230930140806238](https://cdn.jsdelivr.net/gh/fosss666/notebook/img/202309301408309.png)

   ![image-20230930141043149](https://cdn.jsdelivr.net/gh/fosss666/notebook/img/202309301410426.png)

6. 在镜像版本界面就可以看到推送的本地镜像了

   ![image-20230930141153163](https://cdn.jsdelivr.net/gh/fosss666/notebook/img/202309301411231.png)

   

   

### 3. 从阿里云拉取镜像到本地

* 参照第二条命令进行拉取

  ![image-20230930141450623](https://cdn.jsdelivr.net/gh/fosss666/notebook/img/202309301421460.png)

  

  ![image-20230930141745319](https://cdn.jsdelivr.net/gh/fosss666/notebook/img/202309301422243.png)

### 4.本地镜像推送到私有仓库

1. 下载镜像 Docker Registry：

   ```sh
   docker pull registry
   ```

2. 运行私有库Registry，相当于本地有个私有Dockerhub：

   ```shell
   docker run -d -p 5000:5000 \
   -v /mydata/myregistry:/tmp/registry \
   --name=registry \
   --restart=always \
   --privileged=true registry
   ```

3. 为拉取的简单版ubuntu添加ifconfig命令

   ![image-20231001130939742](https://cdn.jsdelivr.net/gh/fosss666/notebook/img/202310011309814.png)

4. commit:

   ![image-20231001131301614](https://cdn.jsdelivr.net/gh/fosss666/notebook/img/202310011313809.png)

5. 将新镜像修改符合私服规范的tag：

   ```sh
   docker tag 镜像名:版本 虚拟机ip:5000/镜像名:版本
   ```

6. 修改配置文件使之支持http：

   ```sh
   vim /etc/docker/daemon.json
   # 添加如下配置：
   {
   	"registry-mirrors":["阿里云镜像地址"],
   	"insecure-registries":["虚拟机ip:5000"]
   }
   ```

   ___重启docker___

7. push到私服库

   ```sh
   docker push 虚拟机ip:5000/镜像名:版本tag
   ```

8. 验证私服库上有什么镜像

   ```sh
   curl -XGET http://虚拟机ip:5000/v2/_catalog
   # 查看镜像tag
   curl http://虚拟机ip:5000/v2/镜像名/tags/list
   ```

9. 拉取镜像

   ```
   docker pull 虚拟机ip:5000/镜像名:版本tag
   ```

## 六、docker高级安装

###  mysql主从复制

1. 新建主服务器容器实例3307

   ```sh
   docker run -d -p 3307:3306 --privileged=true \
   -v /mydata/mysql-master/log:/var/log/mysql \
   -v /mydata/mysql-master/data:/var/lib/mysql \
   -v /mydata/mysql-master/conf:/etc/mysql/conf.d \
   -e MYSQL_ROOT_PASSWORD=123456 \
   --name mysql-master \
   mysql:5.7
   ```

2. 进入/mydata/mysql-master/conf目录下下新建my.cnf，修改配置

   ```sh
   cd /mydata/mysql-master/conf
   vim my.cnf
   ```

   ```
   [mysqld]
   ## 设置server_id，同一局域网中需要唯一
   server_id=101
   ## 指定不需要同步的数据库名称
   binlog-ignore-db=mysql
   ## 开启二进制日志功能
   log-bin=mall-mysql-bin
   ## 设置二进制日志使用内存大小（事务）
   binlog_cache_size=1M
   ## 设置使用的二进制日志格式（mixed,statement,row）
   binlog_format=mixed
   ## 二进制日志过期清理时间。默认值是0，表示不自动清理
   expire_logs_days=7
   ## 跳过主从复制中遇到的所有错误或指定类型的错误，避免slave端复制终端
   ## 如：1062错误是指一些主键重复，1032错误是因为主从数据库数据不一致
   slave_skip_errors=1062
   ```

3. 重启master实例

   ```sh
   docker restart mysql-master
   ```

4. 进去mysql-master容器，创建数据同步用户

   ```sh
   docker exec -it mysql-master bash
   mysql -uroot -p
   ```

   ```sh
   CREATE USER 'slave'@'%' IDENTIFIED BY '123456';
   GRANT REPLICATION SLAVE,REPLICATION CLIENT ON *.* TO 'slave'@'%';
   ```

5. 新建从服务器容器示例3308

   ```sh
   docker run -d -p 3308:3306 --privileged=true \
   -v /mydata/mysql-slave/log:/var/log/mysql \
   -v /mydata/mysql-slave/data:/var/lib/mysql \
   -v /mydata/mysql-slave/conf:/etc/mysql/conf.d \
   -e MYSQL_ROOT_PASSWORD=123456 \
   --name mysql-slave \
   mysql:5.7
   ```

6. 进入/mydata/mysql-slave/conf目录下下新建my.cnf，修改配置

   ```sh
   cd /mydata/mysql-slave/conf
   vim my.cnf
   ```

   ```
   [mysqld]
   ## 设置server_id，同一局域网中需要唯一
   server_id=102
   ## 指定不需要同步的数据库名称
   binlog-ignore-db=mysql
   ## 开启二进制日志功能
   log-bin=mall-mysql-slave1-bin
   ## 设置二进制日志使用内存大小（事务）
   binlog_cache_size=1M
   ## 设置使用的二进制日志格式（mixed,statement,row）
   binlog_format=mixed
   ## 二进制日志过期清理时间。默认值是0，表示不自动清理
   expire_logs_days=7
   ## 跳过主从复制中遇到的所有错误或指定类型的错误，避免slave端复制终端
   ## 如：1062错误是指一些主键重复，1032错误是因为主从数据库数据不一致
   slave_skip_errors=1062
   ## relay_log配置中继日志
   relay_log=mall-mysql-relay-bin
   ## log_slave_updates表示slave将复制事件写进自己的二进制日志
   log_slave_updates=1
   ## slave设置为只读（具有super权限的用户除外）
   read_only=1
   ```

7. 重启slave示例

   ```sh
   docker restart mysql-slave
   ```

8. 在主数据库查看主从同步状态

   ```
   show master status; #可在最后加'\G'来以k:v形式查看
   ```

   ![image-20231003140121859](https://cdn.jsdelivr.net/gh/fosss666/notebook/img/202310031401956.png)

9. 进入mysql-slave容器

   ```sh
   docker exec -it mysql-slave bash
   mysql -uroot -p
   ```

10. 在从数据库中配置主从复制

    ```
    change master to master_host="192.168.60.129",master_user='slave',master_password='123456',master_port=3307,master_log_file="mall-mysql-bin.000001",master_log_pos=617,master_connect_retry=30;
    ```

    > master_host：主数据库的IP地址
    >
    > master_user：在主数据库创建的用于同步数据的用户账号
    >
    > master_password：在主数据库创建的用于同步数据的用户密码
    >
    > master_port：主数据库的运行端口
    >
    > master_log_file：指定从数据库要复制数据的日志文件，通过查看主数据的状态，获取File参数
    >
    > master_log_pos：指定从数据库从哪个位置开始复制数据，通过查看主数据的状态，获取Position参数
    >
    > master_connect_retry：连接失败重试的时间间隔，单位为秒

11. 在从数据库中查看主从同步状态

    ```
    show slave status; #可在最后加'\G'来以k:v形式查看
    ```

    ![image-20231003140418298](https://cdn.jsdelivr.net/gh/fosss666/notebook/img/202310031404515.png)

12. 在从数据库中开启主从同步

    ```
    start slave;
    ```

13. 查看从数据库状态发现已经同步

    ```
    show slave status \G;
    ```

    ![image-20231003140337444](https://cdn.jsdelivr.net/gh/fosss666/notebook/img/202310031403680.png)

14. 主从复制测试

    在主数据库中创建数据库、表，插入数据，在从数据库中查看是否同步。OVER!



