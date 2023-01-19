

## 正向代理

如果把局域网外的Internet想象成一个巨大的资源库，则局域网中的客户端要访问Internet就需要通过代理服务器来访问，这种代理服务区就称为正向代理

在客户端（浏览器）配置代理服务器通过代理服务器进行互联网访问

## 反向代理

反向代理，其实客户端对代理是无感知的，因为客户端不需要任何配置就可以访问，我们只需要将请求发送到反向代理服务器，由反向代理服务器去选择目标服务器获取数据，再返回的客户端，此时反向代理服务器和目标服务器对外就是一个服务器，暴露的是代理服务器的地址，隐藏了真实服务器IP地址

# 负载均衡

单个服务器无法解决访问量过于庞大来的的问题，我们通过增加服务器数量，然后将请求分发到各个服务器上，将原先的请求集中到单个服务器的情况改为分发到多个服务器上，将负载分发到不同的服务器，就是所说的负债均衡

# 动静分离

为了加快网站的解析速度，可以把动态页面和静态页面由不同的服务器来解析，加快解析速度，降低原来服务器的压力

# 安装nginx

nginx官网：http://nginx.org

下载nginx安装包

![image-20220330122740068](C:\Users\uuu\AppData\Roaming\Typora\typora-user-images\image-20220330122740068.png)

![image-20220330123007084](C:\Users\uuu\AppData\Roaming\Typora\typora-user-images\image-20220330123007084.png)





安装前准备

1、gcc编译器是否安装

​	检查是否安装：yum list installed | grep gcc

​	安装：yum install gcc -y

2、openssl库是否安装

​	检查是否安装：yum list installed | grep openssl

​	安装：yum install openssl openssl-devel -y

3、pcre 库是否安装

​	检查是否安装：yum list installed | grep pcre

​	安装：yum install pcre pcre-devel -y

4、zlib库是否安装

​	检查是否安装：yum list installed | grep zlib

​	安装：yum install zlib zlib-devel -y



一次性安装命令

yum install gcc openssl openssl-devel pcre pcre-devel zlib zlib-devel -y



上传nginx上传到Linux

![image-20220330125041999](C:\Users\uuu\AppData\Roaming\Typora\typora-user-images\image-20220330125041999.png)





解压缩ngnix文件 tar -zxvf nginx-1.14.2.tar.gz -C /opt

解压后到nginx主目录中，然后执行./configure  --prefix=/opt/nginx  （指定安装目录，=号两边没有空格）

然后执行make

然后执行make install

安装就完成了





查看防火墙开放的端口 firewall-cmd --list-all

设置开放的端口 sudo firewall-cmd --add-port=80/tcp --permanent

重启防火墙 firewall-cmd --reload



# 常用命令

查看nginx版本号：./nginx -v

普通启动nginx：切换到nginx安装目录的sbin目录下执行 ./nginx

通过配置文件启动 

./nginx -c /opt/nginx/conf/nginx.conf （在sbin目录下）

/opt/nginx/sbin/nginx -c /opt/nginx/conf/nginx.conf

关闭nginx：./nginx -s stop

优雅关闭nginx：kill -QUIT 主pid （在处理完请求后关闭）

快速关闭nginx ：kill -TERM 主pid（无论请求是否处理完成直接关闭）

kill -QUIT 4881

kill -TERM 4849



重启nginx ： ./nginx -s reload （需要在nginx安装目录的sbin目录下）

# nginx的配置文件

nginx的配置文件在nginx的安装目录下的conf目录中  nginx.conf



# nginx配置实例-反向代理1

实现效果： 打开浏览器地址栏上输入www.123.com，就跳转到Linux的tomcat的主页面

准备工作：安装tomcat服务器并启动tomcat 

开放8080端口：	

重启防火墙：firewall-cmd --reload



第一步：在windows系统的host文件中配置域名和ip地址的对应关系

host问价位置C:\Windows\System32\drivers\etc\hosts

第二步 在nginx配置文件中配置请求转发

修改配置文件server_name 192.168.140.129

location / {

​	...

proxy_pass http://127.0.0.1:8080

}

![image-20220330185802708](C:\Users\uuu\AppData\Roaming\Typora\typora-user-images\image-20220330185802708.png)





:wq保存

重启nginx： ./nginx -c reload

![image-20220330185946753](C:\Users\uuu\AppData\Roaming\Typora\typora-user-images\image-20220330185946753.png)

# nginx配置实例-反向代理2

实现效果：

根据访问路径跳转到不同的端口服务中

nginx监听端口为9001

访问http://192.168.140.129:9001/edu 就跳转到127.0.0.1:8080

访问http://192.168.140.129:9001/vod 就跳转到127.0.0.1:8081

准备工作：

准备两个tomcat服务器并启动服务



第一步：

修改nginx配置文件

server{

​	listen 9001;

​	server_name 192.168.140.129;

​	location ~/edu/{

​		proxy_pass http://localhost:8080;

​	}

​	location ~/vod/{

​		proxy_pass http://localhost:8081;

​	}

}

![image-20220330194046598](C:\Users\uuu\AppData\Roaming\Typora\typora-user-images\image-20220330194046598.png)

第二步：重启nginx

第三步：开放8081和9001端口

测试

![image-20220330194747135](C:\Users\uuu\AppData\Roaming\Typora\typora-user-images\image-20220330194747135.png)

![image-20220330194802887](C:\Users\uuu\AppData\Roaming\Typora\typora-user-images\image-20220330194802887.png)

# location指令说明

location [ = | ~ | ~* | ^~] uri{...}



| =    | **精确匹配**；用于标准uri前，要求请求字符串和uri严格匹配。如果匹配成功，就停止匹配，立即执行该location里面的请求。 |
| ---- | ------------------------------------------------------------ |
| ~    | **正则匹配**；用于正则uri前，表示uri里面包含正则，并且区分大小写。 |
| ~*   | **正则匹配**；用于正则uri前，表示uri里面包含正则，不区分大小写。 |
| ^~   | **非正则匹配**；用于标准uri前，nginx服务器匹配到前缀最多的uri后就结束，不会使用正则匹配。 |
| 无   | **普通匹配（最长字符匹配）**；与location顺序无关，是按照匹配的长短来取匹配结果。若完全匹配，就停止匹配。 |



```
#普通匹配
location /{}    #location1
location demo{} #location2
# 最长字符匹配规则
# http://192.168.140.129/demo 这个请求会走location2 
# http://192.168.140.129/demoabc 这个请求也会走location2 

#精准匹配
location =/demo{	#location3
	root html;
	index gp.html;
} 
# http://192.168.140.129/demo 这个请求不会走location3
# http://192.168.140.129/demo/gp.html 这个请求会才走location3 
#精准匹配优先级要高于普通匹配

正则匹配
location ~ \.(jap|png|css|gif)${
	root html/images;
}
```



# 负载均衡实例

实现效果

在浏览器地址栏输入http://192.168.140.129/edu/index.html 实现负载均衡 将请求均分到8080和8081端口



准备工作：

准备两台tomcat

在两台tomcat里面的webapp目录中 创建edu文件夹 然后再edu文件夹中创建index.html

第一步

再nginx配置文件中进行负载均衡的配置

    upstream myserver{
        server 192.168.140.129:8080;
        server 192.168.140.129:8081;
    }
    location / {
    	proxy_pass http://myserver;
    	root   html;
    	index  index.html index.htm;
    }

![image-20220330204816752](C:\Users\uuu\AppData\Roaming\Typora\typora-user-images\image-20220330204816752.png)

![image-20220330204947109](C:\Users\uuu\AppData\Roaming\Typora\typora-user-images\image-20220330204947109.png)

刷新

![image-20220330205003408](C:\Users\uuu\AppData\Roaming\Typora\typora-user-images\image-20220330205003408.png)

Nginx提供的几种分配

1、轮询（默认）

每个请求按时间顺序逐一分配不同的后端服务器，如果后端服务器逗down掉会自动删除

2、weight

weight表示权重，默认为1，权重越高被分配的客户端越多

指定轮询几率，weight和访问率成正比，用于后端服务器性能不均的情况

```
upstream myserver{
    server 192.168.140.129:8080 weight=5;
    server 192.168.140.129:8081 weight=10;
}
```

3、ip_hash

每个请求按照访问ip的hash结果分配，这样每个访客固定访问一个后端服务器，可以解决session的问题

```
upstream myserver{
	ip_hash;
    server 192.168.140.129:8080;
    server 192.168.140.129:8081;
}
```

4、fair（第三方）

按照后端服务器的响应时间分配请求，响应时间短的优先分配

```
upstream myserver{
    server 192.168.140.129:8080;
    server 192.168.140.129:8081;
    fair;
}
```

 # 动静分离实例

两种方式：

一种是纯粹将静态文件独立成单独的域名，放在独立的服务器上（主流）

一种是将动态和静态资源混合放在一起发布，通过nginx分开

通过location指定不同的后缀名实现不同请求的转发。通过expires参数设置，可以使浏览器缓存过期时间，减少与服务器的请求和流量。具体Expires定义：给一个资源设定一个过期时间，也就是说无需去服务端验证，直接通过浏览器自身确认是否过期即可，所以不会产生额外的流量。这种方式非常适合不经常变动的资源，设置3d表示3天内访问这锅URL发送一个请求，对比服务器该文件最后更新时间没有变化则不会从服务器中抓取，返回状态码403.如果有修改则需要从服务器上重新下载，返回状态码200

准备工作：准备一些静态资源（在linux系统的根目录下的static目录下）

![image-20220330220223272](C:\Users\uuu\AppData\Roaming\Typora\typora-user-images\image-20220330220223272.png)

html文件夹放中放了一个静态网页

image文件夹中放了一张图片

修改nginx配置文件

```
		location /html/ {
            root   /static/;
            index  index.html index.htm;
        }
        location /image/{
            root  /static/;
            autoindex on;
        }

```



![image-20220330220713688](C:\Users\uuu\AppData\Roaming\Typora\typora-user-images\image-20220330220713688.png)

运行结果

![image-20220330221244505](C:\Users\uuu\AppData\Roaming\Typora\typora-user-images\image-20220330221244505.png)

![image-20220330221257653](C:\Users\uuu\AppData\Roaming\Typora\typora-user-images\image-20220330221257653.png)

















































