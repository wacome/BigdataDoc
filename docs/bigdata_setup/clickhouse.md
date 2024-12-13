# clickhouse单节点安装配置

## 1、解压clickhouse

```bash
[root@bigdata1 software]# tar -zxvf clickhouse-client-21.9.4.35.tgz -C /opt/module/
[root@bigdata1 software]# tar -zxvf clickhouse-server-21.9.4.35.tgz -C /opt/module/
[root@bigdata1 software]# tar -zxvf clickhouse-common-static-21.9.4.35.tgz -C /opt/module/
[root@bigdata1 software]# tar -zxvf clickhouse-common-static-dbg-21.9.4.35.tgz -C /opt/module/
```

## 2、执行启动各个安装脚本

```bash
[root@bigdata1 module]# /opt/module/clickhouse-client-21.9.4.35/install/doinst.sh
[root@bigdata1 module]# /opt/module/clickhouse-common-static-21.9.4.35/install/doinst.sh
[root@bigdata1 module]# /opt/module/clickhouse-common-static-dbg-21.9.4.35/install/doinst.sh
[root@bigdata1 module]# /opt/module/clickhouse-server-21.9.4.35/install/doinst.sh
# 当执行clickhouse-server-21.9.4.35的安装脚本时，会提示设置root用户的密码，设置为123456
# 之后会提示是否设置远程登录，设置为y
```

## 3、配置远程访问和端口号

```bash
[root@bigdata1 module]# vim /opt/module/clickhouse-server-21.9.4.35/etc/clickhouse-server/config.xml
```

将原本下面的代码注释去掉，并将端口号改为9001
需要去掉两边的注释符号：`<!--`和`-->`

```xml
    <listen_host>::</listen_host>

    <!-- Same for hosts without support for IPv6: -->
    <listen_host>0.0.0.0</listen_host>
```

```xml
<tcp_port>9001</tcp_port>
```

## 4、删除默认监听文件（listen.xml）

```bash
[root@bigdata1 module]# rm -f /etc/clickhouse-server/config.d/listen.xml
```

## 5、启动服务
```bash
[root@bigdata1 module]# clickhouse start
```

会输出这样的信息：

```
chown --recursive clickhouse '/var/run/clickhouse-server/'
Will run su -s /bin/sh 'clickhouse' -c '/usr/bin/clickhouse-server --config-file /etc/clickhouse-server/config.xml --pid-file /var/run/clickhouse-server/clickhouse-server.pid --daemon'
Waiting for server to start
Waiting for server to start
Server started
```

查看服务状态

```bash
[root@bigdata1 module]# clickhouse status
```

服务状态结果为：

```
/var/run/clickhouse-server/clickhouse-server.pid file exists and contains pid = 21095.
The process with pid = 21095 is running.
```