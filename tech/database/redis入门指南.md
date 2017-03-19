# Redis入门指南 第二版 人民邮电出版社

## 1 简介

Redis是一个开源,高性能,基于键值对的缓存与存储系统.通过多种键值数据类型来适应不同场景下的缓存与存储需求.同时Redis的诸多高级功能使其可以胜任消息队列,任务队列等不同的角色

### 1.2 特性

#### 1.2.1 存储结构

Redis以字典结构存储数据.Redis支持的键值数据类型如下:

- 字符串类型
- 散列
- 列表
- 集合
- 有序集合

#### 1.2.2 内存存储与持久化

Redis数据保存在内存中,同时提供了异步方式将内存数据写入磁盘中

#### 1.2.3 功能丰富

Redis虽然作为数据库,但是由于其功能丰富,越来越多的人用它来做缓存,队列系统等.

> 在性能上redis属于当线程模型,memcached支持多线程.redis的新更能已经足够优异,而且功能支持比memcache多,而且原生支持集群,memcache需要第三方工具来集群.所以新项目中直接选择redis将会是非常好的选择

除此之外,redis的列表类型键可以用来实现队列,并且支持阻塞式读取,可以很容易的实现一个高性能的优先级队列.redis同时还支持"发布/订阅"消息模式,可以基于此构建聊天室等系统

#### 1.2.4 简单稳定

redis使用c语言开发,代码量只有3万行.

## 2 准备

### 2.1 安装启动

redis可执行文件

- redis-server:redis服务器
- redis-cli:redis命令行工具
- redis-benchmark:redis性能测试工具
- redis-check-aof:aof文件修复工具
- redis-check-dump:rdb文件检查工具
- redis-sentinel:sentinel服务器

配置文件

- daemonize: yes 是Redis以守护进程模式运行
- pidfile: /var/run/redis_端口号.pid 设置redis的pid文件位置
- port: 端口号 设置redis监听的端口号
- dir: /var/redis/端口号 设置持久化文件存放位置

#### 2.2.2 停止redis

通过shutdown来通知redis,这样redis会把数据同步到磁盘上`redis-cli shutdown`

### 2.3 redis命令行客户端

redis-cli

```
Usage: redis-cli [OPTIONS] [cmd [arg [arg ...]]]
  -h <hostname>      服务器地址 (默认: 127.0.0.1).
  -p <port>          服务器端口 (默认: 6379).
  -s <socket>        服务器socket (是 hostname 和 port 无效).
  -a <password>      连接服务器时的密码
  -r <repeat>        指定的命令执行N次
  -i <interval>      当使用 -r 时,每个命令等待interval秒
                     可以使用二级时间,例如 -i 0.1.
  -n <db>            数据库号码.
  -x                 从STDIN读取最后一个参数.
  -d <delimiter>     分隔符 (default: \n).
  -c                 启动集群模式 (遵循 -ASK and -MOVED 重定向).
  --raw              使用原始格式答复 (当STDOUT不是tty时,默认).
  --no-raw           轻质格式化输出,即使STDOUT不是tty时
  --csv              输出CSV格式
  --stat             打印服务器滚动状态:men,clients,....
  --latency          进入一个特殊模式连续采集延迟问题
  --latency-history  类似 --latency 但是随着时间来跟踪延迟问题,默认是每15秒,使用 -i来修改
  --latency-dist     Shows latency as a spectrum, requires xterm 256 colors.
                     Default time interval is 1 sec. Change it using -i.
  --lru-test <keys>  Simulate a cache workload with an 80-20 distribution.
  --slave            Simulate a slave showing commands received from the master.
  --rdb <filename>   Transfer an RDB dump from remote server to local file.
  --pipe             Transfer raw Redis protocol from stdin to server.
  --pipe-timeout <n> In --pipe mode, abort with error if after sending all data.
                     no reply is received within <n> seconds.
                     Default timeout: 30\. Use 0 to wait forever.
  --bigkeys          Sample Redis keys looking for big keys.
  --scan             List all keys using the SCAN command.
  --pattern <pat>    Useful with --scan to specify a SCAN pattern.
  --intrinsic-latency <sec> Run a test to measure intrinsic system latency.
                     The test will run for the specified amount of seconds.
  --eval <file>      Send an EVAL command using the Lua script at <file>.
  --help             Output this help and exit.
  --version          Output version and exit.

Examples:
  cat /etc/passwd | redis-cli -x set mypasswd
  redis-cli get mypasswd
  redis-cli -r 100 lpush mylist x
  redis-cli -r 100 -i 1 info | grep used_memory_human:
  redis-cli --eval myscript.lua key1 key2 , arg1 arg2 arg3
  redis-cli --scan --pattern '*:12345*'

  (Note: when using --eval the comma separates KEYS[] from ARGV[] items)
```

#### 2.3.2 命令返回值

命令的返回值有5种类型

1. 状态回复:返回执行的状态,例如OK,PONG
2. 错误回复:出现命令不存在或者命令格式有误等情况是,返回具体错误消息
3. 整数回复:redis没有整数类型,但是提供了一些关于整数操作的命令
4. 字符串回复:例如当请求一个字符串类型键的键值或一个其他类型键中的某个元素时就会得到一个字符串回复
5. 多行字符串回复

### 2.4 配置

直接使用配置文件`redis-server /path/to/redis.conf`

不用重新启动redis,在redis运行时通过**CONFIG SET** 例如:`redis>CONFIG SET loglevel warning`

**CONFIG GET** 可以获得当前配置的情况

### 2.5 所数据库

每个数据库对外都是从0开始的递增数字命名,redis默认支持16个数据库,通过修改`databases`来修改这一数字,客户端建立连接后会自动选择0号数据库,不过可以随时使用`SELECT`来更换数据库.例如:`SELECT 1`

redis数据库不支持命名,每个数据库使用相同的密码,数据库的访问权限都一样.

## 3 入门

### 3.1 热身

**获得符合规则的键名列表**

`keys pattern` pattern匹配规则如下

- `?` 匹配一个字符
- `*`匹配任意个(包括0个)字符
- `[]` 匹配括号间人一个字符,可以使用 - 表示范围,例如`[a-z]`
- `\x` 匹配字符x,用于转义符号.如要匹配`?` 就使用`\?` 匹配`*` 就用 `\*`

```
设置名为bar的键
127.0.0.1:6379> set bar 1
OK
keys * 获取所有键
127.0.0.1:6379> keys *
1) "bar"
```

**判断一个键是否存在**

```
127.0.0.1:6379> EXISTS bar
(integer) 1
```

**删除键**

DEL key [key...] 返回删除键的个数

```
127.0.0.1:6379> DEL bar
(integer) 1
127.0.0.1:6379> DEL bar
(integer) 0
这里因为已经删除掉了,所以返回0
```

**获得键值的数据类型**

TYPE key

```
127.0.0.1:6379> TYPE bar
string
```

### 3.2 字符串类型

他可以用来存储任何形式的字符串,一个字符串键允许存储的数据的最大容量是512MB

字符串类型是其他4个类型的基础,其他类型从某种角度来说只是组织字符串的形式不同

#### 3.2.2 命令

**赋值与取值**

```
SET key value
GET key

例如

设置key
127.0.0.1:6379> SET test_key hello

获取key
127.0.0.1:6379> GET test_key
"hello"

当键不存在时返回空结果
127.0.0.1:6379> GET null_key
(nil)
```

**递增数字**

```
INCR key

127.0.0.1:6379> INCR num
(integer) 1

127.0.0.1:6379> INCR num
(integer) 2
如果键不存在,默认值是0,所以第一次放回结果1,如果键值不是整数,redis返回错误

127.0.0.1:6379> set num 'hello'
OK
127.0.0.1:6379> get num
"hello"
127.0.0.1:6379> INCR num
(error) ERR value is not an integer or out of range
```

####  3.2.4命令拾遗

**增加指定的整数**

```
127.0.0.1:6379> INCRBY num number

127.0.0.1:6379> INCRBY num 10
(integer) 10

127.0.0.1:6379> INCRBY num 15
(integer) 25
```

**减少指定的整数**

```
DECRBY key
127.0.0.1:6379> DECRBY num 15
(integer) 10
```

**增加浮点数**

```
127.0.0.1:6379> INCRBYFLOAT num 10.2212
"20.2212"

127.0.0.1:6379> NCRBYFLOAT num 10
"30.2212"
```

**向尾部追加值**

```
APPEND key value
如果键不存在,则相当于set key value

127.0.0.1:6379> APPEND num '12'
(integer) 9
127.0.0.1:6379> get num
"30.221212"
127.0.0.1:6379> INCRBYFLOAT num 10
"40.221212"
127.0.0.1:6379> APPEND num '12sdf'
(integer) 14
127.0.0.1:6379> INCRBYFLOAT num 10
(error) ERR value is not a valid float
上面append '12'后,num还是一个浮点数,可以继续当做浮点数来操作
```

**获取字符串长度**

```
STRLEN key
```

**同时获取/设置多个键值**

```
MGET key [key ...]

MSET key value [key value]

127.0.0.1:6379> MSET num 1 num2 2
OK

127.0.0.1:6379> KEYS *
1) "num"
2) "num2"

127.0.0.1:6379> MGET num num2
1) "1"
2) "2"
```

**位操作**

```
GETBIT key offset
SETBIT key offset value
BITCOUNT key [start] [end]
BITOP operation destkey key [key ...]
```
一个字节有8个二进制位组成.

GETBIT 可以获得一个字符串类型键指定位置的二进制位的值(0或1),索引从0开始.如果二进制位索引超出范围则默认值是0

```
127.0.0.1:6379> set test f
127.0.0.1:6379> getbit test 10
(integer) 0

127.0.0.1:6379> SETBIT test 1 0
(integer) 1
127.0.0.1:6379> get test
"&"
```

BITCOUNT 可以获得字符串中值为1的二进制位的个数

```
127.0.0.1:6379> get test
"&"
127.0.0.1:6379> BITCOUNT test
(integer) 3
```

BITOP 可以对多个字符串类型键进行位运算,并将结果存储在destkey参数指定的键中,bitop支持and,or,xor,not

```
127.0.0.1:6379> set foo1 bar
OK
127.0.0.1:6379> set foo2 aar
OK
127.0.0.1:6379> BITOP or foo3 foo1 foo2
(integer) 3
127.0.0.1:6379> get foo3
"car"
```

### 3.3 散列类型


## 4 进阶

## 5 实战

## 6 脚本

## 7 持久化

## 8 集群

## 9 管理
