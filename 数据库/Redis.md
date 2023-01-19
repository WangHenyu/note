# Redis

> 官方网址 https://redis.io/
>
> 中文文档 http://redisdoc.com/

## 启动与关闭

### 启动警告问题

警告1

> WARNING The TCP backlog setting of 511 cannot be enforced 
>
> because /proc/sys/net/core/somaxconn is set to the lower value of 128

~~~shell
# 1.修改配置文件
vim /etc/sysctl.conf
# 2.添加内容
net.core.somaxconn = 1024
# 3.生效配置
sysctl -p
~~~

警告2

> WARNING overcommit_memory is set to 0! Background save may fail under low memory condition. 
>
> To fix this issue add 'vm.overcommit_memory = 1' to /etc/sysctl.conf and then reboot or run the 
>
> command 'sysctl vm.overcommit_memory=1' for this to take effect

overcommit_memory内存策略

- 0表示内核将检查是否有足够的可用内存供应用进程使用

- 1表示内核允许分配所有的物理内存，而不管当前的内存状态如何

- 2表示内核允许分配超过所有物理内存和交换空间总和的内存

~~~shell
# 1.修改配置文件
vim /etc/sysctl.conf
# 2.添加内容
vm.overcommit_memory = 1
# 3.生效配置
sysctl -p
~~~

警告3

> WARNING you have Transparent Huge Pages (THP) support enabled in your kernel. This will create latency and memory usage 
>
> issues with Redis. To fix this issue run the command 'echo never > /sys/kernel/mm/transparent_hugepage/enabled' as root, and
>
> add it to your /etc/rc.local in order to retain the setting after a reboot. Redis must be restarted after THP is disabled.

~~~shell
echo never > /sys/kernel/mm/transparent_hugepage/enabled
source /etc/rc.local
~~~



### 服务启动与关闭

1.启动服务

- 前台启动 `redis-server`
- 后台启动 `redis-server &`
- 配置文件启动 `redis-server redis.conf &`



2.启动客户端 `redis-cli [可选项]`

- -p [端口号] 连接指定端口的客户端
- -h [IP地址] 连接指定机器上的客户端

~~~shell
redis-cli
redis-cli -p 6379
redis-cli -h 192.168.140.100 -p 6379
~~~



3.退出客户端 `exit` 或 `quit`

4.关闭服务 `redis-cli shutdown`

> 如果redis设置了密码

~~~shell
# 1.进入客户端
redis-cli
# 2.输入密码
auth 123321
# 3.关闭服务
shutdown save
# 4.退出
quit
~~~



5.配置文件常用设置 `vim /安装目录/reids.conf`

~~~properties
# 工作目录,默认是当前目录
dir .
# 允许访问的地址默认是127.0.0.1
# 修改为0.0.0.0则可以在任意IP访问
# 生产环境不要设置为0.0.0.0
bind 0.0.0.0
# 守护进程,修改为yes后即可后台运行
daemonize yes 
# 密码
requirepass 123321
# 监听的端口
port 6379
# 数据库数量,默认16
databases 1
# 设置能够使用的最大内存
maxmemory 512mb
# 日志文件,默认为空
logfile "redis.log"
~~~



## 常用数据类型及命令

### 通用命令

1.查看存在的key `keys [pattern]`

> pattern: 具体的字符串,可以使用通配符
>
> - ?  匹配一个字符
> - \*  匹配任意个字符
> - [] 匹配[]任意一个字符

~~~properties
# 查看全部的key
keys *
# 生产环境不建议使用
~~~

2.删除key `del key [key...]`

3.判断key是否存在 `exists key [key...]`

- 返回值是存在的个数

~~~properties
# 判断多个
exists username password telephone
~~~

4.设置生存时间 `expire key second`

5.查看key的有效剩余时间 `ttl key`

- -1表示没有设置生存时间
- -2表示key不存在

6.移动key `move key index`

7.查看key类型 `type key`

8.重命名key `rename key newkey`

~~~properties
# 重命名
rename username name
~~~



### 字符串类型

> key和value都是字符串类型
>
> value根据格式的不同又被分为三类,但本质上都是字符串
>
> - 普通字符串
> - 整数 可以做自增,自减功能
> - 浮点数 可以做自增,自减功能
>
> 不管是哪种格式,底层都是字节数组形式存储,只不过是编码方式不同
>
> 字符串类型的最大空间不能超过512m

<h4>常见命令</h4>

1.添加数据

- `SET key value` 添加或者修改已经存在的数据

- `MSET key value [key value ...]` 批量添加
- `SETNX key value`: 只有KEY不存在才添加
 - `SETEX key seconds value` 添加数据并且指定有效期
 - `MSETNX key value [key value ...]` 批量添加,但只要有存在的KEY就放弃添加

~~~properties
# 批量添加
mset username root password 123
# 添加并指定有效期
setex telephone 20 17826374628
~~~



2.获取数据

 - `GET key` 根据KEY获取值

 - `  MGET key [key ...]` 根据多个KEY获取值
 - `GETRANGE key start end` 根据KEY获取指定区间的子串

| 字符串   | S    | T    | R    | I    | N    | G    |
| -------- | :--- | ---- | ---- | ---- | ---- | ---- |
| 正数下标 | 0    | 1    | 2    | 3    | 4    | 5    |
| 负数下标 | -6   | -5   | -4   | -3   | -2   | -1   |

~~~properties
# 批量获取
mget username password
# 获取全部字符串
getrange username 0 -1
~~~



3.自增自减

 - `INCR key` 让整型类VALUE自增1
 - `DECR key` 让整型类VALUE自减1 
 - `INCRBY key increment`让整型VALUE自增并指定步长
 - `INCRBYFLOAT key increment` 让浮点类型的数字自增并指定步长

~~~properties
# 正数自增
incrby num 10
# 浮点数自增(负数自减)
incrbyfloat floatnum -1.5
~~~



4.其他命令

- `APPEND key value` 将字符串追加到指定KEY
- `STRLEN key` 获取指定KEY的字符串长度
- `SETRANGE key offset value`修改指定KEY的字符串,从offset开始覆盖



### KEY的结构

> reids的key允许有多个单词形成层级结构
>
> 单词之间使用冒号`:`隔开 
>
> 推荐 `项目名:业务名:类型:id`



### 散列类型

>Hash类型,其value是无序字典,结构类似Java的HashMap
>
>Hash结构可以将对象中的每个字段独立存储
>
>Hash类型的数据是单个key对应多个field-value对

~~~shell
|        |     VALUE     |
|  KEY   |———————————————|
|        | field | value |
——————————————————————————
|        | name  | Jack  |
| user:1 |  age  |  20   |
|        |  sex  |  man  |
~~~

<h4>常用命令</h4>

1.添加数据

- `HSET key field value`添加或者修改hash类型key的field的值
- `HMSET key field value [field value ...]` 批量添加多个hash类型key的field的值
- `HSETNX key field value` 当field不存在时才添加

~~~properties
# 添加hash类型数据
hset user name andy
# 批量添加hash类型数据
hmset user name eric age 20
~~~



2.获取数据

- `HGET key field`：获取hash类型key的field的值

- `HMGET key field [field ...]`：批量获取多个hash类型key的field的值
- `HGETALL key`：获取hash类型的key中的所有的field和value

~~~properties
# 获取hash类型数据
hget user:1 name
# 批量获取
hmget user:2 name age
#获取所有的key和value
hgetall user:2
~~~



3.删除数据

- `HDEL key field [field ...]` 删除hash类型key的field



4.其他命令

- `HLEN key` 获取key的filed的个数
- `HEXISTS key field` 判断key的field是否存在
- `HKEYS key` 获取key的所有field
- `HVALS key` 获取key的所有value
- `HINCRBY key field increment` 让key的字段值自增并指定步长

~~~properties
# 获取所有field
hkeys user:1
# 获取所有value
hvals user:1
# 判断field是否存在
hexists user:1 name
~~~



### 列表类型

>List类型的value结构类似Java中的LinkList,可以看作是双向列表
>
>List类型特点
>
>- 有序
>- 元素可以重复
>- 增删数据快,查询速度慢

~~~shell
            —————        —————        —————
———LPUSH——>|  D  | <——> |  C  | <——> |  A  |<——RPUSH———
<——LPOP————|_____|      |_____|      |_____|———RPOP————>
~~~

<h4>常见命令</h4>

1.添加数据

- `LPUSH key value [value ...]` 向列表左侧插入元素
- `RPUSH key value [value ...]` 向列表右侧插入元素
- `LINSERT key BEFORE|AFTER pivot value` 在指定元素前|后插入元素

~~~properties
# 从列表左侧插入——>dcb
lpush str b c d
# 从列表右侧插入——>bcd
rpush str b c d  
# 返回值: 列表长度
linsert str before b a
~~~



2.获取数据

- `LPOP key`：移除并返回列表左侧的元素,没有元素则返回nil

- `RPOP key`：移除并返回列表右侧的元素,没有元素则返回nil
- `LINDEX key index` 返回列表对应下标元素
- `BLPOP key [key ...] timeout` 从列表左侧移除并返回,若key不存在就阻塞指定时间
- `BRPOP key [key ...] timeout` 从列表右侧移除并返回,若key不存在就阻塞指定时间

~~~properties
# 移除并返回最左侧元素
lpop str
# 获取下标为0的元素
lindex str 0
# 若key不存在就等待10s
#10s若有元素插入就返回插入的元素 否则返回nil
blpop str 10
~~~



3.其他命令

- `LLEN key` 获取指定列表的长度
- `LTRIM key start stop` 截取指定区间的元素组成新列表
- `LRANGE key star stop` 返回指定区间的所有元素
- `LSET key index value` 修改指定列表的下标元素
- `LREM key count value` 移除列表中元素相同的元素

> count > 0 从列表左侧移除count个与value相同元素
>
> count < 0 从列表右侧移除count个与value相同元素
>
> count = 0 移除所有与value相同元素
>
> ltrim 和 lrange 的区别: ltrim修改原数据,lrange不修改原数据

~~~properties
# 截取0-2下标元素赋值给str
ltrim str 0 2
# 返回0-2之间的元素
lrange str 0 2
# 修改列表下标为0的元素
lset str 0 x
# 移除所有与a相同的元素
lrem str 0 a
~~~



### SET类型

>set类型的value的结构类似Java中的HashSet
>
>set类型的特点
>
>- 无序
>- 元素不可重复

<h4>常见命令</h4>

1.单集合操作

- `SADD key member [member ...]` 向set集合添加元素
- `SMEMBERS key` 返回集合中元素
- `SRANDMEMBER key [count]` 随机返回count个元素
- `SREM key member [member ...]` 移除set集合中指定元素
- `SPOP key [count]` 从set集合中随机移除count个元素并返回
- `SCARD key` 返回集合中元素个数
- `SISMEMBER key member` 判断元素在集合中是否存在
- `SMOVE source destination member` 将指定元素移动到新的set集合中

~~~properties
# 添加多个元素
sadd setA a b c d
# 随机移除2个元素 
spop setA 2
# 返回集合中的元素
smembers setA
# 随机返回3个元素 
srandmember setA 3
# 将setA集合中的b元素移动到setB集合
smove setA setB b
~~~



2.多集合操作

- SDIFF key [key ...]  返回集合的差集
- SUNION key [key ...] 返回集合的并集
- SINTER key [key ...] 返回集合的交集

~~~properties
# 返回集合A中存在而集合B中不存在的元素
sdiff setA setB
# 返回集合A和集合B中都存在的元素
sinter setA setB
# 返回集合A、B、C中的所有元素
sunion setA setB setC
~~~



### ZSET类型

> SortedSet是可排序的set集合,与Java中的TreeSet有些类似
>
> SortedSet中的每个元素都带有score属性,可以基于score属性对元素排序
>
> SortedSet类型特点
>
> - 可排序
> - 元素不可重复

<h4>常见指令</h4>

1.添加元素

- `ZADD key score member` 添加元素到zset集合

~~~properties
# 添加元素并指定score
zadd setA 10 a
# 添加多个元素
zadd setA 15 b 20 c
~~~



2.获取元素

- `ZRANGE key min max` 按照score排序后获取指定排名范围内的元素
- `ZRANGEBYSCORE key min max` 按照score排序后获取指定score范围内的元素

~~~properties
# 获取全部元素
zrange setA 0 -1
# 获取分数最小的3个元素
zrange setA 0 2
# 获取分数在10-15间的元素
zrangebyscore setA 10 15
~~~



3.其他指令

- `ZREM key member` 删除szet集合中的指定元素
- `ZSCORE key member` 获取zset集合中的指定元素的score值
- `ZRANK key member` 获取zset集合中的指定元素的排名
- `ZCARD key` 获取zset集合中的元素个数
- `ZCOUNT key min max` 统计score值在给定范围内的所有元素的个数
- `ZINCRBY key increment member` 让szset集合中的指定元素分数自增
- ZDIFF、ZINTER、ZUNION 求差集、交集、并集

~~~properties
# 删除a元素
zrem setA a
# 获取指定b元素的score
zscore setA b
# 获取c元素在集合中的排名 
zrank setA c
# 获取10-20分元素的个数
zcount setA 10 20
# 将c元素的分数加10
zincrby setA 10 c
~~~



## Java客户端

> redis官方提供了各种语言的客户端 https://redis.io/clients
>
> 最常用的Java客户端
>
> - Jedis 以命令作为方法名,线程不安全,多线程环境下需要基于线程池
> - Lettuce 基于Netty实现,线程安全且支持同步、异步、响应式编程
> - Redisson 基于Redis实现的分布式、可伸缩的Java数据结构集合

### Jdeis

> Jedis官方网址： https://github.com/redis/jedis

<h4>快速入门</h4>

1.引入依赖

~~~xml
<!--jedis依赖-->
<dependency>
    <groupId>redis.clients</groupId>
    <artifactId>jedis</artifactId>
    <version>3.2.0</version>
</dependency>
~~~



2.建立连接

~~~java
@BeforeEach
void init(){
    // 建立连接
    jedis = new Jedis("192.168.140.132",6379);
    // 设置密码(无密码可不写)
    jedis.auth("123321");
    // 选择数据库
    jedis.select(0);
}
~~~



3.操作资源

> Jedis的API命名与redis命名相同,直接使用即可

~~~java
@Test
void testPing(){
    // 测试连接,返回PONG则连接成功
    String ping = jedis.ping();
    System.out.println(ping);
    // 获取所有的key
    Set<String> keys = jedis.keys("*");
    keys.forEach(System.out::println);
}

@Test
void testString(){
    // 添加字符串类型数据
    String result = jedis.set("username", "andy");
    // 获取key的值
    String username = jedis.get("username");
    System.out.println("result="+result+"\nusername="+username);
}

@Test
void testHash(){
    // 添加Hash类型的数据
    // 也可以使用map: jedis.hset("user:5",userMap);
    jedis.hset("user:4", "username","alice");
    jedis.hset("user:4", "password","12345");
    jedis.hset("user:4", "telephone","1234");
    // 获取指定field的值
    String password = jedis.hget("user:4", "password");
    String telephone = jedis.hget("user:4", "telephone");
    System.out.println("password="+password+"\ntelephone="+telephone);
}
~~~



4.释放连接

~~~java
@AfterEach
void destroy(){
    if (jedis != null){
        jedis.close();
    }
}
~~~

>连接失败的可能原因：
>
>1.密码问题
>
>2.Redis保护问题（选1种即可）
>
>- 禁用保护模式 在配置文件redis.conf中添加 `protected-mode no`
>- 设置绑定地址或身份验证密码 在redis配置文件中添加 `bind ip地址`
>- 设置密码 在redis配置文件中添加`requirepass 123321`
>
>2.防火墙问题
>
>- 关闭防火墙 `systemctl stop firewalld`
>- 开放防火墙6379端口 `firewall-cmd --add-port=6379/tcp --permanent`

<h4>Jedis连接池</h4>

> Jedis本身是线程不安全的,并且频繁的创建和销毁连接会有性能损耗.因此推荐使用Jedis连接池代替Jedis的直连方式

~~~java
public class JedisConnectionFactory {
    private static final JedisPool jedisPool;
    
    static {
        JedisPoolConfig jedisPoolConfig = new JedisPoolConfig();
        // 最大连接
        jedisPoolConfig.setMaxTotal(8);
        // 最大空闲连接
        jedisPoolConfig.setMaxIdle(8);
        // 最小空闲连接
        jedisPoolConfig.setMinIdle(0);
        // 设置最长等待时间 ms
        jedisPoolConfig.setMaxWaitMillis(200);
        // 创建线程池
        jedisPool = new JedisPool(jedisPoolConfig, "192.168.150.101", 6379,1000,"123321");
    }
    // 获取Jedis对象
    public static Jedis getJedis()
        return jedisPool.getResource();
	}
}

~~~



<h5>SpringBoot整合</h5>

1.创建SpringBoot工程

2.导入依赖

~~~xml
<dependency>
    <groupId>redis.clients</groupId>
    <artifactId>jedis</artifactId>
</dependency>
~~~



3.配置连接池

~~~yaml
why:
  redis:
    port: 6379
    password: "123321"
    host: 192.168.140.132
    timeout: 2000
    jedis:
      pool:
        # 最大空闲数
        max-idle: 10
        # 最大连接数
        max-total: 10
        # 最小空闲数
        min-idle: 2
~~~



4.注入连接池对象

~~~java
@Configuration
public class JedisConfig {
    @Value("${why.redis.port}")
    private int port;
    @Value("${why.redis.password}")
    private String password;
    @Value("${why.redis.host}")
    private String host;
    @Value("${why.redis.timeout}")
    private int timeout;

    @Bean
    @ConfigurationProperties(prefix = "why.redis.jedis.pool")
    public JedisPoolConfig jedisPoolConfig(){
        // 连接池配置
        JedisPoolConfig jedisPoolConfig = new JedisPoolConfig();
        return jedisPoolConfig;
    }

    @Bean
    public JedisPool jedisPool(){
        // 连接池
        JedisPool jedisPool = new JedisPool(jedisPoolConfig(),host,port,timeout,password);
        return jedisPool;
    }
}
~~~



5.使用连接池

~~~java
@SpringBootTest
public class SoringBootApplicationTest {

    @Autowired
    private JedisPool jedisPool;

    @Test
    void test(){
        // 从线程池种获取jedis
        Jedis jedis = jedisPool.getResource();
        // 存数据
        jedis.set("name","小飞棍");
        // 取数据
        System.out.println(jedis.get("name"));
        // 返回给线程池
        if (jedis != null){
            jedis.close();
        }
    }
}
~~~



### SpringDataRedis

> 官网网址： https://spring.io/projects/spring-data-redis
>
> - 提供了对不同Redis客户端的整合（Lettuce和Jedis）
>
> - 提供了RedisTemplate统一API来操作Redis
>
> - 支持Redis的发布订阅模型
>
> - 支持Redis哨兵和Redis集群
>
> - 支持基于JDK、JSON、字符串、Spring对象的数据序列化及反序列化
>
>    ....

<h4>快速入门</h4>

| API                         | 返回值类型      | 说明                  |
| :-------------------------- | :-------------- | :-------------------- |
| redisTemplate.opsForValue() | ValueOperations | 操作String类型数据    |
| redisTemplate.opsForHash()  | HashOperations  | 操作Hash类型数据      |
| redisTemplate.opsForList()  | ListOperations  | 操作List类型数据      |
| redisTemplate.opsForSet()   | SetOperations   | 操作Set类型数据       |
| redisTemplate.opsForZSet()  | ZSetOperations  | 操作SortedSet类型数据 |
| redisTemplate               | 无              | 通用的命令            |

1.创建SpringBoot工程

2.导入依赖

~~~xml
<!--Redis依赖-->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
<!--连接池依赖-->
<dependency>
    <groupId>org.apache.commons</groupId>
    <artifactId>commons-pool2</artifactId>
</dependency>
~~~



3.配置文件

~~~yaml
spring:
  redis:
    host: 192.168.140.132
    password: 123321
    port: 6379
    lettuce:
      pool:
        max-idle: 10
        max-active: 10
        min-idle: 2
        #   连接等待时间
        max-wait: 1000
~~~



4.使用RedisTemplate

~~~java
@SpringBootTest
class RedisApplicationTests {

    @Autowired
    private RedisTemplate redisTemplate;

    @Test
    void testRedis() {
        ValueOperations valueOperations = redisTemplate.opsForValue();
        // 添加字符串类型的数据
        valueOperations.set("name","eric");
        // 获取字符串类型的数据
        Object name = valueOperations.get("name");
        System.out.println(name);
    }

    @Test
    void testHash(){
        HashOperations hashOperations = redisTemplate.opsForHash();
        Map<String,String> userMap = new HashMap<>();
        userMap.put("name","tom");
        userMap.put("email","123@126.com");
        // 添加hash类型的数据
        hashOperations.putAll("user:5",userMap);
        // 获取field的值
        String name = hashOperations.get("user:5", "name").toString();
        String mail = hashOperations.get("user:5", "email").toString();
        System.out.println("name="+name+",email="+mail);
    }
}
~~~



<h4>自定义序列化</h4>

> RedisTemplate可以接收任意Object作为值写入Redis,只不过写入前会把Object序列化为字节形式 默认是采用JDK序列化
>
> 最终存入Redis如这种形式:\xAC\xED\x00\x05t\x00\x06user:5
>
> - 可读性差
> - 内存占用大

1.自定义RedisTemplate

~~~java
@Bean
public RedisTemplate<String,Object> redisTemplate(RedisConnectionFactory factory) {
    RedisTemplate<String,Object> redisTemplate = new RedisTemplate<>();
    // 设置连接工厂
    redisTemplate.setConnectionFactory(factory);
    // 设置序列化工具
    GenericJackson2JsonRedisSerializer jackson2JsonRedisSerializer = new GenericJackson2JsonRedisSerializer();
    // key和hashKey采用string序列化
    redisTemplate.setKeySerializer(RedisSerializer.string());
    redisTemplate.setHashKeySerializer(RedisSerializer.string());
    // value和hashValue采用Json序列化
    redisTemplate.setValueSerializer(jackson2JsonRedisSerializer);
    redisTemplate.setHashValueSerializer(jackson2JsonRedisSerializer);
    return redisTemplate;
}
~~~

2.使用

~~~java
@Test
void testString(){
    ValueOperations<String, Object> stringOperations = redisTemplate.opsForValue();
    stringOperations.set("rouse",new User("rouse","123456"));
    //{"@class":"com.why.pojo.User","username":"rouse","password":"123456"}
    Object rouse = stringOperations.get("rouse");
    System.out.println(rouse);
}
~~~

> 尽管JSON的序列化方式可以满足我们的需求,但依然存在一些问题
>
> 为了在反序列化时知道对象的类型,JSON序列化器会将类的class类型写入json结果中存入Redis,会带来额外的内存开销
>
> 如：{"@class":"com.why.pojo.User","username":"rouse","password":"123456"}

解决方案：手动序列化

<h4>StringRedisTemplate</h4>

> Spring默认提供了StringRedisTemplate类,它的key和value的序列化方式默认就是String方式

StringRedisTemplate简单使用

~~~java
@Autowired
private StringRedisTemplate stringRedisTemplate;

private static final ObjectMapper mapper = new ObjectMapper();

@Test
void testStringRedisTemplate() throws JsonProcessingException {
    // 手动序列化
    String alice = mapper.writeValueAsString(new User("Alice", "654321"));
    // 添加数据
    stringRedisTemplate.opsForValue().set("user:10",alice);
    // 取出数据
    String jsonStr = stringRedisTemplate.opsForValue().get("user:10");
    // 手动反序列化
    User user = mapper.readValue(jsonStr, User.class);
    System.out.println(user);
}
~~~

