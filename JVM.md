# JVM

JDK文档下载地址 https://docs.oracle.com/javase/specs/index.html

网友笔记https://gitee.com/moxi159753/LearningNotes/tree/master/JVM

# 1.JVM体系结构

## 1.1JVM的整体结构

- HotSpot虚拟机是目前市面上高性能虚拟机的代表作之一
- 它采用`解释器`与`即时编译器`并存的架构

![image-20221023163017291](C:\Users\uuu\AppData\Roaming\Typora\typora-user-images\image-20221023163017291.png)

注意：

Java栈是以前的叫法,现在叫虚拟机栈

方法区是一个虚的概念,具体落地就要看JDK的版本了,在JDK1.7及之前叫永久代,在1.7之后叫元空间



## 1.2JVM的架构模型

指令集架构有两种

- 基于`栈`的架构

  1.设计与实现简单,适用于资源受限的系统(嵌入式设备：打印机、机顶盒)

  2.避开了寄存器的分配难题,使用零地址方式分配

  3.指令集更小（但是指令多）,编译器实现容易

  4.不需要硬件支持,可移植性更好,更好实现跨平台

- 基于`寄存器`的架构

  1.完全依赖硬件,可移植性差

  2.性能优秀,执行效率更高（花费更少的指令去完成一项操作）

  3.大部分情况下,基于寄存器架构的指令集都以一地址指令、二地址指令和三地址指令为主

`由于Java跨平台的设计,Java的指令都是根据栈来设计的`.不同平台CPU的架构不同所以不能设计为基于寄存器的

优点是跨平台、指令集更小、编译容易实现

缺点是性能下降、实现 同样的功能需要更多的指令

~~~shell
# 反编译
javap -v xxx.class
~~~



## 1.3JVM的生命周期

- 虚拟机的启动

  Java虚拟机的启动是通过引导类加载器（bootstrap class loader）创建一个初始类来完成的,这个类由虚拟机的具体实现指定

- 虚拟机的执行

  执行一个Java程序的时候,实际上执行的是一个Java虚拟机的进程。程序开始执行的时候它才运行,程序结束时它就停止

- 虚拟机的退出

  虚拟机的退出有下面几种情况

  1.程序正常执行结束

  2.程序在执行的过程中遇到了异常或者错误而异常终止

  3.由于操作出现错误导致Java虚拟机进程终止

  4.某线程调用Runtime类或者System类的exit方法,或Runtime类的halt方法



## 1.4常见的虚拟机

- Classic VM

  由SUN公司发布,是最早商用的虚拟机,这款虚拟机内部`只提供解释器`

- HotSpot VM

  从JDK1.3开始HotSpot虚拟机成为了`默认虚拟机`,内部提供了解释器和即时编译器（JIT）（其他两个商用虚拟机没有方法区的概念）

- JRockit VM

  由BEA公司(08年被Oracle收购)发布,专注于服务器端应用。`JRockit内部不包含解释器实现`,是最快的JVM

- J9 VM

  由IBM发布,市场定位与HotSpot相似,是目前由影响力的三大商用虚拟机之一

- Graal VM

  由Oracle公司发布,在HotSpot虚拟机上增强而成的`跨语言全栈虚拟机`支持Java、Scala、Groovy、C、C++、JS、Python等。发展前景最好的虚拟机



# 2.类加载子系统

## 2.1类加载过程

- 加载阶段

  1.引导类加载器

  2.扩展类加载器

  3.系统类加载器

- 链接阶段

  1.验证

  2.准备

  3.解析

- 初始化阶段

![image-20221023203256873](C:\Users\uuu\AppData\Roaming\Typora\typora-user-images\image-20221023203256873.png)

<h4>类加载子系统的作用</h4>

类加载子系统负责从文件系统或者网络中加载class文件,class文件在文件的开头后特定的标识

ClassLoader只负责将class文件加载内存中,至于它是否可以运行,则由执行引擎（Execution Engine）决定

类加载的信息存放于方法区的内存空间



<h4>2.1.1加载阶段</h4>

1.通过一个类的全限定名获取定义此类的二进制字节流

2.将这个字节流所代表的静态存储结构转化成方法区的运行时数据结构

3.在内存中生成一个代表这个类的java.lang.Class对象,作为方法区这个类的各种数据的访问入口

<h4>2.1.2链接阶段</h4>

链接阶段由分类三个子阶段

- 验证

  1.确保class文件中的字节流中包含的信息符合当前虚拟机的要求,保证被加载类的正确性 不会危害虚拟机自身安全

  2.主要包含四种验证：文件格式验证、元数据验证、字节码验证、符号引用验证

- 准备

  1.为变量分配内存并设置变量的默认初始值

  `这里不包含final修饰的static变量`,因为final在编译的时候就分配了,准备阶段会显式初始化

  `实例变量也不会被赋初始值`,因为类变量分配在方法区中,而实例变量会随着对象一起分配到堆内存中

- 解析

  将常量池中的符号引用转化为直接引用的过程

<h4>初始化阶段</h4>

初始化阶段就是执行类构造器方法\<clinit>()的过程

注意：1.此方法不是我们说的类的构造器(构造函数),类的构造器是虚拟机视角下的\<init>()方法

​	 2.此方法不需要定义 是javac编译器自动收集类中所有`类变量的赋值动作`和`静态代码块中的语句`合并而成

​	 3.若该类具有父类,JVM会保证子类的\<clinit>()执行前,父类的\<clinit>()方法已经被执行完毕

 	4.虚拟机必须保证一个类的\<clinit>()方法在多线程的环境下被`同步加锁`

​	 

~~~java
public class ClassInitDemo{
    static{
        number = 20;
        //System.out.print(number);// 报错,非法的前向引用
    }
    private static int number = 10;
    
    public static void main(String[] args){
        // 程序不会报错且执行结果为10
        // 在链接阶段的准备子阶段将number=0 在初始化阶段先赋值20再赋值10
        System.out.print(ClassInitDemo.number);
    }
}
~~~



## 2.2类加载器的分类

JVM支持两种类型的类加载器

- 引导类加载器（非Java编写的）

- 自定义类加载器（Java语言编写的）

  Java虚拟机中规定`所有派生于抽象类ClassLoader的类加载器都划分为自定义加载器`

  扩展类加载器和系统类加载器都简介继承于抽象类ClassLoader,所以它们属于自定义类加载器

![image-20221023224507823](C:\Users\uuu\AppData\Roaming\Typora\typora-user-images\image-20221023224507823.png)

这里的四者之间的关系是`包含关系`,而不是继承关系

~~~java
public class ClassLoaderDemo {
    public static void main(String[] args) {
        // 获取系统类加载器
        ClassLoader systemClassLoader = ClassLoader.getSystemClassLoader();
        System.out.println(systemClassLoader); // sun.misc.Launcher$AppClassLoader@18b4aac2

        // 获取上层类加载器：扩展类加载器
        ClassLoader extClassLoader = systemClassLoader.getParent();
        System.out.println(extClassLoader); // sun.misc.Launcher$ExtClassLoader@4554617c

        // 获取上层类加载器：引导类加载器
        ClassLoader bootstrapClassLoader = extClassLoader.getParent();
        System.out.println(bootstrapClassLoader); // null 获取不到引导类加载器

        // 对于用户自定义类来说：默认使用系统类加载器加载
        ClassLoader classLoader = ClassLoaderDemo.class.getClassLoader();
        System.out.println(classLoader); // sun.misc.Launcher$AppClassLoader@18b4aac2

        // 对于Java核心类库来说：使用引导类加载器加载
        ClassLoader classLoader1 = String.class.getClassLoader();
        System.out.println(classLoader1); // null
    }
}
~~~




<h4>引导类加载器</h4>

- 引导类加载器（启动类加载器）是使用`C/C++语言实现`的,嵌套在JVM内部
- 它用来加载Java核心类库（JAVA_HOME/jre/lib/rt.jar、resources.jar或sun.boot.class.path路径下的类）
- 并不继承于java.lang.ClassLoader,没有父类加载器
- 加载扩展类加载器和系统类加载器,并为它们指定父加载器
- 处于安全考虑,BootStrap引导类加载器只会加载包名未java、Javax、sun等开头的类

~~~java
    public static void main(String[] args) {
        // 获取getBootstrapClassPath能够加载的路径
        URL[] urLs = Launcher.getBootstrapClassPath().getURLs();
        for (URL urL : urLs) {
            System.out.println(urL);
        }
        /*
        file:/C:/Program%20Files/Java/jdk1.8.0_101/jre/lib/resources.jar
        file:/C:/Program%20Files/Java/jdk1.8.0_101/jre/lib/rt.jar
        file:/C:/Program%20Files/Java/jdk1.8.0_101/jre/lib/sunrsasign.jar
        file:/C:/Program%20Files/Java/jdk1.8.0_101/jre/lib/jsse.jar
        file:/C:/Program%20Files/Java/jdk1.8.0_101/jre/lib/jce.jar
        file:/C:/Program%20Files/Java/jdk1.8.0_101/jre/lib/charsets.jar
        file:/C:/Program%20Files/Java/jdk1.8.0_101/jre/lib/jfr.jar
        file:/C:/Program%20Files/Java/jdk1.8.0_101/jre/classes
        */
    }
~~~



<h4>扩展类加载器</h4>

- Java语言编写,由sun.misc.Launcher$ExtClassLoader实现,派生于ClassLoader类
- 父类加载器为引导类加载器
- 加载JAVA_HOME/jre/lib/ext子目录（扩展目录）中的类库。如果用户创建的JAR放在此目录下也会自动使用扩展类加载器加载

~~~java
    public static void main(String[] args) {
        // 获取扩展类加载器加载的路径
        String extDir = System.getProperty("java.ext.dirs");
        for (String path : extDir.split(";")){
            System.out.println(path);
        }
        /*
        C:\Program Files\Java\jdk1.8.0_101\jre\lib\ext
        C:\WINDOWS\Sun\Java\lib\ext
        */
    }
~~~



<h4>系统类加载器</h4>

- Java语言编写,由sun.misc.Launcher$AppClassLoader实现,派生于ClassLoader类
- 父类加载器为扩展类加载器
- 负责加载classpath类路径下的类库
- `该类加载器是程序中默认的类加载器`



<h4>自定义类加载器</h4>

自定义加载器的使用场景

- 隔离加载类
- 修改类加载的方式
- 扩展加载源
- 防止源码泄露

自定义加载器的具体步骤

1.继承ClassLoader

2.重写findClass()方法

3.如果在编写自定义加载器时没有太过于复杂的需求,可以直接继承URLClassLoader.这样可以避免自己去编写findClass()方法



## 2.3双亲委派机制

Java虚拟机对class文件采用的是`按需加载`,也就是说当需要使用该类时才会将它的class文件加载到内存生成class对象.

而且加载某个类的class文件时,Java虚拟机采用的时`双亲委派机制`,即把请求交给父类加载器处理,它是一种任务委派模式

>举个例子：
>
>用户自己创建了java.lang包,并且在lang包中创建了String类,用户在new String时加载的是Java提供的String类还是自定义的String类?
>
>答案：加载的是Java提供的String类,原因就在于Java虚拟机采用了双亲委派机制

<h4>双亲委派机制的原理</h4>

1.如果一个类加载器收到了类加载的请求,它自己并不会直接进行加载,而是将类加载请求托付给父类加载器去执行

2.如果父类加载器还存在父类加载器,则进一步向上委托,依次递归,直至类加载请求到达顶层的引导类加载器

3.如果父类加载器可以完成类加载任务就加载,否则子类加载器才会尝试自己去加载,这就是双亲委派机制

![image-20221024000849750](C:\Users\uuu\AppData\Roaming\Typora\typora-user-images\image-20221024000849750.png)

~~~java
package java.lang;

public class String{
    static {
        System.out.println("我是自定义的String");
    }

    // 错误: 在类java.lang.String中找不到main方法
    public static void main(String[] args) {}
}
~~~

<h4>双亲委派机制的优点</h4>

- 避免类的重复加载
- 保护程序安全,防止核心API被随意篡改

~~~java
package java.lang;

public class UserDefineClass {

    // java.lang.SecurityException: Prohibited package name: java.lang
    public static void main(String[] args) {}
}
~~~

## 2.4内容补充

- 在JVM中表示两个class是否为同一个类存在两个必要条件

  1.类的权限定类型必须完全一致

  2.加载这个类的ClassLoader必须相同

- JVM必须知道一个类型是启动类加载器还是用户类加载器加载的,如果一个类型是由用户类加载器加载的,

  那么JVM会将这个类加载器的引用作为类型信息的一部分保存在方法区中。当解析到一个类型到另一个类型的引用的时候,

  JVM需要保证这两个类型的类加载器是相同的。

- Java程序对类的使用方式分为：主动使用和被动使用

  两种方式最主要的区别是`类的被动使用不会导致类的初始化`

  主动使用分为7中情况,其余情况都是被动使用

  1.创建类的实例

  2.访问某个类或接口的静态变量,或对该静态变量赋值

  3.调用类的静态方法

  4.反射

  5.初始化一个类的子类

  6.Java虚拟机启动时被标明为启动类的类

  7.JDK7开始提供的动态语言支持（不太理解）
  
  

# 3.运行时数据区

![image-20221024100045484](C:\Users\uuu\AppData\Roaming\Typora\typora-user-images\image-20221024100045484.png)

## 3.1程序计数器

程序计数器（Program Counter Register）也叫PC寄存器。这里并非是广义上所指的物理寄存器,而是对物理的PC寄存器的一种抽象模拟.

它是一块很小的空间,几乎可以忽略不计.也是运行速度最快的存储区域

在JVM规范中,每个线程都有它自己的程序计数器,是线程私有的.生命周期与线程的生命周期一致

<h4>程序计数器的作用</h4>

程序计数器用来`存储指向下一条指令的地址`,也就是将要执行的指令代码。由执行引擎读取下一条指令

程序计数器是程序控制流的指示器,分支、循环、跳转、异常处理、线程恢复等基础功能都需要依赖程序计数器完成

字节码解释器工作时就是通过改变程序计数器的值来取下一条需要执行的字节码指令

![image-20221024103622488](C:\Users\uuu\AppData\Roaming\Typora\typora-user-images\image-20221024103622488.png)

<h4>两个常见问题</h4>

- 使用程序计数器存储字节码指令地址有什么用？

  因为CPU需要不停的切换各个线程,这时候切换回来以后,就得知道接着从哪个位置开始继续执行。JVM的解释器需要通过程序计数器来确定下一条要执行的指令

- 为什么程序计数器要被设定为线程私有的？

  为了能够准确地记录各个线程正在执行地当前字节码指令地址,最好地方法自然是为每一个线程都分配一个程序计数器.这样一来各个线程之间便可以进行独立计算,从而不会出现相互干扰地情况



## 3.2虚拟机栈

### 虚拟机栈概述

Java虚拟机栈（Java Virtual Machine Stack）早期也叫Java栈

每个线程在创建时都会创建一个虚拟机栈,其内部保存一个个的栈帧.每个栈帧对应一个Java方法的调用

虚拟机栈是线程私有的,其生命周期与线程一致

<h4>虚拟机栈的作用</h4>

主要负责Java程序的运行,它保存方法的`局部变量`（基本数据类型、对象的引用地址）`、部分结果并参与方法的调用与返回`

<h4>栈的优点</h4>

- 栈是一种快速有效的分配存储方式,访问速度仅次于程序计数器
- JVM直接对Java栈的操作只有两个：入栈和出栈
- 对于栈来说不存在垃圾回收



### 设置栈的大小

Java虚拟机规范允许Java栈的大小是`动态的`或者是`固定不变的`

~~~shell
# 设置栈大小参数
# 默认单位是字节 K-KB M-MB G-GB
-Xss 64K
# 在哪设置:Run -> Edit Configurations -> VM options 
~~~



### 栈的存储单位

每个线程都有自己的栈,栈中的数据都是以`栈帧`的格式存在

在这个线程上正在执行的每个方法都各自对应一个栈帧

栈帧是一个内存区块,维系着方法在执行过程中的各种属性据信息

<h4>栈帧的内部结构</h4>

每个栈帧中存储着

- `局部变量表`
- `操作数栈`（表达式栈）
- 动态链接（指向运行时常量池的方法引用）
- 方法返回地址（方法正常退出或异常退出的定义）
- 一些附加信息

![image-20221024195914856](C:\Users\uuu\AppData\Roaming\Typora\typora-user-images\image-20221024195914856.png)



### 局部变量表

局部变量表也被称为局部变量数组或本地变量表

`定义为一个数字数组,主要用于存储方法参数和定义在方法内的局部变量`(基本数据类型、对象引用)`以及返回地址类型`

`局部变量表所需的容量大小是在编译器确定下来的`,并保存在方法的Code属性的maximum local variables数据项中。在方法运行期间不会改变局部变量表的大小

`方法嵌套调用的次数由栈的大小决定`,栈越大方法嵌套调用的次数就越多。对于一个函数而言,它的参数和局部变量越多,使得局部变量表越膨胀,它的栈帧就越大,进而导致方法嵌套次数减小。

`局部变量表中的变量只在当前方法调用中生效`,在方法执行时,虚拟机通过使用局部变量表完成参数值到参数变量列表的传递过程。`当方法调用结束后,随着方法栈帧的销毁,局部变量表也会随之销毁`

<h4>Slot</h4>

局部变量表`最基本的存储单位`是Slot(变量槽)

在局部变量表中,`32位以内的类型只占一个Slot`（包括引用类型,returnAddress类型）`64位的类型`（long和double）`占用两个Slot`

> 注意点：
>
> 1.如果需要访问局部变量表中的64位的局部变量时,需要使用`前一个索引`
>
> 2.如果当前帧是`构造方法`或者`实例方法`,那么该对象引用`this将会放在index为0的slot处`
>
> 3.`slot可以重复利用`,如果一个局部变量超出了其作用域,那么在其作用域之后声明的新的局部变量就很可能会复用过期局部变量的槽位
>
> 这也解释了静态方法中为什么不能使用this,因为this不存在于静态方法的局部变量表中

<h4>其他内容补充</h4>

- 在栈帧中,与性能调优关系最为密切的部分就是局部变量表,在方法执行时,虚拟机使用局部变量表完成方法的传递

- 局部变量表中的变量也是重要的垃圾回收根节点,只要被局部变量表中直接或间接引用的对象都不会被回收



### 操作数栈

每一个独立的栈帧中除了包含局部变量表以外,还包含一个先进后出的`操作数栈`,也称为表达式栈

在方法的执行过程中,根据字节码指令往操作数栈中写入数据获取提取数据 即入栈/出栈

![image-20221024221337033](C:\Users\uuu\AppData\Roaming\Typora\typora-user-images\image-20221024221337033.png)

<h4>操作数栈的作用

操作数栈`主要用于保存计算的中间结果,同时作为计算过程中变量临时的存储空间`

当一个方法开始执行时,操作数栈会随着栈帧的创建而创建,每个操作数栈都有明确的栈深度用于存储数值,其大小在编译时就已经确定

保存在Code属性中的max_stack中 32位的类型占用一个栈单位的深度,64位的类型占用两个栈单位的深度









代码追踪

栈顶缓存技术

动态链接

方法的调用

方法返回地址

