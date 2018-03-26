# Graphene 节点安装

## 1 系统要求

- Ubuntu/Windows 64 位操作系统
- Mysql 5.7
- Redis 3.2
- 必须有公网ip
- 建议CPU 2GHZ以上
- 建议内存2G以上
- 建议带宽2Mb以上
- 建议剩余硬盘空间20GB以上

## 2 安装

测试版(testnet)与正式版(mainnet)除安装包、配置文件内容不一样外，安装流程是一样的<br>
只要端口不冲突，可以同时在一台机器安装，但不推荐这样做，除非机器配置足够好<br>
节点使用Redis/Mysql做数据缓存/存储，Graphene启动前必须先安装好他们

### 2.1 Windows版安装

0.确保开启了时钟同步服务

1.下载解压Graphene
`http://7xqoxw.com1.z0.glb.clouddn.com/testnet-2100.rar`
可使用浏览器下载

2.下载安装Mysql

直接在官网下载，下载链接为

[https://dev.mysql.com/downloads/mysql/](https://dev.mysql.com/downloads/mysql/)

选择 Windows 64 位版本

3.下载安装 Redis

下载链接为
[https://github.com/MicrosoftArchive/redis/releases](https://github.com/MicrosoftArchive/redis/releases)
选择Redis-x64-3.2.100.zip下载

解压之后，先打开 redis.windows.conf，将第60行
```
# bind 127.0.0.1
```
改为
```
bind 127.0.0.1
```
保存之后，命令行下运行
```
redis-server.exe --service-install redis.windows.conf --loglevel warning
redis-server.exe --service-start
```
将Redis注册为系统服务并启动

4.新建一个Mysql数据库

安装完毕Mysql后，您可以使用自带的Mysql Workbench，连接数据库服务端，并新建一个数据库，假设Schema的名字为blockchain

5.进入Graphene目录，修改config/volatile.json
```
 "__comment": "mysql connection configuration",
    "database": "",
    "username": "",
    "password": "",
    "host": "localhost",
    "dialect": "mysql",
    "port": 3306
```
database为第四步新建的Schema的名称，例如blockchain。username可以使用root，然后填入密码。如果您使用Mysql云服务，也需要修改host等字段
```
"port": 10086,
"publicIp": "",
```
port默认10086，您可以选择任意一个未被占用的端口。然后填入您的公网ip
```
"forging": {
    "secret": [],
    "access": {
      "whiteList": [
        "127.0.0.1"
      ]
    }
  },
```
在secret里填入您的受托人密钥
```
"forging": {
    "secret": [
      "xxx1",
      "xxx2"
    ],
  },
```
**注意** 不管是一台机器还是多台机器，绝不要配置重复的受托人密钥

6.进行到这步，命令行下运行
```
node ./bin/migrate.js --up
```
然后运行
```
node startup.js --test
```
如果没有报错，则说明一切正常

7.启动服务，运行
```
node startup.js
```

### 2.2 Linux版安装

0.Linux上需要ntp服务，可运行`service --status-all`查看服务是否正常运行。可自行运行
apt-get/yum/zypper等包管理器工具安装ntp

1.下载解压Graphene
```
wget http://7xqoxw.com1.z0.glb.clouddn.com/ltest-2101.tar.gz
tar zxvf ltest-2101.tar.gz
```

2.安装Mysql
直接在官网下载，下载链接为

[https://dev.mysql.com/downloads/mysql/](https://dev.mysql.com/downloads/mysql/)

选择 Linux - Generic 64位版本

安装完毕之后，启动服务，并创建一个新数据库，假设名称为blockchain

3.下载安装Redis，建议使用Redis3.x的版本，您可以在官网下载源码，本地编译生成可执行文件。启动前，注意将`redis.conf`中
```
# bind 127.0.0.1
```
改为
```
bind 127.0.0.1
```
并保存，运行
```
redis-server redis.conf
```
启动redis，您也可以将redis注册为服务，以后重启机器会自动运行

4.进入Graphene目录，修改config/volatile.json
```
 "__comment": "mysql connection configuration",
    "database": "",
    "username": "",
    "password": "",
    "host": "localhost",
    "dialect": "mysql",
    "port": 3306
```
database为新建的Schema的名称，例如blockchain。username可以使用root，然后填入密码。如果您使用Mysql云服务，也需要修改host等字段
```
"port": 10086,
"publicIp": "",
```
port默认10086，您可以选择任意一个未被占用的端口。然后填入您的公网ip
```
"forging": {
    "secret": [],
    "access": {
      "whiteList": [
        "127.0.0.1"
      ]
    }
  },
```
在secret里填入您的受托人密钥
```
"forging": {
    "secret": [
      "xxx1",
      "xxx2"
    ],
  },
```
**注意** 不管是一台机器还是多台机器，绝不要配置重复的受托人密钥

5.进行到这步，命令行下运行
```
./node ./bin/migrate.js --up
```
然后运行
```
./graphene test
```
如果没有报错，则说明一切正常

6.启动服务，运行
```
./graphene start
```
可以运行
```
./graphene status
```
查看服务运行状态

7.其他命令
```
./graphene upgrade
 升级服务，如果有新版本，则会自动下载解压替换并重启服务

./graphene ismainnet
 是否是主网络

./graphene status
 服务状态，是否运行

./graphene stop
 关闭服务
```
