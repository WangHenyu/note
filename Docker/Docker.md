### `Docker`启动`MySQL`

- 在 `/usr/software/mysql8.0` 下创建`conf`、`data`文件夹
- 在`conf`文件夹下创建`my.conf`文件
- 执行命令

~~~shell
docker run --restart=always -d --name mysql8 \
-p 3306:3306 \
-v /usr/software/mysql8.0/conf/my.cnf:/etc/mysql/my.cnf \
-v /usr/software/mysql8.0/data:/var/lib/mysql \
-e MYSQL_ROOT_PASSWORD=root \
mysql:8.0.26
~~~

### `Docker`启动`Redis`

~~~shell
docker run --restart=always \
--log-opt max-size=100m \
--log-opt max-file=2 \
-p 6379:6379 \
--name redis \
-v /usr/software/redis/conf/redis.conf:/etc/redis/redis.conf \
-v /usr/software/redis/data:/data \
-d redis redis-server /etc/redis/redis.conf \  
--appendonly yes \
--requirepass 123456 \
~~~

### Docker启动RabbitMq

```shell
docker run -d --name mq \
-e RABBITMQ_DEFAULT_USER=why \
-e RABBITMQ_DEFAULT_PASS=123 \
--hostname mq \
-p 15672:15672 \
-p 5672:5672 \
 rabbitmq:3-management
```

### Docker启动elasticSearch

```shell
docker run -d --name es \
-e "ES_JAVA_OPTS=-Xms512m -Xmx512m" \
-e "discovery.type=single-node" \
-v es-data:/usr/share/elasticsearch/data \
-v es-plugins:/usr/share/elasticsearch/plugins \
--privileged \
--network es-net \
-p 9200:9200 \
-p 9300:9300 \
elasticsearch:7.12.1
```

命令解释：

- `-e "cluster.name=es-docker-cluster"`：设置集群名称
- `-e "http.host=0.0.0.0"`：监听的地址，可以外网访问
- `-e "ES_JAVA_OPTS=-Xms512m -Xmx512m"`：内存大小
- `-e "discovery.type=single-node"`：非集群模式
- `-v es-data:/usr/share/elasticsearch/data`：挂载逻辑卷，绑定es的数据目录
- `-v es-logs:/usr/share/elasticsearch/logs`：挂载逻辑卷，绑定es的日志目录
- `-v es-plugins:/usr/share/elasticsearch/plugins`：挂载逻辑卷，绑定es的插件目录
- `--privileged`：授予逻辑卷访问权
- `--network es-net` ：加入一个名为es-net的网络中
- `-p 9200:9200`：端口映射配置

### Docker启动Kibana

```shell
docker run -d --name kibana \
--network=es-net \
-e ELASTICSEARCH_HOSTS=http://es:9200 \
-p 5601:5601  \
kibana:7.12.1
```

### Docker启动ZooKeeper

```shell

docker run -d --name zookeeper \
--network app-tier \
-p 2181:2181 \
-e ALLOW_ANONYMOUS_LOGIN=yes \
bitnami/zookeeper:latest
```

### Docker启动Kafka

```shell

docker run -d --name kafka \
--network app-tier \
-p 9092:9092 \
-e ALLOW_PLAINTEXT_LISTENER=yes \
-e KAFKA_CFG_ZOOKEEPER_CONNECT=zookeeper:2181 \
-e KAFKA_CFG_ADVERTISED_LISTENERS=PLAINTEXT://localhost:9092 \
bitnami/kafka:2.7.0
```

1、进入容器，进入kafka安装位置 

~~~shell
cd /opt/bitnami/kafka/bin
~~~
2、通过 kafka-topics.sh 脚本来创建一个名为topic-test1并且副本数为1、分区数为1的topic

~~~shell
kafka-topics.sh --create \
--zookeeper zookeeper:2181 \ 
--topic topic-email \
--replication-factor 1 \ 
--partitions 1
~~~
2、查看topic
~~~shell
kafka-topics.sh --zookeeper zookeeper:2181 --list
~~~
3、删除topic
~~~shell
kafka-topics.sh --zookeeper zookeeper:2181 --delete --topic example
~~~
4、获取topic详情
~~~shell
kafka-topics.sh --zookeeper zookeeper:2181 --describe --topic example
~~~

> 注意
>
> `kafka3.0`已经不需要依赖`zookeeper`来创建topic，新版的`kafka`创建topic指令为下

~~~shell
./kafka-topics.sh --create \
--bootstrap-server 127.0.0.1:9092 \
--replication-factor 1 \
--partitions 1 \
--topic topic-email
~~~

生产几条信息
~~~shell
kafka-console-producer.sh --broker-list localhost:9092 --topic test
~~~

消费
~~~shell
kafka-console-consumer.sh --bootstrap-server slave1:9097  --topic test --from-beginning
~~~
