## 变量

<h6>声明变量</h6>

- `PHP`中的变量无需声明类型 `PHP`会自动识别

~~~php
<?php
    
# 声明int类型变量
$var_name = 0;
# 声明float类型变量
$var_name = 1.0;
# 声明bool类型变量
$var_name = false;
# 声明字符串类型变量
$var_name = 'true';
~~~

<h6>预定义变量</h6>

- 简单来说`PHP`中的预定义变量就是`PHP`的系统变量 它由`PHP`提供

常见的`PHP`预定义变量

- `$GLOBALS` 全局作用域中所有可用变量
- `$_SERVER` 服务器和执行环境信息
- `$_GET HTTP` GET请求中的变量
- `$_POST HTTP` POST请求中的变量
- 更多预定义变量请参考[官网文档](php.net/manual/zh/reserved.variables.php)

```php
<?php

# $GLOBALS 全局作用域中所有可用变量
print_r($GLOBALS);
# $_SERVER 服务器和执行环境信息
print_r($_SERVER);
# $_GET HTTP GET请求中的变量
print_r($_GET);
# $_POST HTTP POST请求中的变量
print_r($_POST);
```

<h6>变量的范围</h6>

- 与`Java`不同的是 在`PHP`中函数无法访问全局变量

如果想在函数内访问全局变量 可以使用

- 超全部变量`$_global`
- 使用`global`关键字 使用该关键字也可以在函数外部访问函数内部定义的变量

~~~php
<?php
    
$_global = 'global';

function localFunc():void
{
    # 无法直接访问(编译错误)
    echo $_global;

    # 方式1
    echo $GLOBALS['_global'];

    # 方式2
    global $_global;
    echo $_global;
}

localFunc();
~~~

<h6>静态变量</h6>

- `PHP`中的静态变量不同于`Java`中的静态变量 
- `PHP`中的静态变量不仅可以作用于类也可以作用于函数

```php
<?php
    
function staticFunc():void
{
    # 静态变量
	static $static_var = 0;
   	# 非静态变量
    $not_static = 0;
    $static_var++;
    $not_static++;
    echo $static_var;
    echo $not_static;
}
staticFunc(); # 1 1
staticFunc(); # 2 1
staticFunc(); # 3 1
```

<h6>可变变量</h6>

- 变量名可以动态的设置和使用

~~~php
<?php
    
$_x = "xxx";
$$_x = "zzz";

# 都输出zzz
echo $xxx;
echo $$_x;

# 可以套娃
$$$_x = "ttt";
echo $zzz;
~~~

## 常量

<h6>定义常量</h6>

```php
<?php
    
# 方式1(不推荐)
define("SPRING","春天");

# 方式2
const SUMMER = "夏天";
```

> 注意
>
> `PHP`中的常量不同于`Java`中的常量
>
> - 在`Java`中对于对象或者数组常量 它们的引用不能改变但是内容可变
> - 而`PHP`中的对象或数组常量不仅不能修改引用而且内容也不能改变

~~~php
<?php

const SEASON = ["春天", "夏天", "秋天", "冬天"];
# 报错
SEASON[0] = "spring";
~~~

<h6>预定义常量</h6>

- `PHP_VERSION`   `PHP`版本信息
- `PHP_INT_MIN`   `Int`类型的最小值
- `PHP_INT_MAX`   `Int`类型的最大值
- `PHP_INT_SIZE` `Int`类型占用的字节数
- 更多预定义常量请参考 [官方文档](https://www.php.net/manual/zh/reserved.constants.php)

```php
<?php
    
echo PHP_VERSION;
echo PHP_INT_MIN;
echo PHP_INT_MAX;
echo PHP_INT_SIZE;
```

<h6>魔术常量</h6> 

- 更多魔术常量请参考 [官方文档](https://www.php.net/manual/zh/language.constants.magic.php)

```php
<?php
    
# 当前文件所在目录
echo __DIR__;
# 当前行号
echo __LINE__;
# 当前文件完整路径
echo __FILE__;
# 当前类名
echo __CLASS__;
# 当前类的方法名
echo __METHOD__;
# 当前函数名称
echo __FUNCTION__;
# 当前命名空间名称
echo __NAMESPACE__;
```

## 运算符

- 错误抑制符 `@`
- 字符串拼接 `.`
- 详细的运算符请参考[官方文档](https://www.php.net/manual/zh/language.operators.php)

~~~php
<?php
    
    $a = 1;
    $b = 0;
    $c = $a/$b; # 运行报错
   @$c = $a/$b; # 运行通过

	# 字符串拼接
	echo 'hello'.'world';
~~~

## 数组

<h6>创建数组</h6>

- `PHP`中的数组可以指定下标 类型与`Java`中的`Map`
- 若创建数组时不指定下标 默认从0开始

```php
<?php
    
# 方式1
$arr1 = array(
    'dog' => "小狗",
    'cat' => '小猫',
    'pig' => '小猪'
);

# 方式2
$arr2 = [
    'dog' => "小狗",
    'cat' => '小猫',
    'pig' => '小猪'
];
```

<h6>遍历数组</h6>

```php
<?php
    
# 遍历数组
foreach ($arr1 as $index => $value){
    echo 'key:',$index,'value:',$value,'<hr>';
}

```

<h6>访问数组元素</h6>

```php
<?php
    
echo $arr1['cat'];
echo $arr2['cat'];
```

<h6>添加元素</h6>

```php
<?php
$arr4 = [0=>'篮球', 5=>'足球', 3=>'网球'];

# 添加元素时指定key
$arr4['pingpong'] = "乒乓球";

# 添加元素时不指定key
$arr4[] = "羽毛球";
# 等同于
$arr4[6] = "羽毛球";
```

<h6>删除元素</h6>

```php
<?php

# 删除数组元素
unset($arr4[0]);
    
# 删除元素后被删除的key不会被重新使用
# 重置索引(key)
$arr4 = array_values($arr4);
```

<h6>数组解包</h6>

~~~php
<?php

$_arr5 = ['foo', 'bar', 'baz'];
# 数组解包
[$_foo, $_bar, $_baz] = $_arr5;
echo $_foo; # foo
echo $_bar; # bar
echo $_baz; # baz

# 赋值指定key的值
[0 => $_temp] = $_arr5;
echo $_temp; # foo


# 使用数组解包交换变量
$_a = 10;
$_b = 20;
[$_a, $_b] = [$_b, $_a];
echo $_a; # 20
echo $_b; # 10

# 合并数组
$_arr6 = [1,2,3];
$_arr7 = [4,5,6];
$_arr8 = [...$_arr6, ...$_arr7];
# 等同于
$_arr8 = $_arr6 + $_arr7;
~~~

<h6>数组拷贝</h6>

~~~php
<?php

$_arr13 = [1,2,3];

# 数组值拷贝
$_arr14 = $_arr13;
$_arr14 = [];
print_r($_arr13); # Array([0]=>1 [1]=>2 [2]=>3)

# 数组引用拷贝
$_arr15 = &$_arr13;
$_arr15 = [];
print_r($_arr13); # Array()
~~~

## 对象

<h6>对象的创建</h6>

- 与Java不同的是调用对象方法和属性不用 `.` 而是使用 `->`
- 构造方法有固定的名字 `__construct`

```php
class Person{
    # 成员属性
    public string $name;
    # 构造方法
    public function __construct(){}
    # 成员方法
    function sayHello():void{
        echo "hello";
    }
}

# 创建对象
$_person1 = new Person();
$_person2 = new('Person');
# 给属性赋值
$_person1->name = 'andy';
# 调用方法
$_person1->sayHello();
```

<h6>只读类</h6>

- `PHP8.2`以后才支持`readonly`类

~~~php
readonly class Phone{
    # 只读类的成员属性必须指定类型
    # 只读类的成员属性不能直接赋值
    public int $width;
    public int $height;
    # 只能在构造函数中赋值
    public function __construct(int $width,int $height){
        $this->width = $width;
        $this->height = $height;
    }
}
$phone = new Phone(10,20);
# 只读类无法修改成员变量
# $phone->width = 20;
echo $phone->width;

~~~

<h6>nullSafe操作符</h6>

- `?->` 对象引用解析为null时不抛出异常，而是返回null
- `nullSafe`操作符在`PHP8.0`后启用

```php
$phone = true ? null : new Phone(10,10);
# 报错
echo $phone->width;
# 不报错
echo $phone?->width;
```

<h6>类中的各种属性</h6>

- 公有属性 `public`修饰: 任何类都可以访问
- 保护属性 `protected`修饰: 只能在本类和子类中访问
- 私有属性 `private`修饰: 只能在本类中访问
- 只读属性 `readonly`修饰: `PHP8.2`后启用
- 静态属性 `static`修饰
- 类常量 `const`修饰

```php
# 属性
class Student{
    # 没有声明访问控制修饰符的属性将默认声明public
    
    # 公有属性
    # 同string $name;
    public string $name;
    # 保护属性
    protected int $age;
    # 私有属性
    private int $money;
    # 只读属性
    readonly string $sex; 
    # 静态属性
    static int $count; 
    # 类常量(无需指定类型）
    public const URL = 'www.baidu.com';

    function showUrl():void
    {
        # 内部使用类常量
        echo self::URL;
    }
}
# 外部使用类常量
echo Student::URL;
```

<h6>继承</h6>

- 和Java中的继承差不多

```php
class Father{
    public function __construct(){
        echo '父类构造函数执行';
    }
}

class Son extends Father{
    public function __construct(){
        parent::__construct();
        echo '子类构造函数执行';
    }
}
$son = new Son();
```

<h6>构造器属性提升</h6>

- 若构造函数参数带访问控制符时 `PHP`会同时把它当作对象属性和构造器参数并赋值到属性
- 构造器属性提升在`PHP8.0`以后启用

```php
class Point {
    public function __construct(public int $x, public int $y = 0) {
        # 相当于执行下面代码
        # $this->x = $x;
        # $this->y = $y;
    }
}
$point = new Point(10,10);
echo $point->x; # 10
echo $point->y; # 10
```

<h6>构造函数与析构函数</h6>

- 构造函数: 在对象创建时调用
- 析构函数: 在到某个对象的所有引用都被删除或者当对象被显式销毁时执行

> 注意
>
> - 每个类只存在1个构造函数 `PHP`中的构造函数不能重载
>
> - 使用默认参数可以做到Java中构造函数重载的效果
>
> - 命名实参在`PHP8.0`后启用 `new Product(id:'1');`

```php
class Product{
    private ?string $id;
    private ?string $name;
    # 构造函数
    public function __construct(?string $id = '', ?string $name = '')
    {
        $this->id = $id;
        $this->name = $name;
    }
    # 使用static方法包装构造
    public static function createObj():static
    {
        return new static('1','可乐');
    }
    # 析构函数
    public function __destruct()
    {
        echo '析构函数执行了 ';
    }

}
$p1 = new Product();
$p2 = new Product(id:'1');
$p3 = new Product(name:'可乐');
$p4 = new Product('1','可乐');

# 析构函数执行了
$p5 = Product::createObj(); 
echo $p1,$p2,$p3,$p4,$p5;

unset($p1); # 析构函数执行了
unset($p2); # 析构函数执行了
unset($p3); # 析构函数执行了
unset($p4); # 析构函数执行了
unset($p5);
```

<h6>抽象类</h6>

- 与`Java`抽象类类似

```php
abstract class Animal{
    # 抽象方法
    protected abstract function eat();
    # 非抽象方法
    protected function run():void
    {
        echo "animal is running";
    }
}
class Dog extends Animal{
    protected function eat(){
        echo 'Dog is eating';
	}
}
```

<h6>接口</h6>

- 与`Java`接口类似

```php
interface People{
    # 接口中只能存在常量
    const EYE_COUNT = 2;

    public function sayHello($name);
}
class Children implements People{
    # 重写方法
    public function sayHello($name)
    {
        echo 'hello'.$name;
    }
}
$worker = new Children();
$worker->sayHello('Jack');
```

<h6>重载(重点)</h6>

- `PHP`中的重载于`Java`中的重载完全不同
- `PHP`中的重载指的是<font color='red'>动态地创建类属性和方法</font>

<h6>属性重载</h6>

> 注意
>
> - 属性重载只能在对象中进行
> - 重载方法不能被声明成 `static`
> - 所有的重载方法都必须被声明为 `public`

- `__get()` : 读取不可访问(protected或private)或不存在的属性的值时调用
- `__set()` : 在给不可访问(protected或private)或不存在的属性赋值时调用
- `__isset()` : 当对不可访问或不存在的属性调用`isset()`或`empty()`时调用
- `__unset()` : 当对不可访问或不存在的属性调用unset()时调用

```php
class Property {
    # 被重载的数据保存在此
    private $data = array();
    # 重载不能被用在已经定义的属性
    public int $declared = 1;
    
    public function __get(string $name)
    {
        if (array_key_exists($name, $this->data)) {
            return $this->data[$name];
        }
        echo "属性{$name}不存在";
        return null;
    }
    
    public function __set(string $name, $value): void
    {
        echo "在给不可访问或不存在的属性{$name}赋值";
        $this->data[$name] = $value;
    }
    
    public function __isset(string $name): bool
    {
        echo "对不可访问或不存在的属性{$name}调用isset()或empty()";
        return isset($this->data[$name]);
    }
    
    public function __unset(string $name): void
    {
        echo "对不可访问或不存在的属性{$name}调用unset()";
        unset($this->data[$name]);
    }
}
$property = new Property();
echo $property->declared
# 在给不可访问或不存在的属性noDeclared赋值
$property->noDeclared = 10;
echo $property->noDeclared;
# 对不可访问或不存在的属性noDeclared调用isset()或empty()
echo isset($property->noDeclared);
# 对不可访问或不存在的属性noDeclared调用unset()
unset($property->noDeclared);
# 属性noDeclared不存在
echo $property->noDeclared; 
```

<h6>方法重载</h6>

- `__call` : 访问不存在的实例方法时调用
- `__callStatic` : 访问不存在的静态方法时调用

```php
class Func{

	public function existFunc():void{
		echo "调用已存在的方法";
	}
    public function __call(string $name, array $arguments){
        echo "调用不存在的实例方法{$name}";
        print_r($arguments);
    }

    public static function __callStatic(string $name, array $arguments){
        echo "调用不存在的静态方法{$name}";
        print_r($arguments);
	x`}
}
$_func = new Func();
$_func->existFunc(); # 调用已存在的方法
$_func->notExistFunc(); # 调用不存在的实例方法notExistFunc
Func::notExistStaticFunc(); # 调用不存在的静态方法notExistStaticFunc
```

## 异常

<h6>异常抛出与捕获</h6>

- 在`PHP`中没有`throws`关键字 
- 方法声明抛出异常使用`@throws`注解

```php
/**
 * @throws Exception
 */
function throwExp():void{
    throw new Exception("我是异常");
}

# 捕获异常
try {
    throwExp();
} catch (Exception $e) {
    echo $e->getMessage();
}
```

<h6>自定义异常</h6>

```php
class MyException extends Exception{
    
    public function errorMessage():string{
    
        return '在'.$this->getFile().
               '第'.$this->getLine().'行存在错误'.
               '错误信息:'.$this->getMessage();
    }
}
```

<h6>处理未捕获的异常</h6>

- set_exception_handler() 设置处理所有未捕获异常的用户定义函数

```php
function handleExp():void{
	echo "正在处理是定义异常";
}
# 设置处理所有未捕获异常的用户定义函数
set_exception_handler('handleExp');
# 输出正在处理是定义异常
throw new MyException('自定义异常');
```

其他关于异常请查看 [文档](https://www.w3school.com.cn/php/php_exception.asp)

## 常用函数

- 字符串函数 [文档](https://www.php.net/manual/zh/ref.strings.php)
- 数组函数 [文档](https://www.php.net/manual/zh/book.array.php)
- 日期函数 [文档](https://www.php.net/manual/zh/ref.datetime.php)
- 文件函数 [文档](https://www.php.net/manual/zh/ref.filesystem.php)

### 字符串常用函数

- `chr(int $codepoint):string` 

  将数字转换为字符

  ```php
  $var = chr(65);
  // 输出A
  echo $var; 
  ```

- `ord(string $character):int`

  将字符转换为0-255之间的数字

  ```php
  $num = ord('A');
  // 输出65
  echo $num;
  ```

- `echo(string ...$expressions):void`

  输出1个或多个字符串

  ```php
  echo 'hello', 'world', '<hr>';
  ```

- `print(string $expression):int`

  输出字符串

  ```php
  print("hello world");
  ```

> echo 与 print 的区别
>
> - echo 的输出速度比print快
> - print 有返回值为1而echo没有返回值

- `explode(string $separator,string $string,int $limit = PHP_INT_MAX):array`

  分割字符串

  - $separator : 分隔字符
  - $string : 输入的字符串
  - $limit : 分割的个数

  ```php
  $str_arr = explode('-', 'hello-world-good-morning');
  print_r($str_arr); // Array ( [0] => hello [1] => world [2] => good [3] => morning )
  
  $str_arr = explode('-', 'hello-world-good-morning', 3);
  print_r($str_arr); // Array ( [0] => hello [1] => world [2] => good-morning )
  
  // $limit若为0 等价于1
  $str_arr = explode('-', 'hello-world-good-morning', 0);
  $str_arr = explode('-', 'hello-world-good-morning', 1);
  
  // $limit若为负数 返回除了最后的 -limit 个元素外的所有元素
  $str_arr = explode('-', 'hello-world-good-morning', -2);
  print_r($str_arr); // Array ( [0] => hello [1] => world )
  
  ```

- `implode(string $separator, array $array):string`

- `join` : `implode`的别名

  用字符串连接数组元素

  ```php
  // 指定连接符
  echo implode("-",['hello','world']); // hello-world
  // 等同于
  echo join("-",['hello','world']); // hello-world
  // 不指定连接符
  echo implode(['Hello','World']);  // HelloWorld
  ```

- `lcfirst(string $string):string` 将第1个字符小写

- `ucfirst(string $string):string` 将第1个字符大写

  ```php
  echo lcfirst('HelloWorld'); // helloWorld
  echo ucfirst('helloWorld'); // HelloWorld
  ```

- `strtolower(string $string):string` 将字符串转化为小写

- `strtoupper(string $string):string` 将字符串转化为大写

  ```php
  echo strtolower("HelloWorld"); // helloworld
  echo strtoupper("HelloWorld"); // HELLOWORLD
  ```

- `ltrim(string $string, string $characters = " \n\r\t\v\x00"):string`

  删除字符串开头的空白字符或其他字符

- `rtrim(string $string, string $characters = " \n\r\t\v\x00"):string`

  删除字符串末端的空白字符或者其他字符

- `trim(string $string, string $characters = " \n\r\t\v\x00"):string`

  去除字符串首尾处的空白字符或者其他字符

  ```php
  echo ltrim(" HelloWorld "); // HelloWorld 尾部的空白字符不删除
  
  // 删除指定字符
  echo ltrim("-HelloWorld", '-'); // HelloWorld
  
  echo rtrim("HelloWorld-", '-'); // HelloWorld
  
  echo trim("-HelloWorld-", "-"); // HelloWorld
  
  ```

- `nl2br(string $string, bool $use_xhtml=true):string`

  在字符串所有新行之前插入HTML换行标记

  ```php
  echo nl2br("Hello\nWorld");
  ```

- `sprintf(string $format, mixed ...$values):string` 

  返回格式化字符串

  ```php
  $format = 'There are %d monkeys in the %s';
  
  // There are 10 monkeys in the tree
  echo sprintf($format, 10, 'tree'); 
  ```

- `str_replace(array|string $search,array|string $replace,string|array $subject,int &$count=null)`

  子字符串替换

  search : 查找的目标值

  replace : search的替换值

  subject : 执行替换的数组或者字符串

  count : 替换发生的次数

  ```php
  echo str_replace("ha", "hello",'ha-ha',$count); // hello-hello
  
  $search = ['a', 'b', 'c'];
  echo str_replace($search, '-', 'AaBbCcD'); // A-B-C-D
  
  $search = ['basketball', 'football'];
  $replace = ['computeGame','phoneGame'];
  echo str_replace($search,$replace,"I like basketball and football"); 
  // I like computeGame and phoneGame
  
  $search  = ['A', 'B', 'C', 'D', 'E'];
  $replace = ['B', 'C', 'D', 'E', 'F'];
  echo str_replace($search, $replace, 'ABCDE'); // FFFFF
  
  $replace = array('Z', 'C', 'D', 'E', 'F');
  echo str_replace($search, $replace, 'ABCDE'); // ZFFFF
  ```

- `str_ireplace` 

  子字符串替换(忽略大小写)

  ```php
  echo str_ireplace("ha", "hello",'ha-HA'); // hello-hello
  ```

- `str_contains(string $haystack, string $needle):bool` 

  是否包含某个字符串 `PHP8.0`后启用

  ```php
  var_dump(str_contains("helloWorld", "hello")); // bool(true)
  ```

- `str_starts_with(string $haystack, string $needle):bool` 是否以某个字符串开头

- `str_end_with(string $haystack, string $needle):bool` 是否以某个字符串结尾

  ```php
  var_dump(str_starts_with("helloWorld", "hello")); // bool(true)
  
  var_dump(str_ends_with("helloWorld", "hello")); // bool(false)
  ```

- `str_split(string $string, int $length = 1):array` 

  将字符串转为数组

  - `$string` : 要分割的字符串

  - `$length` : 每段字符串长度

  > `str_plit` 和 `explode`的异同
  >
  > 同
  >
  > - 两者都是将字符串分割成数组
  >
  > 异
  >
  > - `explode` 是根据指定分隔符分割
  > - `str_plit` 是根据指定长度平均分割

  ```php
  // 如果$length小于1，将会抛出ValueError
  print_r(str_split("HaHaHaHa",4)); // Array ( [0] => HaHa [1] => HaHa )
  print_r(str_split("HaHaHaHa",2)); // Array ( [0] => Ha [1] => Ha [2] => Ha [3] => Ha )
  ```

  - `strlen(string $string):int` 字符串长度

    ```php
    echo strlen("hello"); // 5
    ```

  - `strip_tags(string $string, array|string|null $allowed_tags = null):string` 

    从字符串中去除 `HTML` 和 `PHP` 标签

    ```php
    echo strip_tags("<h1>hello</h1>"); // hello
    echo strip_tags("<?php echo 'hello'?>hello"); // hello
    ```

  - `stripos(string $haystack,string $needle,int $offset=0):int|false`

    查找字符串首次出现的位置(不区分大小写)

    ```php
    // 存在就返回下标 4
    echo stripos("hello", "o");
    // 不存在就返回false
    echo stripos("hello", "p");
    ```

  -  `strripos(...)` 查找字符串最后1次出现的位置(不区分大小写)

  - `strpos(...)` 查找字符串首次出现的位置(区分大小写)

  - `strrpos(...)` 查找字符串最后1次出现的位置(区分大小写)

    ```php
    echo strripos("helloWorld", "l"); // 8
    
    echo strpos("GoodMorning", "g"); // 10
    
    echo strrpos("HelloLucy", "l"); // 3
    ```

  - `substr(string $string, int $offset, ?int $length = null):string` 

    返回字符串的子串

    ```php
    echo substr("hello", 0, 2); // he
    echo substr("hello",-5, 2); // he
    echo substr("hello", 0); // hello
    ```


### 数组常用函数

- `array_key_exists(string|int $key,array $array):bool` 

  检查数组里是否有指定的键名或索引

  ```php
  $arr = ['one'=>"A", "two"=>"B", "three"=>"C"];
  
  echo array_key_exists("one",$arr); // bool(true)
  echo array_key_exists("abc",$arr); // bool(false)
  ```

- `array_keys(array $array):array`  

  返回数组中部分的或所有的键名

  ```php
  $arr = ['one'=>"A", "two"=>"B", "three"=>"C"];
  
  print_r(array_keys($arr)); // Array([0]=>one [1]=>two [2]=>three)
  // 只返回包含此值的key
  print_r(array_keys($_arr,'B')); // Array([0]=>two)
  ```

- `array_values(array $array):array`  

  返回数组中所有的值

  ```php
  $arr = ['a'=>'A','b'=>'B','c'=>'C'];
  print_r(array_values($arr)); // Array([0]=>A [1]=>B [2]=>C)
  ```

- `array_merge(array ...$arrays):array` 

  合并多个数组

  > `array_merge` 和 `+` 的区别
  >
  > - 若key(非数字)相同 `array_merge`后面的会覆盖前面的 而 `+` 不会覆盖
  > - `array_merge`数字key将会被重新编号 而 `+` 数字key不会被重新编号

  ```php
  $_arr1 = [0=>'A',1=>'B','two'=>'C'];
  $_arr2 = [0=>'D',5=>'E','two'=>'F'];
  
  // Array([0]=>A [1]=>B [two]=>F [2]=>D [3]=>E)
  print_r(array_merge($_arr1,$_arr2));
  // Array([0]=>A [1]=>B [two]=>C [5=>E)
  print_r($_arr1 + $_arr2);
  ```

- `array_pop(array &$array):mixed`

  弹出数组最后一个单元

  ```php
  $arr3 = ['A','B','C','D','E'];
  echo array_pop($arr3); // E
  // Array([0]=>A [1]=>B [2]=>C [3]=>D)
  print_r($arr3);
  ```

- `array_push(array &$array, mixed ...$values):int` 

  将一个或多个单元压入数组的末尾

  ```php
  $arr3 = ['A','B','C'];
  echo array_push($arr3,'D','E'); // 5 新数组长度
  print_r($arr3); // Array([0]=>A [1]=>B [2]=>C [3]=>D [4]=>E)
  ```

- `array_shift(array &$array):mixed` 将数组开头的单元移出数组

- `array_unshift(array &$array, mixed ...$values):int` 在数组开头插入1个或多个单元

  ```php
  $arr3 = ['A','B','C','D','E'];
  echo array_shift($arr3); // A
  // 数字key会被重置 非数字key不会
  print_r($arr3); // Array([0] =>B [1]=>C [2]=>D [3]=>E)
  
  $arr3 = ['A','B','C'];
  echo array_unshift($arr3,'D','E'); // 5 新数组长度
  print_r($arr3); // Array([0]=>D [1]=>E [2]=>A [3]=>B [4]=>C)
  ```

- `array_rand(array $array, int $num = 1):int|string|array` 

  从数组中随机取出1个或多个随机key

  ```php
  // 随机取出1个key
  echo array_rand(['A','B','C']);
  // 随机取出两个key
  print_r(array_rand(['A','B','C'], 2));
  ```

- `array_replace(array $array, array ...$replacements):array` 

  使用传递的数组替换第1个数组的元素

  ```php
  // 只会替换同名的key
  $arr5 = [0=>'A',2=>'B',4=>'C'];
  $arr6 = [0=>'D',1=>'E',2=>'F'];
  // Array([0]=>D [2]=>F [4]=>C [1]=>E)
  print_r(array_replace($arr5,$arr6));
  ```

- `array_reverse(array $array, bool $preserve_keys = false):array` 数组反转

  ```php
  print_r(array_reverse(['A','B','C'])); // Array([0]=>C [1]=>B [2]=>A)
  // key也反转
  print_r(array_reverse(['A','B','C'], true)); // Array([2]=>C [1]=>B [0]=>A)
  ```

- `array_search(mixed $needle, array $haystack, bool $strict = false):int|string|false`

  根据value获取首个key

  ```php
  echo array_search('B',['A','B','C','B']); // 1
  // 开启严格匹配 类型必须相同 如果是对象实例必须相同
  echo array_search('2',[1,2,3,4],true); // bool(false)
  ```

- `array_slice()` 从数组中取出一段

  ```php
  array_slice(
      array $array,
      int $offset,
      ?int $length = null,
      bool $preserve_keys = false
  ): array
  ```

  ```php
  $arr7 = ['A','B','C','D','E'];
  print_r(array_slice($arr7, 2)); // Array([0]=>C [1]=>D [2]=>E)
  
  print_r(array_slice($arr7, 2, 2)); // Array([0]=>C [1]=>D)
  
  print_r(array_slice($arr7,-2)); // Array([0]=>D [1]=>E)
  // 不重置key
  print_r(array_slice($arr7,-2, 2, true)); // Array([3]=>D [4]=>E)
  ```

- `array_splice()` 去掉数组中的某一部分并用其它值取代

  ```php
  array_splice(
      array &$array,
      int $offset,
      ?int $length = null,
      mixed $replacement = []
  ): array
  ```

  ```php
  $arr8 = ['A','B','C','D','E'];
  // 删除数组中的元素
  print_r(array_splice($arr8,3)); // 被删除的元素  Array([0]=>D [1]=>E)
  print_r($arr8); // 剩下的元素 Array([0]=>A [1]=>B [2]=>C)
  
  $arr8 = ['A','B','C','D','E'];
  print_r(array_splice($_arr8,2,1,['X','Y','Z']));
  print_r($_arr8); //  Array([0]=>A [1]=>B [2]=>X [3]=>Y [4]=>Z [5]=>D [6]=>E)
  ```

- `count(Countable|array $value, int $mode=COUNT_NORMAL):int`

  获取数组元素个数

  mode参数为`COUNT_NORMAL`或1将递归对数组计数

  ```php
  $arr = [1, 2, 3];
  echo count($arr); // 3
  
  $arr = [
      [1, 2, 3],
      [4, 5, 6],
      [7, 8, 9]
  ];
  echo count($arr,1); // 12
  
  ```

- `in_array(mixed $needle, array $haystack, bool $strict=false):bool`

  判断某个值是否在数组中(区分大小写)

  ```php
  $arr = ['A', 'B', 'C'];
  var_dump(in_array('A', $arr)); // bool(true)
  var_dump(in_array('a', $arr)); // bool(false)
  // 严格模式
  var_dump(in_array('1', [1,2,3], true)); // bool(false)
  
  ```

- `ksort(array &$array):bool` 对数组根据键名升序排序

- `krsort(array &$array):bool` 对数组按照键名逆向排序

  ```php
  $arr10 = [2=>'B', 0=>'A', '3'=>'C'];
  
  ksort($arr10);
  print_r($arr10); // Array([0]=>A [2]=>B [3]=>C)
  
  krsort($arr10);
  print_r($arr10); // Array([3]=>C [2]=>B [0]=>A)
  ```

- `sort(array &$array):bool` 根据value对数组升序排序

- `rsort(array &$array):bool` 根据value对数组降序排序

  ```php
  $arr11 = ['A','D','C','B','E'];
  sort($arr11);
  // Array([0]=>A [1]=>B [2]=>C [3]=>D [4]=>E)
  print_r($arr11);
  
  $arr11 = ['A','D','C','B','E'];
  rsort($arr11);
  // Array([0]=>E [1]=>D [2]=>C [3]=>B [4]=>A)
  print_r($arr11);
  
  ```

- `list(mixed ...$vars):array` 把数组中的值赋给一组变量

  ```php
  list($a,$b,$c) = ['A','B','C','D'];
  echo $a; // A
  echo $b; // B
  echo $c; // C
  
  // 嵌套list
  list($_a,list($_b,$_c)) = [1,[2,3]];
  echo $_a; // 1
  echo $_b; // 2
  echo $_c; // 3
  
  ```

- `shuffle(array &$array):bool` 打乱数组

  ```php
  $_arr11 = ['A','B','C','D','E'];
  shuffle($_arr11);
  print_r($_arr11);
  ```

### 日期常用函数

- `date_default_timezone_set(string $timezoneId):bool` 

  设置默认时区

  ```php
  date_default_timezone_set('UTC');
  ```

- `date(string $format, ?int $timestamp = null):string` 

  格式化 Unix 时间戳

  ```php
  echo date('l'); // Friday
  echo date('D'); // Fri
  echo date('Y-m-d h:i:s'); // 2023-01-06 01:12:10
  ```

- `mktime(时,分,秒,月,日,年):int|false` 

  取得指定日期的 Unix 时间戳

  ```php
  $tomorrow  = date('Y-m-d',
      mktime(0, 0, 0, date("m"), date("d")+1, date("Y"))
  );
  echo $tomorrow;
  ```

- `time():int` 返回当前的 Unix 时间戳(秒数)

  ```php
  echo time(); // 1672968382
  echo date('Y-m-d', time()); // 2023-01-06
  ```

- `strtotime(string $datetime, ?int $baseTimestamp = null):int|false`

  将任何英文文本日期时间描述解析为 Unix 时间戳

  ```php
  echo date('Y-m-d', strtotime('now')); // 2023-01-06
  echo date('Y-m-d', strtotime('yesterday')); // 2023-01-05
  echo date('Y-m-d', strtotime('+1 day')); // 2023-01-07
  echo date('Y-m-d', strtotime('+1 year')); // 2024-01-06
  echo date('Y-m-d', strtotime('next Monday')); // 2023-01-09
  echo date('Y-m-d', strtotime('last Monday')); // 2023-01-02
  ```

### 文件常用函数

- `basename(string $path, string $suffix = "")` 

  返回路径中的文件名部分

  ```php
  echo basename("/usr/software/hello.text"); // hello.text
  echo basename("/usr/software/hello.text", '.text'); // hello
  ```

- `chmod(string $filename, int $permissions):bool` 

  改变文件权限

  ```php
  # 文件不存在则警告 $permissions需要使用八进制数
  chmod("/usr/images/qrcode.jpg",0777);
  chmod("/usr/images/qrcode.jpg",0666);
  ```

- `chown(string $filename, string|int $user):bool` 

  修改文件所有者(需要有root权限)

  ```php
  chown("/usr/images/qrcode.jpg",'root');
  ```

- `copy(string $from,string $to,?resource $context = null):bool` 

  文件拷贝

  ```php
  copy('/usr/images/qrcode.jpg','/usr/images/2022/qrcode.jpg');
  ```

- rename(string $from,string $to,?resource $context = null):bool 

  重命名文件或文件移动

  ```php
  rename('/usr/images/qrcode.jpg','/usr/images/2022/qrcode.jpg');
  ```

- `unlink(string $filename,?resource $context = null):bool` 

  删除文件

  ```php
  unlink('/usr/images/hello.text');
  ```

- `dirname(string $path,int $levels = 1):string` 

  返回路径中的目录部分

  ```php
  echo dirname('/usr/images/hello.text'); // /usr/images
  ```

- `fopen()` 打开文件或者 URL

  ```php
  fopen(
      string $filename,
      string $mode,
      bool $use_include_path = false,
      ?resource $context = null
  ): resource|false
  ```

  mode

  - r  只读   r+ 读写 (指针指向文件头)
  - w 写入 w+ 读写(指针指向文件头并将文件大小截为零)
  - a  写入  a+ 读写(指针指向文件末尾)
  - x 创建并写入  x+ 创建并读写

  ```php
  $_resource = fopen('/usr/images/hello.text','r');
  echo var_dump($_resource);
  ```

- `fclose(resource $stream):bool` 

  关闭一个已打开的文件指针

  ```php
  fclose($_resource);
  ```

- `feof(resource $stream):bool` 

  测试文件指针是否到了文件结束的位置

  ```php
  echo var_dump(feof($_resource)); // bool(false)
  ```

- `fgetc(resource $stream):string|false` 

  从文件指针中读取字符

  

  ```php
  // 打开文件
  $_resource = fopen('/usr/images/hello.text','r');
  // 如果指针没到结束位置就一直读
  while (false !== ($char = fgetc($_resource))){
      echo $char;
  }
  // 关闭文件
  fclose($_resource);
  ```

- `fgets(resource $stream,?int $length = null):string|false`

  从文件指针中读取字符

  ```php
  $_resource = fopen('/usr/images/hello.text','r');
  
  while (false !== ($char = fgets($_resource))){
      echo $char;
  }
  fclose($_resource);
  ```

- `file_exists(string $filename):bool` 

  检查文件或目录是否存在

  ```php
  var_dump(file_exists('/usr/images/hello.text')); # bool(true)
  var_dump(file_exists('/usr/images/hello.txt')); # bool(false)
  ```

- `file_get_contents(string $filename):string` 

  将整个文件读入一个字符串

  ```php
  echo file_get_contents('/usr/images/hello.text');
  ```

- `file_put_contents()` 

  将数据写入文件

  ```php
  file_put_contents(
      string $filename,
      mixed $data,
      int $flags = 0,
      ?resource $context = null
  ): int|false
  ```

  ```php
  // 默认会覆盖 若文件不存在 会自动创建文件
  file_put_contents('/usr/images/hello.text', 'hello');
  // 追加数据
  file_put_contents('/usr/images/hello.text', '你好世界',FILE_APPEND);
  ```

- `filesize(string $filename):int|false` 

  取得文件大小(字节)

  ```php
  echo filesize('/usr/images/hello.text');
  ```

- `fwrite(resource $stream,string $data,?int $length = null):int|false` 写入文件

- `fputs(...)` fwrite的别名

  ```php
  $fileUrl = '/usr/images/hello.txt';
  // 判断文件是否可写
  if (is_writable($fileUrl)){
      // 使用追加模式打开
      if ($stream = fopen($fileUrl, 'a')){
          fwrite($stream, '你好');
          echo file_get_contents($fileUrl);
      }
  }
  ```

- `fread(resource $stream,int $length):string|false` 

  读取文件

  $length 最多读取 length 个字节

  ```php
  echo fread(fopen('/usr/images/hello.txt','r'),20);
  ```

- `is_dir(string $filename):bool` 

  判断是否是目录

  ```php
  if (is_dir('/usr/images')){
      echo '/usr/images 是目录';
  }
  ```

- `is_executable(string $filename):bool` 

  判断文件是否可以执行

  ```php
  if (is_executable('/usr/images/hello.txt')){
      echo '/usr/images/hello.txt 可以执行';
  }else {
      echo '/usr/images/hello.txt 不能执行';
  }
  ```

- `is_file(string $filename):bool` 是否是文件

- `is_readable(string $filename):bool` 是否可读

- `is_writable(string $filename):bool` 是否可写

- `is_writeable` is_writable的别名

- `mkdir()` 创建目录

  ```php
  mkdir(
      string $directory,
      int $permissions = 0777,
      bool $recursive = false,
      ?resource $context = null
  ): bool
  ```

  ```php
  mkdir('/usr/images/2023');
  // 创建目录并指定权限
  mkdir('/usr/images/2024', 0777);
  // 递归创建
  mkdir('/usr/images/2025', 0777,true);
  ```

- `pathinfo(string $path): array|string` 

  返回文件路径的信息

  ```php
  // Array([dirname]=>/usr/images [basename]=>hello.txt [extension]=>txt [filename]=>hello)
  print_r(pathinfo('/usr/images/hello.txt'));
  ```

- `realpath(string $path): string|false` 

  返回绝对路径

  ```php
  // /home/kz/PhpstormProjects/HelloWorld/fileFunc.php
  echo realpath('./fileFunc.php');
  ```

- `rmdir(string $directory):bool` 

  删除空目录

  ```php
  // 非空目录无法删除
  rmdir('/usr/images/2026');
  ```

## 连接数据库

> 1.安装`mysql`扩展
>
> 2.开启`pdo`配置

<h6>连接相关设置</h6>
`PDO::ATTR_ERRMODE`(错误处理模式)

- `PDO::ERRMODE_SILENT` 不报错
- `PDO::ERRMODE_WARNING` 警告
- `PDO::ERRMODE_EXCEPTION` 报出异常(推荐)

`PDO::ATTR_AUTOCOMMIT`

- 0 关闭自动提交
- 1 开启自动提交

`PDO::ATTR_DEFAULT_FETCH_MODE`(返回结果集格式)

- `PDO::FETCH_ASSOC` 关联数组
- `PDO::FETCH_NUM` 索引数组
- `PDO::FETCH_BOTH`
- `PDO::FETCH_OBJ` 对象格式

`ATTR_PERSISTENT`

- 0 关闭持久化连接
- 1 开启持久化连接

```php
<?php

    $dns = 'mysql:dbname=edu_soho;host:127.0.0.1';
    $username = 'root';
    $password = 'root';

    try {
        # 创建PDO对象
        $pdo = new PDO($dns, $username, $password);
        # 设置参数
        $pdo->setAttribute(PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION);
        # $pdo->setAttribute(PDO::ATTR_PERSISTENT, true);
        # $pdo->setAttribute(PDO::ATTR_AUTOCOMMIT, 1);
        # $pdo->setAttribute(PDO::ATTR_DEFAULT_FETCH_MODE, PDO::FETCH_ASSOC);
        # 执行语句
        # exec 执行有影响行数的语句
        $sql = "insert into user value('2','user','user')";
        $affectedCount = $pdo->exec($sql);
        # query 执行有结果集的语句
        $sql = "select * from user";
        $statement = $pdo->query($sql);
        foreach ($statement as $row){
            echo "{$row['id']}--{$row['username']}--{$row['password']}";
        }

        # 关闭连接
        $statement = null;
        $pdo = null;
    }catch (PDOException $e){
        echo $e->getMessage();
    }
```

<h6>PDO预处理对象</h6>

```php
<?php

    $dns = 'mysql:dbname=edu_soho;host:127.0.0.1';
    $user = 'root';
    $pass = 'root';

    try{
        $pdo = new PDO($dns,$user,$pass);
        $pdo->setAttribute(PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION);

        $sql = "insert into user value (?, ?, ?)";
        # 获取PDO预处理对象user
        $statement = $pdo->prepare($sql);
        # 绑定变量
        $statement->bindParam(1, $id);
        $statement->bindParam(2, $username);
        $statement->bindParam(3, $password);
        # 给变量赋值
        $id = '5';
        $username = 'andy';
        $password = 'andy';
        # 执行语句
        # 返回值为bool
        # $statement->execute();
        # 自动根据顺序赋值给占位符
        # $statement->execute(array("6","bob","bob"));

        $sql = "insert into user value (:id, :username, :password)";
        $state = $pdo->prepare($sql);
        /*
        $state->bindParam(":password",$password);
        $state->bindParam(":username",$username);
        $state->bindParam(":id",$id);
        $id = '10';
        $username = 'lucy';
        $password = 'lucy';
        $state->execute();
        */
        # 相当于上面的代码
        # $state->execute(array("id"=>"13", "password"=>"jack", "username"=>"jack"));

        $sql = "select * from user";
        $query_state = $pdo->prepare($sql);
        $query_state->execute();
        # fetch() 只获取第1个
        # $user = $query_state->fetch();
        # fetchAll() 获取全部
        # $users = $query_state->fetchAll();
        # print_r($users);
        # mode默认为PDO::FETCH_BOTH
        # $users = $query_state->fetchAll(PDO::FETCH_ASSOC);
        # print_r($users);

        $query_state->bindColumn('id',$result_id);
        $query_state->bindColumn('username',$result_username);
        $query_state->bindColumn('password',$result_password);

        # $query_state->fetch(PDO::FETCH_ASSOC);
        # echo "{$result_id}--{$result_username}--{$result_password}";

        # 使用fetchAll只能获取最后1条数据 那如何获取多条数据？
        # $query_state->fetchAll(PDO::FETCH_ASSOC);
        # echo "{$result_id}--{$result_username}--{$result_password}";
        # 那如何获取多条数据？
        while ($query_state->fetch(PDO::FETCH_ASSOC)){
            echo "{$result_id}--{$result_username}--{$result_password}", PHP_EOL;
        }
        
    }catch(PDOException $e){
        echo $e->getLine();
        echo $e->getMessage();
    }
```

<h6>事务</h6>

```php
<?php
    
    $dns = 'mysql:dbname=edu_soho;host:127.0.0.1';
    $user = 'root';
    $pass = 'root';

    try {
        $pdo = new PDO($dns, $user, $pass);
        $pdo->setAttribute(PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION);

        # 关闭事务自动提交
        $pdo->setAttribute(PDO::ATTR_AUTOCOMMIT,0);
        # 开启事务
        $pdo->beginTransaction();

        $sql_1 = "update user set money = money-10 where username = 'root'";
        $affectedRow = $pdo->exec($sql_1);
        if (!$affectedRow){
            throw new PDOException("转出失败");
        }
        $sql_2 = "update user set money = money+10 where username = 'user'";
        $affectedRow = $pdo->exec($sql_2);
        if (!$affectedRow){
            throw new PDOException("转入失败");
        }
        # 交易成功 提交事务
        $pdo->commit();
        echo "交易成功";
    }catch (PDOException $e){
        echo $e->getMessage();
        # 交易失败 回滚事务
        $pdo->rollBack();
    }

    # 最后开启事务自动提交
    $pdo->setAttribute(PDO::ATTR_AUTOCOMMIT,1);
```



## 命名空间



## 包管理

> Composer[教程](https://docs.phpcomposer.com/00-intro.html)
>
> Composer[主要资源库]( https://packagist.org/)

<h6>全局安装</h6>

```shell
curl -sS https://getcomposer.org/installer | php
```

```shell
mv composer.phar /usr/local/bin/composer
```

使用Composer

```shell
composer install
```

更新lock文件

```shell
composer update
```


<h6>项目安装</h6>

```shell
curl -sS https://getcomposer.org/installer | php
```

使用Composer

```shell
php composer.phar install
```

更新lock文件

```shell
php composer.phar update
```

## 框架

### Twig

.........

### Symfony

> `Symfony`简介
>
> `Symfony`是一组`PHP`组件 是Web应用程序框架
>
> 本文档`Symfony`版本3.4 `PHP`版本7.2



<h6>创建Symfony应用</h6>

1.安装Composer

2.在终端使用下面命令

```shell
composer create-project symfony/framework-standard-edition my_project_name
```

```shell
# 创建指定版本
composer create-project symfony/framework-standard-edition my_project_name "2.8.*"
```

运行`Symfony`应用

1.进入项目目录

2.启动项目

```shell
php bin/console server:run
```



<h6>创建页面</h6>

- 创建路由

- 创建控制器

  在`src/AppBundle/Controller`下创建`LuckyController.php`

```php
<?php
namespace AppBundle\Controller;

use Symfony\Component\HttpFoundation\Response;
use Symfony\Component\Routing\Annotation\Route;

class LuckyController
{
    /**
     * @Route("/lucky/number")
     */
    public function numberAction()
    {
        $number = random_int(0, 100);

        return new Response('<html><body>Lucky number: '.$number.'</body></html>');
        
        # 返回twig模板
        # return $this->render('lucky/number.html.twig',['number'=>$number]);
    }
}
```

