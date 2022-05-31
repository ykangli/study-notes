# Vue笔记

------

## 模板语法

### 插值语法

语法: {{xxx}} ，xxxx 会作为 js 表达式解析  

功能: 用于解析**标签体内容**  

### 指令语法

功能: 解析**标签属性、解析标签体内容、绑定事件**

举例：v-bind:href = 'xxxx' ，xxxx 会作为 js 表达式被解析

说明：Vue 中有有很多的指令，此处只是用 v-bind 举个例子  

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <title>Title</title>
    <script type="text/javascript" src="../js/vue.js"></script>
    <meta http-equiv="Content-Type" content="text/html;charset=utf-8">
</head>
<body>
<!--容器-->
<div id = "root">
    <h1>Hello,{{name}}</h1>
    <hr/>
    <h1>指令语法</h1>
    <a v-bind:href="school.url">点我去{{school.name}}学习</a>
</div>
<script type="text/javascript">
    Vue.config.productionTip = false;//阻止Vue在启动时生成生产提示

    //创建Vue实例
    new Vue({
        el:'#root',//el用于制定当前的Vue实例为哪个容器服务
        data:{
            name:'Allen',
            school:{
                name:'NWU',
                url:'http://www.baidu.com'
            }
        }
    })
</script>
</body>
</html>
```

## 数据绑定

取data中的数据到页面展示

### 单向数据绑定

1. 语法：`v-bind:href ="xxx" `或简写为 `:href`
2. 特点：**数据只能从 data 流向页面**  

![image-20220519170933566](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220519170933566.png)

### 双向数据绑定

1. 语法：`v-mode:value="xxx" `或简写为 `v-model="xxx"`
2. 特点：数据不仅能从 data 流向页面，还能从页面流向 data  

![image-20220519172144481](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220519172144481.png)

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <meta charset="UTF-8">
    <script type="text/javascript" src="../js/vue.js"></script>
</head>
<body>
<div id="demo">
    单向数据绑定：<input type="text" v-bind:value="age"><br>
    双向数据绑定：<input type="text" v-model="age"><br>
</div>
</body>
<script type="text/javascript">
    new Vue({
        el: '#demo',
        data: {
            name: 'Allen',
            age: '18'
        }
    })
</script>
</html>
```

🔥🔥备注：

双向绑定一般都应用在表单元素上（如：input、select等）

`v-model:value `可以简写为` v-model`，因为`v-model`默认收集的就是`value`值

## el和data的两种写法

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <script type="text/javascript" src="../js/vue.js"></script>
</head>
<body>
<div id="demo">
    <h1>Hello,{{name}}</h1>
    <h1>地址为：{{address}}</h1>
</div>
<script type="text/javascript">

    //el两种写法
    /*let vue = new Vue({
        // el: '#demo',//第一种写法
        data: {
            name: 'Allen',
            age: '18'
        }
    });
    console.log(vue)
    vue.$mount('#demo') //第二种写法
    */

    //data的两种写法
    new Vue({
        el: '#demo',
        //data的第一种写法，对象式
        /*data: {
            name: 'Allen',
            age: '18'
        }*/

        //data的第一种写法，函数式
        data(){
           return{
               name:'Allen'
               address: 'Xian'
           }
        }
    });
</script>
</body>
</html>
```

🔥🔥总结：

1. 后面使用组件时，data必须使用函数式，否则会报错
2. 由Vue管理的函数，一定不要写箭头函数，一旦写了箭头函数this就不再是Vue实例了

## 理解MVVM模型

- M：模型(Model) ：对应 data 中的数据
- V：视图(View) ：模板
- VM：视图模型(ViewModel) ： Vue 实例对象  

<img src="https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220519204657912.png" alt="image-20220519204657912" style="zoom:70%;" />

<img src="https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220519204714472.png" alt="image-20220519204714472" style="zoom: 60%;" />

- ViewModel（Vue对象实例）将View与Model联系起来

- data中所有的属性，最后都出现在了vm身上。

- vm身上所有的属性，及Vue原型上所有属性，在Vue模板中都可以直接使用。

  ```html
  <!DOCTYPE html>
  <html lang="en">
  <head>
      <meta charset="UTF-8">
      <title>Title</title>
      <script type="text/javascript" src="../js/vue.js"></script>
  </head>
  <body>
  <div id="demo">
      <h1>学校地址：{{address}}}</h1>
      <h1>{{_c}}</h1>
      <h1>{{$options}}</h1>
  </div>
  <script type="text/javascript">
      let vm = new Vue({
          el:'#demo',
          data:{
              name:'Allen',
              address:'Xian'
          }
      });
      console.log(vm)
  </script>
  </body>
  </html>
  ```

  ![image-20220519210826060](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220519210826060.png)

## 数据代理

### Object.defineProperty方法

`Object.defineProperty()` 方法会直接在一个对象上定义一个新属性，或者修改一个对象的现有属性，并返回此对象。

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <script type="text/javascript" src="../js/vue.js"></script>
</head>
<body>
<div id="demo">
    <h1>Hello,{{name}}</h1>
    <h1>You address is {{address}}</h1>
</div>
<script type="text/javascript">
    let number = 18
    let person = new Vue({
        el: '#demo',
        data: {
            name: 'Allen',
            address: 'Xian'
        }
    });
    Object.defineProperty(person, 'age', {
        // value:18,
        // enumerable:true, //控制属性是否可以枚举，默认值是false
        // writable:true, //控制属性是否可以被修改，默认值是false
        // configurable:true //控制属性是否可以被删除，默认值是false

        //当有人读取person的age属性时，get函数(getter)就会被调用，且返回值就是age的值
        get() {
            console.log('有人读取了age属性')
            return number
        },

        //当有人修改person的age属性时，set函数(setter)就会被调用，且会收到修改的具体值
        set(value) {
            console.log('有人修改了age属性，且值为', value)
            number = value
        }
    })
    console.log(person)
</script>
</body>
</html>
```

### 何为数据代理

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <script type="text/javascript" src="../js/vue.js"></script>
</head>
<body>
<script type="text/javascript">
    let obj = {x: 200}
    let obj1 = {y: 999}

    <!-- 数据代理：通过一个对象代理对另一个对象中属性的操作（读/写）
    通过obj1改变obj的属性
    -->
    Object.defineProperty(obj1, 'x', {
        get() {
            return obj.x
        },
        set(value) {
            obj.x = value
        }
    })
</script>
</body>
</html>
```

### Vue中的数据代理

![image-20220520195928589](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220520195928589.png)

1. Vue中的数据代理：通过vm对象来代理data对象中属性的操作（读/写）

2. Vue中数据代理的好处：更加方便的操作data中的数据

3. 基本原理：

   通过Object.defineProperty()把data对象中所有属性添加到vm上。

   为每一个添加到vm上的属性，都指定一个getter/setter。

   在getter/setter内部去操作（读/写）data中对应的属性。

## 事件处理

### 事件的基本使用（监听事件）

可以用 `v-on` 指令监听 DOM 事件，并在触发时运行一些 JavaScript 代码。

🔥下面例子中，**点击**是一个事件，当这个点击事件触发时，运行showInfo方法

`v-on:xxx` 简写为` @xxx`

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <script type="text/javascript" src="../js/vue.js"></script>
    <title>Title</title>
</head>
<body>
<!-- 
        事件的基本使用：
            1.使用v-on:xxx 或 @xxx 绑定事件，其中xxx是事件名；
            2.事件的回调需要配置在methods对象中，最终会在vm上；
            3.methods中配置的函数，不要用箭头函数！否则this就不是vm了；
            4.methods中配置的函数，都是被Vue所管理的函数，this的指向是vm 或 组件实例对象；
            5.@click="demo" 和 @click="demo($event)" 效果一致，但后者可以传参；
		-->
<div id="demo">
    <h1>Hello,{{name}}</h1>
    <button v-on:click="showInfo1">Click Here</button>
    <button @click="showInfo2($event, 66)">Click Here</button>
</div>
<script type="text/javascript">
    Vue.config.productionTip = false;//阻止Vue在启动时生成生产提示

    //创建Vue实例
    let vm = new Vue({
        el: '#demo',//el用于制定当前的Vue实例为哪个容器服务
        data: {
            name: 'Allen',
            school: {
                name: 'NWU',
                url: 'http://www.baidu.com'
            }
        },
        methods: {
            showInfo1() {
                alert('学长，你好！')
                console.log(this) //此处的this是vm
            },
            showInfo2(event, number) {
                console.log(event, number)
                alert('学姐，你好！')
            },
        }
    });
</script>
</body>
</html>
```

### 事件修饰符

🔥🔥事件修饰符顾名思义就是对事件进行相应的处理

在事件处理程序中调用 `event.preventDefault()` 或 `event.stopPropagation()` 是非常常见的需求。尽管我们可以在方法中轻松实现这点，但更好的方式是：方法只有纯粹的数据逻辑，而不是去处理 DOM 事件细节。

为了解决这个问题，Vue.js 为 `v-on` 提供了**事件修饰符**。之前提过，修饰符是由点开头的指令后缀来表示的。

- `.stop`
- `.prevent`
- `.capture`
- `.self`
- `.once`
- `.passive`

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <script type="text/javascript" src="../js/vue.js"></script>

    <style>
        * {
            margin-top: 20px;
        }

        .demo1 {
            height: 50px;
            background-color: aqua;
        }

        .demo2 {
            height: 50px;
            background-color: bisque;
        }
    </style>
</head>
<body>

<div id="demo">
    <!--阻止默认事件-->
    <a :href="school.url" @click.prevent="showInfo1">点我</a>

    <!--阻止冒泡-->
    <div class="demo1" @click="showInfo1">
        <button @click.stop="showInfo1">点我提示消息</button>
    </div>

    <!--事件只触发一次  本来按钮只要点击就能继续触发-->
    <div class="demo2">
        <button @click.once="showInfo1">点我提示消息</button>
    </div>

    <!--以下不常用，视频里有-->
    <!--使用事件的捕获模式-->
    <!-- 只有event.target是当前操作的元素时才触发事件； -->
    <!-- 事件的默认行为立即执行，无需等待事件回调执行完毕； -->
</div>

<script type="text/javascript">
    Vue.config.productionTip = false;//阻止Vue在启动时生成生产提示

    //创建Vue实例
    let vm = new Vue({
        el: '#demo',//el用于制定当前的Vue实例为哪个容器服务
        data: {
            name: 'Allen',
            school: {
                name: 'NWU',
                url: 'http://www.baidu.com'
            }
        },
        methods: {
            showInfo1() {
                alert('学长，你好！')
                console.log(this) //此处的this是vm
            },
            showInfo2(event, number) {
                console.log(event, number)
                alert('学姐，你好！')
            },
        }
    });
</script>
</body>
</html>
```

### 键盘事件

按下键盘触发某一事件

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>键盘事件</title>
    <script type="text/javascript" src="../js/vue.js"></script>
</head>
<body>
<!--
			1.Vue中常用的按键别名：
				回车 => enter
				删除 => delete (捕获“删除”和“退格”键)
				退出 => esc
				空格 => space
				换行 => tab (特殊，必须配合keydown去使用)
				上 => up
				下 => down
				左 => left
				右 => right

			2.Vue未提供别名的按键，可以使用按键原始的key值去绑定，但注意要转为kebab-case（短横线命名）

			3.系统修饰键（用法特殊）：ctrl、alt、shift、meta
				(1).配合keyup使用：按下修饰键的同时，再按下其他键，随后释放其他键，事件才被触发。
				(2).配合keydown使用：正常触发事件。

			4.也可以使用keyCode去指定具体的按键（不推荐）

			5.Vue.config.keyCodes.自定义键名 = 键码，可以去定制按键别名
		-->
<!-- 准备好一个容器-->
<div id="demo">
    <h2>欢迎来到{{name}}学习</h2>
    <input type="text" placeholder="按下回车键显示输入1" @keyup.enter="showInfo">
    <input type="text" placeholder="按下回车键显示输入2" @keyup.ctrl.y="showInfo">
</div>

<script type="text/javascript">
    Vue.config.productionTip = false //阻止 vue 在启动时生成生产提示。
    Vue.config.keyCodes.huiche = 13 //定义了一个别名按键
    
    new Vue({
        el: '#demo',
        data: {
            name: 'Allen',
        },
        methods: {
            showInfo(e) {
                console.log(e.key, e.keyCode)
            }
        }
    })
</script>
</body>
</html>
```

## 计算属性与监视属性

### 计算属性

Vue认为，data中的就是属性。data中key为属性名，value为属性值

案例：实现两个输入框联动组合

<img src="https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220530112233183.png" alt="image-20220530112233183" style="zoom:67%;" />

实现方式一：插值语法实现

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>姓名案例_插值语法实现</title>
    <script type="text/javascript" src="../js/vue.js"></script>
</head>
<body>
<div id="demo">
    姓：<input type="text" v-model="firstName"> <br/><br/>
    名：<input type="text" v-model="lastName"> <br/><br/>
    全名：<span>{{firstName}}-{{lastName}}</span>
</div>
</body>

<script type="text/javascript">
    Vue.config.productionTip = false //阻止 vue 在启动时生成生产提示。

    new Vue({
        el: '#demo',
        data: {
            firstName: '张',
            lastName: '三'
        }
    })
</script>
</html>
```

实现方法二：methods实现

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>姓名案例_methods实现</title>
    <script type="text/javascript" src="../js/vue.js"></script>
</head>
<body>
<div id="demo">
    姓:<input type="text" v-model="firstName"><br>
    名:<input type="text" v-model="lastName"><br>
    全民:<span style="background-color: bisque">{{fullName()}}</span>
</div>
</body>

<script type="text/javascript">
    Vue.config.productionTip = false //阻止 vue 在启动时生成生产提示。

    new Vue({
        el: '#demo',
        data: {
            firstName: '张',
            lastName: '三'
        },
        methods: {
            fullName() {
                console.log('@---fullName')
                return this.firstName + '-' + this.lastName
            }
        },
    })
</script>
</html>
```

**下面利用计算属性实现：**

1. 要显示的数据不存在，要通过计算得来。
2. 在 computed 对象中定义计算属性。
3. 在页面中使用{{方法名}}来显示计算的结果。  

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>姓名案例_计算属性实现</title>
    <script type="text/javascript" src="../js/vue.js"></script>
</head>
<body>
<div id="demo">
    姓:<input type="text" v-model="firstName"><br>
    名:<input type="text" v-model="lastName"><br>
    全民:<span style="background-color: bisque">{{fullName}}</span>
</div>
</body>

<script type="text/javascript">
    Vue.config.productionTip = false //阻止 vue 在启动时生成生产提示。

    let vm = new Vue({
        el: '#demo',
        data: {
            firstName: '张',
            lastName: '三'
        },
        computed: {
            //完整写法
            // fullName: {
            //     get() {
            //         console.log('get被调用了！！！')
            //         // console.log(this) //此处的this是vm
            //         return this.firstName + '-' + this.lastName
            //     },
            //     set(value) {
            //         const arr = value.split('-')
            //         this.firstName = arr[0]
            //         this.lastName = arr[1]
            //     }
            // }

            //简写 只读不改才可以用简写
            fullName() {
                console.log('get被调用了！！！')
                return this.firstName + '-' + this.lastName
            }
        }
    });
</script>
</html>
```

### 监视属性

监视属性watch：
    1.**当被监视的属性变化时**, 回调函数自动调用, 进行相关操作
    2.监视的属性必须存在，才能进行监视！！
    3.监视的两种写法：
        (1).new Vue时传入watch配置
        (2).通过vm.$watch监视

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <script type="text/javascript" src="../js/vue.js"></script>
</head>
<body>
<!--
        监视属性watch：
            1.当被监视的属性变化时, 回调函数自动调用, 进行相关操作
            2.监视的属性必须存在，才能进行监视！！
            3.监视的两种写法：
                (1).new Vue时传入watch配置
                (2).通过vm.$watch监视
		 -->
<div id="demo">
    <!--info是通过计算得到的属性-->
    <h2>今天的天气很{{info}}</h2>
    <button @click="changeWeather">切换天气</button>
</div>
</body>

<script type="text/javascript">
    Vue.config.productionTip = false //阻止 vue 在启动时生成生产提示。

    let vm = new Vue({
        el: '#demo',
        data: {
            isHot: true
        },
        methods: {
            changeWeather() {
                this.isHot = !this.isHot
            }
        },
        computed: {
            info() {
                return this.isHot ? 'Hot' : 'Cool'
            }
        },
        // watch: {
        //     isHot: {
        //         immediate:true, //初始化时让handler调用一下
        //         //handler什么时候调用？当isHot发生改变时。
        //         handler(newValue,oldValue){
        //             console.log('isHot被修改了',newValue,oldValue)
        //         }
        //     }
        // }
    });

    vm.$watch('isHot', {
        handler(newValue, oldValue) {
            console.log('isHot被修改了', newValue, oldValue)

        }
    })
</script>
</html>
```

### 深度监视

深度监视：
    (1).Vue中的watch默认不监测对象内部值的改变（一层）。
    (2).配置deep:true可以监测对象内部值改变（多层）。
备注：
    (1).Vue自身可以监测对象内部值的改变，但Vue提供的watch默认不可以！
    (2).使用watch时根据数据的具体结构，决定是否采用深度监视。

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>深度监视</title>
    <script type="text/javascript" src="../js/vue.js"></script>
</head>
<body>
<!--
    深度监视：
        (1).Vue中的watch默认不监测对象内部值的改变（一层）。
        (2).配置deep:true可以监测对象内部值改变（多层）。
    备注：
        (1).Vue自身可以监测对象内部值的改变，但Vue提供的watch默认不可以！
        (2).使用watch时根据数据的具体结构，决定是否采用深度监视。
 -->
<div id="root">
    <h2>今天天气很{{info}}</h2>
    <button @click="changeWeather">切换天气</button>
    <hr/>
    <h3>a的值是:{{numbers.a}}</h3>
    <button @click="numbers.a++">点我让a+1</button>
    <h3>b的值是:{{numbers.b}}</h3>
    <button @click="numbers.b++">点我让b+1</button>
    <button @click="numbers = {a:666,b:888}">彻底替换掉numbers</button>
    <!--    {{numbers.c.d.e}}-->
</div>
</body>

<script type="text/javascript">
    Vue.config.productionTip = false //阻止 vue 在启动时生成生产提示。

    const vm = new Vue({
        el: '#root',
        data: {
            isHot: true,
            numbers: {
                a: 1,
                b: 1,
                c: {
                    d: {
                        e: 100
                    }
                }
            }
        },
        computed: {
            info() {
                return this.isHot ? '炎热' : '凉爽'
            }
        },
        methods: {
            changeWeather() {
                this.isHot = !this.isHot
            }
        },
        watch: {
            isHot: {
                // immediate:true, //初始化时让handler调用一下
                //handler什么时候调用？当isHot发生改变时。
                handler(newValue, oldValue) {
                    console.log('isHot被修改了', newValue, oldValue)
                }
            },
            //监视多级结构中某个属性的变化
            /* 'numbers.a':{
                handler(){
                    console.log('a被改变了')
                }
            } */
            //监视多级结构中所有属性的变化
            numbers: {
                deep: true,
                handler() {
                    console.log('numbers改变了')
                }
            }
        }
    })
</script>
</html>
```

### 监视属性简写

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>监视简写</title>
    <script type="text/javascript" src="../js/vue.js"></script>
</head>
<body>
<div id="root">
    <h2>今天天气很{{info}}</h2>
    <button @click="changeWeather">切换天气</button>
    <hr/>
    <h3>a的值是:{{numbers.a}}</h3>
    <button @click="numbers.a++">点我让a+1</button>
    <h3>b的值是:{{numbers.b}}</h3>
    <button @click="numbers.b++">点我让b+1</button>
</div>
</body>

<script type="text/javascript">
    Vue.config.productionTip = false //阻止 vue 在启动时生成生产提示。

    const vm = new Vue({
        el: '#root',
        data: {
            isHot: true,
            numbers: {
                a: 1,
                b: 1,
                c: {
                    d: {
                        e: 100
                    }
                }
            }
        },
        computed: {
            info() {
                return this.isHot ? '炎热' : '凉爽'
            }
        },
        methods: {
            changeWeather() {
                this.isHot = !this.isHot
            }
        },
        watch: {
            //正常写法
            /*
            isHot: {
                // immediate:true, //初始化时让handler调用一下
                // deep:true,//深度监视
                //handler什么时候调用？当isHot发生改变时。
                handler(newValue, oldValue) {
                    console.log('isHot被修改了', newValue, oldValue)
                }
            },*/

            //简写  不需要配置immediate、deep属性的时候
            isHot(newValue, oldValue) {
                console.log('isHot被修改了', newValue, oldValue)
            },
            numbers: {
                deep: true,
                handler(newValue, oldValue) {
                    console.log('numbers被修改了', newValue, oldValue)
                }
            }

        },
    })

    //正常写法
    /*
    vm.$watch('isHot',{
        immediate:true, //初始化时让handler调用一下
        deep:true,//深度监视
        handler(newValue,oldValue){
            console.log('isHot被修改了',newValue,oldValue)
        }
    }) */

    /*
    //简写
    vm.$watch('isHot', function (newValue, oldValue) {
        console.log('isHot被修改了', newValue, oldValue)
    }) */

</script>
</html>
```

计算属性和监视属性对比

computed和watch之间的区别：

1. computed能完成的功能，watch都可以完成。
2. watch能完成的功能，computed不一定能完成，例如：watch可以进行异步操作。

两个重要的小原则：

1. 所被Vue管理的函数，最好写成普通函数，这样this的指向才是vm 或 组件实例对象。
2. 所有不被Vue所管理的函数（定时器的回调函数、ajax的回调函数等、Promise的回调函数），最好写成箭头函数，这样this的指向才是vm 或 组件实例对象。

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>计算属性和监听属性对比</title>
    <script type="text/javascript" src="../js/vue.js"></script>
</head>
<body>
<!-- 
    computed和watch之间的区别：
        1.computed能完成的功能，watch都可以完成。
        2.watch能完成的功能，computed不一定能完成，例如：watch可以进行异步操作。
    两个重要的小原则：
        1.所被Vue管理的函数，最好写成普通函数，这样this的指向才是vm 或 组件实例对象。
        2.所有不被Vue所管理的函数（定时器的回调函数、ajax的回调函数等、Promise的回调函数），最好写成箭头函数，
            这样this的指向才是vm 或 组件实例对象。
-->
<div id="root">
    姓：<input type="text" v-model="firstName"> <br><br>
    名：<input type="text" v-model="lastName"> <br><br>
    全名：<span>{{fullName}}</span>
</div>
</body>

<script type="text/javascript">
    Vue.config.productionTip = false //阻止 vue 在启动时生成生产提示。

    const vm = new Vue({
        el: '#root',
        data: {
            firstName: '张',
            lastName: '三',
            fullName: '张-三'
        },
        watch: {
            firstName(val) {
                setTimeout(() => {
                    console.log(this)
                    this.fullName = val + '-' + this.lastName
                }, 2000)
            },
            lastName(val) {
                this.fullName = this.firstName + '-' + val
            }
        }
    })
</script>
</html>
```

## class与style绑定

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>绑定样式</title>
    <script type="text/javascript" src="../js/vue.js"></script>
    <style>
        /*
        class选择器
        */
        .basic {
            width: 400px;
            height: 100px;
            border: 1px solid black;
        }

        .happy {
            border: 4px solid red;;
            background: rgba(255, 255, 0, 0.644) linear-gradient(30deg, yellow, pink, orange, yellow);
        }

        .sad {
            border: 4px dashed rgb(2, 197, 2);
            background-color: gray;
        }

        .normal {
            background-color: skyblue;
        }

        .atguigu1 {
            background-color: yellowgreen;
        }

        .atguigu2 {
            font-size: 30px;
            text-shadow: 2px 2px 10px red;
        }

        .atguigu3 {
            border-radius: 20px;
        }
    </style>

</head>
<body>
<div id="root">
    <!-- 绑定class样式--字符串写法，适用于：样式的类名不确定，需要动态指定 -->
    <div class="basic" :class="mood" @click="changeMood">{{name}}</div>
    <br/><br/>

    <!-- 绑定class样式--数组写法，适用于：要绑定的样式个数不确定、名字也不确定 -->
    <div class="basic" :class="classArr">{{name}}</div>
    <br/><br/>

    <!-- 绑定class样式--对象写法，适用于：要绑定的样式个数确定、名字也确定，但要动态决定用不用 -->
    <div class="basic" :class="classObj">{{name}}</div>
    <br/><br/>

    <!-- 绑定style样式--对象写法 -->
    <div class="basic" :style="styleObj">{{name}}</div>
    <br/><br/>
    <!-- 绑定style样式--数组写法 -->
    <div class="basic" :style="styleArr">{{name}}</div>
</div>
</body>

<script type="text/javascript">
    Vue.config.productionTip = false

    const vm = new Vue({
        el: '#root',
        data: {
            name: 'Allen',
            mood: 'normal',
            classArr: ['atguigu1', 'atguigu2', 'atguigu3'],
            classObj: {
                atguigu1: true,
                atguigu2: true,
            },
            styleObj: {
                fontSize: '40px',
                color: 'red',
            },
            styleObj2: {
                backgroundColor: 'orange'
            },
            styleArr: [
                {
                    fontSize: '40px',
                    color: 'blue',
                },
                {
                    backgroundColor: 'gray'
                }
            ]

        },
        methods: {
            changeMood() {
                const arr = ['happy', 'sad', 'normal']
                const index = Math.floor(Math.random() * 3)
                this.mood = arr[index]
            }
        },
    })
</script>
</html>
```

## 条件渲染

用于条件性地渲染一块内容。这块内容只会在指令的表达式返回 true 值的时候被渲染。

- `v-show`与`v-if`区别

  `v-show`底层是利用` style="display: none`

  `v-if `条件为真，元素将会被渲染，条件为假，元素会被销毁

- `template`只能用 `v-if`，而不能用`v-show`

- `v-if`可以和`v-else-if、v-else`一起使用，但要求结构不能被**“打断”**

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>条件渲染</title>
    <script type="text/javascript" src="../js/vue.js"></script>
</head>
<body>
<div id="demo">
    <h2>当前 N 的值为{{n}}</h2>
    <button @click="n++">点击此处 N + 1</button>

    <h2 v-if="n === 1">Hello</h2>
    <h2 v-else-if="n === 2">World</h2>
    <h2 v-else="n === 3">！！！</h2>

    <h2 v-show="n === 4">Smile</h2>

    <!--template只能用 v-if，而不能用`v-show,否则条件无效    -->
    <template v-if="n === 9">
        <h2>你好</h2>
        <h2>同学</h2>
    </template>
</div>

</body>

<script type="text/javascript">
    new Vue({
        el: '#demo',
        data: {
            n: 0
        }
    })
</script>
</html>
```

🔥🔥 使用 `<template> `标签在页面加载时该标签中的内容不会显示，加载后可以使用 JavaScript 来显示它

##  列表渲染

### v-for遍历数组、对象、字符串、指定次数

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>基本列表</title>
    <script type="text/javascript" src="../js/vue.js"></script>
</head>
<body>
<div id="demo">
    <!--遍历对象-->
    <ul style="background-color: aquamarine">
        <li v-for="value in person">
            {{value}}
        </li>
    </ul>

    <!--数组遍历   :key="obj.id" 表示唯一标识是id-->
    <ul style="background-color: bisque">
        <li v-for="obj in array" :key="obj.id">
            {{obj.id}}-{{obj.name}}-{{obj.age}}
        </li>
    </ul>

    <!-- 遍历字符串 -->
    <h2>测试遍历字符串（用得少）</h2>
    <ul>
        <li v-for="(char,index) of str" :key="index">
            {{char}}-{{index}}
        </li>
    </ul>

    <!-- 遍历指定次数 -->
    <h2>测试遍历指定次数（用得少）</h2>
    <ul>
        <li v-for="(number,index) of 5" :key="index">
            {{index}}-{{number}}
        </li>
</div>
</body>

<script type="text/javascript">
    new Vue({
        el: '#demo',
        data: {
            person: {
                name: 'Allen',
                address: 'Xi\'an',
                age: 18
            },
            array: [
                {id: '202032982', name: '张三', age: 18},
                {id: '202032983', name: '李四', age: 19},
                {id: '202032984', name: '王五', age: 20}
            ],
            str: 'hello'
        }
    })
</script>
</html>
```

### key的原理

key为什么应该是唯一标识，而不应该只是随意设定为 index 或者不写

key的原理是什么？

#### 以`index`为` key`（或者不写，不写默认是 index）

后面跟着输入框会出现的问题

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>key的原理</title>
    <script type="text/javascript" src="../js/vue.js"></script>
</head>
<body>
<div id="demo">
    <h2>人员列表（遍历数组）</h2>
    <button @click="add">添加一个人</button>

    <!--数组遍历   :key="obj.id" 表示唯一标识是id-->
    <ul style="background-color: bisque">
        <li v-for="person in persons">
            {{person.id}}-{{person.name}}-{{person.age}}
            <input type="text" style="border-bottom-color: blueviolet">
        </li>
    </ul>

</div>
</body>

<script type="text/javascript">
    new Vue({
        el: '#demo',
        data: {
            persons: [
                {id: '001', name: '张三', age: 18},
                {id: '002', name: '李四', age: 19},
                {id: '003', name: '王五', age: 20}
            ],
            str: 'hello'
        },
        methods: {
            add() {
                const p = {id: '004', name: '老刘', age: 30}
                //往数组前面添加
                this.persons.unshift(p)
            }
        }
    })
</script>
</html>
```

![image-20220531155533095](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220531155533095.png)

#### 以 id（唯一标识）作为key

![image-20220531155951687](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220531155951687.png) 

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>key的原理</title>
    <script type="text/javascript" src="../js/vue.js"></script>
</head>
<body>
<div id="demo">
    <h2>人员列表（遍历数组）</h2>
    <button @click="add">添加一个人</button>

    <!--数组遍历   :key="obj.id" 表示唯一标识是id-->
    <ul style="background-color: bisque">
        <li v-for="person in persons" :key="person.id">
            {{person.id}}-{{person.name}}-{{person.age}}
            <input type="text" style="border-bottom-color: blueviolet">
        </li>
    </ul>

</div>
</body>

<script type="text/javascript">
    new Vue({
        el: '#demo',
        data: {
            persons: [
                {id: '001', name: '张三', age: 18},
                {id: '002', name: '李四', age: 19},
                {id: '003', name: '王五', age: 20}
            ],
            str: 'hello'
        },
        methods: {
            add() {
                const p = {id: '004', name: '老刘', age: 30}
                //往数组前面添加
                this.persons.unshift(p)
            }
        }
    })
</script>
</html>
```

## 列表过滤

有时，我们想要显示一个数组经过过滤后的版本，而不实际变更或重置原始数据。在这种情况下，可以创建一个计算属性，来返回过滤后的数组。





