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



