# Answer 安装指引

安装 Answer 之前，您需要先安装基本环境。
 - 数据库
     - [MySQL](http://dev.mysql.com)：版本 >= 5.7

然后，您可以通过以下几种方式来安装 Answer：

 - 采用 Docker 部署
 - 二进制安装
 - 源码安装

## 使用 Docker-compose 安装 Answer
```bash
$ mkdir answer && cd answer
$ wget https://raw.githubusercontent.com/answerdev/answer/main/docker-compose.yaml
$ docker-compose up
```

启动完成后使用浏览器访问 [http://127.0.0.1:9080/](http://127.0.0.1:9080/).

你可以使用默认的用户名：( **`admin@admin.com`** ) 和密码：( **`admin`** ) 进行登录.

## 使用Docker 安装 Answer
可以从 Docker Hub 或者 GitHub Container registry 下载最新的 tags 镜像

### 用法
将配置和存储目录挂在到镜像之外 volume (/var/data -> /data)，你可以修改外部挂载地址

```
# 将镜像从 docker hub 拉到本地
$ docker pull answerdev/answer:latest

# 创建一个挂载目录
$ mkdir -p /var/data

# 先运行一遍镜像
$ docker run --name=answer -p 9080:80 -v /var/data:/data answerdev/answer

# 第一次启动后会在/var/data 目录下生成配置文件
# /var/data/conf/config.yaml
# 需要修改配置文件中的Mysql 数据库地址
vim /var/data/conf/config.yaml

# 修改数据库连接 connection: [username]:[password]@tcp([host]:[port])/[DbName]
...

# 配置好配置文件后可以再次启动镜像即可启动服务
$ docker start answer
```

## 使用二进制 安装 Answer
可以使用编译完成的各个平台的二进制文件运行 Answer 项目
### 用法
从 GitHub 最新版本的tag中下载对应平台的二进制文件压缩包

 1. 解压压缩包
 2. 使用命令 cd 进入到刚刚创建的目录
 3. 执行命令 ./answer init
 4. Answer 会在当前目录生成 ./data 目录
 5. 进入 data 目录修改 config.yaml 文件
 6. 将数据库连接地址修改为你的数据库连接地址

     connection: [username]:[password]@tcp([host]:[port])/[DbName]
 7. 退出 data 目录，执行 ./answer run -c ./data/conf/config.yaml

## 当前支持的命令
用法: answer [command]

- help: 帮助
- init: 初始化环境
- run: 启动
- check: 环境依赖检查
- dump: 备份数据

## 配置文件 config.yaml 参数说明

```
server:
  http:
    addr: 0.0.0.0:80 #项目访问端口号
data:
  database:
    connection: root:root@tcp(127.0.0.1:3306)/answer #mysql数据库连接地址
  cache:
    file_path: "/tmp/cache/cache.db" #缓存文件存放路径
i18n:
  bundle_dir: "/data/i18n" #国际化文件存放目录
swaggerui:
  show: true #是否显示swaggerapi文档，地址 /swagger/index.html
  protocol: http #swagger 协议头
  host: 127.0.0.1 #可被访问的ip地址或域名
  address: ':80'  #可被访问的端口号
service_config:
  secret_key: "answer" #加密key
  web_host: "http://127.0.0.1" #页面访问使用域名地址
  upload_path: "./upfiles" #上传目录
```

## 编译镜像
如果修改了源文件并且要重新打包镜像可以使用以下语句重新打包镜像
```
docker build -t  answer:v1.0.0 .
```
## 常见问题
 1. 项目无法启动，answer 主程序启动依赖配置文件 config.yaml 、国际化翻译目录 /i18n 、上传文件存放目录 /upfiles，需要确保项目启动时加载了配置文件 answer run -c config.yaml 以及在 config.yaml 正确的指定 i18n 和 upfiles 目录的配置项
