# docker常用容器启动

## docker启动mysql容器

```shell
docker run -itd \
		   -p 3306:3306 \
		   --name mysql \
		   -v /why/mydata/mysql3306/log:/var/log/mysql \
		   -v /why/mydata/mysql3306/data:/var/lib/mysql \
		   -v /why/mydata/mysql3306/conf:/etc/mysql \
		   -e MYSQL_ROOT_PASSWORD=why0417 \
mysql:5.7.25
```

## docker启动rabbitmq容器

```shell
docker run \
		-e RABBITMQ_DEFAULT_USER=why \
		-e RABBITMQ_DEFAULT_PASS=123 \
		--name mq \
		--hostname mq1 \
		-p 15672:15672 \
		-p 5672:5672 \
		-d \
 rabbitmq:3-management
```

## docker启动elasticsearch容器

```sh
docker run -d \
	--name es \
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



## docker启动kibana容器

```shell
docker run -d \
		--name kibana \
		-e ELASTICSEARCH_HOSTS=http://es:9200 \
		--network=es-net \
		-p 5601:5601  \
kibana:7.12.1
```



