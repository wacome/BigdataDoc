# redis安装配置

## 1、解压redis

```bash
[root@bigdata1 software]# tar -zxvf redis-6.2.6.tar.gz -C /opt/module/
```

## 2、配置环境变量

```bash
[root@bigdata1 module]# vim /etc/profile.d/bigdata.sh
```

```bash
#REDIS_HOME
export REDIS_HOME=/opt/module/redis-6.2.6
export PATH=$PATH:$REDIS_HOME/bin
```

```bash
[root@bigdata1 module]# source /etc/profile
```

## 3、编译安装redis

```bash
[root@bigdata1 module]# cd /opt/module/redis-6.2.6
[root@bigdata1 redis-6.2.6]# make
```

若显示以下信息，则说明编译成功

```
Hint: It's a good idea to run 'make test' ;)

make[1]: Leaving directory `/opt/module/redis-6.2.6/src'
```

安装redis

```bash
[root@bigdata1 redis-6.2.6]# make PREFIX=/opt/module/redis-6.2.6 install
```

若显示以下信息，则说明安装成功

```
    INSTALL redis-server
    INSTALL redis-benchmark
    INSTALL redis-cli
make[1]: Leaving directory `/opt/module/redis-6.2.6/src'
```

## 4、启动redis

```bash
[root@bigdata1 redis-6.2.6]# redis-server ./redis.conf &
```

## 5、redis客户端连接

另开一个bigdata1终端，连接redis

```bash
[root@bigdata1 redis-6.2.6]# redis-cli
```

> [!NOTE]
>
> 若比赛需要配置外网访问redis，需要修改redis.conf中的如下配置
> ```bash
> [root@bigdata1 redis-6.2.6]# vim redis.conf
> ```
> 将bind注释取消
> ```conf
> bind * -::*
> ```
> 将protected-mode设置为no
> ```conf
> protected-mode no
> ```
> 重启redis
> ```bash
> [root@bigdata1 redis-6.2.6]# redis-cli shutdown
> [root@bigdata1 redis-6.2.6]# redis-server ./redis.conf &
> ```