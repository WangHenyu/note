# JWT快速入门

## 身份验证流程

> 基于token的身份验证流程
>
> 1.客户端使用用户名和密码请求登录
>
> 2.服务端收到请求,验证用户名和密码
>
> 3.验证成功后,服务端会签发一个token,再把这个token返回给客户端
>
> 4.客户端收到token后可以把它存储起来,比如放到cookie中
>
> 5.客户端每次向服务端请求资源时需要携带服务端签发的token,可以在cookie或者header中携带
>
> 6.服务端收到请求，然后去验证客户端请求里面带着的token,如果验证成功,就向客户端返回请求数据

![image-20220823203855149](C:\Users\uuu\AppData\Roaming\Typora\typora-user-images\image-20220823203855149.png)

> 这种基于token的认证方式相比传统的session认证方式更节约服务器资源,并且对移动端和分布式更加友好.其优点如下：
>
> - 支持跨域访问：cookie是无法跨域的而token由于没有用到cookie(前提是将token放到请求头中)所以跨域后不会存在信息丢失问题
> - 无状态：token机制在服务端不需要存储session信息因为token自身包含了所有登录用户的信息,所以可以减轻服务端压力
> - 无需考虑CSRF：由于不再依赖cookie，所以采用token认证方式不会发生CSRF，所以也就无需考虑CSRF的防御

## JWT简介

> JWT官方地址: https://jwt.io/
>
> JWT就是上述流程当中token的具体实现方式,其全称是`JSON Web Token`

JWT的优点

- 基于Json,非常方便解析
- 可以在令牌中自定义丰富的内容,易扩展
- 通过非对称加密算法及数字签技术,可防止篡改安全性高

JWT的缺点

- 令牌内容较长,占用内存空间较大



## JWT组成

> JWT本质上就是字符串,它由三部分组成：头部、荷载、签名
>
> 形式如`xxx.yyy.zzz`

<h5>头部Header</h5>

> JWT头部用于描述JWT最基本的信息(元数据),其表示形式是JSON对象
>
> - alg属性表示签名所使用的算法
> - typ属性表示令牌的类型

~~~js
{
    "alg":"HS256",
    "typ":"JWT"
}
~~~



<h5>载荷Payload</h5>

> 有效载荷部分存放的是有效信息,这些有效信息分为三部分

- 标准中注册的声明

~~~js
/* JWT指定七个默认字段供选择 */
{
	"iss"："发行人",
	"sub"："主题",
	"aud"："用户",
	"iat"："签发时间",
	"exp"："到期时间",
	"nbf"："在此之前不可用",
	"jti"："JWTID用于标识该JWT"
}

~~~

- 公共的声明

  公共的声明可以添加任何信息,但不建议添加敏感信息

- 私有的声明

  私有声明是提供者和消费者共同定义的声明,也不建议添加敏感信息

因为默认情况下JWT采用的是BASE64算法,这是1种对称的加密算法,任何人都能解读其内容,因此在荷载部分不建议添加敏感信息

~~~js
{
    /* 标准声明 */
    "sub": "123456",
    "iat": "1661354863409",
    "exp": "1661354883254",
    /* 自定义声明(共有或私有) */
    "name": "Jack",
}
~~~



<h5>签名Signature</h5>

> 签名信息由三部分组成
>
> - Header(BASE64编码后的)
> - Payload(BASE64编码后的)
> - Secret(盐,一定要保密)

注意：secret是保存在服务端的,JWT也是在服务端生成的,盐就是用来签发和解析JWT的,一旦盐泄露了JWT的安全性就无法得到保障了



<h5>完整的JWT</h5>

<font color='red'>eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9</font>.<font color='orange'>eyJzdWIiOiIxMjM0NTYxMjMx</font>

<font color='orange'>MjMyMTMxMjMyMzEzNzg5MCIsIm5hbWUiOiJKb2huIERvZSIsImlhdCI6MTUxN</font>

<font color='orange'>jIzOTAyMn0</font>.<font color='skblue'>g21q_f8wKjKv1t-5Xh0b6ssgJMxGYdgYd0WIHxncljg</font>



## JJWT使用

1.导入依赖

~~~xml
<dependency>
    <groupId>io.jsonwebtoken</groupId>
    <artifactId>jjwt</artifactId>
    <version>0.9.1</version>
</dependency>
~~~

2.创建Token

> `HS256`要求您使用至少32字节长的密钥才能保证安全性

~~~java
    @Test
    void testCreateToken() {
        // 创建JwtBuilder对象
        JwtBuilder jwtBuilder = Jwts.builder()
                // 声明标识 {"jti": "8888"}
                .setId("8888")
                // 设置用户 {"sub":"Andy"}
                .setSubject("Andy")
                // 设置签发时间 {"ita":1661357455}
                .setIssuedAt(new Date())
                // 设置过期时间
                .setExpiration(new Date(System.currentTimeMillis()+1000*30))
                // 设置签名算法和密钥（至少32字节）
                .signWith(SignatureAlgorithm.HS256,"secret...")
                // 自定义声明
                .claim("username","admin")
                .claim("email","123@qq.com");

        // 生成JWT字符串
        String jwt = jwtBuilder.compact();
        System.out.println(jwt);

        // 可以解析-->{"alg":"HS256"}
        System.out.println(Base64Codec.BASE64.decodeToString(jwt.split("\\.")[0]));
        // 可以解析-->{"jti":"8888","sub":"Andy","iat":166135715}
        System.out.println(Base64Codec.BASE64.decodeToString(jwt.split("\\.")[1]));
        // 无法解析
        System.out.println(Base64Codec.BASE64.decodeToString(jwt.split("\\.")[2]));
    }
~~~



3.解析Token

~~~java
    @Test
    void testParseToken(){
        String jwt = "eyJhbGciOiJIUzI1NiJ9.eyJqdGkiOiI4ODg4Iiwic3ViIjoiQW5keSI" +
                "sImlhdCI6MTY2MTM1OTQ3NSwiZXhwIjoxNjYxMzU5NTA1LCJ1c2VybmFtZSI6" +
                "ImFkbWluIiwiZW1haWwiOiIxMjNAcXEuY29tIn0.qGI-0TYLJq2IBEaejZEAFDmTjStFVVYgJYqoenvZfoA";
        Claims claims = Jwts.parser()
                // 设置密钥才能解析
                .setSigningKey("secret...")
                .parseClaimsJws(jwt)
                .getBody();

        System.out.println(claims);
        // 获取自定义声明
        System.out.println("username:"+claims.get("username"));
        System.out.println("email:"+claims.get("email"));
        // 获取签发、过期时间
        SimpleDateFormat simpleDateFormat = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
        System.out.println("签发时间:"+simpleDateFormat.format(claims.getIssuedAt()));
        System.out.println("过去时间:"+simpleDateFormat.format(claims.getExpiration()));
    }

~~~

<h4>版本变化</h4>

> `特别注意`:jjwt在0.10版本以后发生了较大变化
>
> - pom依赖要引入多个
> - 之前对密钥的长度没有强制要求,现在强制要求密钥满足规范中的长度要求,否则会报异常
> - 之前的签名和验签方法都是传入密钥的字符串,最新的方法需要传入Key对象
> - 解析的API也有所改变

依赖

~~~xml
<dependency>
    <groupId>io.jsonwebtoken</groupId>
    <artifactId>jjwt-api</artifactId>
    <version>0.11.2</version>
</dependency>
<dependency>
    <groupId>io.jsonwebtoken</groupId>
    <artifactId>jjwt-impl</artifactId>
    <version>0.11.2</version>
    <scope>runtime</scope>
</dependency>
<dependency>
    <groupId>io.jsonwebtoken</groupId>
    <artifactId>jjwt-jackson</artifactId>
    <version>0.11.2</version>
    <scope>runtime</scope>
</dependency>
~~~

API用法变化

~~~java
    void test03(){
        // 密钥
        final String SECRET_KEY = "4f6b75650bb54f4db351973b9d9810d60a6b2a9z9q132k1mld";
        // 将字符串密钥转会成KEY对象
        SecretKey secretKey = Keys.hmacShaKeyFor(SECRET_KEY.getBytes(StandardCharsets.UTF_8));

        JwtBuilder jwtBuilder = Jwts.builder()
                .setId("8888")
                // 传入KEY对象
                // 在0.10之前 signWith(签名算法,密钥字符串)
                // 在0.10之后 signWith(密钥KEY对象,签名算法)
                .signWith(secretKey,SignatureAlgorithm.HS256);
        String jwt = jwtBuilder.compact();

        
        // 在0.10之前 Jwts.parser().setSigningKey(密钥字符串).parseClaimsJws(jwt);
        // 在0.10之后 Jwts.parserBuilder().setSigningKey(密钥KEY对象).build().parseClaimsJws(jwt);
        Claims claims = Jwts.parserBuilder()
                // 传入KEY对象
                .setSigningKey(secretKey)
                .build()
                .parseClaimsJws(jwt)
                .getBody();

        System.out.println(claims);
    }
~~~





## 算法签名和密钥

> jwt的签名算法有三种：
>
> - `HMAC`[哈希消息验证码(对称)]: HS256/HS384/HS512
> - `RSASSA`[RSA签名算法(非对称)]: RS256/RS384/RS512
> - `ECDSA`[椭圆曲线数据签名算法(非对称)]: ES256/ES384/ES512
>
> JWT规范标识了12种标准签名算法-3种密钥算法和9种非对称密钥算法
>
> - HS256:使用SHA-256的HMAC
> - HS384:使用SHA-384的HMAC
> - 更多请查看官网...

HMAC-SHA签名算法需要个秘密密钥，该密钥至少应与算法的签名（摘要）长度相等,这表示

- `HS256`是HMAC-SHA-256它会生成256位（32字节）长的摘要,因此`HS256` 要求您使用至少32字节长的密钥
- `HS384`是HMAC-SHA-384它会生成384位（48字节）长的摘要,因此`HS384` 要求您使用至少48字节长的密钥
- `HS512`是HMAC-SHA-512它会生成512位（64字节）长的摘要,因此`HS512` 要求您使用至少64字节长的密钥



<h4>对称签名</h4>

> 生成jwt和解析jwt时使用相同的密钥

~~~java
void test(){

    final String SECRET_KEY = "4f6b75650bb54f4db351973b9d9810d60a6b2a9z9q132k1mld";
    SecretKey secretKey = Keys.hmacShaKeyFor(SECRET_KEY.getBytes(StandardCharsets.UTF_8));

    JwtBuilder jwtBuilder = Jwts.builder()
            .setId("8888")
            // 设置密钥
            .signWith(secretKey,SignatureAlgorithm.HS256);
    // 生成jwt串
    String jwt = jwtBuilder.compact();
    // 解析jwt串
    Claims claims = Jwts.parserBuilder()
            // 设置密钥后才能解析
            .setSigningKey(secretKey)
            .build()
            .parseClaimsJws(jwt)
            .getBody();

    System.out.println(claims);
}
~~~



<h4>非对称签名</h4>

> 生成jwt串的时候需要指定私钥,解析jwt串的时候需要指定公钥
>
> 公钥加密和私钥加密的区别
>
> - 公钥加密准确来说就是加密的过程
> - 而私钥加密准确来说是签名的过程

<h5>生成私钥和公钥</h5>

先安装openssl并配置环境变量

代开cdm执行命令

~~~shell
# 生成2048位的私钥
openssl genrsa -out rsa_private_key.pem 2048
# 查看私钥
openssl pkcs8 -topk8 -inform PEM -in rsa_private_key.pem -outform PEM -nocrypt
# 生成公钥
openssl rsa -in rsa_private_key.pem -pubout -out rsa_public_key.pem
# 查看公钥
rsa_public_key.pem
~~~

~~~java
void test(){
    final String PUBLIC_KEY = "MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAvMQopDFxXxVTDHT2yHNV" +
                              "8DDTVZ0mMTwGaR2heBVkA8/HSKMoJbXf++E3nbDMrmQHlVQ+mnDelPBbv6pAvVDL" +
                              "hwuIsxXu7ask8kCmRj/NsRrwL+6p92LJPzyquhOaUaom+wD4cWyTFDtL4iUwKgEW" +
                              "wh2e3v4o1zQf5JIUKMEXo1HzMZAmXD45pr2sTv/hVbjW2qQzAoMYJgU69VN9Hrvj" +
                              "ufuxIOGlAG+VBtUbPeEpM9hOzz8+hy1xzOBKV8RFKWNfrXDYfiWgpl/RYFltE1BP" +
                              "VJAyG7HwvPX3PeVguvzaRKLm0Ejrk3Xh8V94fLtONtOMN4e+0EC4G7+DfGeLvEcR" +
                              "VQIDAQAB";
    final String PRIVATE_KEY = "MIIEvQIBADANBgkqhkiG9w0BAQEFAASCBKcwggSjAgEAAoIBAQC8xCikMXFfFVMM" +
                               "dPbIc1XwMNNVnSYxPAZpHaF4FWQDz8dIoygltd/74TedsMyuZAeVVD6acN6U8Fu/" +
                               "qkC9UMuHC4izFe7tqyTyQKZGP82xGvAv7qn3Ysk/PKq6E5pRqib7APhxbJMUO0vi" +
                               "JTAqARbCHZ7e/ijXNB/kkhQowRejUfMxkCZcPjmmvaxO/+FVuNbapDMCgxgmBTr1" +
                               "U30eu+O5+7Eg4aUAb5UG1Rs94Skz2E7PPz6HLXHM4EpXxEUpY1+tcNh+JaCmX9Fg" +
                               "WW0TUE9UkDIbsfC89fc95WC6/NpEoubQSOuTdeHxX3h8u04204w3h77QQLgbv4N8" +
                               "Z4u8RxFVAgMBAAECggEAKE+trC7A2sDXjOv3faQfwJz0rK5UJyR96/m/b0EGLEg1" +
                               "cx4gpzj5XbVhZqQIed9zvSCSA/JBnkXcGgi2LZew9yRv62quLQQk7jT/LTZX60/o" +
                               "5huOIX3vozkF7pncFT+EdorlA3ffyuzEzkUJzZaQ8SBZOp7FQvvnKa1Gb9OYhDBQ" +
                               "mu16NhxFUhjP03+MmU2NZDxpONcFL0LknOouICKnNukSBr0P4zCHOJOnC947bgki" +
                               "phYPCOMCX/pYqexlMdgYIZ83XGvBQpzAsUfTMKBumH6dXRZv6m866nCyTKM4Crkp" +
                               "FAUAPN0jVRoVr7TNTb5ZcZkqsAvcEatyix9MJo+LewKBgQDfut3O3lucWYeT8bzE" +
                               "FpNIlLypJWHUtDaYbAxsMv1DklNmF4o3Lhwe0iu4TrnLPgPPqP87pzkeVovR+X+l" +
                               "2JH9CsHwrlOFZSe/OkL+kyYo2ewFY4LYzm2qh2rZ/UxldIFv+ShAa3atctWb5qBk" +
                               "NQSS5C09SC2Dsr+oSi0jD2FJ3wKBgQDX/kW99asEty5lVGSdD9eFL56oDKpUwJ11" +
                               "03HUw2EOxaxa4WjLZ+qNTANg/FrvSo43jVQaMpX7etpjtkuW2fjePwGHaZ+b9PO1" +
                               "qnasZCFr0I/tsSw7FNdsNTNizRAi54Dw2eNgvUbgiA+RZLfRtwnu9uDclUyJoVGm" +
                               "//c8ttEzSwKBgQCnBRyjW7IuVVP2aSfPhz7ql0cESwjuvp5nSXo4qBQUBr/F3lrr" +
                               "eLu7cpD65aKkDKbRtsrboKnucROubEET8UqDCi3gXHOziOGUS41J+VYqdw1QtIox" +
                               "CmIqRS/KjuMidkhO7EVVWiRdtfWrjg8836uLcVYogqKfx6h2nPF3U/sAJwKBgGA5" +
                               "0r5PTVJ7fXr9PxnN7flS/r7lbZj2eu/8NHFSXdIj9KZEV9iOwisS20qBAs4qi/Np" +
                               "zjchpDn2QIc9tq1NDJeOwFKgls/3MAAWImqo7U7ICucdEJR9s39ssCjrLK4wLvye" +
                               "RbNaf9IWK8Fdeps+jXldmU4MYZ3LNcJNtMris47DAoGARsxakxq8pehn8VIkZqnM" +
                               "06mMBw8vIq1FtPRYKj1QCjRQnHFr5SEMqZKUQwJGMfHnCxCBhrbXXrNkemodO6VH" +
                               "sv1wCewqCaW7cKYizETDt7CQiTCK+h2X54RbYNhrOUngC+FjTa4dXLGBp+rCdTcd" +
                               "jSzv4zt/MV1f4hT5IOyPm+g=";

    // RSA算法是一种非对称加密算法
    RSA rsa = new RSA(PRIVATE_KEY, null);
    RSAPrivateKey privateKey = (RSAPrivateKey)rsa.getPrivateKey();

    String jwt = Jwts.builder()
            .setId("8888")
            // 私钥加密
            .signWith(privateKey,SignatureAlgorithm.RS256)
            .compact();

    RSA rsa1 = new RSA(null, PUBLIC_KEY);
    RSAPublicKey publicKey = (RSAPublicKey) rsa1.getPublicKey();

    Claims claims = Jwts.parserBuilder()
            // 公钥解密 
            .setSigningKey(publicKey)
            .build()
            .parseClaimsJws(jwt)
            .getBody();
    System.out.println(claims);

}
~~~



---



# SpringSecurity

> SpringSecurity是Spring家族中的一个安全管理框架
>
> SpringSecurity核心功能
>
> ​	认证\<authentication>: 验证当前访问系统的是不是本系统的用户
>
> ​	授权\<authorization>: 经过认证后判断当前用户是否有权限进行某个操作

## 快速入门

1.创建Boot工程

2.引入依赖

~~~xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-security</artifactId>
</dependency>
~~~



3.创建controller

~~~java
@RestController
public class HelloController {

    @RequestMapping("/hello")
    public String hello(){ return "hello"; }
}
~~~



4.启动并访问

![image-20220823202756147](C:\Users\uuu\AppData\Roaming\Typora\typora-user-images\image-20220823202756147.png)

> 出现此页面就说明SpringSecurity成功启动
>
> 默认username: user
>
> 默认passowrd: 输出在控制台上



<h4>SpringSecurity流程</h4>

> SpringSecurity本质是过滤器链,其内部包含了各种功能的过滤器

![image-20220823204034582](C:\Users\uuu\AppData\Roaming\Typora\typora-user-images\image-20220823204034582.png)

<h5>核心过滤器</h5>

UsernamePasswordAuthenticationFilter: 负责处理在登陆页面填写了用户名密码后的登陆请求（认证）

ExceptionTranslationFilter 处理过滤器链中抛出的AccessDeniedException和AuthenticationException

FilterSecurityInterceptor 负责权限校验的过滤器（授权）

<h5>默认加载的16个过滤器</h5>

~~~java
filters = {ArrayList@6649}  size = 16
  0 = {DisableEncodeUrlFilter@6653} 
  1 = {WebAsyncManagerIntegrationFilter@6654} 
  2 = {SecurityContextPersistenceFilter@6655} 
  3 = {HeaderWriterFilter@6656} 
  4 = {CsrfFilter@6657} 
  5 = {LogoutFilter@6658} 
  6 = {UsernamePasswordAuthenticationFilter@6659} 
  7 = {DefaultLoginPageGeneratingFilter@6660} 
  8 = {DefaultLogoutPageGeneratingFilter@6661} 
  9 = {BasicAuthenticationFilter@6662} 
 10 = {RequestCacheAwareFilter@6663} 
 11 = {SecurityContextHolderAwareRequestFilter@6664} 
 12 = {AnonymousAuthenticationFilter@6665} 
 13 = {SessionManagementFilter@6666} 
 14 = {ExceptionTranslationFilter@6667} 
 15 = {FilterSecurityInterceptor@6668} 
~~~



<h4>认证流程</h4>

> Authentication接口: 它的实现类表示当前访问系统的用户,封装了用户相关信息
>
> AuthenticationManager接口：定义了认证Authentication的方法 
>
> UserDetailsService接口：加载用户特定数据的核心接口,里面定义了根据用户名查询用户信息的方法
>
> UserDetails接口：提供核心用户信息,里面封装着由UserDetailsService获取处理的用户信息

![image-20220823205019898](C:\Users\uuu\AppData\Roaming\Typora\typora-user-images\image-20220823205019898.png)

UserDetailService默认实现类是InMemonyUserDetailManager,默认是在内存中查找用户信息

而我们希望的是从数据库中查询用户信息,所以需要自定义UserDetailService的实现类替换掉默认的InMemonyUserDetailManager



## 自定义登录逻辑

### 思路分析

> 1.由于认证后需要生成JWT存入Redis,默认的UsernamePasswordAuthenticationFilter已无法满足前后端分离项目需求,所以我们要自定义登录接口
>
> 2.由于我们希望的是从数据库中查询用户信息,所以需要自定义UserDetailsService的实现类替换掉默认的InMemoryUserDetailsManager

![image-20220823215955222](C:\Users\uuu\AppData\Roaming\Typora\typora-user-images\image-20220823215955222.png)

登录

- 自定义UserDetailsService

    在这个实现类中去查询数据库

- 自定义登录接口

    调用ProviderManager的方法进行认证如果认证通过生成JWT把用户信息存入redis中

校验

- 定义Jwt认证过滤器

    获取token、解析token获取其中的userid、从redis中获取用户信息、存入SecurityContextHolder



### 准备工作

1.创建数据库表

~~~sql
CREATE TABLE `sys_user` (
  `id` BIGINT(20) NOT NULL AUTO_INCREMENT COMMENT '主键',
  `user_name` VARCHAR(64) NOT NULL DEFAULT 'NULL' COMMENT '用户名',
  `nick_name` VARCHAR(64) NOT NULL DEFAULT 'NULL' COMMENT '昵称',
  `password` VARCHAR(64) NOT NULL DEFAULT 'NULL' COMMENT '密码',
  `status` CHAR(1) DEFAULT '0' COMMENT '账号状态（0正常 1停用）',
  `email` VARCHAR(64) DEFAULT NULL COMMENT '邮箱',
  `phonenumber` VARCHAR(32) DEFAULT NULL COMMENT '手机号',
  `sex` CHAR(1) DEFAULT NULL COMMENT '用户性别（0男，1女，2未知）',
  `avatar` VARCHAR(128) DEFAULT NULL COMMENT '头像',
  `user_type` CHAR(1) NOT NULL DEFAULT '1' COMMENT '用户类型（0管理员，1普通用户）',
  `create_by` BIGINT(20) DEFAULT NULL COMMENT '创建人的用户id',
  `create_time` DATETIME DEFAULT NULL COMMENT '创建时间',
  `update_by` BIGINT(20) DEFAULT NULL COMMENT '更新人',
  `update_time` DATETIME DEFAULT NULL COMMENT '更新时间',
  `del_flag` INT(11) DEFAULT '0' COMMENT '删除标志（0代表未删除，1代表已删除）',
  PRIMARY KEY (`id`)
) ENGINE=INNODB AUTO_INCREMENT=2 DEFAULT CHARSET=utf8mb4 COMMENT='用户表'
~~~



2.创建boot工程

3.引入依赖

~~~xml
<dependencies>
    <!--mybatis-plus-->
    <dependency>
        <groupId>com.baomidou</groupId>
        <artifactId>mybatis-plus-boot-starter</artifactId>
        <version>3.4.3</version>
    </dependency>
    <!--mysql驱动-->
    <dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
    </dependency>
    <!--redis依赖-->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-redis</artifactId>
    </dependency>
    <!--fastjson依赖-->
    <dependency>
        <groupId>com.alibaba</groupId>
        <artifactId>fastjson</artifactId>
        <version>1.2.33</version>
    </dependency>
    <!--jwt依赖-->
    <dependency>
        <groupId>io.jsonwebtoken</groupId>
        <artifactId>jjwt</artifactId>
        <version>0.9.0</version>
    </dependency>
    <!--Lombok-->
    <dependency>
        <groupId>org.projectlombok</groupId>
        <artifactId>lombok</artifactId>
        <optional>true</optional>
    </dependency>
    <!--SpringSecurity-->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-security</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-test</artifactId>
        <scope>test</scope>
    </dependency>
    <dependency>
        <groupId>org.junit.platform</groupId>
        <artifactId>junit-platform-launcher</artifactId>
        <scope>test</scope>
    </dependency>
    <dependency>
        <groupId>org.springframework.security</groupId>
        <artifactId>spring-security-test</artifactId>
        <scope>test</scope>
    </dependency>
</dependencies>

~~~



4.添加配置

~~~yaml
spring:
  datasource:
    url: jdbc:mysql://localhost:3306/ssm?characterEncoding=utf-8&serverTimezone=UTC
    username: root
    password: why0417
    driver-class-name: com.mysql.cj.jdbc.Driver
  redis:
    host: 192.168.140.132
    password: 123321
    port: 6379

mybatis-plus:
  configuration:
    log-impl: org.apache.ibatis.logging.stdout.StdOutImpl
~~~

5.配置类

~~~java
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.data.redis.connection.RedisConnectionFactory;
import org.springframework.data.redis.core.RedisTemplate;
import org.springframework.data.redis.serializer.GenericJackson2JsonRedisSerializer;
import org.springframework.data.redis.serializer.RedisSerializer;

@Configuration
public class RedisConfig {

    /**
     * 配置Redis序列化器
     */
    @Bean
    @SuppressWarnings(value = { "unchecked", "rawtypes" })
    public RedisTemplate<String,Object> redisTemplate(RedisConnectionFactory factory){
        RedisTemplate<String,Object> redisTemplate = new RedisTemplate<>();
        redisTemplate.setConnectionFactory(factory);
        GenericJackson2JsonRedisSerializer jackson2JsonRedisSerializer = new GenericJackson2JsonRedisSerializer();

        redisTemplate.setKeySerializer(RedisSerializer.string());
        redisTemplate.setHashKeySerializer(RedisSerializer.string());

        redisTemplate.setValueSerializer(jackson2JsonRedisSerializer);
        redisTemplate.setHashValueSerializer(jackson2JsonRedisSerializer);

        return redisTemplate;
    }
}
~~~



6.响应类

~~~java

/**
 * 为空的属性不会被返回
 */
@Data
@JsonInclude(JsonInclude.Include.NON_NULL)
public class ResponseResult<T> {

    /**
     * 状态码
     */
    private Integer code;
    /**
     * 提示信息
     */
    private String msg;
    /**
     * 数据
     */
    private T data;

    public ResponseResult(Integer code, String msg) {
        this.code = code;
        this.msg = msg;
    }

    public ResponseResult(Integer code, T data) {
        this.code = code;
        this.data = data;
    }
    public ResponseResult(Integer code, String msg, T data) {
        this.code = code;
        this.msg = msg;
        this.data = data;
    }
}
~~~



7.工具类

~~~java
import io.jsonwebtoken.Claims;
import io.jsonwebtoken.JwtBuilder;
import io.jsonwebtoken.Jwts;
import io.jsonwebtoken.SignatureAlgorithm;
import javax.crypto.SecretKey;
import javax.crypto.spec.SecretKeySpec;
import java.util.Base64;
import java.util.Date;
import java.util.UUID;

/**
 * JWT工具类
 */
public class JwtUtil {
    /**
     * 有效期为 
     */
    public static final Long JWT_TTL = 60 * 60 *1000L;
    /**
     * 设置秘钥明文
     */
    public static final String JWT_KEY = "why";

    public static void main(String[] args) throws Exception {
        // String token = "";
        // Claims claims = parseJWT(token);
        // System.out.println(claims);
    }

    /**
     * 生成UUID
     */
    public static String getUUID(){
        String token = UUID.randomUUID().toString().replaceAll("-", "");
        return token;
    }

    /**
     * 生成jtw
     */
    public static String createJWT(String subject) {
        // 不设置过期时间
        JwtBuilder builder = getJwtBuilder(subject, null, getUUID());
        return builder.compact();
    }

    /**
     * 生成jtw
     */
    public static String createJWT(String subject, Long ttlMillis) {
        // 设置过期时间
        JwtBuilder builder = getJwtBuilder(subject, ttlMillis, getUUID());
        return builder.compact();
    }

    private static JwtBuilder getJwtBuilder(String subject, Long ttlMillis, String uuid) {

        if(ttlMillis==null){
            ttlMillis=JwtUtil.JWT_TTL;
        }
        long nowMillis = System.currentTimeMillis();
        long expMillis = nowMillis + ttlMillis;
        Date expDate = new Date(expMillis);
        return Jwts.builder()
                .setId(uuid)
                // 主题,可以是JSON数据
                .setSubject(subject)
                // 签发者
                .setIssuer("wang")
                // 签发时间
                .setIssuedAt(new Date(nowMillis))
                //使用HS256对称加密算法签名, 第二个参数为秘钥
                .signWith(SignatureAlgorithm.HS256, generalKey())
                // 过期时间
                .setExpiration(expDate);
    }

    /**
     * 创建token
     */
    public static String createJWT(String id, String subject, Long ttlMillis) {
        JwtBuilder builder = getJwtBuilder(subject, ttlMillis, id);
        return builder.compact();
    }

    /**
     * 生成加密后的秘钥 secretKey
     */
    public static SecretKey generalKey() {
        byte[] encodedKey = Base64.getDecoder().decode(JwtUtil.JWT_KEY);
        SecretKey key = new SecretKeySpec(encodedKey, 0, encodedKey.length, "AES");
        return key;
    }

    /**
     * 解析jwt
     */
    public static Claims parseJWT(String jwt) throws Exception {
        SecretKey secretKey = generalKey();
        return Jwts.parser()
                .setSigningKey(secretKey)
                .parseClaimsJws(jwt)
                .getBody();
    }
}
~~~

RedisTempalte进行封装的工具类

~~~java
import java.util.*;
import java.util.concurrent.TimeUnit;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.data.redis.core.BoundSetOperations;
import org.springframework.data.redis.core.HashOperations;
import org.springframework.data.redis.core.RedisTemplate;
import org.springframework.data.redis.core.ValueOperations;
import org.springframework.stereotype.Component;


/**
 * 忽略unchecked、rawtypes警告信息
 */
@Component
@SuppressWarnings(value = { "unchecked", "rawtypes" })
public class RedisCache
{

    @Autowired
    public RedisTemplate redisTemplate;

    /**
     * 缓存基本的对象，Integer、String、实体类等
     *
     * @param key 缓存的键值
     * @param value 缓存的值
     */
    public <T> void setCacheObject(final String key, final T value)
    {
        redisTemplate.opsForValue().set(key, value);
    }

    /**
     * 缓存基本的对象，Integer、String、实体类等
     *
     * @param key 缓存的键值
     * @param value 缓存的值
     * @param timeout 时间
     * @param timeUnit 时间颗粒度
     */
    public <T> void setCacheObject(final String key, final T value, final Integer timeout, final TimeUnit timeUnit)
    {
        redisTemplate.opsForValue().set(key, value, timeout, timeUnit);
    }

    /**
     * 设置有效时间
     *
     * @param key Redis键
     * @param timeout 超时时间
     * @return true=设置成功；false=设置失败
     */
    public boolean expire(final String key, final long timeout)
    {
        return expire(key, timeout, TimeUnit.SECONDS);
    }

    /**
     * 设置有效时间
     *
     * @param key Redis键
     * @param timeout 超时时间
     * @param unit 时间单位
     * @return true=设置成功；false=设置失败
     */
    public boolean expire(final String key, final long timeout, final TimeUnit unit)
    {
        return redisTemplate.expire(key, timeout, unit);
    }

    /**
     * 获得缓存的基本对象。
     *
     * @param key 缓存键值
     * @return 缓存键值对应的数据
     */
    public <T> T getCacheObject(final String key)
    {
        ValueOperations<String, T> operation = redisTemplate.opsForValue();
        return operation.get(key);
    }

    /**
     * 删除单个对象
     *
     * @param key
     */
    public boolean deleteObject(final String key)
    {
        return redisTemplate.delete(key);
    }

    /**
     * 删除集合对象
     *
     * @param collection 多个对象
     * @return
     */
    public long deleteObject(final Collection collection)
    {
        return redisTemplate.delete(collection);
    }

    /**
     * 缓存List数据
     *
     * @param key 缓存的键值
     * @param dataList 待缓存的List数据
     * @return 缓存的对象
     */
    public <T> long setCacheList(final String key, final List<T> dataList)
    {
        Long count = redisTemplate.opsForList().rightPushAll(key, dataList);
        return count == null ? 0 : count;
    }

    /**
     * 获得缓存的list对象
     *
     * @param key 缓存的键值
     * @return 缓存键值对应的数据
     */
    public <T> List<T> getCacheList(final String key)
    {
        return redisTemplate.opsForList().range(key, 0, -1);
    }

    /**
     * 缓存Set
     *
     * @param key 缓存键值
     * @param dataSet 缓存的数据
     * @return 缓存数据的对象
     */
    public <T> BoundSetOperations<String, T> setCacheSet(final String key, final Set<T> dataSet)
    {
        BoundSetOperations<String, T> setOperation = redisTemplate.boundSetOps(key);
        Iterator<T> it = dataSet.iterator();
        while (it.hasNext())
        {
            setOperation.add(it.next());
        }
        return setOperation;
    }

    /**
     * 获得缓存的set
     *
     * @param key
     * @return
     */
    public <T> Set<T> getCacheSet(final String key)
    {
        return redisTemplate.opsForSet().members(key);
    }

    /**
     * 缓存Map
     *
     * @param key
     * @param dataMap
     */
    public <T> void setCacheMap(final String key, final Map<String, T> dataMap)
    {
        if (dataMap != null) {
            redisTemplate.opsForHash().putAll(key, dataMap);
        }
    }

    /**
     * 获得缓存的Map
     *
     * @param key
     * @return
     */
    public <T> Map<String, T> getCacheMap(final String key)
    {
        return redisTemplate.opsForHash().entries(key);
    }

    /**
     * 往Hash中存入数据
     *
     * @param key Redis键
     * @param hKey Hash键
     * @param value 值
     */
    public <T> void setCacheMapValue(final String key, final String hKey, final T value)
    {
        redisTemplate.opsForHash().put(key, hKey, value);
    }

    /**
     * 获取Hash中的数据
     *
     * @param key Redis键
     * @param hKey Hash键
     * @return Hash中的对象
     */
    public <T> T getCacheMapValue(final String key, final String hKey)
    {
        HashOperations<String, String, T> opsForHash = redisTemplate.opsForHash();
        return opsForHash.get(key, hKey);
    }

    /**
     * 删除Hash中的数据
     *
     * @param key
     * @param hkey
     */
    public void delCacheMapValue(final String key, final String hkey)
    {
        HashOperations hashOperations = redisTemplate.opsForHash();
        hashOperations.delete(key, hkey);
    }

    /**
     * 获取多个Hash中的数据
     *
     * @param key Redis键
     * @param hKeys Hash键集合
     * @return Hash对象集合
     */
    public <T> List<T> getMultiCacheMapValue(final String key, final Collection<Object> hKeys)
    {
        return redisTemplate.opsForHash().multiGet(key, hKeys);
    }

    /**
     * 获得缓存的基本对象列表
     *
     * @param pattern 字符串前缀
     * @return 对象列表
     */
    public Collection<String> keys(final String pattern)
    {
        return redisTemplate.keys(pattern);
    }
}
~~~

~~~java
import java.io.IOException;
import javax.servlet.http.HttpServletResponse;

public class WebUtils
{
    /**
     * 将字符串渲染到客户端
     *
     * @param response 渲染对象
     * @param string 待渲染的字符串
     * @return null
     */
    public static String renderString(HttpServletResponse response, String string) {
        try
        {
            response.setStatus(200);
            response.setContentType("application/json");
            response.setCharacterEncoding("utf-8");
            response.getWriter().print(string);
        }
        catch (IOException e)
        {
            e.printStackTrace();
        }
        return null;
    }
}
~~~



8.实体类

~~~java
@Data
@AllArgsConstructor
@NoArgsConstructor
@TableName("sys_user")
public class User implements Serializable {
    
    private static final long serialVersionUID = -40356785423868312L;

    /**
     * 主键
     */
    @TableId
    private Long id;
    /**
     * 用户名
     */
    private String userName;
    /**
     * 昵称
     */
    private String nickName;
    /**
     * 密码
     */
    private String password;
    /**
     * 账号状态（0正常 1停用）
     */
    private String status;
    /**
     * 邮箱
     */
    private String email;
    /**
     * 手机号
     */
    private String phonenumber;
    /**
     * 用户性别（0男，1女，2未知）
     */
    private String sex;
    /**
     * 头像
     */
    private String avatar;
    /**
     * 用户类型（0管理员，1普通用户）
     */
    private String userType;
    /**
     * 创建人的用户id
     */
    private Long createBy;
    /**
     * 创建时间
     */
    private Date createTime;
    /**
     * 更新人
     */
    private Long updateBy;
    /**
     * 更新时间
     */
    private Date updateTime;
    /**
     * 删除标志（0代表未删除，1代表已删除）
     */
    private Integer delFlag;
}
~~~



9.创建Mapper

~~~java
@Mapper
public interface UserMapper extends BaseMapper<User> {

}
~~~



10.测试MybatisPlus

~~~java
@SpringBootTest
class SecurityApplicationTests {

    @Autowired
    private UserMapper userMapper;

    @Test
    void testMybatisPlus() {
        List<User> users = userMapper.selectList(null);
        users.forEach(System.out::println);
    }
}
~~~

测试成功准备工作就完成了





### 核心代码

1.实现UserDetailsService接口

> 使用自定义的类去替换SpringSecurity默认的InMemoryUserDetailsManager
>
> 实现功能:从数据库中查询用户信息

~~~java
@Service
public class InDataBaseUserDetailManager implements UserDetailsService {

    @Autowired
    private UserMapper userMapper;

    @Override
    public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {
        // 从数据库中查询信息
        LambdaQueryWrapper<User> queryWrapper = new LambdaQueryWrapper();
        queryWrapper.eq(User::getUserName,username);
        User user = userMapper.selectOne(queryWrapper);
        // 查不到用户信息就抛出异常
        if (Objects.isNull(user)) {
            throw new RuntimeException("用户名或者密码错误");
        }
        // TODO 根据用户查询权限信息添加到LoginUser中

        return new LoginUser(user);
    }
}
~~~



2.实现UserDetails接口

> 由于loadUserByUsername的返回值是UserDetails接口,所以我们需要自定义类去实现UserDetails接口

~~~java
@Data
@NoArgsConstructor
@AllArgsConstructor
public class LoginUser implements UserDetails {

    private User user;

    @Override
    public Collection<? extends GrantedAuthority> getAuthorities() {return null;}

    @Override
    public String getPassword() {return user.getPassword();}

    @Override
    public String getUsername() {return user.getUserName();}

    @Override
    public boolean isAccountNonExpired() {return true;}

    @Override
    public boolean isAccountNonLocked() {return true;}

    @Override
    public boolean isCredentialsNonExpired() {return true;}

    @Override
    public boolean isEnabled() {return true;}
}
~~~

> 实现上面两个接口已经将默认的InMemoryUserDetailsManager替换成我们自定义的InDataBaseUserDetailManager
>
> 启动项目测试注意点
>
>     如果想让用户的密码是明文存储需要在密码前加{noop}.如果不想加{noop}就需要使用BCryptPasswordEncoder替换默认PasswordEncoder

3.使用BCryptPasswordEncoder替换默认PasswordEncoder

> 我们只需要使用把BCryptPasswordEncoder对象注入Spring容器中,SpringSecurity就会使用该PasswordEncoder来进行密码校验
>
> 我们可以定义SpringSecurity的配置类,SpringSecurity要求这个配置类要继承WebSecurityConfigurerAdapter

~~~java
@Configuration
public class SecurityConfig /*extends WebSecurityConfigurerAdapter*/ {

    @Bean
    public PasswordEncoder passwordEncoder(){
        return new BCryptPasswordEncoder();
    }
}
~~~

~~~java
@Autowired
BCryptPasswordEncoder bCryptPasswordEncoder;

@Test
void testPassword(){
    String encode = bCryptPasswordEncoder.encode("123");
    // 每次加密后的码都不同
    // $2a$10$h9xZtaB..DsM6FDON/Tb4OGW1RKbS7Ly2J/X3BGs0K1rEPaDyyjn6
    // $2a$10$iCzXY.J7tNnZ5yBgCFqDGea6RH4jzlrHVYh1dl.xJS/xDZHbkPpfC
    // $2a$10$EA0.04oN9x1UTNczcATTouJirGAuMVbhfaWaHlocl8vpKc.IWqYmq
    System.out.println(encode);
    boolean isMatch1 = bCryptPasswordEncoder.matches("123", 
                       "$2a$10$h9xZtaB..DsM6FDON/Tb4OGW1RKbS7Ly2J/X3BGs0K1rEPaDyyjn6");
    boolean isMatch2 = bCryptPasswordEncoder.matches("123",
                       "$2a$10$iCzXY.J7tNnZ5yBgCFqDGea6RH4jzlrHVYh1dl.xJS/xDZHbkPpfC");
    boolean isMatch3 = bCryptPasswordEncoder.matches("123",
                       "$2a$10$EA0.04oN9x1UTNczcATTouJirGAuMVbhfaWaHlocl8vpKc.IWqYmq");
    // 虽然每次加密后的码都不同,但拿着明文密码和加密后的码都能匹配成功
    System.out.println(isMatch1); // true
    System.out.println(isMatch2); // true
    System.out.println(isMatch3); // true
}
~~~



4.定义登录接口

~~~java
@Controller
public class LoginController {

    @Autowired
    private LoginService loginService;

    @ResponseBody
    @PostMapping("/user/login")
    public ResponseResult login(@RequestBody User user){
        return loginService.login(user);
    }
}

public interface LoginService {

    ResponseResult login(User user);
}

@Service
public class LoginServiceImpl implements LoginService {

    @Autowired
    private AuthenticationManager authenticationManager;
    @Autowired
    private RedisCache redisCache;

    @Override
    public ResponseResult login(User user) {

        // 将前端传递的用户信息封装成Authentication对象
        UsernamePasswordAuthenticationToken authentication =
                new UsernamePasswordAuthenticationToken(user.getUserName(),user.getPassword());
        // 使用AuthenticationManager进行认证
        Authentication authenticate = authenticationManager.authenticate(authentication);
        // 如果认证返回的对象为空说明认证失败
        if (Objects.isNull(authenticate)){
            throw new RuntimeException("用户名或密码错误");
        }
        // 从Authentication中获取用户信息
        LoginUser loginUser = (LoginUser)authenticate.getPrincipal();
        String userId = loginUser.getUser().getId().toString();
        // 根据用户ID生成JWT
        String jwt = JwtUtil.createJWT(userId);
        // 将用户信息存入redis
        redisCache.setCacheObject(SystemConstant.TOKEN_LOGIN_PREFIX + userId,loginUser);
        // 将token响应给前端
        Map<String,String> map = new HashMap<>();
        map.put("token",jwt);
        return new ResponseResult(200,"登录成功",map);
    }
}	
~~~

> 由于SpringSecurity默认拦截所有请求,我们需要通过配置文件放行登录接口
>
> 由于我们通过AuthenticationManager的authenticate方法来进行用户认证所以需要把AuthenticationManager注入容器

~~~java
@Configuration
public class SecurityConfig extends WebSecurityConfigurerAdapter {

    @Bean
    public PasswordEncoder passwordEncoder(){
        return new BCryptPasswordEncoder();
    }

    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http
                //关闭csrf
                .csrf().disable()
                //不通过Session获取SecurityContext
                .sessionManagement().sessionCreationPolicy(SessionCreationPolicy.STATELESS)
                .and()
                .authorizeRequests()
                // 对于登录接口 允许匿名访问
                .antMatchers("/user/login").anonymous()
                // 除上面外的所有请求全部需要鉴权认证
                .anyRequest().authenticated();
    }

    @Bean
    @Override
    public AuthenticationManager authenticationManagerBean() throws Exception {
        return super.authenticationManagerBean();
    }

}
~~~



5.创建JWT认证过滤器

~~~java
@Component
public class JwtAuthenticationFilter extends OncePerRequestFilter {

    @Autowired
    private RedisCache redisCache;

    @Override
    protected void doFilterInternal(HttpServletRequest request, HttpServletResponse response, FilterChain filterChain)
                                    throws ServletException, IOException {

        // 获取token
        String token = request.getHeader("token");
        // 没有携带token说明用户未登录
        if (!StringUtils.hasText(token)){
            // 放行请求
            filterChain.doFilter(request,response);
            return;
        }
        // 解析token
        String userId;
        try {
            userId = JwtUtil.parseJWT(token).getSubject();
        } catch (Exception e) {
            e.printStackTrace();
            throw new RuntimeException("非法token");
        }
        // 根据userId从Redis中获取用户信息
        String redisKey = SystemConstant.TOKEN_LOGIN_PREFIX + userId;
        LoginUser loginUser = redisCache.getCacheObject(redisKey);
        if (Objects.isNull(loginUser)){
            throw new RuntimeException("用户未登录禁止访问");
        }
        // 将用户信息封装到authentication
        // TODO 权限信息暂时没有
        UsernamePasswordAuthenticationToken authentication = new UsernamePasswordAuthenticationToken(loginUser,null,null);

        // 将用户信息存入SecurityContextHolder
        SecurityContextHolder.getContext().setAuthentication(authentication);
        filterChain.doFilter(request,response);
    }
}
~~~

> 将自定义JWT认证过滤器配置在过SpringSecurity滤器链中
>
> 配置在UsernamePasswordAuthenticationFilter之前

~~~java
@Configuration
public class SecurityConfig extends WebSecurityConfigurerAdapter {
    
    @Autowired
    private JwtAuthenticationFilter jwtAuthenticationTokenFilter;

    @Override
    protected void configure(HttpSecurity http) throws Exception {
        // 其他配置...
        // 把token校验过滤器添加到过滤器链中
        http.addFilterBefore(jwtAuthenticationTokenFilter, UsernamePasswordAuthenticationFilter.class);
    }
}
~~~



6.退出登录

> 自定义退出登录接口
>
> 从SecurityContextHolder中获取用户认证信息
>
> 从Redis中删除用户认证信息



## 授权流程

>在SpringSecurity中,会使用默认的FilterSecurityInterceptor来进行权限校验
>
>在FilterSecurityInterceptor中会从SecurityContextHolder获取其中的Authentication,然后获取其中的权限信息判断当前用户是否拥有访问当前资源所需的权限.所以我们在项目中只需要把当前登录用户的权限信息也存入Authentication,然后设置我们的资源所需要的权限即可

<h4>RBAC权限模型</h4>

>RBAC权限模型（Role-Based Access Control）即：基于角色的权限控制

~~~shell
         用户表                              角色表                                    权限表
+----+-----------+-----+         +----+-----------+----------+          +----+-----------+-------------+
| id | user_name | ... |         | id | role_name | role_key |          | id | menu_name |    perms    | 
+----+-----------+-----+         +----+-----------+----------+          +----+-----------+-------------+
|  1 |    andy   | ... |         |  1 |   管理员   |  admin   |    	 |  1 |  删除图书  | book:delete |
+----+-----------+-----+         +----+-----------+----------+          +----+-----------+-------------+
|  2 |    eric   | ... |         |  2 |   借阅人   |  reader  |    	 |  2 |  查询图书  | book:query  |
+----+-----------+-----+         +----+-----------+----------+          +----+-----------+-------------+
        |                                    |  |                                     |
        |                                    |  |                                     |
        |             用户角色关联表	       |  |           角色权限关联表	          |
        |         +---------+---------+      |  |       +---------+---------+         |
        | M       | user_id | role_id |    N |  | M     | role_id | menu_id |       N | 
        |         +---------+---------+      |  |       +---------+---------+         |          
        |         |    1    |    1    |      |  |       |    1    |    1    |         |
        |__ __ __ +---------+---------+ __ __|  | __ __ +---------+---------+ __ __ __|
                  |    1    |    2    |                 |    1    |    2    |  
                  +---------+---------+                 +---------+---------+  
                  |    2    |    2    |                 |    2    |    2    |  
                  +---------+---------+                 +---------+---------+  
~~~

## 授权实现

0.创建上述五张数据库表

1.开启基于注解的权限控制方案

~~~java
@SpringBootApplication
@MapperScan(basePackages = "com.why.mapper")
@EnableGlobalMethodSecurity(prePostEnabled = true)
public class SecurityApplication {

    public static void main(String[] args) {
        ConfigurableApplicationContext run = SpringApplication.run(SecurityApplication.class, args);
    }
}
~~~



2.使用相应注解

~~~java
@RestController
public class BookController {

    // 只有拥有book:query权限的用户才能访问此接口
    @RequestMapping("/book/query")
    @PreAuthorize("hasAnyAuthority('book:query')")
    public String query(){return "query books....";}

    // 只有拥有book:delete权限的用户才能访问此接口
    @RequestMapping("/book/delete")
    @PreAuthorize("hasAnyAuthority('book:delete')")
    public String delete(){return "delete books....";}
}
~~~



3.从数据库中获取权限信息

~~~java
@Mapper
public interface MenuMapper extends BaseMapper<Menu> {

    List<String> selectPermsByUserId( Long id);
}

~~~

~~~xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="com.why.mapper.MenuMapper">

    <select id="selectPermsByUserId" resultType="string">
        select distinct m.perms
        from sys_user_role ur
        left join sys_role r on r.id = ur.role_id
        left join sys_role_menu rm on rm.role_id = r.id
        left join sys_menu m on m.id = rm.menu_id
        where ur.user_id = #{id}
    </select>
</mapper>
~~~

4.封装用户权限信息

~~~java
@Service
public class InDataBaseUserDetailManager implements UserDetailsService {

    @Autowired
    private UserMapper userMapper;
    @Autowired
    private MenuMapper menuMapper;

    @Override
    public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {
        // 从数据库中查询信息...
        // TODO 根据用户查询权限信息添加到LoginUser中
        List<String> perms = menuMapper.selectPermsByUserId(user.getId());
        return new LoginUser(user,perms);
    }
}
~~~

5.在JWT认证过滤器中获取用户权限信息,用于权限校验

~~~java
@Component
public class JwtAuthenticationFilter extends OncePerRequestFilter {

    @Autowired
    private RedisCache redisCache;

    @Override
    protected void doFilterInternal(HttpServletRequest request, HttpServletResponse response, FilterChain filterChain)
                                    throws ServletException, IOException {

        // 省略之前的代码...
        
        // 获取在Redis中的用户的权限信息
        Collection<? extends GrantedAuthority> authorities = loginUser.getAuthorities();
        // 将用户信息封装到authentication
        // TODO 将权限信息封装到authentication
        UsernamePasswordAuthenticationToken authentication = new 		
            						UsernamePasswordAuthenticationToken(loginUser,null,authorities);

        // 将用户信息存入SecurityContextHolder
        SecurityContextHolder.getContext().setAuthentication(authentication);
        filterChain.doFilter(request,response);
    }
}
~~~

## 其他权限校验

<h4>基于注解的权限控制</h4>

> hasAnyAuthority
>
> ​	hasAnyAuthority方法可以传入多个权限,只有用户有其中任意权限都可以访问对应资源
>
> hasRole
>
> ​	hasRole要求有对应的角色才可以访问,但是它内部会把我们传入的参数拼接上`ROLE_`后再去比较
>
> ​	所以这种情况下数据库中用户对应的权限也要有`ROLE_`这个前缀才可以
>
> hasAnyRole
>
> ​	hasAnyRole可以传入多个角色,只要有任意的角色就可以访问,同样需要加`ROLE_`前缀

~~~java
@PreAuthorize("hasAnyAuthority('book:update','book:insert')")
@PreAuthorize("hasRole('reader')")
@PreAuthorize("hasAnyRole('admin','reader')")
~~~

<h4>基于配置的权限控制</h4>

> 也通过配置进行权限的控制

~~~java

@Configuration
public class SecurityConfig extends WebSecurityConfigurerAdapter {

    @Override
    protected void configure(HttpSecurity http) throws Exception {
        // 其他配置...
        
        // 基于配置的权限校验
        http.authorizeRequests().antMatchers("/book/query").hasAnyAuthority("book:query")
                .antMatchers("/book/insert","/book/update").hasRole("admin")
                .antMatchers("/book/delete").hasAnyRole("admin","manager");
    }
}
~~~



>自定义失败处理

> 前面的代码中如果在认证失败的情况下只是简单的抛出了异常,而前端无法进行统一处理
>
> 如果希望在认证失败或者是授权失败的情况下也能和接口相同的返回结构的json时需要自定义失败处理器
>
> 在SpringSecurity中如果我们在认证或者授权的过程中出现了异常会被`ExceptionTranslationFilter`捕获到
>
> 在ExceptionTranslationFilter中会去判断是认证失败还是授权失败出现的异常
>
> - 如果是`认证过程`中出现的异常会被封装成AuthenticationException然后调用`AuthenticationEntryPoint`对象的方法去进行异常处理。
>
> - 如果是`授权过程`中出现的异常会被封装成AccessDeniedException然后调用`AccessDeniedHandler`对象的方法去进行异常处理
>
> 所以如果我们需要自定义异常处理,我们只需要自定义`AuthenticationEntryPoint`和`AccessDeniedHandler`然后配置给SpringSecurity即可

先创建认证失败处理器

~~~java
@Component
public class AuthenticationFailedHandler implements AuthenticationEntryPoint {
    @Override
    public void commence(HttpServletRequest request, 
                         HttpServletResponse response, AuthenticationException authException)
                         throws IOException, ServletException {
	    
        response.setStatus(200);
        response.setContentType("application/json");
        response.setCharacterEncoding("utf-8");
        // 统一返回处理对象
        ResponseResult responseResult = new ResponseResult(HttpStatus.UNAUTHORIZED.value(),"认证失败");
        // 转化未json字符串
        String json = JSON.toJSONString(responseResult);
        // 响应给前端
        response.getWriter().print(json);
    }
}
~~~

创建授权失败处理器

~~~java
@Component
public class AccessFailedHandler implements AccessDeniedHandler {

    @Override
    public void handle(HttpServletRequest request, 
                       HttpServletResponse response, AccessDeniedException accessDeniedException)
                       throws IOException, ServletException {
        response.setStatus(200);
        response.setContentType("application/json");
        response.setCharacterEncoding("utf-8");
        ResponseResult responseResult = new ResponseResult(HttpStatus.FORBIDDEN.value(),"权限不足");
        String json = JSON.toJSONString(responseResult);
        response.getWriter().print(json);
    }
}
~~~

将失败处理器配置到SpringSecurity

~~~java
@Configuration
public class SecurityConfig extends WebSecurityConfigurerAdapter {

    @Autowired
    private AuthenticationFailedHandler authenticationFailedHandler;

    @Override
    protected void configure(HttpSecurity http) throws Exception {
	    // 其他配置...
        
        http.exceptionHandling()
                // 添加授权失败处理器
                .accessDeniedHandler(accessFailedHandler)
                // 添加认证失败处理器
                .authenticationEntryPoint(authenticationFailedHandler);
    }
}
~~~



## 跨域问题

>浏览器出于安全的考虑,使用 XMLHttpRequest对象发起 HTTP请求时必须遵守同源策略,否则就是跨域的HTTP请求,默认情况下是被禁止的同源策略要求源相同才能正常进行通信,即协议、域名、端口号都完全一致
>
>前后端分离项目,前端项目和后端项目一般都不是同源的,所以肯定会存在跨域请求的问题

第1步：对SpringBoot进行配置允许跨域

~~~java
@Configuration
public class CorsConfig implements WebMvcConfigurer {

    @Override
    public void addCorsMappings(CorsRegistry registry) {
      // 设置允许跨域的路径
        registry.addMapping("/**")
                // 设置允许跨域请求的域名
                .allowedOriginPatterns("*")
                // 是否允许cookie
                .allowCredentials(true)
                // 设置允许的请求方式
                .allowedMethods("GET", "POST", "DELETE", "PUT")
                // 设置允许的header属性
                .allowedHeaders("*")
                // 跨域允许时间
                .maxAge(3600);
    }
}
~~~

第2步：开启SpringSecurity跨域访问

~~~java
@Configuration
public class SecurityConfig extends WebSecurityConfigurerAdapter {

    @Override
    protected void configure(HttpSecurity http) throws Exception {
        // 其他配置...

        // 允许跨域
        http.cors();
    }
}
~~~



## 其他问题

### CSRF

> CSRF是指跨站请求伪造（Cross-site request forgery）是web常见的攻击之一
>
> 简单来说跨站请求伪造就是当你访问了网站A后未及时退出,而后又访问了危险网站B,危险网站B伪造了1个请求要求你访问网站A,
>
> 此时你带着Cookie访问网站A,而安全网站A无法判断是你发出的请求还是危险网站发出的请求,进而达到了模拟用户操作
>
> SpringSecurity如何防止CSRF攻击?
>
> ​	SpringSecurity去防止CSRF攻击的方式就是通过csrf_token,后端会生成一个csrf_token,前端发起请求的时候需要携带这个csrf_token
>
> ​    后端会有过滤器进行校验，如果没有携带或者是伪造的就不允许访问
>
> 我们可以发现CSRF攻击依靠的是cookie中所携带的认证信息,但是在前后端分离的项目中我们的认证信息其实是token,而token并不是存储中cookie中
>
> 并且需要前端代码去把token设置到请求头中才可以,所以CSRF攻击也就不用担心了

~~~java
@Configuration
public class SecurityConfig extends WebSecurityConfigurerAdapter {

    @Override
    protected void configure(HttpSecurity http) throws Exception {
        // 关闭csrf
        http.csrf().disable()
    }
}
~~~



### 认证成功处理器

> 实际上在`UsernamePasswordAuthenticationFilter`进行登录认证的时候
>
> 如果认证失败了是会调用AuthenticationFailureHandler的方法进行认证失败后的处理的
>
> AuthenticationFailureHandler就是登录失败处理器

~~~java
@Component
public class MySuccessHandler implements AuthenticationSuccessHandler {

    @Override
    public void onAuthenticationSuccess(HttpServletRequest request,
                                        HttpServletResponse response, Authentication authentication) 
        					          throws IOException, ServletException {
        System.out.println("认证成功了");
    }
}
~~~

配置认证成功处理器

~~~java
@Configuration
public class SecurityConfig extends WebSecurityConfigurerAdapter {

    @Autowired
    private AuthenticationSuccessHandler successHandler;

    @Override
    protected void configure(HttpSecurity http) throws Exception {
        // 配置认证成功处理器
        http.formLogin().successHandler(successHandler);

        http.authorizeRequests().anyRequest().authenticated();
    }
}
~~~



### 认证失败处理器

> 实际上在`UsernamePasswordAuthenticationFilter`进行登录认证的时候
>
> 如果认证失败了是会调用AuthenticationFailureHandler的方法进行认证失败后的处理的
>
> AuthenticationFailureHandler就是登录失败处理器

~~~java
@Component
public class MyFailureHandler implements AuthenticationFailureHandler {
    @Override
    public void onAuthenticationFailure(HttpServletRequest request, 
                                        HttpServletResponse response, AuthenticationException exception)
        							 throws IOException, ServletException {
        System.out.println("认证失败了");
    }
}
~~~

配置认证失败处理器

~~~java
@Configuration
public class SecurityConfig extends WebSecurityConfigurerAdapter {

    @Autowired
    private AuthenticationFailureHandler failureHandler;

    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http.formLogin()
                // 配置认证失败处理器
                .failureHandler(failureHandler);

        http.authorizeRequests().anyRequest().authenticated();
    }
}
~~~

### 登出成功处理器

> 登出成功处理器与认证成功处理器相似
>
> SpringSecurity会调用LogoutSuccessHandler的方法进行登出成功后的处理的

~~~java
@Component
public class SGLogoutSuccessHandler implements LogoutSuccessHandler {
    @Override
    public void onLogoutSuccess(HttpServletRequest request, 
                                HttpServletResponse response, Authentication authentication) 
        				      throws IOException, ServletException {
        System.out.println("注销成功");
    }
}
~~~

配置登出成功处理器

~~~java
@Configuration
public class SecurityConfig extends WebSecurityConfigurerAdapter {

    @Autowired
    private LogoutSuccessHandler logoutSuccessHandler;

    @Override
    protected void configure(HttpSecurity http) throws Exception {

        http.logout()
                //配置注销成功处理器
                .logoutSuccessHandler(logoutSuccessHandler);

        http.authorizeRequests().anyRequest().authenticated();
    }
}
~~~

## 配置总结

~~~java
@Configuration
public class SecurityConfig extends WebSecurityConfigurerAdapter {


    @Autowired
    private AccessFailedHandler accessFailedHandler;
    @Autowired
    private AuthenticationFailedHandler authenticationFailedHandler;
    @Autowired
    private JwtAuthenticationFilter jwtAuthenticationTokenFilter;

    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http.authorizeRequests()
                // 放行
                .antMatchers("/user/login").anonymous()
                // 放行,与上面代码功能相同
                // .mvcMatchers("/user/login").permitAll()
                // 判断是否有权限
                .antMatchers("/book/query").hasAnyAuthority("book:query")
                .antMatchers("/book/insert","/book/update").hasRole("admin")
                // 剩余请求都需要认证
                .anyRequest().authenticated()
                .and()
            // 允许跨域
            .cors().and()
            // 关闭csrf攻击
            .csrf().disable()
            // 允许配置会话管理
            .sessionManagement().sessionCreationPolicy(SessionCreationPolicy.STATELESS).and();

        //把token校验过滤器添加到过滤器链中
        http.addFilterBefore(jwtAuthenticationTokenFilter, UsernamePasswordAuthenticationFilter.class);

        http.exceptionHandling()
                // 添加授权失败处理器
                .accessDeniedHandler(accessFailedHandler)
                // 添加认证失败处理器
                .authenticationEntryPoint(authenticationFailedHandler);
    }

    @Bean
    @Override
    public AuthenticationManager authenticationManagerBean() throws Exception {
        return super.authenticationManagerBean();
    }
}

~~~



WebSecurityConfigurerAdapter过时问题

> WebSecurityConfigurerAdapter已过时被弃用解决方案
>
> - 使用 SecurityFilterChain Bean 来配置 HttpSecurity
>
> - 使用 WebSecurityCustomizer Bean 来配置 WebSecurity

1.安全过滤器链配置方法

~~~java
@Configuration
public class SecurityConfig {   
    
    @Bean
    public SecurityFilterChain securityFilterChain(HttpSecurity http) throws Exception {
        http
            .csrf().disable()
            .sessionManagement().sessionCreationPolicy(SessionCreationPolicy.STATELESS)
            .and()
            .authorizeRequests()
            .antMatchers("/user/login").anonymous()
            .anyRequest().authenticated();
        
        return http.build();
    }   
}
~~~

2.核心过滤器配置方法

~~~java
@Configuration
public class SecurityConfig {
    
    @Bean
    public WebSecurityCustomizer webSecurityCustomizer() {
        return web -> web.ignoring().antMatchers("/login");
    }
}

~~~

3.认证管理器AuthenticationManager配置方法

~~~java
@Configuration
public class SecurityConfig {

    @Autowired
    private AuthenticationConfiguration authenticationConfiguration;
    @Bean
    public AuthenticationManager authenticationManager() throws Exception{
        AuthenticationManager authenticationManager = authenticationConfiguration.getAuthenticationManager();
        return authenticationManager;
    }
}
~~~



# OAuth2.0

## OAuth简介



## 四种授权模式



## Github案例

> 首先要注册Github的OAuth2应用
>
> github -> settings ->Developer settings -> OAuth Apps -> Register a new application

![image-20220825224853314](C:\Users\uuu\AppData\Roaming\Typora\typora-user-images\image-20220825224853314.png)

1.创建boot工程

2.引入依赖

~~~xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-oauth2-client</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-security</artifactId>
</dependency>
~~~

3.添加配置

~~~yaml
spring:
  security:
    oauth2:
      client:
        registration:
          github:
            # 创建完github的oauth应用后可查看
            client-id: 94a19711f120290b242c
            client-secret: 130ab674bd7a4accf86e92900c3be6502b5f544b
            # 一定要和Authorization callback URL相同
            redirect-url: http://localhost:8080/login/oauth2/code/github
~~~

4.开启OAuth认证

~~~java
@Configuration
public class SecurityConfig extends WebSecurityConfigurerAdapter {

    @Override
    protected void configure(HttpSecurity http) throws Exception {

        http.authorizeRequests()
                .anyRequest().authenticated()
                .and()
                .oauth2Login();
    }
}
~~~

5.创建控制器

~~~java
@RestController
public class HelloController {

    @RequestMapping("/index")
    public String index(){
        return "index";
    }

    @RequestMapping("/hello")
    public DefaultOAuth2User hello(){
        // 用户信息
        Authentication authentication = SecurityContextHolder.getContext().getAuthentication();
        DefaultOAuth2User principal =(DefaultOAuth2User) authentication.getPrincipal();
        return principal;
    }
}
~~~

6.访问页面可以看出

![image-20220825234105082](C:\Users\uuu\AppData\Roaming\Typora\typora-user-images\image-20220825234105082.png)