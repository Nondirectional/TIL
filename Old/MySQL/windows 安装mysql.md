# windows 安装mysql全攻略



点击链接进入下载https://dev.mysql.com/downloads/mysql/> 

Select Operating System:选择Microsoft Windows

下载其中的**Windows (x86, 64-bit), ZIP Archive**文件

解压到目标安装目录下，如`C:\mysql-8.0.15-winx64\`

配置环境变量

新建`MYSQL_HOME`变量，值为目标安装目录路径,如`C:\mysql-8.0.15-winx64\`

在Path变量最前端插入`%MYSQL_HOME%\bin;`然后保存

在mysql的安装目录下创建一个`my.ini`文件，其中内容为

```ini
[mysqld]
# 设置3306端口
port=3306
# 设置mysql的安装目录
basedir=C:\mysql-8.0.15-winx64
# 设置mysql数据库的数据的存放目录
datadir=C:\mysql-8.0.15-winx64\data
# 允许最大连接数
max_connections=200
# 允许连接失败的次数。这是为了防止有人从该主机试图攻击数据库系统
max_connect_errors=10
# 服务端使用的字符集默认为UTF8
character-set-server=utf8mb4
# 创建新表时将使用的默认存储引擎
default-storage-engine=INNODB
# 默认使用“mysql_native_password”插件认证
default_authentication_plugin=mysql_native_password
[mysql]
# 设置mysql客户端默认字符集
default-character-set=utf8mb4
[client]
# 设置mysql客户端连接服务端时默认使用的端口
port=3306
default-character-set=utf8mb4
```

注意设置，basedir和datadir这两项

打开cmd命令行

输入**mysqld -install**安装mysql

输入**mysqld --initialize**初始化mysql参数配置，将会随机生成root用户的密码

输入**net start mysql**启动mysql服务





进入mysql安装目录下的data目录，找到以.err为后缀名的文件，打开后找到

如`[Note] [MY-010454] [Server] A temporary password is generated for root@localhost: +%&Mix.fF7dX`其中:后的就是root用户的登录密码

输入**mysql -u root -p**，输入上行所得到的root用户登录密码进行登录

登陆后输入**ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY'新的用户密码';** 把新的用户密码替换成你想要的用户密码对root用户登录密码进行修改

