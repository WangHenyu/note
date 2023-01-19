# 1.Vue2基础

Vue官网：https://cn.vuejs.org/

## 1.1.初始Vue

```html
<head>
    <meta charset="UTF-8">
    <title>初始Vue</title>
    <!-- 引入Vue.js -->
    <script type="text/javascript" src="../js/vue.js"></script>
</head>
<body>
    <!-- 准备好容器 -->
    <div id="root">
        <h1>hello {{name}}</h1>
    </div>
    
    <script type="text/javascript">
        //阻止Vue在启动时生成生产提示
        Vue.config.productionTip = false

        //创建Vue实例 参数是配置对象
        new Vue({
            el: '#root', //element用于指定当前Vue实例为哪个容器服务
            data: {name: 'Vue'}, //存储数据，提供给el指定容器使用
        })
    </script>
</body>
```

> 总结：
>
> ​      1.想让Vue工作必须先创建Vue实例，并且传入配置对象
>
> ​      2.容器中的代码依旧符合html规范
>
> ​      3.Vue实例和容器只能一一对应
>
> ​      4.{{xxx}}中的xxx要写js表达式，并且xxx可以自动读取data中的所有属性

<h4>抽取代码片段</h4>

> 文件 => 首选项 => 用户代码片段 => 新建全局代码片段/或文件夹代码片段：vue-html.code-snippets
>
> 注意：制作代码片段的时候,字符串中如果包含文件中复制过来的“Tab”键的空格,要换成“空格 键”的空格

~~~js
{
  "vue htm": {
    "scope": "html",
    "prefix": "vuehtml",
    "body": [
      "<!DOCTYPE html>",
      "<html lang=\"en\">",
      "",
      "<head>",
      "代码省略了 你可以看自己需求加代码"
      "</head>",
      "",
      "<body>",
      "代码省略了 你可以看自己需求加代码"
      "</body>",
      "",
      "</html>",
    ],
    "description": "my vue template in html"
  }
}
~~~



## 1.2.模板语法

```html
<body>
    <div id="root">
        <h1>你好，{{name}}</h1>
        <hr>
        <a v-bind:href="web.url">点我跳转到{{web.name}}</a>
        <!-- v-bind:可以简写为: --> 
        <a :href="web.url.toUpperCase()">点我跳转到{{web.name}}</a>
    </div>

    <script>
        Vue.config.productionTip = false

        new Vue({
            el: '#root',
            data: {
                name: 'jack',
                web:{
                    name: '百度',
                    url: 'http://www.baidu.com'
                }
                
            }
        })
    </script>
</body>
```

> 总结：
>
> ​	Vue模板语法分为两大类：
>
> ​		1.插值语法：{{xxx}} xxx为js表达式
>
> ​		2.指令语法：v-xxx  xxx为js表达式

## 1.3.数据绑定

```html
<body>
    <div id="root">
        单向数据绑定：<input type="text" v-bind:value="name">
        双向数据绑定：<input type="text" v-model:value="name">
        <!-- v-model只能用于表单类元素了（都有value属性） -->
        <!-- 简写 -->
        单向数据绑定：<input type="text" :value="name">
        双向数据绑定：<input type="text" v-model="name">
    </div>

    <script>
        Vue.config.productionTip = false

        new Vue({
            el: '#root',
            data: {
                name:'单向绑定'
            }
        })
    </script>
</body>
```

> 总结：
>
> Vue中有两种数据绑定的方式：
>
> ​	1.单向绑定（v-bind）：数据只能从data流向页面
>
> ​    2.双向绑定（v-model）：数据不仅能从data流向页面也能从页面流向data
>
> ​		v-bind:属性=""  —简写&rarr;  :属性=""
>
> ​		v-model:value=""  —简写&rarr;  v-model="" 



## 1.4.MVVM模型

![image-20220703142022805](C:\Users\uuu\AppData\Roaming\Typora\typora-user-images\image-20220703142022805.png)  

> MVVM模型：
>
> ​    M（模型Model）：对应data中的数据
>
> ​    V（试图View）：模板
>
> ​    VM（视图模型ViewModel）：Vue实例对象
>
>   总结:
>
> ​    data中的所有属性最后都出现在vm实例对象上
>
> ​    vm实例上的所有属性及Vue原型上所有属性都可以在Vue模板中直接使用

  ## 1.5.el和data的两种写法

<h3>el的两种写法</h3>

```html
<!--第一种写法-->
<script>
    Vue.config.productionTip = false

    const vm = new Vue({
        el: '#root', 
        data: {}
    })
</script>

<!--第二种写法-->
<script>
    Vue.config.productionTip = false

    const vm = new Vue({
        data: {}
    })
    vm.$mount("#root")
</script>
```

<h3>data的两种写法</h3>

```html
<!-- 第一种写法：对象式 -->
<script>
    Vue.config.productionTip = false

    const v = new Vue({
        el: '#root', 
        data: {
            name: 'jack'
        }
        
    })
</script>

<!-- 第二种写法：函数式 -->
<script>
    Vue.config.productionTip = false

    const v = new Vue({
        el: '#root',
        data: function(){    //简写形式-> data(){...}
            return {
                name: 'jack'
            }
        }
    })
</script>
```

> 总结：
>
>   el的两种写法：
>
> ​	1.new Vue的时候配置el属性
>
> ​    2.先创建Vue实例,然后再通过```vm.$mount("选择器")```指定容器
>
>   data的两种写法：
>
> ​    1.对象式
>
> ​    2.函数式
>
>   关于data函数式写法的重要原则：
>
> ​    由Vue管理的函数只能写普通函数,不能使用箭头函数
>
> ​    一旦写了箭头函数,this就不再指向Vue实例

## 1.6.回顾defineProperty方法

```html
<body>
    <script type="text/javascript">
        let num = 18

        let person = {
            name: "张三",
            sex: "男",
            // age: num 无法动态的修改age属性的值
        }

        //给person对象添加age属性
        Object.defineProperty(person,"age",{
            // value: 18,
            // writable:true,//控制属性是否可以被修改
            // enumerable: true,//控制属性是否可以枚举
            // configurable:true,//控制属性是否可以被删除
            get(){ //当读取person的age属性时自动调用
                return num;
            },
            set(value){ //当修改person的age属性时自动调用
                num = value
            }
            //get()和set()不能与value和writable属性共存
        })
    </script>
</body>
```

折叠代码

```
//#region
//.....
//#endregion
```

## 1.7.数据代理

```html
<script type="text/javascript">
    
    let obj = {x:100}
    let obj2 = {y:200}

    Object.defineProperty(obj2,"x",{
        get(){
            return obj.x
        },
        set(value){
            obj.x = value
        }
    })
</script>
```

![image-20220701195704663](C:\Users\uuu\AppData\Roaming\Typora\typora-user-images\image-20220701195704663.png)

在代理对象上通过```Object.defineProperty() ```添加被代理对象的属性

通过obj2对象可以操作obj对象中的x属性,这就是数据代理

## 1.8.Vue中的数据代理

```html
<script>
    Vue.config.productionTip = false

    let data = {
            name: "张三",
            age: "20"
        }

    const vm = new Vue({
        el: '#root',
        data
    })

</script>
```

验证vm中的_data就是配置对象中的data

![image-20220701202644640](C:\Users\uuu\AppData\Roaming\Typora\typora-user-images\image-20220701202644640.png)

vm上添加的getter/setter

![image-20220701202800840](C:\Users\uuu\AppData\Roaming\Typora\typora-user-images\image-20220701202800840.png)

如何实现数据代理?

​	以name属性为例,当访问vm.name时会调用name属性的getter方法返回data中的name值

​	当修改vm.name时会调用name属性的setter方法修改data中的name属性值

![image-20220701203316688](C:\Users\uuu\AppData\Roaming\Typora\typora-user-images\image-20220701203316688.png)

总结：

​	通过vm对象代理data对象的中属性

​	原理：

​		通过```Object.defineProperty()``` 把data对象的所有属性添加到vm上 

​	 （data相当于上例中的obj对象 vm相当于上例中的obj2对象）

​		为每个添加到vm对象上的属性提供getter 和 setter 方法

​		在getter/setter方法内部操作data中对应的属性

![image-20220701205851011](C:\Users\uuu\AppData\Roaming\Typora\typora-user-images\image-20220701205851011.png)

## 1.9.事件处理

```html
<body>
    <div id="root">
        <button v-on:click="showInfo">点我显示信息</button>
        <!-- 简写形式 -->
        <button @click="showInfo">点我显示信息</button>
        <!-- 传递参数可以直接在函数名后加()
             注意：传递参数时，默认不传递event对象
             如果需要传递event对象，必须使用$event传递 -->
        <button @click="showInfo2($event,66)">点我显示信息（传参）</button>
    </div>

    <script>
        Vue.config.productionTip = false

        new Vue({
            el: '#root',
            data: {},
            methods: {
                showInfo(event){
                	//默认传递event事件对象
                    console.log(event);//事件对象
                    console.log(this);//此处的this指向vm对象
                },
                showInfo2(event,number){
                    console.log(event,number);
                }
            }
        })
    </script>
</body>
```

事件的基本使用：

​    1.使用```v-on:xxx```或```@xxx绑定事件```,xxx是事件名

​    2.事件的回调函数需要配置在methods对象中,最终会出现在vm上

​    3.methods中配置的函数不要使用箭头函数

​    4.传递参数时,默认不传递event对象,如果需要传递event对象,必须使用$event传递

<h3>事件修饰符</h3>

| 事件修饰符 | 说明           |
| ---------- | -------------- |
| prevent    | 阻止默认事件   |
| stop       | 阻止事件冒泡   |
| once       | 事件只触发一次 |
|capture |使用事件的捕获模式|
|self | 只有event.target是当前操作元素时才触发事件|
|passive|事件的默认行为立即执行 无需等待回调函数执行完毕|

```html
<body>
    <div id="root">
        <a href="http://www.baidu.com" @click.prevent="show">百度</a>
        <!-- 事件修饰符可以连着写，如 -->
        <a href="http://www.baidu.com" @click.prevent.once="show">百度</a>
    </div>

    <script>
        Vue.config.productionTip = false

        new Vue({
            el: '#root',
            data: {},
            methods: {
                show(e){
                    //阻止默认行为,效果同 @click.prevent
                    //e.preventDefault()
                    alert('abc')
                }
            }
        })
    </script>
</body>
```

基础知识补充

```
css：
	overflow: auto; 空间不够显示滚动条
事件：
	<ul @scroll="函数名"></ul>
	<ul @wheel="函数名"></ul>
	
	scroll: 滚动条滚动时触发事件
	wheel: 鼠标滚轮滚动时触发事件 
```

<h3>键盘事件</h3>

Vue中常用的按键别名

| 按键        | 别名                     |
| ----------- | ------------------------ |
| 回车        | enter                    |
| 删除        | delete                   |
| 退出        | esc                      |
| 空格        | space                    |
| 换行        | tab                      |
| 上/下/左/右 | up / down / left / right |

特殊的键：

​	Tab只能配合keydown使用

​	系统修饰键：ctrl、alt、shift、meta(win)

​		配合keyup使用：按下修饰键的同时按下其他键,然后释放其他键,事件才能被触发

​		配合keydown使用：正常触发事件

```html
<body>
    <div id="root">
        <input type="text" placeholder="按下键盘显示提示" @keydown.enter="showTip">
        <!-- 特殊别名（一般是复合别名）
				@keydown.CapsLock  错误
				@keydown.caps-lock 正确
		-->
        <input type="text" placeholder="按下键盘显示提示" @keydown.CapsLock="showTip">
        <input type="text" placeholder="按下键盘显示提示" @keydown.caps-lock="showTip">
        <!--系统修饰键盘可以连着写-->
        <input type="text" placeholder="按下Ctrl+Y显示提示" @keyup.ctrl.y="showTip">
		<input type="text" placeholder="按下x或y显示提示" @keydown.x.y="showTip">

    </div>

    <script>
        Vue.config.productionTip = false

        new Vue({
            el: '#root',
            data: {},
            methods: {
                showTip(event){
                    console.log(event.key,event.keyCode);
                }
            }
        })

    </script>
</body>
```



在Vue中也可以使用keyCode去指定具体的按键（不推荐）

```html
<input type="text" placeholder="按下键盘显示提示" @keydown.13="showTip">
```

键盘上各个键对应的keyCode（了解）

| key  | keycode |
| ---- | ------- |
| Escape | 27 |
| CapsLock | 20|
| Shift | 16|
| Control | 17|
| Delete | 46|
| Backspace|  8|
| 空格 | 32 |
|Tab|9|
| Enter（回车） | 13|
| 左/上/右/下 | 37/38/39/40 |
| a/b/c/d/... | 65/66/67/68/... |
|0/1/2/3/4/...|48/49/50/51/52/...|

自定义别名按键（了解）

```html
<body>
    <div id="root">
        <input type="text" placeholder="按下键盘显示提示" @keydown.huiche="showTip">
    </div>

    <script>
        Vue.config.productionTip = false
        //自定义回车按键的别名
        Vue.config.keyCodes.huiche = 13
        new Vue({
            el: '#root',
            data: {},
            methods: {
                showTip(event){
                    console.log(event.key,event.keyCode);
                }
            }
        })
    </script>
</body>
```

## 1.10.计算属性

姓名案例：当输入框中的内容发生变化时,姓名也会发生相应的变化

![image-20220702160753553](C:\Users\uuu\AppData\Roaming\Typora\typora-user-images\image-20220702160753553.png)

```html
<!--插值语法实现-->
<body>
    <div id="root">
        姓：<input type="text" v-model="firstName"><br>
        名: <input type="text" v-model="lastName"><br>
        姓名：<span>{{firstName}}-{{lastName}}</span>
    </div>

    <script>
        Vue.config.productionTip = false

        new Vue({
            el: '#root',
            data: {
                firstName: '张',
                lastName: '三'
            }
        })
    </script>
</body>
```

使用插值语法虽然可以实现这个案例,但是当需求发生变化时,{{xxx}}中的表达式可能会变得很复杂,代码可读性差

```html
<!--methods方法实现-->
<body>
    <div id="root">
        姓：<input type="text" v-model="firstName"><br><br>
        名：<input type="text" v-model="lastName"><br><br>
        姓名：<span>{{fullName()}}</span>
    </div>

    <script>
        Vue.config.productionTip = false

        new Vue({
            el: '#root',
            data: {
                firstName: '张',
                lastName: '三'
            },
            methods: {
                fullName(){
                    //这里可以对firstName/lastName操作
                    return this.firstName+'-'+this.lastName
                }
            }
        })
    </script>
</body>
```

上面这种方式效率较低

```html
<!-- 计算属性实现 -->
<body>
    <div id="root">
        姓：<input type="text" v-model="firstName"><br><br>
        名：<input type="text" v-model="lastName"><br><br>
        姓名：<span>{{fullName}}</span>
    </div>

    <script>
        Vue.config.productionTip = false

        const vm = new Vue({
            el: '#root',
            data: {
                firstName: '张',
                lastName: '三'
            },
            //计算属性配置项
           computed:{
                //写在data中的是属性    
                //写在computed中的是计算属性
                fullName:{ //fullName就是计算属性
                    get(){
                        //什么时候调用
                        //1.初次读取fullName时
                        //2.所依赖的数据发生变化时
                        return this.firstName+'-'+this.lastName
                    },
                    set(value){
                        //set不是必须的
                        //fullName发生变化时调用
                        const arr = value.split('-')
                        this.firstName = arr[0]
                        this.lastName = arr[1]
                    }
                }
           }
        })
    </script>
</body>
```

<h3>计算属性的简写形式</h3>

如果计算属性<font color = 'red'>只读不改</font>才可以使用简写形式

```js
computed:{
	fullName(){
		return this.firstName+'-'+this.lastName
	}
}
```

总结：

​	计算属性的定义：要用的属性不存在,要通过已有的属性计算得来

​	计算属性最终会出现在vm身上,可直接使用	

​	相比于methods方法实现,这种方式内部有缓存机制,效率更高



## 1.11.监视属性（侦听属性）

天气案例：点击按钮切换天气（将炎热改为凉爽）

![image-20220702195705224](C:\Users\uuu\AppData\Roaming\Typora\typora-user-images\image-20220702195705224.png)

```html
<body>
    <div id="root">
        <h1>今天天气很{{weather}}</h1>
        <button @click="changeWeather">点击切换天气</button>
    </div>

    <script>
        Vue.config.productionTip = false

        const vm = new Vue({
            el: '#root',
            data: {
                isHot: true
            },
            computed: {
                weather(){
                    return this.isHot ? '炎热' : '凉爽'
                }
            },
            methods: {
                changeWeather(){
                    this.isHot = !this.isHot
                }
            },
            //监视属性的配置项
            watch: {
                //key为要监视的属性
                isHot:{
                    //初始化时是否执行handler函数
                    immediate: false,
                    //handler函数可以接收被监视属性被修改前后的值
                    handler(newValue,oldValue){
                        console.log('天气被修改了',newValue,oldValue);
                    }
                }
            }
        })
    </script>
</body>
```

监视属性的另外一种写法（创建好vm对象后,使用$watch()函数添加监视属性）

这种方式适合在创建vue实例对象时不明确要监视哪个属性

```js
vm.$watch('isHot',{
     immediate: false,
     handler(newValue,oldValue){
        console.log('天气被修改了',newValue,oldValue);
     }
})
```

<h3>监视属性的简写形式</h3>

当监视属性<font color='red'>只配置handler属性</font>>时才能使用简写形式

```js
watch: {
    isHot (newValue,oldValue){
        console.log('天气被修改了',newValue,oldValue);
    }
}
//另一种简写形式
vm.$watch('isHot',function(newValue,oldValue){
    console.log('天气被修改了',newValue,oldValue);
})
```

<h3>深度监视</h3>

案例：点击按钮数值加1,并在控制台中打印变化的值

![image-20220702203401682](C:\Users\uuu\AppData\Roaming\Typora\typora-user-images\image-20220702203401682.png)

```html
<body>
    <div id="root">
        <h1>a的值是{{numbers.a}}</h1>
        <h1>b的值是{{numbers.b}}</h1>
        <button @click="numbers.a++">点击a+1</button>
        <button @click="numbers.b++">点击b+1</button>
    </div>

    <script>
        Vue.config.productionTip = false

        const vm = new Vue({
            el: '#root',
            data: {
                numbers: {
                    a:10,
                    b:20
                }
            },
            watch: {
                //a: { 当监视的是多级结构中的属性,这样写就无法监视到
                'numbers.a':{ //正确写法
                    handler(newValue,oldValue){
                        console.log('a的值变化了',newValue,oldValue);
                    }
                },
                numbers:{
                    deep: true, //开启深度监视,用于监视多级结构中的属性
                    handler(){
                        console.log('numbers中的值变化了');
                    }
                }
            }
        })

    </script>
</body>
```

总结：
        1.Vue中的watch默认不监视对象内部的属性值的改变
        2.配置deep:true可以监视到对象内部属性的改变

<h3>计算属性VS监视属性</h3>

姓名案例watch实现

```html
<!-- watch实现 -->
<body>
    <div id="root">
        姓：<input type="text" v-model="firstName"><br><br>
        名：<input type="text" v-model="lastName"><br><br>
        姓名：<span>{{fullName}}</span>
    </div>

    <script>
        Vue.config.productionTip = false

        const vm = new Vue({
            el: '#root',
            data: {
                firstName: '张',
                lastName: '三',
                fullName:'张-三'
            },
            watch:{
                firstName(newVal){
                    this.fullName = newVal +'-'+ this.lastName
                },
                lastName(newVal){
                    this.fullName = this.firstName +'-'+ newVal
                }
            }
        })
    </script>
</body>
```

新需求：在修改完姓之后3秒页面的全名再发生变化

```js
//这种写法是错误的	
computed:{
	fullName(){
		setTimeOut(()=>{
			return this.firstName+'-'+this.lastName
		},3000)
	}
}
//这种写法是正确的	
watch:{
    firstName(newVal){
        setTimeOut(()=>{
			this.fullName = newVal +'-'+ this.lastName
		},3000)
    },
    lastName(newVal){
        this.fullName = this.firstName +'-'+ newVal
    }
}

```

总结：

​	1.computed能完成的功能 使用watch也都能完成

​	2.watch能完成的功能 computed不一定能完成  例如watch可以进行异步操作

​	3.计算属性中不能开启异步任务去维护数据

​	4.两个重要的小原则：

​		4.1所有被Vue管理的函数最好写成普通函数

​		4.2所有不被Vue管理的函数最好写成箭头函数（定时器的回调、ajax的回调、Promise的回调）

## 1.12.绑定样式

css样式绑定

```html
<!--css代码省略-->
<body>
    <div id="root">
        绑定样式：:class="xxx" xxx可以是字符串、数组、对象
        <!-- 字符串写法,适用于样式类名不确定,需要动态指定 -->
        <div class="basic" :class="classStr" @click="changeClass"></div>
        <!-- 数组写法,适用于样式个数不确定、样式名称也不确定 -->
        <div class="basic" :class="classArr"></div>
        <!-- 对象写法,适用于样式个数确定、样式名称也确定,但动态决定用不用 -->
        <div class="basic" :class="classObj"></div>
    </div>

    <script>
        Vue.config.productionTip = false

        const vm = new Vue({
            el: '#root',
            data: {
                classStr: 'style1',
                classArr: ['style1','style2','style3'],
                classObj:{
                    style1: true,  //使用style1样式
                    style2: true,
                    style3: false  //不使用style3样式
                }
            },
            methods: {
                changeClass(){
                    this.classStr = 'style2'
                }
            }
        })
    </script>
</body>
```

style样式绑定

```html
<body>
    <div id="root">
        <div :style="styleObj">绑定样式</div>
    </div>

    <script>
        Vue.config.productionTip = false

        const vm = new Vue({
            el: '#root',
            data: {
                styleObj: {
                    fontSize: '40px',
                    color: 'red'
                }
            }
        })
    </script>
</body>
```

## 1.13.条件渲染

```html
<body>
    <div id="root">
        <div v-show="isShow">{{info}}</div>  <!-- <div style="display: none;"> -->
        
        <!-- 注意：如果使用v-if和v-else配合使用时不允许被打断 -->
        <div v-if="num === 1">Vue</div>
        <div v-else-if="num === 2">React</div>
        <div v-else>Angular</div>

        <!-- template标签：
                template标签只能配合v-if使用,不能配合v-show
                template不会破坏结构-->
        <template v-if="isShow">
            <h1>Vue</h1>
            <h1>React</h1>             
        </template>
    </div>

    <script>
        Vue.config.productionTip = false

        const vm = new Vue({
            el: '#root',
            data: {
                info: 'hello vue',
                isShow: true,
                num: 0
            }
        })
    </script>
</body>
```

总结：
    1.v-if
        写法：```v-if="布尔表达式"```
        适用于切换频率较低的场景
        不展示的DOM结点会被删除
        配合v-else使用时要求结构不能被打断
    2.v-show
        写法：```v-show="布尔表达式"```
        适用于切换频率较高的场景
        不展示的DOM结点不会被移除,仅仅使用样式隐藏

## 1.14.列表渲染

```html
<body>
    <div id="root">
        <ul>
            <!-- 遍历数组 -->
            <li v-for="p in persons" :key="p.id">{{p.name}}-{{p.age}}</li>
            <li v-for="(p,index) in persons" :key="index">{{p}}-{{index}}</li>
            <li v-for="p of persons" :key="p.id">{{p.name}}-{{p.age}}</li>
            <!-- 遍历对象 -->
            <li v-for="(value,key) in persons[0]" :key="key">{{key}}-{{value}}</li>
            <!-- 遍历字符串 -->
            <li v-for="(char,index) in str" :key="index">{{char}}-{{index}}</li>
            <!-- 遍历指定次数: number从1开始,index从0开始-->
            <li v-for="(number,index) in 5" :key="index">{{number}}-{{index}}</li>
        </ul>
    </div>

    <script>
        Vue.config.productionTip = false

        const vm = new Vue({
            el: '#root',
            data: {
                persons: [
                    {id:'001',name:'张三',age:18},
                    {id:'002',name:'李四',age:19},
                    {id:'003',name:'王五',age:20},
                ],
                str: 'hello'
            }
        })
    </script>
</body>
```

index作为key时diff算法过程

![image-20220703135924560](C:\Users\uuu\AppData\Roaming\Typora\typora-user-images\image-20220703135924560.png)

id作为key时diff算法过程

![image-20220703140000034](C:\Users\uuu\AppData\Roaming\Typora\typora-user-images\image-20220703140000034.png)

key的工作原理总结：

​	1.key是虚拟DOM对象的标识

​	2.当状态中的数据发生变化时,Vue会根据新数据生成新的虚拟DOM,再根据diff算法转换成真实DOM

​	3.对比规则：

​		3.1旧虚拟DOM中找到了与新虚拟DOM相同的key

​				a.若虚拟DOM中的内容没有改变,直接使用之前生成的真实DOM

​				b.若虚拟DOM中的内容发生改变,则生成新的真实DOM,替换掉之前的真实DOM

​		3.2旧虚拟DOM中未找到与新虚拟DOM相同的key

​				创建新的真实DOM,然后渲染到页面

用index作为key可能会引发的问题

​	若对数据进行逆序添加、删除等破坏顺序操作时

​		1.如果结构中没有包含输入类DOM,会产生没必要的真实DOM更新

​		2.如果结构中包含输入类DOM,会产生错误DOM更新

如何选择key

​	1.最好使用数据的唯一标识作为key

​	2.如果不存在破坏顺序的操作,仅用于渲染列表用于展示,那么可以使用index作为key

## 1.15.列表过滤

案例：在输入框中输入关键字实现模糊搜索

![image-20220703154021026](C:\Users\uuu\AppData\Roaming\Typora\typora-user-images\image-20220703154021026.png)

监视属性实现

```html
<body>
    <div id="root">
        <input type="text" placeholder="请输入关键字" v-model="keyWord"/>
        <ul>
            <li v-for="(p,index) in filterPersons" :key="p.id">{{p.name}}-{{p.age}}-{{p.sex}}</li>
        </ul>
    </div>

    <script>
        Vue.config.productionTip = false

        const vm = new Vue({
            el: '#root',
            data: {
                keyWord: '',
                persons: [
                    {id:'001',name:'马冬梅',age:18,sex:'女'},
                    {id:'002',name:'周冬雨',age:19,sex:'女'},
                    {id:'003',name:'周杰伦',age:20,sex:'男'},
                    {id:'004',name:'温兆伦',age:20,sex:'男'},
                ],
                filterPersons: []
            },
            watch: {
                keyWord: {
                    immediate: true,
                    handler(newValue){
                        this.filterPersons = this.persons.filter((p)=>{
                        return p.name.indexOf(newValue) !== -1
                    })
                    }
                }
            }
        })
    </script>
</body>
```

计算属性实现

```html
<body>
    <div id="root">
        <input type="text" placeholder="请输入关键字" v-model="keyWord"/>
        <ul>
            <li v-for="(p,index) in filterPersons" :key="index">{{p.name}}-{{p.age}}-{{p.sex}}</li>
        </ul>
    </div>

    <script>
        Vue.config.productionTip = false

        const vm = new Vue({
            el: '#root',
            data: {
                keyWord: '',
                persons: [
                    {id:'001',name:'马冬梅',age:18,sex:'女'},
                    {id:'002',name:'周冬雨',age:19,sex:'女'},
                    {id:'003',name:'周杰伦',age:20,sex:'男'},
                    {id:'003',name:'温兆伦',age:20,sex:'男'},
                ],
            },
            computed:{
                filterPersons(){
                    var arr = this.persons.filter((p)=>{
                        return p.name.indexOf(this.keyWord) !== -1
                    })
                    return arr
                }
            }
        })
    </script>
</body>
```

需求升级：为搜索的到的数据添加排序功能

![image-20220703161728095](C:\Users\uuu\AppData\Roaming\Typora\typora-user-images\image-20220703161728095.png)

```html
<body>
    <div id="root">
        <input type="text" placeholder="请输入关键字" v-model="keyWord"/>
        <input type="button"  @click="sortType = 2" value="按年龄升序"/>
        <input type="button"  @click="sortType = 1" value="按年龄降序"/>
        <input type="button"  @click="sortType = 0" value="按原顺序排序"/>
        <ul>
            <li v-for="(p,index) in filterPersons" :key="index">{{p.name}}-{{p.age}}-{{p.sex}}</li>
        </ul>
    </div>

    <script>
        Vue.config.productionTip = false
        
        const vm = new Vue({
            el: '#root',
            data: {
                keyWord: '',
                sortType: 0,
                persons: [
                    {id:'001',name:'马冬梅',age:19,sex:'女'},
                    {id:'002',name:'周冬雨',age:21,sex:'女'},
                    {id:'003',name:'周杰伦',age:18,sex:'男'},
                    {id:'003',name:'温兆伦',age:20,sex:'男'},
                ],
            },
            computed:{
                filterPersons(){
                    var arr = this.persons.filter((p)=>{
                        return p.name.indexOf(this.keyWord) !== -1
                    })
                    if(this.sortType){
                        arr.sort((p1,p2)=>{
                            return this.sortType === 2 ? p1.age-p2.age : p2.age-p1.age
                        })
                    }
                    return arr
                }
            }
        })
    </script>
</body>
```

## 1.16.Vue检测数据变化的原理

<h3>模拟简单默认数据监视</h3>

```html
<body>
    <script>
        function Observer(obj) {
            //获取对象中的所有key,放入到keys数组中
            const keys = Object.keys(obj)
            //遍历每个key,为其添加get/set函数
            keys.forEach((key) => {
                Object.defineProperty(this, key, {
                    get() {
                        return obj[key]
                    },
                    set(value) {
                        console.log(`${key}属性改变了,准备开始重新解析模板...`);
                        obj[key] = value
                    }
                })
            })
        }

        let data = {
            name: 'jack',
            age: 20
        }

        let obs = new Observer(data)
        let vm = {}
        vm._data = data = obs
        
    </script>
</body>
```

![image-20220703192644507](C:\Users\uuu\AppData\Roaming\Typora\typora-user-images\image-20220703192644507.png)

<h3>Vue.set()和vm.$set()</h3>

Vue提供了Vue.set()和vm.$set()可以实现后添加的数据也能有响应式的功能

语法：```Vue.set(target,'key/index','value') ```或 ```vm.$set(target,'key/index','value')```

​	target：往谁的身上追加属性,<font color='red'>target不允许是vm或vm._data</font>

​	key/index: 要追加的属性名或者数组下标

​	value: 属性值

这种方法存在局限

​	<font color='red'>set()不能给data追加属性,只能给data中的对象追加属性</font>

<h3>数组监视</h3>

```html
<body>
    <div id="root">
        <ul>
            <li v-for="(h, index) in hobby" :key="index">{{h}}</li>
        </ul>
    </div>
    <script>
       Vue.config.productionTip = false

       const vm = new Vue({
        el:'#root',
        data: {
            hobby:['唱歌','跳舞','打球']
        }
       })
    </script>
</body>
```

![image-20220703211308645](C:\Users\uuu\AppData\Roaming\Typora\typora-user-images\image-20220703211308645.png)

由于数组内并没有提供set和get,所以在修改hobby[0]时Vue监测不到数据发生变化,导致页面也不会变化

Vue将被监听的数组的更变方法进行了包装,所以他们会触发视图的更新,这些被包装的方法有：

- push() 在数组尾部插入元素
- pop() 移除数组尾部元素
- shift() 移除数组首部元素
- unshift() 在数组首部插入元素
- splice() 在指定位置替换、添加或删除数组元素
- sort() 对数组进行排序
- reverse() 反转数组

<h3>Vue监视数据变化的原理</h3>

1.Vue会监视data中所有层次的数据

2.监视对象中的数据

​	通过setter实现监视,且要在new Vue时就传入要监视的数据

​	对象中后追加的属性Vue默认不做响应式处理

​	如果需要对后追加的属性做响应式处理需要使用```Vue.set()```或```vm.$set()```

3.监视数组中的属性

​	对于数组中非对象属性,Vue默认不提供setter和getter,直接修改vue监视不到变化

​	通过包裹数组更新元素的函数实现

<font color='red'>注意：通过Vue.set()和vm.$set()不能给vm或vm._data添加属性</font>

## 1.17.收集表单数据

```html
<body>
    <div id="root">
        <form action="#" method="post" @submit.prevent="submit">
            账号：<input type="text" v-model.trim="username"><br><br>
            密码：<input type="password" v-model="password"><br><br>
            年龄：<input type="number" v-model.number="age"><br><br> <!-- 只收集数字 -->
            性别：
            男<input type="radio" name="sex"v-model="sex" value="male"> 
            女<input type="radio" name="sex"v-model="sex" value="female"><br><br>
            爱好：
            唱歌<input type="checkbox" name="hobby" v-model="hobby" value="sing">
            跳舞<input type="checkbox" name="hobby" v-model="hobby" value="dance">
            打球<input type="checkbox" name="hobby" v-model="hobby" value="ball"><br><br>
            校区：
            <select name="area" v-model="area">
                <option value="">请选择校区</option>
                <option value="富春">富春校区</option>
                <option value="滨江">滨江校区</option>
            </select><br><br>
            其他信息：<textarea v-model.lazy="info"></textarea><br><br>
            <input type="checkbox" v-model="agree">
            同意并接收<a href="#">《用户协议》</a><br><br> 
            <input type="submit" value="提交">
        </form>
    </div>

    <script>
        Vue.config.productionTip = false

        const vm = new Vue({
            el: '#root',
            data: {
                username: '',
                password: '',
                age:'',
                sex: 'male',
                hobby: [],
                area: '',
                info:'',
                agree: ''
            },
            methods: {
                submit(){
                    const data = JSON.stringify(thi._data)
                }
            },
        })
    </script>
</body>
```

总结：

​	1.若收集的是\<input type="text"> 则v-model收集的是用户输入的value值

​	2.若收集的是\<input type="radio"> 则v-model收集的是value值且要配置value属性

​	3.若收集的是\<input type="checkbox"> 则v-model收集的是

​		3.1没有配置value属性.收集checked的值(true/false)

​		3.1配置了value属性.如果v-model的初始值是非数组那么收集的还是checked的值.如果v-model的初始值			是数组.那么收集的就是value组成的数组

​	4.v-model的三个修饰符

​		4.1 lazy 失去焦点再收集数据

​		4.2 number 输入的字符串转为有效数组,一般配置type="number"使用

​		4.3trim 去除输入首位多余空格

## 1.18.过滤器

BootCDN网址：https://www.bootcdn.cn/

```html
<head>
    <meta charset="UTF-8">
    <title>Document</title>
    <script src="../js/vue.js"></script>
    <script src="../js/dayjs.min.js"></script>
</head>
<body>
    <div id="root">
        <!-- 计算属性实现 -->
        <div>{{formatTime}}</div>
        <!-- 过滤器实现 -->
        <div>{{time | timeFormater}}</div>
        <!-- 过滤器传参 -->
        <div>{{time | timeFormater('YYYY年MM月DD日')}}</div>
        <!-- 过滤器串联使用 -->
        <div>{{time | timeFormater('YYYY年MM月DD日') | getYear}}</div>
    </div>

    <script>
        Vue.config.productionTip = false
        //配置全局过滤器
        Vue.filter('globalFilter',function(value){
            return value.toUpperCase()
        })

        const vm = new Vue({
            el: '#root',
            data: {
                time: 1656912659058,
            },
            computed: {
                formatTime(){
                    return dayjs(this.time).format('YYYY-MM-DD HH:mm:ss')
                }
            },
            filters: {
                timeFormater(value,formatStr = 'YYYY-MM-DD HH:mm:ss'){
                    //第一个参数一定是value为管道符|前面的值
                    //后面的参数为传递的参数
                    return dayjs(value).format(formatStr)
                },
                getYear(value){
                    return value.slice(0,5)
                }
            }
        })
    </script>
</body>
```

总结：

​	1.定义：对显示的数据进行特定格式化后再显示(适用于简单逻辑的处理)

​	2.语法：

​		```Vue.filter('filterName',callback)```  全局过滤器

​		```new Vue{filters:{}```}  局部过滤器

​	3.过滤器可以接收额外的参数,第一个参数是管道符前面的值

​	4.过滤器可以串联使用

​	5.过滤器不改变原本的数据而是产生对应新的数据

## 1.19.内置指令

| 内置指令 | 说明 |
| -------- | ---- |
|v-bind |单向绑定 可简写为:xxx|
|v-model |双向绑定|
|v-for |列表渲染|
|v-on| 绑定事件 可简写为@|
|v-if |条件渲染|
|v-show |条件渲染|
|v-text |向其所在的结点中渲染文本内容,不解析标签|
|v-html |向其所在结点中渲染html结构内容|
|v-cloak| 没有值,Vue创建完容器后会删掉v-cloak属性|
|v-once |没有值,所在结点在初次动态渲染后就视为静态内容了|
|v-pre |没有值,跳过所在结点的编译过程,加快编译速度|

```html
<body>
    <div id="root">
        <!-- 不解析标签 -->
        <div v-text="info"></div>
        <!-- 会解析标签 -->
        <!-- v-html存在安全问题,容易导致xss攻击 -->
        <div v-html="info"></div>
        <!-- 仅渲染一次以后数据变化也不会更新数据 -->
        <div v-once>{{info}}</div>
        <!--用到插值语法、指令语法的结点不要使用v-pre-->
        <div v-pre>你好</div>
        <div v-pre>{{info}}</div> <!--不要这样写-->
    </div>

    <script>
        Vue.config.productionTip = false

        const vm = new Vue({
            el: '#root',
            data: {
                info: '<h1>Hello Vue</h1>'
            }
        })

    </script>
</body>
```

```html
<style>
    /* 一般配合css使用,可以解决网速慢时展示出未经编译的模板{{xxx}} */
    [v-cloak]{
        display: none;
    }
</style>
```

## 1.20.自定义指令

需求1：自定义v-big指令 功能和v-text类似但是会把数值放大10倍

![image-20220704171228245](C:\Users\uuu\AppData\Roaming\Typora\typora-user-images\image-20220704171228245.png)

```html
<body>
    <div id="root">
        当前的num值是：<span v-text="num"></span><br><br>
        num值放大10倍：<span v-big="num"></span><br><br>
        <button @click="num++">点击num加1</button>
    </div>
</body>

<script>
    Vue.config.productionTip = false

    const vm = new Vue({
        el: '#root',
        data: {
            num: 5
        },
        directives: {
            //定义指令的第一种方式，函数式
            /* 
            调用时机：
                1.指令与元素绑定成功时
                2.指令所在模板被重新解析时
            */
            big(element,binding){ //big: function(element,binding){} 完整写法
                //element:指令所在真实DOM
                //bingding:binding.value常用
                element.innerText = binding.value * 10
            }
        }
    })
</script>
```

需求2：自定义v-fbind指令 功能与v-bind类似但可以默认获取焦点

```html
<body>
    <div id="root">
        <input type="text" v-bind:value="num"><br><br>
        <input type="text" v-fbind="num"><br><br>
        <button @click="num++">点击num加1</button>
    </div>
</body>

<script>
    Vue.config.productionTip = false

    const vm = new Vue({
        el: '#root',
        data: {
            num: 5
        },
        directives: {
            //自定义指令的第二种方式：对象式
            fbind:{
                bind(ele,binding){
                    //指令和元素成功绑定时调用
                    ele.value = binding.value
                },
                inserted(ele,binding){
                    //指令所在元素被插入页面时调用
                    ele.focus()
                },
                update(ele,binding){
                    //指令所在模板被重新解析时调用
                    ele.value = binding.value
                    ele.focus()
                }
            }
        }
    })
</script>
```

自定义指令总结：

​	1.局部指令：定义在配置对象的directives中

​	2.全局指令：Vue.directive('指令名',配置对象)或Vue.directive('指令名',回调函数)

​	3.定义指令时不加v- 但使用指令时要加v-

​	4.指令名如果是多个单词用-隔开,不要写成驼峰式

​	5.directives中的函数中的this指向Window

## 1.21.生命周期

1.生命周期函数又叫生命周期钩子

2.生命周期钩子就是Vue在关键的时刻帮我们调用的特殊名称的函数

3.生命周期函数的名字不可更改

4.生命周期函数中的this指向vm实例对象或组件实例

5.vm.$destory() 完全销毁vm实例

![生命周期](C:\Users\uuu\AppData\Roaming\Typora\typora-user-images\vue生命周期钩子.png)

| 生命周期钩子          | 说明                    |
| --------------------- | ----------------------- |
| beforeCreate          | 数据代理被创建之前      |
| created               | 数据代理被创建之后      |
| beforeMount           | 真实DOM被挂在到页面之前 |
| mounted（常用）       | 真实DOM被挂在到页面之后 |
| beforeUpdate          | _data数据发生变化之前   |
| updated               | _data数据发生变化之后   |
| beforeDestroy（常用） | vm对象被销毁之前        |
| destroyed             | vm对象被销毁之后        |

<h3>template属性使用</h3>

```html
<body>
    <div id="root">
		<!--这里无需写代码-->
    </div>
</body>

<script>
    Vue.config.productionTip = false

    const vm = new Vue({
        el: '#root',
        //不能用<template>标签作为组件的根标签
        template: `
            <div> 
                <h1>num的值为：{{num}}</h1>
                <button @click="num++">num++</button>    
            </div>
        `,
        data: {
            num: 0
        }
    })
</script>
```

# 2.Vue组件化

传统方式编写应用

![image-20220705085937369](C:\Users\uuu\AppData\Roaming\Typora\typora-user-images\image-20220705085937369.png)

使用组件的方式编写应用

![image-20220705090834718](C:\Users\uuu\AppData\Roaming\Typora\typora-user-images\image-20220705090834718.png)

组件可以嵌套使用

![image-20220705091032593](C:\Users\uuu\AppData\Roaming\Typora\typora-user-images\image-20220705091032593.png)

什么是组件？

​	实现应用中局部功能代码和资源的集合

组件可以分成2种

​	非单文件组件：一个文件种包含n个组件

​	单文件组件：只包含一个组件的xxx.vue

## 2.1.创建非单文件组件

```html
<body>
    <div id="root">
        <!-- 第三步：使用组件：编写组件标签（components配置对象中的key） -->
        <school></school>
        <hr>
        <!-- 使用多次组件它们之间的数据不会产生干扰 -->
        <student></student>
        <student></student>
        
    </div>
</body>

<script>
    Vue.config.productionTip = false

    //第一步：创建school组件
    const sch = Vue.extend({
        template:`
            <div>
                <h1>学校名称：{{schoolName}}</h1>
                <h1>学校地址：{{address}}</h1>
            </div>`,
        //组件没有el配置项
        //data必须使用函数式
        data() {
            return {
                schoolName: '浙江中医药大学',
                address: '杭州滨江区'
            }
        },
    })
    //第一步：创建student组件
    const stu = Vue.extend({
        template:`
            <div>
                <h1>学生姓名：{{studentName}}</h1>
                <h1>学生年龄：{{age}}</h1>
            </div>`,
        data() {
            return {
                studentName: '张三',
                age: 20
            }
        },
    })

    const vm = new Vue({
        el: '#root',
        //第二步：注册组件（局部注册）
        components: {
            school:sch,
            student:stu
        }
    })
</script>
```

![image-20220705095036125](C:\Users\uuu\AppData\Roaming\Typora\typora-user-images\image-20220705095036125.png)

定义组件的简写形式

```js
//完整形式
const school = Vue.extend({...})
//简写形式
const school = {...}
```



全局注册组件

```js
Vue.component('组件名',组件常量)
```

总结：

​	Vue中使用组件的三个步骤：

​		1.定义组件

​		2.注册组件

​		3.使用组件

​	组件名命名规范

​		1.组件名由一个单词组成

​			第一种写法：首字母小写（school）

​			第二种写法：首字母大写（School）

​		2.组件名由多个单词组成

​			第一种写法：用-划分单词（my-school）

​			第二种写法：每个单词首字母大写（MySchool）这种要在脚手架中使用

​		3.组件名尽可能回避HTML中已有元素名称,如h1、H1...

​	组件标签的写法

​		第一种写法：\<school>\</school>

​		第二种写法：\<school/> 这种要在脚手架中使用

## 2.2.组件的嵌套

```html
<body>
    <div id="root">
        <school></school>
    </div>
</body>

<script>
    Vue.config.productionTip = false

    const stu = {
        template:`
            <div>
                <h1>学生姓名：{{studentName}}</h1>
                <h1>学生年龄：{{age}}</h1>
            </div>`,
        data() {
            return {
                studentName: '张三',
                age: 20
            }
        },
    }

    const sch = Vue.extend({
        //注册在哪个组件就在哪个组件中使用
        template:`
            <div>
                <h1>学校名称：{{schoolName}}</h1>
                <h1>学校地址：{{address}}</h1>
                <student></student>
            </div>`,
        data() {
            return {
                schoolName: '浙江中医药大学',
                address: '杭州滨江区'
            }
        },
        //组件定义要在组件注册之前
        components:{
            student:stu
        }
    })

    const vm = new Vue({
        el: '#root',
        //第二步：注册组件（局部注册）
        components: {
            school:sch,
        }
    })
</script>
```

## 2.3.VueComponent构造函数

1.组件的本质是VueComponent构造函数且不是程序员定义的而是Vue.extend生成的

2.程序员只需写\<XXX/>或\<XXX>\</XXX>组件标签,Vue在解析时会为我们创建该组件的实例对象

3.每次调用Vue.extend返回的都是全新的VueComponent

4.this指向

​	在组件配置中this指向的是VueCompanent实例对象

​	在new Vue(options)配置中this指向vm实例对象

## 2.4.重要的内置关系

prototype 显示原型属性

\__proto__  隐式原型属性

<font color='red'>VueComponent.prototype.\__proto__ === Vue.prototype</font>

![image-20220705115340251](C:\Users\uuu\AppData\Roaming\Typora\typora-user-images\image-20220705115340251.png)

Vue为什么要设置这种关系？

​	让组件实例对象可以访问到Vue原型上的属性和方法

## 2.5.创建单文件组件

创建School.vue文件

```vue
<template>
    <!--组件的结构  -->
    <div class="demo">
        <h1>学校名称：{{schoolName}}</h1>
        <h1>学校地址：{{address}}</h1>
    </div>
</template>

<script>
    //组件交互相关代码
    export default {
        name: 'School',//一般与文件名保持相同
        data() {
            return {
                schoolName:'浙江大学',
                address:'杭州西湖区'
            }
        },
    }
</script>

<style>
    /* 组件的样式 */
    .demo{
        background-color: aqua;
    }
</style>
```

创建Student.vue组件

```vue
<template>
    <!--组件的结构  -->
    <div>
        <h1>学生姓名：{{name}}</h1>
        <h1>学生年龄：{{age}}</h1>
    </div>
</template>

<script>
    //组件交互相关代码
    export default {
        name: 'Student',//一般与文件名保持相同
        data() {
            return {
                name:'Jack',
                age:18
            }
        },
    }
</script>
```

创建App.vue组件 (管理其他单文件组件)

```vue
<template>
    <div>
        <School></School>
        <Student></Student>
    </div>
</template>

<script>
    import School from './School.vue'
    import Student from './Student.vue'

    export default {
        //注册组件
        components: { 
            Student,
            School
         },
    }
</script>
```

创建main.js文件（作为程序的入口）

```js
import App from './App.vue';

new Vue({
    el: "#root",
    template:`<App></App>`,
    components:{App}
})
```

创建index.html文件

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>单文件组件</title>
</head>
<body>
    <div id="root">
        
    </div>
	
    <!--引入vue.js和main.js-->
    <script type="text/javascript" src="../../js/vue.js"></script>
    <script type="text/javascript" src="./main.js"></script>
</body>
</html>
```

以上代码无法直接在浏览器上运行.因为浏览器不支持ES6模块化语法

# 3.Vue脚手架

## 3.1.初始化脚手架



CLI (Command Line Interface) 翻译过来为脚手架

文档：https://cli.vuejs.org/zh/

```
初始化脚手架步骤：
	1.全局安装@vue/cli 
		npm install -g @vue/cli
	2.切换到要创建项目的目录然后使用命令创建项目
		vue create 项目名
	3.启动项目
		npm run serve
		
如果出现下载缓慢可以配置npm淘宝镜像
	npm config set registry https://registry.npm.taobao.org
```

项目启动成功

![image-20220705142812597](C:\Users\uuu\AppData\Roaming\Typora\typora-user-images\image-20220705142812597.png)

访问网页

![image-20220705142745196](C:\Users\uuu\AppData\Roaming\Typora\typora-user-images\image-20220705142745196.png)



## 3.2.目录说明

```
>node_modules--------------->用来管理项目中使用依赖
>public
	favicon.icon------------>页签图标
	index.html-------------->项目主页
>src------------------------>用来书写vue的源代码
	>assets----------------->用来存放静态资源
	>components------------->用来书写Vue组件
	App.vue----------------->根组件
	main.js----------------->项目的入口文件
.gitignore------------------>git忽略文件
babel.config.js------------->babel的配置文件（ES6转ES5）
jsconfig.json--------------->指定了JavaScript 语言服务提供的功能的根文件和选项
package-lock.json----------->包版本控制文件
package.json---------------->类似与pom.xml依赖管理
README.md------------------->对项目的说明
vue.config.js--------------->可选配置文件(可以个性化的配置脚手架)
```

## 3.3.利用脚手架实现2.5案例

1.main.js

```js
//引入Vue(阉割版) 完整版是'vue/dist/vue'
import Vue from 'vue';
//引入App组件
import App from './App.vue';

Vue.config.productionTip = false

new Vue({
    el: "#app",
    //将App组件放入容器
    render: h=>h(App)
})
```

1.替换App.vue

```vue
<template>
    <div>
        <img src="./assets/logo.png" alt="logo">
        <School></School>
        <Student></Student>
    </div>
</template>

<script>
    import School from './components/School.vue'
    import Student from './components/Student.vue'

    export default {
        //注册组件
        components: { 
            Student,
            School
         }
    }
</script>
```

2.将Student和School组件放入components目录下

```vue
<template>
    <!--组件的结构  -->
    <div class="demo">
        <h1>学校名称：{{schoolName}}</h1>
        <h1>学校地址：{{address}}</h1>
    </div>
</template>

<script>
    //组件交互相关代码
    export default {
        name: 'MySchool',//一般与文件名保持相同
        data() {
            return {
                schoolName:'浙江大学',
                address:'杭州西湖区'
            }
        },
    }
</script>

<style>
    /* 组件的样式 */
    .demo{
        background-color: aqua;
    }
</style>
```

```vue
<template>
    <!--组件的结构  -->
    <div>
        <h1>学生姓名：{{name}}</h1>
        <h1>学生年龄：{{age}}</h1>
    </div>
</template>

<script>
    //组件交互相关代码
    export default {
        name: 'MyStudent',//一般与文件名保持相同
        data() {
            return {
                name:'Jack',
                age:18
            }
        },
    }
</script>
```



3.启动项目

![image-20220705155045506](C:\Users\uuu\AppData\Roaming\Typora\typora-user-images\image-20220705155045506.png)

## 3.4.render

```js
new Vue({
    el: "#app",
    //将App组件放入容器
    render: h=>h(App)
    
    /*完整写法 
    render(createElement){
        return createElement(App)
    }
     */
})
```

关于不同版本的Vue

	- vue.js 和 vue.runtime.xxx.js的区别

​	   .vue.js是完整版的Vue.包含核心功能+模板解析器

​	   .vue.runtime.xxx.js是运行版的Vue.只包含核心功能

- 因为.vue.runtime.xxx.js没有模板解析器所以不能使用template配置项

  需要使用render函数接收到的createElement函数去指定具体的内容

## 3.5脚手架配置

```shell
#Vue脚手架隐藏了所有webpack相关配置
#查看具体的webpack配置
vue inspect > output.js
#修改output.js配置文件无法生效要在vue.config.js文件中修改
```

自定义配置在vue.config.js文件中

```js
module.exports = {
  pages: {
    index: {
      // page 的入口
      entry: 'mySrc/myMain.js',
      // 模板来源
      template: 'public/myIndex.html'
      //...
      //https://cli.vuejs.org/zh/config/#pages
    }
  },
  lintOnSave:false //关闭语法检查
}
```

## 3.6.ref属性

```vue
<template>
    <div>
        <h1 v-text="info" ref="title"></h1>
        <button @click="showDOM" ref="btn">显示ref</button>
        <School ref="sch"></School>
    </div>
</template>

<script>
    import School from './components/School.vue'

    export default {
        name: 'App',
        components: { 
            School
         },
         data() {
            return {
                info:'欢迎学习Vue'
            }
         },
         methods: {
            showDOM(){
                console.log(this.$refs.title); //真实DOM h1
                console.log(this.$refs.btn);   //真实DOM button
                console.log(this.$refs.sch);   //School组件实例对象
            }
         },

    }
</script>
```

总结：

​	1.ref属性被用于给元素或子组件注册应用信息(类似id)

​	2.ref属性用在HTML标签上获取的是真实DOM.用在组件标签上获取的是组件实例对象

​	3.打标识 ```\<h1 ref='xxx'>...\</h1>```

​	3.获取方式```this.$refs.xxx```

## 3.7.props配置项

Student.vue

```html
<template>
    <!--组件的结构  -->
    <div>
        <h1>学生姓名：{{name}}</h1>
        <h1>学生性别：{{sex}}</h1>
        <h1>学生年龄：{{age+1}}</h1>
    </div>
</template>

<script>
    //组件交互相关代码
    export default {
        name: 'MyStudent',
        data() {
            return {}
        },
        //props:['name','sex','age'] //简单接收(string类型)

        //接收的同时对数据类型进行限制
        props: {
            name: String,
            age: Number,
            sex: String
        }

        //最完整写法
        /* 
        props: {
            name: {
                type: String, //类型是String
                required:true //name是必须要传的
            },
            sex: {
                type: Number,
                default: 18   //默认值
            },
            age: {
                type: String,
                required:true
                }
        }
         */
    }
</script>
```

App.vue

```vue
<template>
    <div>
        <Student name="Marry" sex="女" age="18"></Student>
    </div>
</template>

<script>

    import Student from './components/Student.vue'

    export default {
        //注册组件
        components: { 
            Student,
         },
    }
</script>

```

![image-20220705195102125](C:\Users\uuu\AppData\Roaming\Typora\typora-user-images\image-20220705195102125.png)

总结

​	props配置项的作用: 让组件接收外部参数

​	接收到的props是不可以改的.如果想修改就在data中添加属性 ```myAge: this.age```

```
<School :name="name"/> 传递的是data属性或计算属性或函数或表达式
<School name="name"/> 传递的是普通字符串"name"
```



## 3.8.mixin

创建文件mixin.js

```js
export const mixin = {
    //组件配置对象中的可复用的配置项都可以写在这
	methods:{
		sayHello(){
			alert("hello")
		}
	}
}
```

引入混合

```vue
<script>
	//引入mixin.js文件
	import {mixin} from '../mixin'
	
	export default {
		name: 'Student',
		data(){
			retrun{}
		},
		//引入混合
		mixins:[mixin]
	}
</script>
```

总结： 

   1.可以将多个组件共用的配置提取成混合对象

 	2.	如果data、methods中属性发生冲突以组件配置为准
 	3.	但是对于生命周期钩子而言 两个都要且混合中的生命钩子先执行
 	4.	局部混合：组件配置对象中的```mixin: [xxx,xxx]```
 	5.	全局混合：在main.js中

```js
import {mixin1,mixin2} from '../mixin'
Vue.mixin(mixin1)
Vue.mixin(mixin1)
```

## 3.9.插件

定义插件: 创建文件plugin.js

```js
export default{
	install(Vue){
		//配置全局过滤器
		Vue.filter('filterName',function(value){...})
		//定义全局指令
		Vue.directive('directName',{...})
		//定义全局混入
		Vue.mixin({...})
		//其他功能增强代码...
	}
}
```

使用插件

```js
//先引入插件
import plugins from './plugins'

//使用插件
Vue.use(plugins)
```

总结：

​	功能：用于增强Vue

​	本质：包含一个install方法的对象

​	install方法的第一个参数是Vue,之后的参数是插件使用者传递的参数

## 3.10.scoped

如果不同组件中的样式名相同会引发冲突（与引入顺序有关）

解决方法

```html
<style scoped>
	/*....*/
</style>
```

但是App组件中的style标签不适用scoped

因为在App组件中写的样式一般都是多个组件共用的样式

如果App组件中的style标签加了scoped则样式只能在App组件中使用

## 3.11.TodoList案例

案例Github地址：

组件化编码流程

​	1.拆分静态组件：组件按照功能点拆分 命名不要与HTML元素冲突

​	2.实现动态组件：考虑好数据存放的位置 数据是一个组件在使用还是一些组件在使用

​			一个组件在使用：放在组件自身即可

​			一些组件在使用：放在他们共同的父组件上

​	3.实现交互：从绑定事件开始

## 3.12.组件间数据传递

父组件向子组件传递数据：子组件使用props配置项接收父组件在组件标签中传递的数据

子组件向父组件传递数据：父组件事先定义函数传给子组件,然后子组件调用父组件的函数

​											  通过函数的形参将数据传给父组件

兄弟组件之间传递数据：（最初级的做法）

​	1.将共用的数据定义在父组件中

​	2.在父组件中定义操作数据的函数

​	3.若组件A想给兄弟组件B传递数据,那么父组件将函数传递给A将数据传递给B

## 3.13.WebStorage

```html
<body>
    <button onclick="saveData()">点我保存数据</button>
    <button onclick="readData()">点我获取数据</button>
    <button onclick="deleteData()">点我删除数据</button>
    <button onclick="deleteAll()">点我清空数据</button>

    <script>
        function saveData(){
            //key,value都是字符串,如果不是字符串默认会调用toString()方法
            //如果value要存对象,要先将对象转化为字符串JSON.stringify(obj)
            window.localStorage.setItem('message','hello')
            window.localStorage.setItem('person',JSON.stringify({name: 'Jack',age: 20}))
        }
        function readData(){
            let message = localStorage.getItem('message')
            let personStr = localStorage.getItem('person')
            //将JSON字符串转化为对象
            const personObj = JSON.parse(personStr)
        }
        function deleteData(){
            localStorage.removeItem('person')
        }
        function deleteAll(){
            localStorage.clear()
        }
    </script>
</body>
```

<font color='red'>sessionStorage和localStorage的API相同这里不在演示</font>

总结：

​	1.存储大小一般为5MB左右

​	2.浏览器通过```Window.localStorage```和```Window.sessionStorage```实现本地存储机制

​	3.sessionStorage存储的内容会随着浏览器窗口关闭而消失

​	4.localStorage存储的内容需要手动清理缓存才会消失

​	5.```xxxxStorage.getItem('x')``` 如果本地存储中不存在key为x则返回值为null

​	6.```JSON.parse(null) ```的结果依然是null

## 3.14.组件的自定义事件

案例：通过自定义事件实现子组件向父组件传递数据

App.vue

```html
<template>
    <div>
        <h1>App</h1>
        <!-- 通过函数获取子组件的数据 -->
        <School :getSchool="getSchoolData"/>

        <!-- 通过自定义事件获取子组件的数据（第一种写法） -->
        <!-- <Student @myEvent="getStudentData"/> -->

        <!-- 通过自定义事件获取子组件的数据（第二种写法） -->
        <!-- 这种方式比较灵活,比如可以过10秒再给组件绑定事件 -->
        <Student ref="student"/>
    </div>
</template>

<script>
    import Student from './components/Student.vue'
    import School from './components/School.vue'
    
    export default {
        //注册组件
        components: { 
            Student,
            School
         },
        methods: {
            getSchoolData(data){
                console.log('我是App组件 我接收到了School组件的数据',data);
            },
            //getStudentData(data,...params) 接收多个参数
            getStudentData(data){
                console.log('我是App组件 我接收到了Student组件的数据',data);
            }
        },
        mounted() {
            //绑定事件的另外一种方式
            this.$refs.student.$on('myEvent',this.getStudentData)
            //this.$refs.student.$once('myEvent',this.getStudentData) 事件只执行一次
        },
    }
</script>
```

Student.vue

```vue
<template>
    <div>
        <h1>学生姓名：{{name}}</h1>
        <h1>学生性别：{{sex}}</h1>
        <button @click="sendName">向App组件发送数据</button>
        <button @click="unbind">解绑自定义事件</button>
    </div>
</template>

<script>
    export default {
        name: 'MyStudent',
        data() {
            return {
                name: 'Jack',
                sex: 'men'
            }
        },
        methods: {
            sendName(){
                this.$emit('myEvent',this.name) //触发是定义事件myEvent
            },
            unbind(){
                this.$off('myEvent') //解绑一个自定义事件
                this.$off(['myEvent1','myEvent2']) //解绑多个自定义事件
                this.$off() //解绑所有自定义事件
            }
        }
    }
</script>
```

School.vue

```vue
<template>
    <div>
        <h1>学校名称：{{name}}</h1>
        <h1>学校地址：{{address}}</h1>
        <button @click="sendName">向App组件发送数据</button>
    </div>
</template>

<script>
    export default {
        name: 'MyStudent',
        props:['getSchool'],
        data() {
            return {
                name: '浙江大学',
                address: '浙江杭州'
            }
        },
        methods: {
            sendName(){
                this.getSchool(this.name)
            }
        },
    }
</script>
```

组件如何绑定原生DOM事件

```
<Student @click.native = "xxx"> 正确写法

<Student @click = "xxx"> 错误写法 这样写vue会把click当作自定义事件
```

总结：

​	1.自定义事件适用于子组件向父组件传递数据（事件的回调写在父组件中）

​	2.绑定自定义事件 v-on:自定义事件名  $on('自定义事件名',回调函数)

​	3.触发自定义事件 ```this.$emit('自定义事件名')```  （给哪个组件绑定事件就在哪个组件中触发）

​	4.解绑自定义事件 ```this.$off('自定义事件名') ```    （给哪个组件绑定事件就在哪个组件中解绑）

​	5.组件上也可以绑定原生的DOM事件,需要使用native修饰符

​	6.使用```tihs.$ref.xxx.$on('事件名',回调函数)``` 如果回调不是在methods中

​			6.1.如果函数是普通函数那么this是绑定事件的组件实例

​			6.2.如果使用的是箭头函数那么this指向的是父组件的实例

## 3.15全局事件总线

全局事件总线可以实现任意组件间的通信

![image-20220707213430172](C:\Users\uuu\AppData\Roaming\Typora\typora-user-images\image-20220707213430172.png)

哪个组件向收到数据就在哪个组件中给$bus绑定事件,哪个组件想发送数据就在哪个组件中触发事件

如上图所示

​	组件C给组件A发送数据,首先在组件A中给$bus绑定event1事件,然后组件C在触发event1事件的同时携带些数据

​	组件D给组件B发送数据,首先在组件B中给$bus绑定event2事件,然后组件D在触发event1事件的同时携带些数据

接收数据的组件绑定事件发送数据的组件触发事件



案例：Student组件向School组件发送学生信息

main.js

```js
import Vue from 'vue';
import App from './App.vue';

Vue.config.productionTip = false

new Vue({
    el: "#app",
    render: h=>h(App),
    beforeCreate(){
        //原理VueComponent.prototype.__proto__ === Vue.prototype
        Vue.prototype.$bus = this //安装全局事件总线
    }
    
})
```

App.vue

```vue
<template>
    <div>
        <h1>App</h1>
        <School></School>
        <Student></Student>
    </div>
</template>

<script>

    import Student from './components/Student.vue'
    import School from './components/School.vue'

    export default {
        //注册组件
        components: { 
            Student,
            School
         },
    }
</script>
```

School.vue

```vue
<template>
    <div>
        <h1>学校名称：{{name}}</h1>
        <h1>学校地址：{{address}}</h1>
    </div>
</template>

<script>
    export default {
        name: 'MyStudent',
        data() {
            return {
                name: '浙江大学',
                address: '浙江杭州'
            }
        },
        methods: {
            getStudentMsg(studentMsg){
                console.log('我是School组件我收到了学生信息');
                console.log(studentMsg.name,studentMsg.sex);
            }
        },
        mounted() {
            //给$bus绑定自定义事件myEvent
            this.$bus.$on('myEvent',this.getStudentMsg)
        },
        beforeDestroy() {
            //给$bus解绑事件
            this.$bus.$off('myEvent')
        },
    }
</script>
```

Student.vue

```vue
<template>
    <div>
        <h1>学生姓名：{{name}}</h1>
        <h1>学生性别：{{sex}}</h1>
        <button @click="sendStuMsg">点击发送学生信息</button>
    </div>
</template>

<script>
    export default {
        name: 'MyStudent',
        data() {
            return {
                name: 'Jack',
                sex: 'men'
            }
        },
        methods: {
            sendStuMsg(){
                const student = {name:this.name,sex:this.sex}
                //触发事件myEvent
                this.$bus.$emit('myEvent', student)
            }
        },
    }
</script>
```

![image-20220707225719739](C:\Users\uuu\AppData\Roaming\Typora\typora-user-images\image-20220707225719739.png)

总结：

​	1.安装全局事件总线

​	2.使用事件总线

​			接收数据：School组件想接收数据,就在School组件中给$bus绑定自定义事件,事件的<font color='red'>回调留在School组件</font>

​			提供数据：Student组件提供数据就找Student组件中触发事件

​	3.最好在beforeDestroy() 钩子中使用$off去解绑当<font color='red'>前组件所用到的事件</font>

![image-20220709154022529](C:\Users\uuu\AppData\Roaming\Typora\typora-user-images\image-20220709154022529.png)

## 3.16消息订阅与发布

消息订阅与发布也是完成任意组件间的通信

安装消息订阅与发布第三方库 pubsub.js

```
npm i pubsub-js
```

School.vue

```html
<template>
    <div>
        <h1>学校名称：{{name}}</h1>
        <h1>学校地址：{{address}}</h1>
    </div>
</template>

<script>
    import pubsub from 'pubsub-js'

    export default {
        name: 'MyStudent',
        data() {
            return {
                name: '浙江大学',
                address: '浙江杭州',
            }
        },
        mounted() {
            //订阅消息
            this.pubId = pubsub.subscribe('message',(messageName,data)=>{
                //如果写成普通函数this为undefined
                //如果写成箭头函数this为组件实例对象
                console.log('有人发布了'+messageName+'消息,School组件收到消息：'+data);

            })
        },
        beforeDestroy() {
            //取消订阅
            pubsub.unpublish(this.pubId)
        },
    }
</script>
```

Student.vue

```vue
<template>
    <div>
        <h1>学生姓名：{{name}}</h1>
        <h1>学生性别：{{sex}}</h1>
        <button @click="sendStuMsg">点击发送学生信息</button>
    </div>
</template>

<script>
    import pubsub from 'pubsub-js'

    export default {
        name: 'MyStudent',
        data() {
            return {
                name: 'Jack',
                sex: 'men'
            }
        },
        methods: {
            sendStuMsg(){
                //发布message消息
                pubsub.publish('message',this.name)
            }
        },
    }
</script>
```

![image-20220708124519712](C:\Users\uuu\AppData\Roaming\Typora\typora-user-images\image-20220708124519712.png)

总结

​	1.安装pubsub ：```npm i pubsub-js```

​	2.引入：```import pubsub from 'pubsub-js'```

​	3.接收数据：想要收到数据的组件订阅消息 ```pubsub.subscribe('消息名',回调)```

​	4.提供数据：发送数据的组件发布消息```pubsub.publish('消息名',数据)```

​	5.最好在beforeDestory钩子中取消订阅 ```pubsub.unpublish(消息ID)```

## 3.17.$nextTick

语法：this.$nextTick(回调函数)

作用：在下一次DOM更新结束后执行其指定的回调函数

适用：当数据改变后,要基于更新后的新DOM进行某些操作时要在nextTick所指定的回调函数中执行

## 3.18.过度和动画

在插入、更新、移除DOM元素时给元素添加样式类名

![image-20220708154845991](C:\Users\uuu\AppData\Roaming\Typora\typora-user-images\image-20220708154845991.png)

元素进入时的样式

​	1.v-enter 进入的起点

​	2.v-enter-active 进入的过程

​	3.v-enter-to 进入的终点

元素离开时的样式

​	1.v-leave 离开时的起点

​	2.v-leave-active 离开的过程

​	3.v-leave-to 离开的终点

案例：点击按钮h1标签向左滑动然后消失再次点击按钮h1向右滑动出现

![image-20220708145604840](C:\Users\uuu\AppData\Roaming\Typora\typora-user-images\image-20220708145604840.png)

动画实现

```html
<template>
  <div>
    <button @click="isShow = !isShow">显示/隐藏</button>
    <!-- 想让谁拥有动画效果就用<transition>标签包裹谁 -->
    <transition name="h1" appear> 
        <h1 v-show="isShow">你好啊</h1>
    </transition>
  </div>
</template>

<script>
    export default {
        name: 'MyTest',
        data() {
            return {
                isShow: true
            }
        },
    }
</script>

<style>
    h1{
        background-color: orange;
    }
    /* vue规定的样式名 */
    /* 如果transition标签没有加name属性 可以使用v-enter-active...*/
    .h1-enter-active{animation: dhName 0.5s linear;}
    .h1-leave-active{animation: dhName 0.5s linear reverse;}
    /* linear 匀速 */

    @keyframes dhName{
        from{
            /* 动画的起点 */
            transform: translateX(-100%);
        }
        to{
            /* 动画的终点 */
            transform: translateX(0px);
        }
    }
</style>
```

过度实现

```vue
<template>
  <div>
    <button @click="isShow = !isShow">显示/隐藏</button>

    <transition name="h1" appear> 
        <h1 v-show="isShow">你好啊</h1>
    </transition>
  </div>
</template>

<script>
    export default {
        name: 'MyTest1',
        data() {
            return {
                isShow: true
            }
        },
    }
</script>

<style>
    h1{
        background-color: orange;
    }
    /* vue规定的样式名 */
    /* 进入的起点、离开的终点 */
    .h1-enter .h1-leave-to{
        transform: translateX(-100%);
    }
    /* 进入的终点、离开的起点 */
    .h1-enter-to .h1-leave{
        transform: translateX(0px);
    }
    /* 来去时候激活的样式 */
    .h1-enter-active .h1-leave-active{
        animation: 0.5 linear;
    }
</style>
```

多元素过度

```html
<transition-group name="h1" appear>
	<!-- 必须配置key属性 -->
	<h1 v-show="isShow" key="1">XXX</h1>
	<h1 v-show="isShow" key="2">XXX</h1>
</transition-group>
```

集成第三方动画

animate网址 https://animate.style/

安装

```
npm i animate.css --save
```

引入

```
import 'animate.css'
```

案例

```vue
<template>
  <div>
    <button @click="isShow = !isShow">显示/隐藏</button>
    <!-- name的值是固定的 -->
    <transition-group appear
                      name="animate__animated animate__bounce" 
                      enter-active-class="animate__swing"
                      leave-active-class="animate__backOutUp"
    >
        <h1 v-show="!isShow" key="1">XXX</h1>
        <h1 v-show="isShow" key="2">YYY</h1>
    </transition-group>
  </div>
</template>

<script>
    //引入css
    import 'animate.css'
    export default {
        name: 'MyTest1',
        data() {
            return {
                isShow: true
            }
        },
    }
</script>

<style>
    h1{
        background-color: orange;
    }    
</style>
```

# 4.Vue中的AJAX

## 4.1.使用axios

安装axios

```shell
npm i axios
```

引入axios

```js
import axios from 'axios'
```

使用axios

```html
<script>
	import axios from 'axios'
	export default {
		name:'APP',
		methods:{
			getStu(){
				axios.get('http://localhost:5000/student').then(
					response=>{
						//返回成功的代码片段
                        console.log('请求成功',response.data)
					},
					error=>{
						//失败的代码片段
                        console.log('请求失败',error.message)
					}
				)
			}
		}
	}
</script>
```

## 4.2.跨域问题

跨域问题是指违背了同源策略

​	同源策略规定了 协议名、主机名和端口号必须保持一致

如何解决跨域问题？

​	配置代理服务器

![image-20220708192702930](C:\Users\uuu\AppData\Roaming\Typora\typora-user-images\image-20220708192702930.png)

注意：代理服务器和服务器之间的传递数据不用ajax所以不受同源策略限制

使用vue-cli配置代理服务器

配置代理的第一种方式：

1.修改vue.config.js配置文件

```js
//添加下面代码
//这种方式只能配置一个代理服务器
module.exports = {
  devServer: {
    proxy: 'http://localhost:5000'
  }
}
```

2.修改axios请求地址

```js
axios.get('http://localhost:8080/student').then(...)
```

说明：

​	优点：配置简单,请求的资源直接发给代理服务器即可

​	缺点：不能配置多个代理,不能灵活的控制请求是否走代理

​	工作方式：当请求代理服务器不存在的资源时,该请求会转发给服务器



配置代理的第二种方式：

1.修改vue.config.js配置文件

```js
module.exports = {
  devServer: {
    proxy: {
      //'/stu'是请求前缀
      '/stu': {
        target: 'http://localhost:5000',
        pathRewrite:{'^/stu',''}, //转发时把路径中的/stu换成空字符串
        ws: true, //webscoket 默认为true
        changeOrigin: true //用于控制请求头中的host值 默认为true
      },
      //配置多个代理服务器
      '/car': {
        target: 'http://localhost:5001',
        pathRewrite:{'^/car',''}
      }
    }
  }
}
```

2.修改axios请求地址

```js
axios.get('http://localhost:8080/stu/student').then(...)
```

说明：

​	优点：可以配置多个代理,可以灵活的控制请求是否走代理

​	缺点：配置繁琐,请求资源时必须加前缀



案例：github用户搜索

![image-20220708221931765](C:\Users\uuu\AppData\Roaming\Typora\typora-user-images\image-20220708221931765.png)

样式、main.js、App.vue代码省略

Search.vue

```vue
<template>
    <div class="search">
        <h1>Search User</h1>
        <input type="text" placeholder="search" v-model="keyWord">
        <input type="button" value="search" @click="searchUser">
    </div>
</template>

<script>
    import axios from "axios";

    export default {
        name: 'MySearch',
        data() {
            return {
                keyWord: ''
            }
        },methods: {
            searchUser(){
                this.$bus.$emit('updateUser',{isFirst: false,isLoding: true,errorMessage: '',users: []})
                axios.get(`https://api.github.com/search/users?q=${this.keyWord}`).then(
                    response => {
                        console.log(response.data.items);
                        this.$bus.$emit('updateUser',{isLoding: false,errorMessage: '',users: response.data.items})
                    },error => {
                        console.log('请求失败',error.message);
                        this.$bus.$emit('updateUser',{isLoding: false,errorMessage: error.message,users: []})
                    }
                )
            }
        },

    }
</script>
```

List.vue

```vue
<template>
    <div class="row">
      <div class="center" v-show="info.isFirst">WelCome!</div>
      <div class="center" v-show="info.isLoding">Loding...</div>
      <div class="center" v-show="info.errorMessage">{{info.errorMessage}}</div>
      <div class="card" 
           v-show="info.users.length" 
           v-for="user in info.users" 
           :key="user.id">
        <a :href="user.html_url" target="_blank">
          <img
            :src="user.avatar_url"
            style="width: 100px"
          />
        </a>
        <p class="card-text">{{user.login}}</p>
      </div>
    </div>
</template>

<script>
export default {
  name: "MyList",
  data() {
    return {
        info:{
        isFirst: true,
        isLoding: false,
        errorMessage: '',
        users: []
        }
    }
  },
  methods: {
    updateUser(dataObj){
        this.info = {...this.info,...dataObj}
    }
  },
  mounted() {
    this.$bus.$on('updateUser',this.updateUser)
  },
};
</script>
```

## 4.3.vue-resource

vue-resource也是对xhr的封装的插件（了解即可）

安装插件

```shell
npm i vue-resource
```

引入和使用插件

```js
import vueResource from 'vue-resource'

Vue.user(vueResource)
```

用法

```js
//用法与axios相同

this.$http.get('url').then(
	response=>{},
	error=>{}
)
```

## 4.4.插槽

实现效果一（默认插槽）

![image-20220709133212226](C:\Users\uuu\AppData\Roaming\Typora\typora-user-images\image-20220709133212226.png)

Category.vue (样式省略)

```vue
<template>
    <div class="category">
        <h3>{{title}}分类</h3>
        <!-- 默认插槽 组件传递的结构会出现在这个标签中 -->
        <!-- 当组件没有传递具体结构时显示默认值 -->
        <slot>我是默认值</slot>
    </div>
  
</template>

<script>
    export default {
        name: 'MyCategory',
        props:['title']
    }
</script>
```

App.vue

```vue
<template>
    <div class="container">
        <Category title="美食">
            <img src="https://s3.ax1x.com/2021/01/16/srJlq0.jpg">
        </Category>

        <Category title="饮料" :listData="drink">
            <ul>
                <li v-for="(d,index) in drink" :key="index">{{d}}</li>
            </ul>
        </Category>

        <Category title="电影">
            <video controls src="http://clips.vorwaerts-gmbh.de/big_buck_bunny.mp4"></video>
        </Category>
    </div>
</template>

<script>
    import Category from './components/Category.vue'

    export default {
        name: 'App',
        //注册组件
        components: { Category},
        data() {
            return {
                foods:['烧烤','炸鸡','牛排','小龙虾'],
                drink:['可乐','雪碧','啤酒','加多宝'],
                films:['《拆弹专家》','《复仇者联盟》','《夏洛特烦恼》','《飞驰人生》']
            }
        },
    }
</script>
```

具名插槽

```html
<!-- Category.vue中代码片段 -->
<div class="category">
    <h3>{{title}}分类</h3>
	<!-- 具名插槽 -->
    <slot name="slotName1">我是默认值1</slot>
    <slot name="slotName2">我是默认值2</slot>
</div>


<!-- App.vue中代码片段 -->
<Category title="饮料">
	<!-- 使用具名插槽方式1 slot="插槽名" -->
    <img slot="slotName1" src="#">
    <!-- 使用具名插槽方式2 v-slot:插槽名 -->
    <!-- 注意v-slot只能在template标签中使用 -->
    <template v-slot:slotName2>
        <a href="#">汽水</a>
        <a href="#">奶茶</a>
        <a href="#">咖啡</a>
    </template>
</Category>
```

作用域插槽

实现效果

![image-20220709151255896](C:\Users\uuu\AppData\Roaming\Typora\typora-user-images\image-20220709151255896.png)

Category.vue

```vue
<template>
    <div class="category">
        <h3>{{title}}分类</h3>
        <!-- 作用域插槽 -->
        <slot :foods="foods">我是默认值</slot>
    </div>
  
</template>

<script>
    export default {
        name: 'MyCategory',
        props:['title'],
        data() {
            return {
                foods:['烧烤','炸鸡','牛排','小龙虾'],
            }
        },
    }
</script>
```

App.vue

```vue
<template>
    <div class="container">
        <Category title="游戏">
            <!-- 第一种写法 -->
            <!-- 使用作用域插槽必须要用template标签包裹 -->
            <template scope="xxx">
                <ul>
                    <li v-for="(g,index) in xxx.foods" :key="index">{{g}}</li>
                </ul>
            </template>
        </Category>
        <Category title="游戏">
            <!-- 第二种写法 -->
            <template scope="{foods}">
                <ol>
                    <li v-for="(g,index) in foods" :key="index">{{g}}</li>
                </ol>
            </template>
        </Category>
        <Category title="游戏">
            <!-- 第三种写法（推荐） -->
            <template slot-scope="{foods}">
                <h4 v-for="(g,index) in foods" :key="index">{{g}}</h4>
            </template>
        </Category>
    </div>
</template>

<script>
    import Category from './components/Category.vue'
    export default {
        name: 'App',
        //注册组件
        components: { Category},
    }
</script>
```

总结：

​	1.插槽的作用：让夫组件可以向子组件指定位置插入HTML结构 同时也是一种组件间通信方式 适用于父&rarr;子

​	2.分类：默认插槽、具名插槽、作用域插槽

​	3.作用域插槽的注意点：数据在组件自身,但根据数据生成的结构需要组件的使用者来决定

# 5.vuex

## 5.1.vuex概念

vuex是专门在Vue中实现集中式状态（数据）管理的Vue插件,对vue应用中多个组件的共享状态进行集中式管理（读/写）,也是组件间的通信方式 且适用于任意组件间的通信

vuex的Github地址 https://github.com/vuejs/vuex

<h3>对比vuex和全局事件总线</h3>

![image-20220709154148022](C:\Users\uuu\AppData\Roaming\Typora\typora-user-images\image-20220709154148022.png)

![image-20220709154437491](C:\Users\uuu\AppData\Roaming\Typora\typora-user-images\image-20220709154437491.png)

<h3>什么时候使用vuex</h3>

1.多个组件依赖同一状态 (数据)

2.来自不同组件的行为需要变更同一状态

<h3>vuex原理图</h3>

![vuex](C:\Users\uuu\AppData\Roaming\Typora\typora-user-images\vuex.png)

## 5.2.搭建vuex环境

安装vuex

```shell
#vue2中用vuex3，vue3中用vuex4
npm i vuex@3
```

在src文件夹中创建store文件夹,然后在这个文件夹中创建index.js文件

```js
//该文件用于创建Vuex中最为核心的store
import Vue from 'vue'
import Vuex from 'vuex'

//应用vuex插件之后就多了一个配置项store
Vue.use(Vuex)

//准备actions——用于响应组件中的动作
const actions = {}
//准备mutations——用于操作数据
const mutations = {}
//准备state——用于存储数据
const state = {}

//创建并暴露store
export default new Vuex.Store({
    actions,
    mutations,
    state
})
```

main.js

```js
import Vue from 'vue';
import App from './App.vue';
import store from './store/index'

Vue.config.productionTip = false

new Vue({
    el: "#app",
    render: h=>h(App),
    store,
})
```



案例

![image-20220709210940339](C:\Users\uuu\AppData\Roaming\Typora\typora-user-images\image-20220709210940339.png)

App.vue

```vue
<template>
    <div class="container">
        <Count/>
    </div>
</template>

<script>
    import Count from './components/Count.vue'
    export default {
        name: 'App',
        //注册组件
        components: { Count},
    }
</script>
```

index.js

```js
import Vue from 'vue'
import Vuex from 'vuex'

//应用vuex插件
Vue.use(Vuex)

//准备actions——用于响应组件中的动作
const actions = {
    //actions中的方法名一般小写
    addOdd(context,value){
        if(context.state.sum%2)
            context.commit('ADD',value)
    },
    addWait(context,value){
        setTimeout(()=>{
            context.commit('ADD',value)
        },1000)
    }
}
//准备mutations——用于操作数据
const mutations = {
    //mutatios中的方法名一般大写
    ADD(state,value){
        state.sum += value
    },
    SUB(state,value){
        state.sum -= value
    },
}
//准备state——用于存储数据
const state = {
    sum: 0
}

//创建并暴露store
export default new Vuex.Store({
    actions,
    mutations,
    state
})
```

Count.vue

```vue
<template>
    <div>
        <h1>当前求和为：{{$store.state.sum}}</h1>
        <select v-model="n">
            <option :value="1">1</option>
            <option :value="2">2</option>
            <option :value="3">3</option>
        </select>
        <button @click="increment">+</button>
        <button @click="decrement">-</button>
        <button @click="incrementOdd">奇数再加1</button>
        <button @click="incrementWait">等一等加1</button>
    </div>
</template>

<script>
    export default {
        name: 'MyCount',
        data() {
            return {
                n: 1
            }
        },methods: {
            increment(){
                //可以直接提交
                this.$store.commit('ADD',this.n)
            },
            decrement(){
                this.$store.commit('SUB',this.n)
            },
            incrementOdd(){
                this.$store.dispatch('addOdd',this.n)
            },
            incrementWait(){
                this.$store.dispatch('addWait',this.n)
            }
        },
    }
</script>
```

## 5.3.getters配置项

```js
//省略代码....

const getters = {
    bigSum(state){
        return state.sum*10
    }
}

//创建并暴露store
export default new Vuex.Store({
    actions,
    mutations,
    state,
    getters
})
```

组件中使用

```html
<h1>{{$store.getters.bigSum}}</h1>
```

## 5.4.四个map方法

1.mapState方法：用于帮助我们映射state中的数据为计算属性

```html
<script>
    import { mapState } from 'vuex'
    
    export default {
        //其他配置项...
        computed:{
            /*xuexiao(){
                return this.$store.state.school
            },
            xinming(){
                return this.$store.state.name
            },*/
            //对象写法(相当于上面注释的代码)
            ...mapState({xuexiao:'school',xinming:'name'})
            //数组写法(前提是对象中的key和value相等)
            //如...mapState({school:'school',name:'name'})可以简写为下面形式
            //...mapState['school','name']
        }
    }
</script>
```

2.mapGetters方法：用于帮助我们映射getters中的数据为计算属性

```html
<script>
    import { mapGetters, mapState } from 'vuex'
    
    export default {
        //其他配置项...
        computed:{
            /*bigSum(){
                return this.$store.getters.bigSum
            },*/
            //对象写法(相当于上面注释的代码)
            ...mapGetters({bigSum:'bigSum'})
            //数组写法(前提是对象中的key和value相等)
            ...mapGetters['bigSum']
        }
    }
</script>
```

3.mapMutations方法：用于帮助我们生成与mutations对话的方法,即包含```$store.commit(xxx)```的函数

```html
<script>
    import { mapMutations } from 'vuex'
    
    export default {
        //其他配置项...
        methods:{
            /*increment(){
                this.$store.commit('ADD',this.n)
            },
            decrement(){
                this.$store.commit('SUB',this.n)
            },*/
            //对象写法(相当于上面注释的代码)
            ...mapMutations({increment:'ADD',decrement:'SUB'})
            //数组写法(前提是对象中的key和value相等)
            //...mapMutations['ADD','SUB']
        }
    }
</script>
```

4.mapActionsfa方法：用于帮助我们生成与actions对话的方法,即包含```$store.dispatch(xxx)```的函数

```html
<script>
    import { mapActions } from 'vuex'
    
    export default {
        //其他配置项...
        methods:{
            /*incrementOdd(){
                this.$store.dispatch('addOdd',this.n)
            },
            incrementWait(){
                this.$store.dispatch('addWait',this.n)
            },*/
            //对象写法(相当于上面注释的代码)
            ...mapActions({incrementOdd:'addOdd',incrementWait:'addWait'})
            //数组写法(前提是对象中的key和value相等)
            //...mapActions['addOdd','addWait']
        }
    }
</script>
```

注意：<font color='red'>使用mapMutations和mapMutations方法参数需要在模板绑定事件时传</font> 如下所示

```
<button @click="increment(n)">+</button>
<button @click="decrement(n)">-</button>
<button @click="incrementOdd(n)">奇数再加1</button>
<button @click="incrementWait(n)">等一等加1</button>
```

## 5.5.vuex的模块化

```js
import Vue from 'vue'
import Vuex from 'vuex'

//应用vuex插件
Vue.use(Vuex)

const countOption = {
    namespaced:true,//默认是false
    actions:{
        addOdd(context,value){/*内容略...*/},
        addWait(context,value){/*内容略...*/}
    },
    mutations:{
        ADD(state,value){/*内容略...*/},
        SUB(state,value){/*内容略...*/},
    },
    state:{
        sum: 0
    },
    getters:{
        bigSum(state){/*内容略...*/}
    },
}
const personOption = {
    namespaced:true,
    actions:{/*内容略...*/},
    mutations:{/*内容略...*/},
    state:{
        personList:[
            {id:'001',name:'Jack'},
            {id:'002',name:'Eric'}
        ]
    },
    getters:{/*内容略...*/},
}

//创建并暴露store
export default new Vuex.Store({
    modules:{
        //可以简写countOption，
        countOption:countOption,
        personOption:personOption
    }
})
```

```vue
<script>
    import { mapState,mapMutations,mapActions } from 'vuex'
    export default {
        //其他配置项省略...
        methods: {
            /*increment(){
                this.$store.commit('countOption/ADD',this.n)
            },*/
            ...mapMutations('countOption',{increment:'ADD',decrement:'SUB'}),
            
            /*incrementOdd(){
                 this.$store.dispatch('countOption/addOdd',this.n)
            }*/
            ...mapActions('countOption',{incrementOdd:'addOdd',incrementWait:'addWait'})
        },
        computed:{
            /*sum(){
                return this.$store.state.countOption.sum
            }*/
            ...mapState('countOption',{sum:'sum'}),
            ...mapState('personOption',['personList']),
            
            /*bigSum(){
                return this.$store.getters['countOption/bigSum']
            }*/
            ...mapGettera('countOption',['bigSum']),
        }
    }
</script>
```

# 6.路由

1.路由就是一组key-value的对应关系

2.多个路由需要经过路由器的管理

![image-20220710144456949](C:\Users\uuu\AppData\Roaming\Typora\typora-user-images\image-20220710144456949.png)

## 6.1.路由的基本使用

实现案例

![image-20220710192324014](C:\Users\uuu\AppData\Roaming\Typora\typora-user-images\image-20220710192324014.png)

安装vue-router

```shell
#vue-router@3只能在vue2中使用
#vue-router@4只能在vue3中使用
npm i vue-router@3 
```

在src文件夹中创建文件夹router,然后在这个文件夹中创建index.js

```js
//该文件专门用于创建路由器
import VueRouter from 'vue-router'
//引入组件
import About from '../components/About'
import Home from '../components/Home'

//创建并暴露路由器
export default new VueRouter({
    routes:[
        {
            path: '/about',
            component: About
        },
        {
            path: '/home',
            component: Home
        },
    ]
})
```

main.js

```js
import Vue from 'vue';
import App from './App.vue';
import VueRouter from 'vue-router';
import router from "./router/index";

Vue.config.productionTip = false

Vue.use(VueRouter)

new Vue({
    el: "#app",
    render: h=>h(App),
    router
})
```

App.vue(样式省略)

```vue
<template>
    <div class="container">
        <Banner/>
        <div class="btns">
            <!-- 路由切换 -->
            <router-link to="/about" active-class="active" class="optionAbout">About</router-link>
            <router-link to="/home"  active-class="active" class="optionHome">Home</router-link>
        </div>
        <div class="component">
            <!-- 制定组件的呈现位置 -->
            <router-view></router-view>
        </div>
    </div>
</template>

<script>
    import Banner from './components/Banner'
    export default {
        name: 'App',
        //注册组件
        components: { Banner },
    }
</script>
```

Home.vue和About.vue

```vue
<template>
    <h1>我是Home组件</h1>
</template>

<script>
    export default {
        name: 'MyHome'
    }
</script>

<!--------------------------------------------------------------------------------->

<template>
    <h1>我是About组件</h1>
</template>

<script>
    export default {
        name: 'MyAbout'
    }
</script>

```

使用路由的几个注意点：

​	1.路由组件一般存放在src/pages文件夹中 普通组件放在components文件夹中

​	2.切换路由时,被切换掉的路由默认是被销毁了

​	3.每个组件都有自己的$route属性,里面存放着自己的路由信息

​	4.整个应用只有一个router,通过组件的$router属性可以获得

## 6.2.嵌套路由

```js
routes:[
    {
    	//一级路由
        path: '/about',
        component: About,
        children:[
            {
                //子级路由path属性无需加'/'
                path:'news',
                component: News
            },
            {
                path:'msgs',
                component:Message
            }
        ]
    },
    {...},
    {...},
]
```

在使用router-link标签时的to属性要写完整的路径

```html
<router-link to="/about/news" active-class="active" class="aboutBtn">News</router-link>
<router-link to="/about/msgs" active-class="active" class="aboutBtn">Msgs</router-link>
```

## 6.3.路由传参

<h3>query参数</h3>

```html
<!-- 字符串写法 -->
<!-- 传递query参数 -->
<router-link to="/about/news/detail?id=666&title=hello">News</router-link>
<!-- 传递动态query参数 -->
<router-link :to="`/about/news/detail?id=${m.id}&title=${m.title}`">News</router-link>

<!-- 对象写法 -->
<router-link :to="{
    path: '/about/news/detail',
    query:{
        id: m.id,
        title: m.title
    }
}">
    xxx内容
</router-link>

<!-- 接收query参数 -->
{{$route.query.id}}
{{$route.query.title}}
```

<h3>params参数</h3>

使用params参数需要在创建路由时配置path参数时使用占位符

```js
export default new VueRouter({
    routes:[
        {
        	//一级路由
            name: 'guanyu',
            path: '/about',
            component: About,
            children:[
                {
                    //二级路由
                    name: 'xinwen'
                    path:'news/:id/:title',
                    component: News,
                },
            ]
        },
    ]
})
```



```html
<!-- 字符串写法 -->
<!-- 传递params参数 -->
<router-link to="/about/news/666/hello">News</router-link>
<!-- 传递动态params参数 -->
<router-link to="`/about/news/${m.id}/${m.title}`">News</router-link>

<!-- 对象写法 -->
<!-- 注意：对象写法使用params传递参数时必须使用name，不允许使用path -->
<router-link :to="{
    name: 'xinwen',
    params:{
        id: m.id,
        title: m.title
    }
}">
    xxx内容
</router-link>

<!-- 接收params参数 -->
{{$route.params.id}}
{{$route.params.title}}
```



## 6.4.路由命名

作用：简化路由跳转

```js
export default new VueRouter({
    routes:[
        {
        	//一级路由
            name: 'guanyu',
            path: '/about',
            component: About,
            children:[
                {
                    //二级路由
                    path:'news',
                    component: News,
                    children:[
                        {
                        	//三级路由
                            name: 'xiangqing',
                            path:'detail',
                            component: Detail
                        } 
                    ]
                },
                {...},
                {...}
            ]
        },
        {...},
        {...}
    ]
})
```

使用命名路由在跳转时可以使用name属性,这样就无需写很长的path属性

```html
<router-link :to="{
    name: 'xiangqing',
    query:{
        id: m.id,
        title: m.title
    }
}">
    xxx内容
</router-link>
```

## 6.5.路由的props配置

```js
export default new VueRouter({
    routes:[
        {
            path: '/home',
            component: Home,
            //第一种写法：值为对象，该对象中的所有k-v都会以props的形式传给Home组件
            // props:{a:1,b:100}
            //第二种写法：值为布尔值，若为true，就会把该组件收到的params参数以props的形式传给Home组件
            // props:true
            //第三种写法：值为函数，将函数的返回值对象的k-v以props的形式传给Home组件
            props($route){
                return {id:$route.query.id,title:$route.query.title}
            }
            //第三种写法的结构赋值写法
            // props({query}){
            //     return {id:query.id,title:query.title}
            // }
        },
    ]
})
```

## 6.6.replace属性

\<router-link>标签的replace属性

​	1.作用：控制路由跳转时操作浏览器历史记录的模式

​	2.浏览器的历史记录有两种写入方式

​			push模式：追加历史记录（默认值）

​			replace：替换当前记录

​	3.开启replace模式：

```html
<router-link replace ...>xxx</router-link>
```

## 6.7.编程式路由导航

编程式路由导航不借助\<router-link>实现路由跳转,让路由跳转更加灵活

```html
<button @click='pushShow(m)'>xxx</button>
```

<font color='red'>$router.push</font>

```js
methods:{
	pushShow(m){
		this.$router.push({
			path: '...',
            //path只能配合query使用，不能配合params
			query:{
				id:m.id,
				title:m.title
			}
		})
	}
}
```

<font color='red'>$router.replace</font>

```js
methods:{
	pushShow(m){
		this.$router.replace({
			name: '...',
			//query和params都行
			params:{
				id:m.id,
				title:m.title
			}
		})
	}
}
```

历史记录前进和后退

```js
method:{
	test(){
		this.$router.back() //后退一步
		this.$router.forward() //前进一步
		this.$router.go(num) //num为正数表示前进num步，负数表示后退num步
	}
}
```

## 6.8.缓存路由组件

作用：让不展示的路由保持挂载,不被销毁

```html
<!-- News是组件名 -->
<keep-alive include="News">
	<router-view></router-view>
</keep-alive>

<!-- 缓存多个组件不被销毁 -->
<keep-alive :include="[News,Messages,...]">
	<router-view></router-view>
</keep-alive>
```

## 6.9.两个新的生命周期钩子

```vue
<script>
    export default {
        name: 'MyNews',
        activated(){
        	//路由组件被激活时调用
        },
        deactivated(){
        	//路由组件失活时调用
        },
    }
</script>
```

总结：

​	1.作用：路由组件所独有的钩子,用于捕获路由的激活状态

​	2.具体名字：

​			activated 路由组件被激活时调用

​			deactivated 路由组件失活时调用

## 6.10.路由守卫

<h3>全局前置路由守卫</h3>

```js
//....

//创建路由
const router = new VueRouter({
    routes:[{...},{...},...]
})

//全局前置路由守卫——初始化和每次路由切换前调用
router.beforeEach((to,from,next)=>{
	//to表示切换到哪个组件 from表示从哪个组件切换 next是放行路由切换
	if(to.name === 'xinwen' || to.path === '/message'){
		if(localStorage.getItem('school') === 'zcmu'){
			//如果school是zcmu就放行
			next()
		}else{
			alert('暂无权限')
		}
	}else{
		next()
	}
})

//暴露路由
export default router
```

<h3>全局后置路由守卫</h3>

```js
const router = new VueRouter({
    routes:[
        {	
            name: 'guanyu'
            path: '/about',
            component: About,
         	//meta路由元信息
            meta:{
            	//可以配置些自定义信息
            	isAuth: true,
            	title: '关于'
        	}
        },
        {...},
    ]
})

//全局后置路由守卫——初始化和每次路由切换后调用
router.afterEach((to,from)=>{
	documemnt.title = to.meta.title || 'xxx'
})
```

<h3>独享路由守卫</h3>

独享路由守卫只有前置守卫没有后置守卫

```js
const router = new VueRouter({
    routes:[
        {	
            name: 'guanyu'
            path: '/about',
            component: About,
            meta: {isAuth: true,title: '关于'},
            //配置独享路由守卫，About组件独享的
            beforeEnter:(to,from,next)=>{
            	//......
            }
        },
        {...},
    ]
})
```

<h3>组件内路由守卫</h3>

```html
<!-- 写在组件内 -->
<script>
    export default {
        name: 'MyNews',
        data() { return {/* ... */}},
        beforeRouteEnter (to, from, next) {
            //通过路由规则进入该组件时调用
        },
        beforeRouteLeave (to, from, next) {
            //通过路由规则离开该组件时调用
        }
    }
</script>
```

## 6.11.路由器的工作模式

hash模式 (默认)

​	1.地址中带有#, #后面的时hash值,hash值不会带给服务器

​	2.兼容性好

history模式

​	1.地址中不会出现# 比较美观

​	2.兼容性略差

​	3.应用部署上线时需要后端人员支持,解决刷新页面服务端404问题

```js
const router = new VueRouter({
	mode:'history'，
	routes: [{...},{...},...]
})
```

项目打包命令

```shell
#会生成dist文件夹
npm run build
```

创建简单服务器

```
1.创建文件夹用VSCode打开
2.npm init初始化
3.npm i express，安装express
4.创建server.js文件，编写下面代码
5.创建static文件夹，将dist种的文件拷贝到该文件夹
6.node server启动
7.打开浏览器访问http://localhost:5000
```

```js
const express = require('express')

const app = express()

app.use(express.static(__dirname+'/static'))

/*
app.get('/person',(req,res)=>{
    res.send({
        name:'Jack',
        age: 20
    })
})
*/

app.listen(5000,(err)=>{
    if(!err)
        console.log('服务器启动成功');
})
```

connect-history-api-fallback可以解决history模式刷新页面404问题

```
//安装
npm i connect-history-api-fallback 

//导入
const history = require('connect-history-api-fallback')

//使用
app.use(history())
//要在这行代码之前使用
app.use(express.static(__dirname+'/static'))
```

# 7.VueUI组件库

## 7.1.移动端常用UI组件库 

1. Vant https://youzan.github.io/vant 
2. Cube UI https://didi.github.io/cube-ui 
3. Mint UI http://mint-ui.github.io 

## 7.2 PC 端常用UI组件库 

1. Element UI https://element.eleme.cn 
2. IView UI https://www.iviewui.com

## 7.3.ElementUI简单使用

安装

```
npm i element-ui
```

在main.js中引入

```js
//完整引入
import ElementUI from 'element-ui';
import 'element-ui/lib/theme-chalk/index.css';

Vue.use(ElementUI);
```

接下来CV就行了

![image-20220711141731825](C:\Users\uuu\AppData\Roaming\Typora\typora-user-images\image-20220711141731825.png)

## 7.4 按需引入

安装插件

```shell
npm install babel-plugin-component -D
```

修改babel.config.js

```js
{
  "presets": [["@babel/preset-env", { "modules": false }]],
  "plugins": [
    [
      "component",
      {
        "libraryName": "element-ui",
        "styleLibraryName": "theme-chalk"
      }
    ]
  ]
}
```

只引入Button和Select的话

```js
//按需引入
import {Button,Select} from 'element-ui';

Vue.component(Button.name,Button)
Vue.component(Select.name,Select)
/*
或者写为
Vue.use(Button)
Vue.use(Select)
*/
```

若出现模块找不到就安装模块

如 Error：Cannot find module 'xxx'

```shell
npm i xxx
```

# 8.Vue3

## 8.1创建vue3.0工程

使用vue-cli创建

```shell
#查看vue-cli版本 确保在4.5.0以上
vue -V 或 vue --version
#创建
vue create 项目名
#启动
npm run serve
```

使用vite创建

```shell
#创建工程
npm init vite-app 项目名
#进入工程目录
cd 项目名
#安装依赖
npm install
#运行
npm run dev
```

<h3>vue3工程中的变化</h3>

1.vue中main.js引入的不在是Vue构造函数

```
//vue3引入的不再是Vue的构造函数
//而是名为createApp的工厂函数
import { createApp } from 'vue'
import App from './App.vue'

//创建应用实例对象并挂载
createApp(App).mount('#app')
```

2.vue3组件模板结构中可以没有根标签

```vue
<template>
	<div>xxx</div>
	<div>yyy</div>
	<Hello/>
</template>

<!-- 下面代码省略... -->
```

## 8.2.常用Composition API

### 8.2.1.setup函数

```vue
<template>
  <h3>姓名：{{name}}</h3>
  <h3>年龄：{{age}}</h3>
  <button @click="sayHello">sayHello</button>
</template>

<script>
  //import {h} from 'vue'

  export default {
    name: 'App',
    setup(){
      let name = 'Jack'
      let age = 20

      function sayHello() {
          alert(`大家好,我是${name},我今年${age}岁`)
      }

      //返回对象
      return {
        name,
        age,
        sayHello
      }

      //返回渲染函数 需要引入h (import {h} from 'vue')
      //return ()=>{ return h('h1',name) }
    }
  }
</script>

```

总结：

4. 组件中所用到的：数据、方法等等，均要配置在setup中
5. setup函数的两种返回值：
   1. 若返回一个对象，则对象中的属性、方法, 在模板中均可以直接使用（重点关注！）
   2. 若返回一个渲染函数：则可以自定义渲染内容（了解）
3. 注意点：
   1. 尽量不要与Vue2.x配置混用

      Vue2.x配置（data、methos、computed...）中<font color='red'>可以访问到</font>setup中的属性、方法

      但在setup中<font color='red'>不能访问到</font>Vue2.x配置（data、methos、computed...）

      如果有重名, setup优先
   2. setup不能是一个async函数

### 8.2.2.ref函数

```vue
<template>
  <h3>姓名：{{name}}</h3>
  <h3>年龄：{{age}}</h3>
  <h3>工作类型：{{job.type}}</h3>
  <h3>工作薪水：{{job.salary}}</h3>
  <button @click="changeMsg">修改信息</button>
</template>

<script>
  import { ref } from '@vue/reactivity'

  export default {
    name: 'App',
    setup(){
      // 这样写的数据是非响应式的
      // let name = 'Jack'
      // let age = 20
      let name = ref('Jack')
      let age = ref(20)
      let job = ref({
          type:'前端工程师',
          salary: '20K'
      })

      function changeMsg(){
        name.value = 'Eric'
        age.value = '18'
        job.value.type = '后端工程师'
      }

      //返回对象
      return {
        name,
        age,
        changeMsg
      }
    }
  }
</script>
```

总结：

​	1.作用: 定义一个响应式的数据

​	2.语法: ```const xxx = ref(initValue)``` 

​			创建一个包含响应式数据的引用对象（reference对象，简称ref对象）

​			JS中操作数据： ```xxx.value```

​			模板中读取数据: 不需要.value，直接：```<div>{{xxx}}</div>```

​			备注：

​				接收的数据可以是：基本类型、也可以是对象类型

​				基本类型的数据：响应式依然是靠``Object.defineProperty()``的```get```与```set```完成的

​				对象类型的数据：内部求助了Vue3.0中的一个新函数—— ```reactive```函数

### 8.2.3.reactive函数

```vue
<template>
  <h3>工作类型：{{job.type}}</h3>
  <h3>工作薪水：{{job.salary}}</h3>
  <h3>工作薪水：{{job.a.b.c}}</h3>
  <h3>个人爱好：{{hobby}}</h3>
  <button @click="changeMsg">修改信息</button>
</template>

<script>
  import { reactive, ref } from '@vue/reactivity'

  export default {
    name: 'App',
    setup(){
      //reactive只能用于引用数据类型 
      //不能用于基本数据类型
      let job = reactive({
          type:'前端工程师',
          salary: '20K',
          a:{
            b:{
                c: 666
              }
          }
      })

      let hobby = reactive(['唱歌','跳舞','篮球'])

      function changeMsg(){
        job.type = '后端工程师'
        job.salary = '30k'
        job.a.b.c = 888
        hobby[0] = 'rapper'
      }

      //返回对象
      return {
        job,
        hobby,
        changeMsg
      }
    }
  }
</script>
```

总结：

​	1.作用: 定义一个对象类型的响应式数据（基本类型不要用它，要用```ref```函数）

​	2.语法：```const 代理对象= reactive(源对象)```接收一个对象（或数组）返回一个代理对象

​	3.reactive定义的响应式数据是“深层次的”

​	4.内部基于 ES6 的 Proxy 实现，通过代理对象操作源对象内部数据进行操作

### 8.2.4.Vue3响应式原理

回顾Vue2中的响应式原理

- 实现原理：

  ​	对象类型：通过```Object.defineProperty()```对属性的读取、修改进行拦截（数据劫持）

  ​	数组类型：通过重写更新数组的一系列方法来实现拦截

- 存在问题：

  ​	新增属性、删除属性, 界面不会更新

  ​	直接通过下标修改数组, 界面不会自动更新



模拟Vue3中的响应式

```html
<script>
    //源数据
    let person = {
        name: 'Jack',
        sex: '男'
    }

    //p是代理对象 person是源数据
    const p = new Proxy(person,{
        get(target,propName){
            //target是被代理的对象，propName是属性名
            console.log(`person的${propName}属性被读取了`);
            //return target[propName]
            return Reflect.get(target,propName)
        },
        set(target,propName,value){
            console.log(`person的${propName}属性被修改了,修改的值是${value}`);
            //target[propName] = value
            Reflect.set(target,propName,value)
        },
        deleteProperty(target,propName){
            console.log(`person的${propName}属性被删除了`);
            //return delete target[propName]
            return Reflect.deleteProperty(target,propName)
        }
    })
</script>
```

实现原理: 

- 通过Proxy（代理）:  拦截对象中任意属性的变化 包括：属性值的读写、属性的添加、属性的删除等。
- 通过Reflect（反射）:  对源对象的属性进行操作。

### 8.2.5.reactive和ref的对比

1.从定义数据角度对比：

​	ref用来定义：基本类型数据

​	reactive用来定义：对象（或数组）类型数据

​	备注：ref也可以用来定义对象（或数组）类型数据, 它内部会自动通过reactive转为代理对象

2.从原理角度对比：

​	ref通过``Object.defineProperty()``的```get```与```set```来实现响应式（数据劫持）

​	reactive通过使用Proxy来实现响应式（数据劫持）, 并通过Reflec操作源对象内部的数据

3.从使用角度对比：

​	ref定义的数据：操作数据需要.value, 读取数据时模板中直接读取不需要.value

​	reactive定义的数据：操作数据与读取数据：均不需要.value

### 8.2.6.setup的两个注意点

1.setup执行的时机

​	在beforeCreate之前执行一次, this是undefined

2.setup的参数

​	props：值为对象,包含：组件外部传递过来, 且组件内部声明接收了的属性

​	context：上下文对象

​		1.attrs: 值为对象, 包含：组件外部传递过来, 但没有在props配置中声明的属性,相当于 ```this.$attrs```

​		2.slots: 收到的插槽内容, 相当于 ```this.$slots```

​		3.emit: 分发自定义事件的函数, 相当于 ```this.$emit```

### 8.2.7.计算属性

```vue
<template>
    姓：<input type="text" placeholder="请输入姓" v-model="person.firstName"> <br><br>
    名：<input type="text" placeholder="请输入名" v-model="person.lastName"> <br><br>
    <h1>全名：{{fullName}}</h1>
</template>

<script>
    import { reactive } from '@vue/reactivity'
    import { computed } from '@vue/runtime-core'

    export default {
        name: 'Demo',
        setup() {
            //数据
            let person = reactive({
                firstName: '张',
                lastName: '三'
            })
            //计算属性 简写形式
            let fullName = computed(()=>{
                return person.firstName + '-' + person.lastName
            })
            //计算属性 完整写法
            /* 
            let fullName = computed({
                get(){...},
                set(value){...},
            }) 
            */
            return{
                person,
                fullName
            }
        }
    }
</script>

```

### 8.2.8监视属性

```vue
<script>
    import { ref } from '@vue/reactivity'
    import { watch } from '@vue/runtime-core'
    
    export default {
        name: 'Demo',
        setup() {
            let sum = ref(0)
            let messgae = ref('hello')
            let person = {
                name: 'Jack',
                job:{
                    type: '前端工程师',
                    salary: '20K'
                }
            }

            //情况一：监视ref定义的属性
            watch(sum,(newVal,oldVal)=>{
                console.log('sum的值改变了',newVal,oldVal);
            },{immediate:true})

            //情况二：同时监视多个ref定义的属性
            watch([sum,messgae],(newVal,oldVal)=>{
                //同时监视多个属性时newVal和oldVal是数组
                console.log('sum或message的值改变了',newVal,oldVal);
            })

            //情况三：监视reactive定义的响应数据的全部属性
            watch(person,(newVal,oldVal)=>{
                //注意：
                //  1.无法获取oldVal （oldVal和newVal的值相同）
                //  2.强制开启深度监视 （deep:false无效）
                console.log('person变化了',newVal,oldVal);
            })

            //情况四：监视reactive所定义的响应式数据的某个属性
            watch(()=>person.name,(newVal,oldVal)=>{
                console.log('person变化了',newVal,oldVal);
            })

            //情况五：监视reactive所定义的响应式数据的某些属性
            watch([()=>person.name,()=>person.age],(newVal,oldVal)=>{
                console.log('person变化了',newVal,oldVal);
            })

            //特殊情况：监视reactive所定义的响应式数据的对象类型属性
            watch(()=>person.job,(newVal,oldVal)=>{
                console.log('person变化了',newVal,oldVal);
            },{deep:true})

            return{
                sum,messgae,person
            }
        }
    }
</script>
```

两个小坑：

- 监视reactive定义的响应式数据时：oldValue无法正确获取、强制开启了深度监视（deep配置失效）
- 监视reactive定义的响应式数据中某个属性时：deep配置有效

<h3>watchEffect函数</h3>

```js
<script>
    import { ref } from '@vue/reactivity'
    import { watchEffect } from '@vue/runtime-core'
    
    export default {
        name: 'Demo',
        setup() {
            let sum = ref(0)
            let person = {
                name: 'Jack',
                job:{
                    type: '前端工程师',
                    salary: '20K'
                }
            }

            watchEffect(()=>{
                const x1 = sum.value
                const x2 = person.job.salary
                console.log('sum或salary的值变化了');
            })

            return{
                sum,person
            }
        }
    }
</script>
```

总结：	

​	watch的套路是：既要指明监视的属性，也要指明监视的回调

​	watchEffect的套路是：不用指明监视哪个属性，监视的回调中用到哪个属性，那就监视哪个属性

​	watchEffect有点像computed：

​		但computed注重的计算出来的值（回调函数的返回值），所以必须要写返回值

​		而watchEffect更注重的是过程（回调函数的函数体），所以不用写返回值

### 8.2.9.Vue3生命周期

![image-20220712164748251](C:\Users\uuu\AppData\Roaming\Typora\typora-user-images\image-20220712164748251.png)

```vue
<script>
    import { onMounted, onUnmounted, onUpdated, ref } from 'vue'
    
    export default {
        name: 'Demo',
        setup() {
            let sum = ref(0)
            
            //通过组合式API的形式使用生命周期钩子
            onMounted(()=>{
                console.log('===mounted===');
            })
            onUpdated(()=>{
                console.log('===updated===');
            })
            onUnmounted(()=>{
                console.log('===unmounted===');
            })

            return{
                sum
            }
        }
    }
</script>
```



Vue3.0中可以继续使用Vue2.x中的生命周期钩子, 但有有两个被更名：

- ```beforeDestroy```改名为 ```beforeUnmount```
- ```destroyed```改名为 ```unmounted```

Vue3.0也提供了 组合式API（写在setup函数中） 形式的生命周期钩子, 与Vue2.x中钩子对应关系如下：

- `beforeMount` ===>`onBeforeMount`
- `mounted`===>`onMounted`
- `beforeUpdate`===>`onBeforeUpdate`
- `updated` ===>`onUpdated`
- `beforeUnmount`= ==>`onBeforeUnmount`
- `unmounted` ===>`onUnmounted`

### 8.2.10.自定义hook函数

案例：显示鼠标坐标

![image-20220712173713069](C:\Users\uuu\AppData\Roaming\Typora\typora-user-images\image-20220712173713069.png)

普通写法

```vue
<template>
    <h1>当前鼠标的坐标：X{{point.x}} Y{{point.y}}</h1>
</template>

<script>
    import { onBeforeUnmount, onMounted, reactive, } from 'vue'
    
    export default {
        name: 'Demo',
        setup() {
            let point = reactive({
                x:0,
                y:0
            })
            
            function savePoint(event){
                point.x = event.pageX
                point.y = event.pageY
            }

            onMounted(()=>{
                window.addEventListener('click',savePoint)
            })

            onBeforeUnmount(()=>{
                window.removeEventListener('click',savePoint)
            })
        

            return{
                point
            }
        }
    }
</script>
```

将setup中关于显示鼠标坐标的组合式API封装成hook函数

首先再src文件夹下创建hooks文件夹,在该文件夹中创建usePoint.js

```js
import { onBeforeUnmount, onMounted, reactive, } from 'vue'

export default function(){
    let point = reactive({
        x:0,
        y:0
    })
    
    function savePoint(event){
        point.x = event.pageX
        point.y = event.pageY
    }

    onMounted(()=>{
        window.addEventListener('click',savePoint)
    })

    onBeforeUnmount(()=>{
        window.removeEventListener('click',savePoint)
    })
    
    return point
}
```

在组件中使用hook函数

```vue
<template>
    <h1>当前鼠标的坐标：X{{point.x}} Y{{point.y}}</h1>
</template>

<script>
    import usePoint from '../hooks/usePoint'
    
    export default {
        name: 'Demo',
        setup() {
            //其他功能的数据、函数...

            //使用自定义的hook函数
            let point = usePoint()
        
            return{
                point
            }
        }
    }
</script>
```

总结：

​	1.什么是hook？

​			本质是一个函数，把setup函数中使用的Composition API进行了封装

​	2.自定义hook的优势: 复用代码, 让setup中的逻辑更清楚易懂

### 8.2.11.toRef和toRefs函数

```vue
<script>
    import { reactive, toRef, toRefs } from 'vue'

    export default {
        name: 'Demo',
        setup() {
            let person = reactive({
                name: 'Jack',
                age: 20,
                job:{
                    type: '前端工程师',
                    salary: '20K'
                }
            })
            return{
                person,
                name: toRef(person,'name'),
                age: toRef(person,'age'),
                type: toRef(person.job,'type'),
                salary: toRef(person.job,'salary'),
                // ...toRefs(person)
            }
        },
    }
</script>
```

总结：

- 作用：创建一个 ref 对象，其value值指向另一个对象中的某个属性
- 语法：```const name = toRef(person,'name')```
- 应用:   要将响应式对象中的某个属性单独提供给外部使用时


- 扩展：```toRefs``` 与```toRef```功能一致，但可以批量创建多个 ref 对象，语法：```toRefs(person)```

## 8.3.其他Composition API

### 8.3.1.shallowRef和shallowReactive
1.shallowReactive：只处理对象最外层属性的响应式（浅响应式）

2.shallowRef：只处理基本数据类型的响应式, 不进行对象的响应式处理

什么时候使用?
-  如果有一个对象数据，结构比较深, 但变化时只是外层属性变化 ===> shallowReactive
-  如果有一个对象数据，后续功能不会修改该对象中的属性，而是生新的对象来替换 ===> shallowRef

### 8.3.2.readonly和shallowReadonly

```js
let person = reactive({
    name: 'Jack',
    age: 20,
    job:{
        type: '前端工程师',
        salary: '20K'
    }
})

//person中所有的属性都变成只读的
person = readonly(person)
//person中浅层次的属性变成只读的
person = shallowReadonly(person)
```

总结：

​	1.readonly: 让一个响应式数据变为只读的（深只读）

​	2.shallowReadonly：让一个响应式数据变为只读的（浅只读）

​	3.应用场景: 不希望数据被修改时

### 8.3.3.toRaw和markRaw

```js
let person = reactive({
    name: 'Jack',
    age: 20,
    job:{
        type: '前端工程师',
        salary: '20K'
    }
})

//将响应式的数据变为普通数据
const p = toRow(person)

let car = {name:"宝马",price:'40W'} 
//标记一个对象，它永远不会再成为响应式对象
p.car = markRaw(car)

```

总结：

1.toRaw：

- 作用：将一个由reactive生成的响应式对象转为普通对象
- 使用场景：用于读取响应式对象对应的普通对象, 对这个普通对象的所有操作, 不会引起页面更新

2.markRaw：

- 作用：标记一个对象，使其永远不会再成为响应式对象

- 应用场景:

  1. 有些值不应被设置为响应式的，例如复杂的第三方类库等
  2. 当渲染具有不可变数据源的大列表时，跳过响应式转换可以提高性能

  

### 8.3.4.customRef

作用：创建自定义的ref,并对其依赖项目跟踪和更新触发进行显示控制

实现案例：上面的输入框修改1秒后下面的输入框改变（实现防抖效果）

![image-20220712205326336](C:\Users\uuu\AppData\Roaming\Typora\typora-user-images\image-20220712205326336.png)

```vue
<template>
    <input type="text" v-model="keyWord">
    <br><br>
    <input type="text" :value="keyWord">
</template>

<script>
  import { customRef } from 'vue'

  export default {
    name: 'App',
    setup(){
      //自定义ref函数
      function myref(value,delay){
        let timer
        //通过customRef去实现自定义
        return customRef((track,trigger)=>{
          return {
            get(){
              //通知vue追踪value的变化
              track()
              return value
            },
            set(newValue){
              clearTimeout(timer) //实现防抖
              timer = setTimeout(() => {
                value = newValue
                //通知vue重新解析模板
                trigger()  
              }, delay);
            }
          }
        })
      }

      let keyWord = myref('hello',1000)

      return{
        keyWord
      }
    }
  }
</script>
```

### 8.3.5.provide和inject

作用：实现祖与后代组件间通信

套路：父组件有一个 `provide` 选项来提供数据，后代组件有一个 `inject` 选项来开始使用这些数据

具体写法：

1. 祖组件中：

   ```js
   import { provide } from 'vue'
   //...
   setup(){
   	......
       let car = reactive({name:'奔驰',price:'40万'})
       provide('car',car)
       ......
   }
   ```

2. 后代组件中：

   ```js
   import { inject } from 'vue'
   //...
   setup(props,context){
   	......
       const car = inject('car')
       return {car}
   	......
   }
   ```

### 8.3.6.响应式数据的判断

- isRef: 检查一个值是否为一个 ref 对象
- isReactive: 检查一个对象是否是由 `reactive` 创建的响应式代理
- isReadonly: 检查一个对象是否是由 `readonly` 创建的只读代理
- isProxy: 检查一个对象是否是由 `reactive` 或者 `readonly` 方法创建的代理

### 8.3.7.组合式API的优点

我们可以更加优雅的组织我们的代码,函数。让相关功能的代码更加有序的组织在一起

<div style="width:420px;height:340px;overflow:hidden;display:inline-block;">
    <img src="https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/bc0be8211fc54b6c941c036791ba4efe~tplv-k3u1fbpfcp-watermark.image"style="height:360px"/>
</div>
<div style="width:350px;height:340px;overflow:hidden;display:inline-block;">
    <img src="https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/6cc55165c0e34069a75fe36f8712eb80~tplv-k3u1fbpfcp-watermark.image"style="height:360px"/>
</div>

## 8.4新的组件

### 8.4.1.Fragment

在Vue2中: 组件必须有一个根标签

在Vue3中: 组件可以没有根标签, 内部会将多个标签包含在一个Fragment虚拟元素中

好处: 减少标签层级, 减小内存占用

### 8.4.2.Teleport

什么是Teleport？—— Teleport 是一种能够将我们的组件html结构移动到指定位置的技术

```vue
<teleport to="移动位置">
	<div v-if="isShow" class="mask">
		<div class="dialog">
			<h3>我是一个弹窗</h3>
			<button @click="isShow = false">关闭弹窗</button>
		</div>
	</div>
</teleport>
```

### 8.4.3.Suspense

等待异步组件时渲染一些额外内容，让应用有更好的用户体验

使用步骤：

- 异步引入组件

  ```js
  import {defineAsyncComponent} from 'vue'
  const Child = defineAsyncComponent(()=>import('./components/Child.vue'))
  ```

- 使用```Suspense```包裹组件，并配置好```default``` 与 ```fallback```

  ```vue
  <template>
  	<div class="app">
  		<h3>我是App组件</h3>
  		<Suspense>
  			<template v-slot:default>
  				<Child/>
  			</template>
  			<template v-slot:fallback>
  				<h3>加载中.....</h3>
  			</template>
  		</Suspense>
  	</div>
  </template>
  ```

## 8.5其他

### 8.5.1.全局API的转移

Vue 2.x 有许多全局 API 和配置 例如注册全局组件、注册全局指令等

Vue3.0中对这些API做出了调整：

- 将全局的API，即：```Vue.xxx```调整到应用实例（```app```）上

  | 2.x 全局 API（```Vue```） | 3.x 实例 API (`app`)                        |
  | ------------------------- | ------------------------------------------- |
  | Vue.config.xxxx           | app.config.xxxx                             |
  | Vue.config.productionTip  | <strong style="color:#DD5145">移除</strong> |
  | Vue.component             | app.component                               |
  | Vue.directive             | app.directive                               |
  | Vue.mixin                 | app.mixin                                   |
  | Vue.use                   | app.use                                     |
  | Vue.prototype             | app.config.globalProperties                 |

### 8.5.2.其他改变

1.data选项应始终被声明为一个函数。

2.过度类名的更改：

- Vue2.x写法

  ```css
  .v-enter,
  .v-leave-to {
    opacity: 0;
  }
  .v-leave,
  .v-enter-to {
    opacity: 1;
  }
  ```

- Vue3.x写法

  ```css
  .v-enter-from,
  .v-leave-to {
    opacity: 0;
  }
  
  .v-leave-from,
  .v-enter-to {
    opacity: 1;
  }
  ```

3.移除keyCode作为 v-on 的修饰符，同时也不再支持```config.keyCodes```

4.移除v-on.native修饰符

- 父组件中绑定事件

  ```vue
  <my-component
    v-on:close="handleComponentEvent"
    v-on:click="handleNativeClickEvent"
  />
  ```

- 子组件中声明自定义事件

  ```vue
  <script>
    export default {
      emits: ['close']
    }
  </script>
  ```

5.移除过滤器（filter）

6......