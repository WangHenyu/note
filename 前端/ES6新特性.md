# ES6新特性

## 1.let关键字

- 不允许重复声明

```html
<script>
	let a = 10;
	let a = 20; //let不允许重复声明，Identifier 'a' has already been declared

	var b = 1;
	var b = 2; //var可以重复声明
</script>
```

- 块儿级作用域

```html
<script>
	{
		//let name = '张三';
		var name = '张三';
	}
	console.log(name); //读取不到let声明的变量 能读取到var声明的变量
</script>
```

- 不存在变量提升

```html
<script>
	console.log(age);
	let age = 20; //报错：Cannot access 'age' before initialization
	var age = 20;
</script>
```
- 不影响作用域链

```html
<script>
	{
		let sex = '男';
		function fn(){
			console.log(sex);
		}
		fn(); //可以正常输出
	}
</script>
```


## 2.const关键字

const关键字总结：

- 声明必须赋初始值
-  标识符一般为大写
-  不允许重复声明
-  值不允许修改
-  块儿级作用域


## 3.变量的结构赋值

```html
<script>
    //1.数组的结构
    const people = ['张三','李四','王五','赵六'];
    let [zs,ls,ww,zl] = people;
    console.log('zs = '+ zs); //zs = 张三
    console.log('ls = '+ ls); //ls = 李四
    console.log('ww = '+ ww); //ww = 王五
    console.log('zl = '+ zl); //zl = 赵六

    //2.对象的结构
    const stu = {
        name: '狗蛋',
        age: 18,
        xuexi: function(){ console.log('学习') }
    }
    //{}的变量和对象属性名保持相同
    let {name,age,xuexi} = stu;
    console.log(name); //狗蛋
    console.log(age); //18
    xuexi(); //学习

	//3.复杂解构
	let wangfei = {
		name: '王菲',
		age: 18,
		songs: ['红豆', '流年', '暧昧', '传奇'],
		history: [
			{name: '窦唯'},
			{name: '李亚鹏'},
			{name: '谢霆锋'}
		]
	};
	let {songs: [one, two, three], history: [first, second, third]} = wangfei;
</script>
```



## 4.模板字符串

```html
<script>
	//1.声明模板字符串 使用``
	let str = `我是字符串`;
	console.log(typeof(str)); //string

	//2.内容中可以出现换行符
	let str1 = `<ul>
					<li>苹果</li>
					<li>香蕉</li>
					<li>草莓</li>
				</ul>`;
	console.log(str1);

	//3.变量拼接 使用${}
	let lang = 'javascript';
	let out = `${lang}是世界上最好的语言`;
	console.log(out); //javascript是世界上最好的语言       
</script>
```

模板字符串总结：

- 内容中可以出现换行符
- 变量拼接使用${}



## 5.对象简化写法

```html
<script>
	//对象的简化写法
    let name = '张三'
    let age = 20
    let xuexi = function(){console.log('我爱学习')}

    //ES6允许大括号内直接写入变量和函数作为对象的属性和方法
    
    let stu = {                         //let stu = {
        name,                           //     name: name,
        age,                            //     age: age,
        xuexi,                          //     xuexi: xuexi
        eat: function(){                //     eat(){
            console.log('吃饭')          //          console.log('吃饭')
        }                               //      }
    }                                   //}
    console.log(stu)
</script>
```



## 6.箭头函数

```html
<script>
	//1.声明箭头函数
	let fn = ()=>{
		//函数体
	}
    
	//2.箭头函数中的this始终指向声明时所在作用域的this的值
	let getName = ()=>{
		console.log(this); //此时this指向Windows对象
	}
    
    //3.箭头函数不能作为构造函数实例化对象
    let Person = (name,age)=>{
		this.name = name;
		this.age = age;
	}
    let person = new Person('zs',20); //报错，Person is not a constructor

    //4.箭头函数中不能使用arguments变量
	let fn1 = ()=>{
		console.log(arguments);
    }
    fn1(1,2,3); //报错,arguments is not defined at fn1

    //5.箭头函数的简写
    //5.1.当参数有且只有一个时可以省略小括号
	let fn2 = a=> a*a;
    console.log(fn2(3)); //9

    //5.2.当方法体中只有一条语句时可以省略花括号，此时return必须省略
	let fn3 = (a,b)=> a+b;
	console.log(fn3(1,2)); //3
</script>
```

箭头函数总结：

- 箭头函数中的this始终指向声明时所在作用域的this的值
- 箭头函数不能作为构造函数实例化对象
- 箭头函数中不能使用arguments变量
- 当参数有且只有一个时可以省略小括号
- 当方法体中只有一条语句时可以省略花括号



## 7.函数参数的默认值

```html
<script>
    //函数参数的默认值
    //具有默认值的参数一般放在最右边
    function add(a,b,c=10){
        return a+b+c;
    }
    let result = add(1,2);
    console.log(result); //13

    //可以与解构赋值结合
    function connect({host='127.0.0.1',username,password}){
        console.log(host)
        console.log(username)
        console.log(password)
    }
    connect({
        //host: 'localhost',
        username: 'zhansgshang',
        password: '123'
    });
</script>
```



## 8.rest参数

```html
<script>
	//ES5获取实参
    function date(){
        console.log(arguments) //arguments是对象
    }
    date('闪电鸟','火焰鸟','急冻鸟')

    //ES6获取实参 （rest参数）
    //rest参数必须要放在参数的最后
    function date1(...args){
        console.log(args) //agrs是数组
    }
    date1('雷公','水君','炎帝')
</script>
```

rest参数总结：

- rest参数的语法：...args
- rest参数必须要放在参数的最后



## 9.扩展运算符

```html
<script>
	const animal = ['固拉多','盖欧卡','裂空座']

    function fn(){
        console.log(arguments)
    }
	//扩展运算符 ...
    fn(...animal)//等同于fn('固拉多','盖欧卡','裂空座') 

	//1.数组合并
    const a = ['小火龙','杰尼龟','妙蛙种子']
    const b = ['喷火龙','水箭龟','妙蛙花']
    const ab = a.concat(b)  //方式一
    const ab1 = [...a,...b] //方式二
    
    //2.数组克隆
    const hzw = ['路飞','娜美','布鲁克','索隆']
    const copy = [...hzw]

    //3.将伪数组转为真数组
    const divs = document.querySelectorAll('div')
    const divArr = [...divs]
    
    //4.对象复制
    const person = {"name":"jack","age":20}
    const personCopy = {...person}
    
    //5.合并对象(与数组合并类似)
    
</script>
```



## 10.Symbol数据类型

ES6 引入了一种新的原始数据类型 Symbol，表示独一无二的值。

- Symbol 的值是唯一的，用来解决命名冲突的问题
- Symbol 值不能与其他数据进行运算
- Symbol 定义 的 对象属 性 不能 使 用 for…in 循 环遍 历 ，但 是可 以 使 用 Reflect.ownKeys 来获取对象的所有键名

```html
	<script>
		//创建Symbol
        let symbol = Symbol();
        let symbol1 = Symbol('张三')
        let symbol2 = Symbol('张三')
        console.log(symbol1 === symbol2) //false
        let symbol3 = Symbol.for('李四')
        let symbol4 = Symbol.for('李四')
        console.log(symbol3 === symbol4) //true
	</script>
```

对象添加Symbol类型的属性

```html
	<script>
        let game = {
            name: "王者荣耀",
            open: function(){
                console.log("打开王者荣耀");
            },
            close: function(){
                console.log("关闭王者荣耀");
            }
        }

        //如果game对象的有open函数就会被这个函数覆盖
        game.close = function(){
            console.log("我是新添加的close函数");
        }
		game.close(); //我是新添加的close函数
        
        let open = Symbol()
        //对象添加Symbol类型的属性(第一种方式)
        game[open] = function(){
            console.log("我是新添加的open函数");
        }

        game.open(); //打开王者荣耀
        game[open](); //我是新添加的open函数

        let escape = Symbol['escape']
        //对象添加Symbol类型的属性(第二种方式)
        let youxi = {
            name: "三国杀",
            [Symbol('kill')]: function(){ //这种方式定义的方法无法调用
                console.log("杀");
            },
            [escape]: function(){   //这种方式可以调用，用youxi[escape]()
                console.log("闪");
            }
        }
        youxi[escape](); //闪
	</script>
```



## 11.迭代器

- ES6 创造了一种新的遍历命令 for...of 循环，Iterator 接口主要供 for...of 消费 

- 原生具备 iterator 接口的数据(可用 for of 遍历) 

  - Array 

  - Arguments 

  - Set 

  - Map 

  - String 

  - TypedArray 

  - NodeList 

- 工作原理 

  - 创建一个指针<font color= 'red'>对象</font>，指向当前数据结构的起始位置
  - 第一次调用对象的<font color = 'red'> next 方法</font>，指针自动指向数据结构的第一个成员 
  - 接下来不断调用 next 方法，指针一直往后移动，直到指向最后一个成员 
  -  每调用 next 方法返回一个包含 <font color = 'red'>value 和 done </font>属性的对象 

  注: 需要自定义遍历数据的时候，要想到迭代器。

```html
<script>
	const people = {
        name: '张三',
        hobby: [
            '唱歌',
            '跳舞',
            'rap',
            '篮球'
        ],
        //自定义遍历数据
        [Symbol.iterator](){
            let index = 0;
            let _this = this;
            return {
                next(){
                    if(index<_this.hobby.length){
                        const result = {value:_this.hobby[index],done:false};
                        index++;
                        return result;
                    }else{
                        return{value:undefined,done:true};
                    }
                }
            }
        }
    }

    //要想使用for of遍历必须要添加iterator属性
    for (const e of people) {
        console.log(e);
    }
</script>
```

## 12.生成器函数

生成器函数是 ES6 提供的一种异步编程解决方案

```html
<script>
	//语法
	function* 生成器函数名(){}
    
	//yield相当于是函数代码的分隔符
	function* fn() {
		console.log(111);
		yield '一只没有耳朵';
		console.log(222);
		console.log(333);
		yield '一只没有尾巴';
		console.log(444);
		return '真奇怪';
	}
	let iterator = fn();
	iterator.next() //调用console.log(111)
	iterator.next() //调用console.log(222) 和 console.log(333)
	iterator.next() //调用console.log(444)
	console.log(iterator.next())//
</script>
```

- 生成器函数返回的结果是迭代器对象，调用迭代器对象的 next 方法可以得到 yield 语句后的值
- yield 相当于函数的暂停标记，也可以认为是函数的分隔符，每调用一次 next 方法，执行一段代码
- next 方法可以传递实参，作为 yield 语句的返回值

```html
<script>
    //生成器函数的参数传递
    function* gen(args){
        console.log(args);  //AAA
        let one = yield 111;
        console.log(one);   //BBB
        let two = yield 222;
        console.log(two);   //CCC
        let three = yield 333;
        console.log(three); //DDD
    }

    let iterator = gen('AAA');
    iterator.next()

    //next函数可以传入实参，第n次调用next(),传入的实参将作为第n-1个yield的返回结果
    iterator.next('BBB')
    iterator.next('CCC')
    iterator.next('DDD')
</script>
```

```html
<script>
	//生成器函数的使用
    //1s后控制台输出111，再过2s后控制台输出222...
    
    //使用定时器实现
    setTimeout(()=>{
        console.log(111);
        setTimeout(()=>{
            console.log(222);
            setTimeout(()=>{
                console.log(333);
                //回调地狱，代码可读性太低
            },3000);
        },2000);
    },1000);

    //使用生成器函数实现
    function one(){setTimeout(()=>{
        console.log(111);
        iterator.next();
    },1000)}
    function two(){setTimeout(()=>{
        console.log(222);
        iterator.next();
    },2000)}
    function three(){setTimeout(()=>{
        console.log(333);
        iterator.next();
    },3000)}
    
    function* gen(){
        yield one();
        yield two();
        yield three();
    }
    //调用生成器函数
    let iterator = gen();
    iterator.next();
</script>
```



## 13.Promise

Promise 是 ES6 引入的异步编程的新解决方案。语法上 Promise 是一个构造函数， 用来封装异步操作并可以获取其成功或失败的结果。

- Promise 构造函数: Promise (excutor) {} 
- Promise.prototype.then 方法
- Promise.prototype.catch 方法

```html
<script>
    //实例化Promise对象
    const p = new Promise(function(resolve,reject){
        setTimeout(function(){
            let data = '数据库中的用户数据';
            //调用resolve()函数后这个Promise对象的状态会变为成功
            //resolve(data);

            let error = '数据库读取失败';
            //调用reject()函数后这个Promise对象的状态会变为失败
            reject(error);
        },1000)
    })

    //p的状态为成功这调用第一个回调函数，状态为失败就调用第二个回调函数
    p.then(function(value){
        //成功的回调函数
        console.log(value);
    },function(reason){
        //失败的回到函数
        console.log(reason);
    })
</script>
```

Promise封装读取文件

```js
//引入fs模块
const fs = require('fs');

//调用方法读取文件
// fs.readFile('./example.txt',(err,data)=>{
//     if(err)
//         //如果失败就抛出异常
//         throw err;
//     //如果没有出错就输出内容
//     console.log(data.toString());
// })

//使用Promis封装
const  p = new Promise(function(resolve,reject){
    fs.readFile('./example.txt',(err,data)=>{
        if(err)
            reject(err);
        resolve(data.toString());
    });
});

p.then(function(value){
    console.log(value);
},function(reason){
    console.log('数据库读取失败');
});
```



```shell
PS C:\Users\uuu\Desktop\项目文件\VSCode\ES6> node .\IOTest.js
发生什么事了？
发生什么事了？
发生什么事了？
发生什么事了？
发生什么事了？
PS C:\Users\uuu\Desktop\项目文件\VSCode\ES6> 
```

Promise封装ajax

```html
<script>
    原生ajax
    1.创建XMLHttpRequest对象
    const xhr = new XMLHttpRequest()

    //2.初始化
    xhr.open("GET","https://api.apiopen.top/get")

    //发送ajax请求.
    xhr.send()

    //绑定事件，处理响应结果.
    xhr.onreadystatechange = function(){
        if(xhr.readyState === 4){
            if(xhr.status >= 200 && xhr.status < 300){
                console.log(xhr.response)
            }else{
                console.log(xhr.status)
            }
        }
    }

    //使用Promise封装
    const p = new Promise(function (resolve, reject) {
        const xhr = new XMLHttpRequest()
        xhr.open("GET", "https://api.apiopen.top/get")
        xhr.send()
        xhr.onreadystatechange = function () {
            if (xhr.readyState === 4) {
                if (xhr.status >= 200 && xhr.status < 300) {
                    //成功
                    resolve(xhr.response)
                } else {
                    //失败
                    reject(xhr.status)
                }
            }
        }
    })

    p.then(function(value){
        console.log(value)
    },function(reason){
        console.log(reason)
    })

</script>
```

关于then方法

```html
<script>
    //then函数的返回结果是什么？返回结果是新的Promise对象
    //1.如果回调函数中返回的是非Promise的值，then返回的Promise对象状态为成功，对象的成功值为返回值
    //2.如果回调函数中没有写return语句,默认返回的是undefined,结果同1
    //3.如果回调函数中返回的是Promise对象,then返回的Promise对象状态由被返回的Promise对象决定
    //4.如果回调函数中抛出错误,返回的Promise对象状态为失败,错误的值就是抛出的值

    const p = new Promise((resolve,reject)=>{
        setTimeout(() => {
            resolve('用户数据')
        }, 1000);
    })

    const result = p.then(value=>{
        console.log(value)
        //return 123         //result的状态为成功，值为123
        //throw 'fail'       //result的状态为失败，值为"fail"
        return new Promise((resolve,reject)=>{
            //resolve('ok')  //result的状态为成功，值为"ok"
            reject('error')  //result的状态为失败，值为"error"
        }) 
    },reason=>{
        console.log(reason)
    })

    //链式调用
    p.then(value=>{}).then(value=>{}).then(value=>{})
    
    //catch()用来指定Promise对象失败的回调
    p.catch(function(reason){
        console.error(reason)
    })
</script>
```



## 14.Set集合

```html
<script>
	//Set集合常用API：
    //1) size   返回集合的元素个数
    //2) add    增加一个新元素，返回当前集合
    //3) delete 删除元素，返回 boolean 值
    //4) has    检测集合中是否包含某个元素，返回 boolean 值
    //5) clear  清空集合，返回 undefined


    //声明空set集合
    let set1 = new Set();
    //声明set集合并传入初始参数
    let set2 = new Set(['小智','小刚','小霞','小智']);

    //Set集合会自动去重,Set实现
    for (const e of set2) {
        console.log(e)
    }
</script>
```

set集合实践

```html
<script>
	let arr = [1,2,3,4,5,1,2,3]
    let arr1 = [1,3,5,7,9]
    
    //1.数组去重
    let result = [...new Set(arr)]
    
    //2.交集
    let result1 = [...new Set(arr)].filter(item => {
        let s1 = new Set(arr1)
        if(s1.has(item)){
            return true
        }else{
            return false
        }
    })
    console.log(result1) //[1, 3, 5]
    //简化版
    let result2 = [...new Set(arr)].filter(item => new Set(arr1).has(item))

    //3.并集
    let union = [...new Set([...arr,...arr1])]
    console.log(union) //[1, 2, 3, 4, 5, 7, 9]

    //4.差集
    let diff = [...new Set(arr)].filter(item => !(new Set(arr1).has(item)))
    console.log(diff) //[2, 4]
</script>
```



## 15.Map集合

```html
<script>
    //Map集合常用API：
    // 1) size   返回 Map 的元素个数
    // 2) set    增加一个新元素，返回当前 Map
    // 3) get    返回键名对象的键值
    // 4) has    检测 Map 中是否包含某个元素，返回 boolean 值
    // 5) clear  清空集合，返回 undefined

    //1.声明空map
    let map1 = new Map();
    //2.声明非空map
    let map2 = new Map([
        ['name','张三'],
        ['age','20']
    ]);

    //添加元素
    map2.set('sex','男')
    
    map2.set('hobby',['唱歌','跳舞','篮球'])

    map2.set('sing',function sing(){
        console.log('我会唱歌')
    })

    let school = { name: '浙江大学'}

    map2.set(school,['紫金港校区','玉泉校区','之江校区']) //key可以是对象

    //Map实现了iterator接口,可以使用for of遍历
    for (let e of map2) {
        console.log(e) //e是数组
    }
</script>
```



## 16.class类

ES5和ES6创建对象的方式对比

```html
<script>
    //ES5创建对象
    function Phone(brand,price){
        this.brand = brand;
        this.price = price;
    }

    //添加方法
    Phone.prototype.call = function(){
        console.log(this.brand+'打电话')
    }

    //实例化对象
    let Huawei = new Phone('华为',4999);

    Huawei.call();

    //ES6创建对象
    class Phone_class{
        //构造方法，必须叫这个名
        constructor(brand,price){
            this.brand = brand;
            this.price = price;
        }

        //添加对象方法
        call(){
            console.log(this.brand+'打电话');
        }
    }

    //实例化对象
    let xiaomi = new Phone_class('小米',3999);

    xiaomi.call();
</script>
```

class静态成员

```html
<script>
	class Phone{
        static price = 4399;
    }

    Phone.brand = '小米';
    Phone.prototype.size = '5.5inch';

    let xiaomi = new Phone();
    
    console.log(Phone.brand);  //小米
    console.log(Phone.price);  //4399
    console.log(Phone.size);   //undefined
    console.log(xiaomi.brand); //undefined
    console.log(xiaomi.price); //undefined
    console.log(xiaomi.size);  //5.5inch

    //结论：通过static和直接类名.属性定义的属性属于类，通过prototype定义的属性属于对象
    //注意：static只能在class中使用 无法在function中使用
</script>
```

关于对象的属性权限

```html
<script>
	//public成员
    //1.构造函数的“this”变量用来给对象添加public成员 2.prototype添加的属性也是public
    function Person(name,age){
        this.name = name;
        this.age = age;
    }
    Person.prototype.sex = '男';

    let zs = new Person('张三',20);

    console.log(zs.name); //张三
    console.log(zs.age);  //20
    console.log(zs.sex);  //男

    //private成员
    //private成员由构造函数产生,普通的var变量和构造函数的参数都称为private成员
    function User(name){
        this.username = name;
        var password = '123';
        var telephone = '13956481254'

        this.getPhone = function(){
            //retrun this.password; 不能这样写，返回的是undefined
            return telephone;
        }
    }

    let xm = new User('小明');
    console.log(xm.username);   //小明
    console.log(xm.name);       //undefined
    console.log(xm.password);   //undefined
    console.log(xm.telephone);  //undefined
    console.log(xm.getPhone()); //13956481254

    console.log(User.password); //undefined
    console.log(User.telephone);//undefined
    console.log(User.username); //undefined
</script>
```

ES5和ES6继承类语法

```html
<script>
    //ES5构造函数继承
    //父级构造函数
    function Phone(brand,price){
        this.brand = brand;
        this.price = price;
    }
    Phone.prototype.calltel = function(){
        console.log('我可以打电话');
    }

    //子类构造函数
    function SmartPhone(brand,price,color,size){
        Phone.call(this,brand,price);
        this.color = color;
        this.size = size;
    }

    //设置子类构造函数的原型对象
    SmartPhone.prototype = new Phone;
    SmartPhone.prototype.constructor = SmartPhone;

    SmartPhone.prototype.playGame = function(){
        console.log('我可以玩游戏');
    }

    const xiaomi = new SmartPhone('小米',3999,'绿松蓝','5.5inch');
    
    xiaomi.calltel();
    xiaomi.playGame();

    //ES6继承
    //父类
    class Phone1{
        constructor(brand,price){
            this.brand = brand;
            this.price = price;
        }

        calltel(){
            console.log('我可以打电话');
        }
    }

    //子类
    class SmartPhone1 extends Phone1{
        constructor(brand,price,color,size){
            super(brand,price);
            this.color = color;
            this.size = size;
        }

        playGame(){
            console.log("我可以玩游戏")
        }
    }

    const iqoo = new SmartPhone1('iqoo',3999,'黑色','5.7inch');

    iqoo.calltel();
    iqoo.playGame();
</script>
```

方法重写

```html
<script>
	//父类
    class Phone1{
        constructor(brand,price){
            this.brand = brand;
            this.price = price;
        }

        calltel(){
            console.log('我可以打电话');
        }
    }

    //子类
    class SmartPhone1 extends Phone1{
        constructor(brand,price,color,size){
            super(brand,price);
            this.color = color;
            this.size = size;
        }

        playGame(){
            console.log("我可以玩游戏")
        }
        calltel(){
            console.log("我可以视频通话")
        }
    }

    const iqoo = new SmartPhone1('iqoo',3999,'黑色','5.7inch');

    //iqoo.calltel();//如果子类没有重写calltel()方法，则输出我可以打电话
    iqoo.calltel();//我可以视频通话
    iqoo.playGame();
</script>
```

class中的setter和setter 

```html
<script>
    class Phone{
        get price(){
            console.log('价格属性被读取了');        
        }
        set price(newVale){
            console.log('价格属性被修改了');
        }        
    }

    let phone = new Phone();

    phone.price         //价格属性被读取了
    phone.price = 4999  //价格属性被修改了

</script>
```



## 17.数字扩展

```html
<script>
	//Number.EPSILON:是JavaScript中表示的最小精度
    //2.220446049250313e-16
    function equal(a,b){
        if(Math.abs(a-b) < Number.EPSILON){
            return true;
        }else{
            return false;
        }
    }

    console.log(Number.EPSILON)
    console.log(0.1+0.2 === 0.3);   //false
    console.log(equal(0.1+0.2,0.3));//true

    let b = 0b1010; //二进制
    let o = 0o777;  //八进制
    let x = 0xff;   //十六进制

    console.log(b); //十进制数为10
    console.log(o); //十进制数为511
    console.log(x); //十进制数为255
    
    //判断数值是否为有限数
    console.log(Number.isFinite(100));      //true
    console.log(Number.isFinite(Infinity)); //false
    
    //判断数值是否为NaN
    console.log(Number.isNaN(123)); //false
    console.log(Number.isNaN(NaN)); //true

    //将字符串转换为整数和浮点数
    console.log(Number.parseInt('123abc')); //123
    console.log(Number.parseFloat('3.1a')); //3.1

    //将数字的小数部分抹掉
    console.log(Math.trunc(3.1415926)); //3

    //判断数值为正数,0还是负数。1表示正数，0表示0，-1表示负数
    console.log(Math.sign(100));   //1
    console.log(Math.sign(0));     //0
    console.log(Math.sign(-100));  //-1
</script>
```



## 18.对象方法扩展

```html
	<script>
        //1.Object.is(a,b) 判断两个数值是否完全相等
        console.log(Object.is(NaN,NaN)); //true
        console.log(NaN === NaN);        //false

        //2.object.assign 对象的合并
        const config1 = {
            host: 'localhost',
            username: 'root',
            password: 'root',
            port1: 3306
        }
        const config2 = {
            host: '127.0.0.1',
            username: 'root',
            password: '1234',
            port2: 3307
        }
        console.log(Object.assign(config1,config2))

        /*
        合并结果,如果出现同名属性，后面的对象将会覆盖前面的对象
        {
            "host": "127.0.0.1",
            "username": "root",
            "password": "1234",
            "port1": 3306,
            "port2": 3307
        }
        */

        //3.Object.setPrototypeOf(a,b) 将b对象设置为a的原型对象
        const school = {
            name: '浙江中医药大学'
        }
        const area = {
            xiaoqu: ['滨江','富春']
        }
        Object.setPrototypeOf(school,area);
        //4.Object.getPrototypeOf(a)获取a的原型对象
        console.log(Object.getPrototypeOf(school));
        console.log(school);

	</script>
```



## 19.模块化

模块化的优势有以下几点：

- 防止命名冲突
- 代码复用 
- 高维护性



export 暴露语法

- 分别暴露 （module1.js）

```js
//分别暴露
export let school = '浙江大学'

export function fn(){
    console.log('我是一个函数');
}
```

- 统一暴露 （module2.js）

```js
let name = 'zs'

function sayHi(){
    console.log('hello');
}

//同一暴露
export{name,sayHi}
```

- 默认暴露 （module3.js）

```js
export default{
    username: 'root',
    password: '1234',
    login: function(){
        console.log('登陆中...');
    }
}
```





import 引入语法的三种方式

```js
<script type="module">
    //1.通用的引入方式
    import * as m1 from './module1.js';
    import * as m2 from './module2.js';
    import * as m3 from './module3.js';
    console.log(m1);
    console.log(m2);
    console.log(m3);
    //m3.login() 对于这种引入方式,default暴露的方法无法直接使用
    m3.default.login() //正确方法（其他两种可以正常调用）
    
    //2.解构赋值形式(可以使用as重新命名)
    import {name as username,sayHi} from './module2.js';
    console.log("name = "+username+',sayHi = '+sayHi);
    import {default as m33} from './module3.js';
    m33.login();

    //3.简便形式（只针对默认暴露）
    import m333 from './module3.js';
    console.log(m333);
    m333.login();
</script>
```

## 20.ES6转码

1.安装工具 babel-cli  、babel-preset-env 、browserify （或webpack）

2.初始化 npm init --yes 

3.安装 npm i babel-cli babel-preset-env  -D

4.转化代码 npx babel model -d dist/js --presets-babel-preset-env

5.打包 npx browserify  dist/js/app.js -o dist/bundle.js

# ES7新特性

```html
<script>
    //ES7新特性
    //1.Array.prototype.includes,includes方法用来检测数组中是否包含某个元素，返回布尔类型值
    const arr = ['西游记','红楼梦','三国演义','水浒传'];
    console.log(arr.includes('三国演义')); //true
    console.log(arr.includes('聊斋志异')); //false

    //2.指数操作符,在 ES7 中引入指数运算符「**」，用来实现幂运算，功能与 Math.pow 结果相同
    console.log(10**2);             //100
    console.log(Math.pow(10,2));    //100
</script>
```



# ES8新特性

## 1.async函数

```html
<script>
    //async函数,返回结果为Promise对象
    async function fn(){
        //return '123';//如果返回的不是Promise对象则函数返回的Promise对象状态为成功
        //throw new Error('出错啦!');//如果抛出错误则函数返回的Promise对象状态为失败
        return new Promise((resolve,reject)=>{
            //如果返回的是Promise对象，则函数返回的Promise对象状态由被返回的Promise对象决定
            resolve('成功'); 
            //reject('失败');
        })
    }
    const result = fn();
    console.log(result);
</script>
```



## 2.await表达式

```html
<script>
    // 4.1.2.await 表达式
    // 1. await 必须写在 async 函数中
    // 2. await 右侧的表达式一般为 promise 对象
    // 3. await 返回的是 promise 成功的值
    // 4. await 的 promise 失败了, 就会抛出异常, 需要通过 try...catch 捕获处理

    const p = new Promise((resolve,reject)=>{
        //resolve('成功');
        reject('失败');
    });

    async function fn(){
        // let result = await p;
        // console.log(result); 成功

        try {
            let result = await p;
        } catch (error) {
            console.log(error) //失败
        }
    }

    fn();
</script>
```

async和await结合读取文件

```js
//1. 引入 fs 模块
const fs = require("fs");

//读取『为学』
function readWeiXue() {
    return new Promise((resolve, reject) => {
        fs.readFile("./resources/为学.md", (err, data) => {
            //如果失败
            if (err) reject(err);
            //如果成功
            resolve(data);
        })
    })
}

function readChaYangShi() {
    return new Promise((resolve, reject) => {
        fs.readFile("./resources/插秧诗.md", (err, data) => {
            //如果失败
            if (err) reject(err);
            //如果成功
            resolve(data);
        })
    })
}

function readGuanShu() {
    return new Promise((resolve, reject) => {
        fs.readFile("./resources/观书有感.md", (err, data) => {
            //如果失败
            if (err) reject(err);
            //如果成功
            resolve(data);
        })
    })
}

//声明一个 async 函数
async function main(){
    //获取为学内容
    let weixue = await readWeiXue();
    //获取插秧诗内容
    let chayang = await readChaYangShi();
    // 获取观书有感
    let guanshu = await readGuanShu();

    console.log(weixue.toString());
    console.log(chayang.toString());
    console.log(guanshu.toString());
}

main();
```

## 3.对象方法扩展

```html
<script>
    //声明对象
    const school = {
        name:"尚硅谷",
        cities:['北京','上海','深圳'],
        xueke: ['前端','Java','大数据','运维']
    };

    //获取对象所有的键
    console.log(Object.keys(school));
    //获取对象所有的值
    console.log(Object.values(school));
    //entries
    console.log(Object.entries(school));
    //创建 Map
    const m = new Map(Object.entries(school));
    console.log(m.get('cities'));

    //对象属性的描述对象
    console.log(Object.getOwnPropertyDescriptors(school));

    const obj = Object.create(null, {
        name: {
            //设置值
            value: '尚硅谷',
            //属性特性
            writable: true,
            configurable: true,
            enumerable: true
        } 
    });
</script>
```



# ES9新特性

```html
	<!-- 
        Rest 参数与 spread 扩展运算符在 ES6 中已经引入，不过 ES6 中只针对于数组，
        在 ES9 中为对象提供了像数组一样的 rest 参数和扩展运算符
     -->
    <script>
        //rest 参数
        function connect({host, port, ...user}){
            console.log(host);
            console.log(port);
            console.log(user);
        }

        connect({
            host: '127.0.0.1',
            port: 3306,
            username: 'root',
            password: 'root',
            type: 'master'
        });

        //对象合并
        const skillOne = {
            q: '天音波'
        }
        const skillTwo = {
            w: '金钟罩'
        }
        const skillThree = {
            e: '天雷破'
        }
        const skillFour = {
            r: '猛龙摆尾'
        }

        const mangseng = {...skillOne, ...skillTwo, ...skillThree, ...skillFour};
        console.log(mangseng)

        // ...skillOne   =>  q: '天音波', w: '金钟罩'
    </script>
```

# ES10新特性

## 1.Object.fromEntries 

```html
	<script>
        //二维数组
        const result = Object.fromEntries([
            ['name','尚硅谷'],
            ['xueke', 'Java,大数据,前端,云计算']
        ]);

        //Map
        const m = new Map();
        m.set('name','ATGUIGU');
        const result = Object.fromEntries(m);

        //Object.entries ES8
        const arr = Object.entries({
            name: "尚硅谷"
        })
        console.log(arr);
    </script>
```

## 2.trimStart 和 trimEnd 

```html
    <script>    
        let str = '   iloveyou   ';
        console.log(str);              //iloveyou后面无空格
        console.log(str.trimStart());  //iloveyou后面有空格
        console.log(str.trimEnd());	   //   iloveyou
    </script>
```



## 3.Array.prototype.flat 与 flatMap 

```html
    <script>
        //flat 平
        //将多维数组转化为低位数组
        // const arr = [1,2,3,4,[5,6]];
        const arr = [1,2,3,4,[5,6,[7,8,9]]];
        //参数为深度 是一个数字
        console.log(arr.flat(2));

        //flatMap
        const arr = [1,2,3,4];
        const result = arr.flatMap(item => [item * 10]);
        console.log(result);
    </script>
```



## 4.Symbol.prototype.description

```html
    <script>
        //创建 Symbol
        let s = Symbol('尚硅谷');
        console.log(s.description);
    </script>
```

# ES11新特性

## 1.私有属性

```html
    <script>
        class Person{
            //公有属性
            name;
            //私有属性
            #age;
            #weight;
            //构造方法
            constructor(name, age, weight){
                this.name = name;
                this.#age = age;
                this.#weight = weight;
            }

            intro(){
                console.log(this.name);
                console.log(this.#age);
                console.log(this.#weight);
            }
        }

        //实例化
        const girl = new Person('晓红', 18, '45kg');
        console.log(girl.name);
        console.log(girl.#age); //无法访问
        console.log(girl.#weight); //无法访问
        girl.intro();
    </script>
```

## 2.可选链操作符

```html
	<script>
        // 可选链操作符:  ?.
        function main(config){
            // const dbHost = config && config.db && config.db.host;
            const dbHost = config?.db?.host;
            console.log(dbHost);
        }

        main({
            db: {
                host:'192.168.1.100',
                username: 'root'
            },
            cache: {
                host: '192.168.1.200',
                username:'admin'
            }
        })
    </script>
```

## 3.BigInt

JS 中的`Number`类型只能安全地表示`-9007199254740991 (-(2^53-1))` 和`9007199254740991(2^53-1)`之间的整数，任何超出此范围的整数值都可能失去精度

```html
    <script>
        //大整形
        // let n = 521n;
        // console.log(n, typeof(n));

        //函数
        // let n = 123;
        // console.log(BigInt(n));
        // console.log(BigInt(1.2));

        //大数值运算
        let max = Number.MAX_SAFE_INTEGER;
        console.log(max);
        console.log(max + 1);
        console.log(max + 2);

        console.log(BigInt(max))
        console.log(BigInt(max) + BigInt(1))
        console.log(BigInt(max) + BigInt(2))
    </script>
```

## 4.globalThis

全局属性 `globalThis` 包含全局的 `this` 值，类似于全局对象（global object）

```html
    <script>
        console.log(globalThis); //Window
    </script>
```

## 5.动态import

```html
<script type="module">
    const btn = document.getElementById("btn");

    btn.onclick = function(){
        import("./m1.js").then(module => {
            module.study();
        });
    };
</script>
```