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
- 日期函数 文档
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



## 命名空间



## 连接数据库



## 包管理



## 单元测试



## 框架
