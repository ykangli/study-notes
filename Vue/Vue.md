# Vue笔记

------

## Vue核心

### 模板语法

#### 插值语法

语法: {{xxx}} ，xxxx 会作为 js 表达式解析  

功能: 用于解析**标签体内容**  

#### 指令语法

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

### 数据绑定

取data中的数据到页面展示

#### 单向数据绑定

1. 语法：`v-bind:href ="xxx" `或简写为 `:href`
2. 特点：**数据只能从 data 流向页面**  

![image-20220519170933566](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220519170933566.png)

#### 双向数据绑定

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

### el和data的两种写法

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

### 理解MVVM模型

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

### 数据代理

#### Object.defineProperty方法

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

#### 何为数据代理

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

#### Vue中的数据代理

![image-20220520195928589](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220520195928589.png)

1. Vue中的数据代理：通过vm对象来代理data对象中属性的操作（读/写）

2. Vue中数据代理的好处：更加方便的操作data中的数据

3. 基本原理：

   通过Object.defineProperty()把data对象中所有属性添加到vm上。

   为每一个添加到vm上的属性，都指定一个getter/setter。

   在getter/setter内部去操作（读/写）data中对应的属性。

### 事件处理

#### 事件的基本使用（监听事件 v-on）

可以用 `v-on` 指令监听 DOM 事件，并在**触发事件时运行一些 JavaScript 代码。**

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

#### 事件修饰符

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

#### 键盘事件

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

### 计算属性与监视属性

#### 计算属性

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

#### 监视属性

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

#### 深度监视

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

#### 监视属性简写

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

### class与style绑定

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

### 条件渲染

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

###  列表渲染

#### v-for遍历数组、对象、字符串、指定次数

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

#### key的原理

key为什么应该是唯一标识，而不应该只是随意设定为 index 或者不写

key的原理是什么？

##### 以`index`为` key`（或者不写，不写默认是 index）

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

##### 以 id（唯一标识）作为key

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

#### 列表过滤

有时，我们想要显示一个数组经过过滤后的版本，而不实际变更或重置原始数据。在这种情况下，可以创建一个**计算属性**，来返回过滤后的数组。

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>列表过滤</title>
    <script type="text/javascript" src="../js/vue.js"></script>
</head>
<body>
<div id="demo">
    <h2>人员列表</h2>
    <input type="text" placeholder="请输入名字" v-model="keyWord">

    <!--数组遍历   :key="obj.id" 表示唯一标识是id-->
    <ul style="background-color: bisque">
        <li v-for="person in filterPersons" :key="person.id">
            {{person.id}}-{{person.name}}-{{person.age}}
        </li>
    </ul>

</div>
</body>

<script type="text/javascript">
    new Vue({
        el: '#demo',
        data: {
            keyWord: '',
            persons: [
                {id: '001', name: '马冬梅', age: 19, sex: '女'},
                {id: '002', name: '周冬雨', age: 20, sex: '女'},
                {id: '003', name: '周杰伦', age: 21, sex: '男'},
                {id: '004', name: '温兆伦', age: 22, sex: '男'}
            ],
            str: 'hello'
        },
        computed: {
            filterPersons() {
                return this.persons.filter((person) => {
                    return person.name.indexOf(this.keyWord) !== -1
                })
            }
        }

    })
</script>
</html>
```

#### 列表排序

利用计算属性进行排序

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>列表排序</title>
    <script type="text/javascript" src="../js/vue.js"></script>
</head>
<body>
<div id="demo">
    <h2>人员列表</h2>
    <input placeholder="请输入名字" type="text" v-model="keyword">
    <button @click="sortType = 2">年龄升序</button>
    <button @click="sortType = 1">年龄降序</button>
    <button @click="sortType = 0">原顺序</button>

    <!--数组遍历   :key="obj.id" 表示唯一标识是id-->
    <ul style="background-color: bisque">
        <li v-for="person in filterPersons" :key="person.id">
            {{person.id}}-{{person.name}}-{{person.age}}
        </li>
    </ul>
</div>

</body>

<script type="text/javascript">
    new Vue({
        el: '#demo',
        data: {
            keyword: '',
            //降序 升序 原序列
            sortType: 0,
            persons: [
                {id: '001', name: '马冬梅', age: 19, sex: '女'},
                {id: '002', name: '周冬雨', age: 20, sex: '女'},
                {id: '003', name: '周杰伦', age: 21, sex: '男'},
                {id: '004', name: '温兆伦', age: 22, sex: '男'}
            ]
        },
        computed: {
            filterPersons() {
                const arr = this.persons.filter((person) => {
                    return person.name.indexOf(this.keyword) !== -1
                })
                if (this.sortType) {
                    arr.sort((a, b) => {
                        return this.sortType === 1 ? b.age - a.age : a.age - b.age
                    })
                }
                return arr
            }
        }
    })

</script>
</html>
```

#### 更新时的一个问题

Vue如何监测数据更新

案例：数组中的属性被改变，但Vue没有监测到

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>更新时的一个问题</title>
    <script type="text/javascript" src="../js/vue.js"></script>
</head>
<body>
<div id="demo">
    <h2>人员列表</h2>
    <button @click="updateMei">点我更新马冬梅</button>

    <!--数组遍历   :key="obj.id" 表示唯一标识是id-->
    <ul style="background-color: bisque">
        <li v-for="person in persons" :key="person.id">
            {{person.id}}-{{person.name}}-{{person.age}}
        </li>
    </ul>
</div>

</body>

<script type="text/javascript">
    const  vm = new Vue({
        el: '#demo',
        data: {
            persons: [
                {id: '001', name: '马冬梅', age: 19, sex: '女'},
                {id: '002', name: '周冬雨', age: 20, sex: '女'},
                {id: '003', name: '周杰伦', age: 21, sex: '男'},
                {id: '004', name: '温兆伦', age: 22, sex: '男'}
            ]
        },
        methods: {
            updateMei () {
                // this.persons[0].name = '马老师' //奏效，Vue能监视到
                // this.persons[0].age = 50 ///奏效，Vue能监视到
                // this.persons[0].sex = '男' ///奏效，Vue能监视到
                this.persons[0] = {id:'001',name:'马老师',age:50,sex:'男'} //不奏效，Vue不能监视到
            }
        }
    })

</script>
</html>
```

![image-20220605122837758](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220605122837758.png)

上图中，通过直接修改数组 `this.persons[0] = {id:'001',name:'马老师',age:50,sex:'男'} //不奏效，Vue不能监视到` 

data中的实际数据已经改变，但Vue却监视不到

#### 模拟一个数据监测

待补充

#### Vue.set的使用

待补充

#### Vue监测数据改变的原理_数组

待补充

#### 总结Vue数据监测

待补充



### 收集表单数据

v-model 默认收集 value值

收集表单数据：
            若：<input type="text"/>，则v-model收集的是value值，用户输入的就是value值。
            若：<input type="radio"/>，则v-model收集的是value值，且要给标签配置value值。
            若：<input type="checkbox"/>
                1.没有配置input的value属性，那么收集的就是checked（勾选 or 未勾选，是布尔值）
                2.配置input的value属性:
                    (1)v-model的初始值是非数组，那么收集的就是checked（勾选 or 未勾选，是布尔值）
                    (2)v-model的初始值是数组，那么收集的的就是value组成的数组
            备注： v-model的三个修饰符：
                		lazy：失去焦点再收集数据
               		 number：输入字符串转为有效的数字
              		  trim：输入首尾空格过滤

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>收集表单数据</title>
    <script type="text/javascript" src="../js/vue.js"></script>
</head>
<body>
<div id="demo">
    <form @submit="submit">
        账号：<input type="text" v-model.trim="userInfo.account"> <br><br>
        密码：<input type="password" v-model="userInfo.password"> <br><br>
        性别：
        男<input type="radio" v-model="userInfo.sex" value="male">
        女<input type="radio" v-model="userInfo.sex" value="female"><br><br>
        爱好：
        吃饭<input type="checkbox" v-model="userInfo.hobby" value="eat">
        学习<input type="checkbox" v-model="userInfo.hobby" value="study">
        打游戏<input type="checkbox" v-model="userInfo.hobby" value="game"><br><br>
        所属校区
        <select v-model="userInfo.city">
            <option value="">请选择校区</option>
            <option value="xian">西安</option>
            <option value="shanghai">上海</option>
            <option value="shenzhen">深圳</option>
            <option value="wuhan">武汉</option>
        </select>
        <br/><br/>
        其他信息：
        <textarea v-model.lazy="userInfo.other"></textarea> <br/><br/>
        <input type="checkbox" v-model="userInfo.agree">阅读并接受<a href="http://www.atguigu.com">《用户协议》</a>
        <button>提交</button>
    </form>

</div>

</body>

<script type="text/javascript">
    new Vue({
        el: '#demo',
        data: {
            userInfo: {
                account: '',
                password: '',
                age: 18,
                sex: 'female',
                hobby: [],
                city: 'xian',
                other: '',
                agree: ''
            }
        },
        methods: {
            demo(){
                console.log(JSON.stringify(this.userInfo))
            }
        }
    })

</script>
</html>
```

### 过滤器

1. 功能: 对要显示的数据进行特定格式化后再显示
2. 注意: 并没有改变原本的数据, 是产生新的对应的数据  

BootCDN   稳定、快速、免费的前端开源项目 CDN 加速服务

过滤器：
            定义：对要显示的数据进行特定格式化后再显示（适用于一些简单逻辑的处理）。
            语法：
                1.注册过滤器：Vue.filter(name,callback) 或 new Vue{filters:{}}
                2.使用过滤器：{{ xxx | 过滤器名}}  或  v-bind:属性 = "xxx | 过滤器名"
            备注：
                1.过滤器也可以接收额外参数、多个过滤器也可以串联
                2.并没有改变原本的数据, 是产生新的对应的数据

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>过滤器</title>
    <script type="text/javascript" src="../js/vue.js"></script>
    <script type="text/javascript" src="../js/dayjs.min.js"></script>

</head>
<body>
<div id="demo">
    <h2>显示格式化后的时间</h2>
    <!-- 计算属性实现 -->
    <h2>现在是：{{fmtTime}} 计算属性得到</h2>
    <h2>现在是：{{getTime()}} methods得到</h2>
    <h2>现在是：{{time | timeFormater}} 过滤器得到</h2>

</div>

<div id="root">
    <h2>{{msg | mySlice}}</h2>
</div>

</body>

<script type="text/javascript">
    Vue.filter('mySlice', function (value) {
        return value.slice(0, 4)
    })

    new Vue({
        el: '#demo',
        data: {
            time: 1654484026041
        },
        computed: {
            fmtTime() {
                return dayjs(this.time).format('YYYY年MM月DD日 HH:mm:ss')
            }
        },
        methods: {
            getTime() {
                return dayjs(this.time).format('YYYY年MM月DD日 HH:mm:ss')
            }
        },

        //局部过滤器，只能在该Vue实例中使用
        filters: {
            timeFormater(value, str = 'YYYY年MM月DD日 HH:mm:ss') {
                // console.log('@',value)
                return dayjs(value).format(str)
            }
        }
    })

      new Vue({
        el: '#root',
        data: {
            msg: 'hello,atguigu!'
        }
    })

</script>
</html>
```

### 内置指令

#### v-text_指令

- 作用：向其所在的节点中渲染文本内容。
- 与插值语法的区别：v-text会替换掉节点中的内容，{{xx}}则不会。

```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset="UTF-8" />
		<title>v-text指令</title>
		<!-- 引入Vue -->
		<script type="text/javascript" src="../js/vue.js"></script>
	</head>
	<body>
		<!-- 
        我们学过的指令：
            v-bind	: 单向绑定解析表达式, 可简写为 :xxx
            v-model	: 双向数据绑定
            v-for  	: 遍历数组/对象/字符串
            v-on   	: 绑定事件监听, 可简写为@
            v-if 	 	: 条件渲染（动态控制节点是否存存在）
            v-else 	: 条件渲染（动态控制节点是否存存在）
            v-show 	: 条件渲染 (动态控制节点是否展示)
        v-text指令：
            1.作用：向其所在的节点中渲染文本内容。
            2.与插值语法的区别：v-text会替换掉节点中的内容，{{xx}}则不会。
		-->
		<!-- 准备好一个容器-->
		<div id="root">
			<div>你好，{{name}}</div>
			<div v-text="name"></div>
			<div v-text="str"></div>
		</div>
	</body>

	<script type="text/javascript">
		Vue.config.productionTip = false //阻止 vue 在启动时生成生产提示。
		
		new Vue({
			el:'#root',
			data:{
				name:'尚硅谷',
				str:'<h3>你好啊！</h3>'
			}
		})
	</script>
</html>
```

#### v-html

- 作用：向指定节点中渲染包含html结构的内容。 
- 与插值语法的区别：
- v-html会替换掉节点中所有的内容，{{xx}}则不会。
- v-html可以识别html结构。
- 严重注意：v-html有安全性问题！！！！
                  (1)在网站上动态渲染任意HTML是非常危险的，容易导致XSS攻击。
                  (2)一定要在可信的内容上使用v-html，永不要用在用户提交的内容上！

```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset="UTF-8" />
		<title>v-html指令</title>
		<!-- 引入Vue -->
		<script type="text/javascript" src="../js/vue.js"></script>
	</head>
	<body>
		<!-- 
        v-html指令：
            1.作用：向指定节点中渲染包含html结构的内容。
            2.与插值语法的区别：
                (1).v-html会替换掉节点中所有的内容，{{xx}}则不会。
                (2).v-html可以识别html结构。
            3.严重注意：v-html有安全性问题！！！！
                (1).在网站上动态渲染任意HTML是非常危险的，容易导致XSS攻击。
                (2).一定要在可信的内容上使用v-html，永不要用在用户提交的内容上！
		-->
		<!-- 准备好一个容器-->
		<div id="root">
			<div>你好，{{name}}</div>
			<div v-html="str"></div>
			<div v-html="str2"></div>
		</div>
	</body>

	<script type="text/javascript">
		Vue.config.productionTip = false //阻止 vue 在启动时生成生产提示。

		new Vue({
			el:'#root',
			data:{
				name:'尚硅谷',
				str:'<h3>你好啊！</h3>',
				str2:'<a href=javascript:location.href="http://www.baidu.com?"+document.cookie>兄弟我找到你想要的资源了，快来！</a>',
			}
		})
	</script>
</html>
```

#### v-cloak_指令

可用来防止闪现, 与 css 配合:  `[v-cloak] { display: none }  `

js 外部引入过慢，js不能及时渲染，会导致先在页面上出现 js 内容。

```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset="UTF-8" />
		<title>v-cloak指令</title>
		<style>
			[v-cloak]{
				display:none;
			}
		</style>
		<!-- 引入Vue -->
	</head>
	<body>
		<!-- 
        v-cloak指令（没有值）：
            1.本质是一个特殊属性，Vue实例创建完毕并接管容器后，会删掉v-cloak属性。
            2.使用css配合v-cloak可以解决网速慢时页面展示出{{xxx}}的问题。
		-->
		<!-- 准备好一个容器-->
		<div id="root">
			<h2 v-cloak>{{name}}</h2>
		</div>
		<script type="text/javascript" src="http://localhost:8080/resource/5s/vue.js"></script>
	</body>
	
	<script type="text/javascript">
		console.log(1)
		Vue.config.productionTip = false //阻止 vue 在启动时生成生产提示。
		
		new Vue({
			el:'#root',
			data:{
				name:'尚硅谷'
			}
		})
	</script>
</html>
```

#### v-once

v-once指令：
            1.v-once所在节点在初次动态渲染后，就视为静态内容了。
            2.以后数据的改变不会引起v-once所在结构的更新，可以用于优化性能。

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>过滤器</title>
    <script type="text/javascript" src="../js/vue.js"></script>

</head>
<body>
<div id="demo">
    <h2 v-once>初始的值为{{n}}</h2>
    <h2>当前的值为{{n}}</h2>
    <button v-on:click="n++">点我增加</button>


</div>

</body>

<script type="text/javascript">
    Vue.filter('mySlice', function (value) {
        return value.slice(0, 4)
    })


    new Vue({
        el: '#demo',
        data: {
            n: 1
        },
    })
    
</script>
</html>
```

#### v-pre

 v-pre指令：
            1.跳过其所在节点的编译过程。
            2.可利用它跳过：没有使用指令语法、没有使用插值语法的节点，会加快编译。

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>过滤器</title>
  <script type="text/javascript" src="../js/vue.js"></script>

</head>
<body>
<div id="demo">
  <!--跳过其所在节点的编译过程-->
  <h2 v-pre>初始的值为{{n}}</h2>
  <h2>当前的值为{{n}}</h2>
  <button v-on:click="n++">点我增加</button>
</div>
</body>

<script type="text/javascript">
  new Vue({
    el: '#demo',
    data: {
      n: 1
    },
  })
</script>
</html>
```

### 自定义指令

两种实现方式：函数式和对象式

函数式相当于只有`bind`和`update`

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>自定义指令</title>
    <script type="text/javascript" src="../js/vue.js"></script>

</head>
<body>
<div id="demo">
    <!--
        需求1：定义一个v-big指令，和v-text功能类似，但会把绑定的数值放大10倍。
        需求2：定义一个v-fbind指令，和v-bind功能类似，但可以让其所绑定的input元素默认获取焦点。
        自定义指令总结：
            一、定义语法：
                (1).局部指令：
                    new Vue({
                        directives:{指令名:配置对象} 或 directives{指令名:回调函数}
                    })
                (2).全局指令：
                    Vue.directive(指令名,配置对象) 或 Vue.directive(指令名,回调函数)

            二、配置对象中常用的3个回调：
                (1).bind：指令与元素成功绑定时调用。
                (2).inserted：指令所在元素被插入页面时调用。
                (3).update：指令所在模板结构被重新解析时调用。

            三、备注：
                1.指令定义时不加v-，但使用时要加v-；
                2.指令名如果是多个单词，要使用kebab-case命名方式，不要用camelCase命名。
		-->
    <!--使用v-big 函数式-->
    <h2>当前的值为<span v-text="n"></span></h2>
    <h2>放大10倍后的n值是：<span v-big="n"></span></h2>
    <button @click="n++">点我n+1</button>

    <!--使用v-fbind 对象式    element.value和binding.value绑定-->
    <input type="text" v-fbind:value="n">


</div>

</body>

<script type="text/javascript">

    new Vue({
        el: '#demo',
        data: {
            n: 1
        },
        directives: {
            //函数式实现
            //element为真实的DOM元素span
            //big函数何时会被调用？1.指令与元素成功绑定时（一上来）。2.指令所在的模板被重新解析时。
            big(element, binding) {
                console.log(element)
                console.log(binding)
                element.innerText = binding.value * 10
            },

            //是 'fbind':{} 的简写
            fbind: {
                //指令与元素成功绑定时（一上来）
                bind(element, binding) {
                    element.value = binding.value
                },
                //指令所在元素被插入页面时
                inserted(element, binding) {
                    element.focus()
                },
                //指令所在的模板被重新解析时
                update(element, binding) {
                    element.value = binding.value
                }
            }
        }
    })

</script>
</html>
```

🔥🔥指令名如果是多个单词，要使用kebab-case命名方式，不要用camelCase命名   v-fbind-number

下面写法为  

```html
'fbind-number': {}
```

### 生命周期

每个 Vue 实例在被创建时都要经过一系列的初始化过程——例如，需要设置数据监听、编译模板、将实例挂载到 DOM 并在数据变化时更新 DOM 等。同时在这个过程中也会运行一些叫做**生命周期钩子**的函数，这**给了用户在不同阶段添加自己的代码的机会**。

8 个 生命周期函数，4 对

![生命周期](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/生命周期.png)

#### beforeCreate

![image-20220607110844351](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220607110844351.png)

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>生命周期</title>
    <script type="text/javascript" src="../js/vue.js"></script>

</head>
<body>
<div id="demo">
    <h2>n的值为：{{n}}</h2>
</div>
</body>

<script type="text/javascript">

    new Vue({
        el: '#demo',
        data: {
            n: 1
        },
        beforeCreate(){
            console.log('beforeCreate')
            debugger
        },
    })

</script>
</html>
```

上图中可说明，在`beforeCreate`阶段 Vue实例中无 vue._data，无methonds

#### created

![image-20220607111417892](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220607111417892.png)

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>生命周期</title>
    <script type="text/javascript" src="../js/vue.js"></script>

</head>
<body>
<div id="demo">
    <h2>n的值为：{{n}}</h2>
</div>
</body>

<script type="text/javascript">

    new Vue({
        el: '#demo',
        data: {
            n: 1
        },
        methods: {
            hello() {
                
            }
        },
        beforeCreate(){
            console.log('beforeCreate')
        },
        created() {
            console.log('create')
            debugger
        }
    })

</script>
</html>
```

在`created`阶段可以通过 `vm `访问到` data`中的数据和`methods`中的方法

`beforeCreate `和 `created `指的是 数据监测、数据代理创建之前和创建之后，而不是 Vue实例vm

#### beforeMount

![image-20220607113000768](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220607113000768.png)

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>生命周期</title>
    <script type="text/javascript" src="../js/vue.js"></script>

</head>
<body>
<div id="demo">
    <h2>n的值为：{{n}}</h2>
</div>
</body>

<script type="text/javascript">

    new Vue({
        el: '#demo',
        data: {
            n: 1
        },
        methods: {
            hello() {

            }
        },
        beforeCreate() {
            console.log('beforeCreate')
        },
        created() {
            console.log('create')
        },
        beforeMount() {
            console.log('beforeMount')
            console.log(this)
            debugger
        }
    })

</script>
</html>
```

由上图可见，在`beforMount`阶段，页面呈现的是未经 `Vue`编译的 DOM 结构

#### mounted

![image-20220607113244478](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220607113244478.png)

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>生命周期</title>
    <script type="text/javascript" src="../js/vue.js"></script>

</head>
<body>
<div id="demo">
    <h2>n的值为：{{n}}</h2>
</div>
</body>

<script type="text/javascript">

    new Vue({
        el: '#demo',
        data: {
            n: 1
        },
        methods: {
            hello() {

            }
        },
        beforeCreate() {
            console.log('beforeCreate')
        },
        created() {
            console.log('create')
        },
        beforeMount() {
            console.log('beforeMount')
        },
        mounted() {
            console.log('mounted')
            console.log(this)
            debugger
        }
    })

</script>
</html>
```

在`mounted`阶段，页面出现的是经过 Vue 编译的 DOM结构

#### beforeUpdate

![image-20220607145948261](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220607145948261.png)

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>生命周期</title>
    <script type="text/javascript" src="../js/vue.js"></script>

</head>
<body>
<div id="demo">
    <h2>n的值为：{{n}}</h2>
</div>
</body>

<script type="text/javascript">

    new Vue({
        el: '#demo',
        data: {
            n: 1
        },
        methods: {
            hello() {

            }
        },
        beforeCreate() {
            console.log('beforeCreate')
        },
        created() {
            console.log('create')
        },
        beforeMount() {
            console.log('beforeMount')
        },
        mounted() {
            console.log('mounted')

        },
        beforeUpdate() {
            console.log('beforeUpdate')
            console.log(this)
            debugger
        }
    })

</script>
</html>
```

初始时 n 为 1，此时已将 n 修改为 4，但页面仍展示为 1 。

在`beforeUpdate`阶段，数据是新的，但页面是旧的。

#### updated

页面和数据保持同步

#### beforeDestrory

vm中所有的：data、methods、指令等都处于可用状态

🔥🔥 一般不会在beforeDestroy操作数据，因为即便操作数据，也不会再触发更新流程了。

#### **destroy**

- 销毁后借助Vue开发者工具看不到任何信息。
- 销毁后自定义事件会失效，但原生DOM事件依然有效。

## Vue组件化编程

<img src="https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220607153807364.png" alt="image-20220607153807364" style="zoom:67%;" />

<img src="https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220607153841433.png" alt="image-20220607153841433" style="zoom:67%;" />

<img src="https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220607154136262.png" alt="image-20220607154136262" style="zoom:67%;" />

### 非单文件组件

#### 基本使用

组件的使用分为三步：

1. 创建（定义）组件

   `el`不要写，为什么？最终所有的组件都要经过一个`vm`的管理，**由vm中的el决定服务哪个容器**。

   **`data`必须写成函数式**，为什么？避免组件被复用时，数据存在引用关系。

2. 注册组件 （局部注册或者全局注册）

3. 使用组件

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>非单文件组件的基本使用</title>
    <script type="text/javascript" src="../js/vue.js"></script>

</head>
<body>
<!--
        Vue中使用组件的三大步骤：
            一、定义组件(创建组件)
            二、注册组件
            三、使用组件(写组件标签)

        一、如何定义一个组件？
            使用Vue.extend(options)创建，其中options和new Vue(options)时传入的那个options几乎一样，但也有点区别；
            区别如下：
                1.el不要写，为什么？ ——— 最终所有的组件都要经过一个vm的管理，由vm中的el决定服务哪个容器。
                2.data必须写成函数，为什么？ ———— 避免组件被复用时，数据存在引用关系。
            备注：使用template可以配置组件结构。

        二、如何注册组件？
            1.局部注册：靠new Vue的时候传入components选项
            2.全局注册：靠Vue.component('组件名',组件)

        三、编写组件标签：
            <school></school>
-->

<div id="demo" style="background-color: cadetblue">
    <!--第三步：编写组件标签，使用组件-->
    <school></school>
    <student></student>
    <hello></hello>
</div>

<div id="demo2" style="background-color: bisque">
    <!--第三步：编写组件标签，使用组件-->
    <hello></hello>
</div>
</body>

<script type="text/javascript">
    Vue.config.productionTip = false

    //第一步：创建组件
    const sc = Vue.extend({
        //结构
        template: `
          <div>
          <h2>学校名称：{{ schoolName }}</h2>
          <h2>学校位置：{{ address }}</h2>
          </div>
        `,
        //组件的data一定要写为函数式 ===> 避免组件被复用时，数据存在引用关系。
        data() {
            return {
                schoolName: '西北大学',
                address: '西安'
            }
        },
    });
    const stu = Vue.extend({
        //结构
        template: `
          <div>
          <h2>学生姓名：{{ studentName }}</h2>
          <h2>学生年龄：{{ age }}</h2>
          </div>
        `,
        data() {
            return {
                studentName: 'Allen',
                age: 18
            }
        }
    });
    const hi = Vue.extend({
        //结构
        template: `
          <div><h2>{{ word }}</h2></div>
        `,
        data() {
            return {
                word: 'Hello, welcome to NWU',
            }
        }
    });

    //也可以全局注册组件
    Vue.component('hello', hi)

    new Vue({
        el: '#demo',
        data: {
            msg: 'GO'
        },
        //第二步：注册组件 （局部注册）
        components: {
            school: sc,
            student: stu
        }
    })
    new Vue({
        el: '#demo2',
        data: {
            msg: 'Come on'
        }
    })
</script>
</html>
```

#### 几个注意点

组件名、组件标签、组件定义简写

```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset="UTF-8" />
		<title>几个注意点</title>
		<script type="text/javascript" src="../js/vue.js"></script>
	</head>
	<body>
		<!-- 
        几个注意点：
            1.关于组件名:
                一个单词组成：
                    第一种写法(首字母小写)：school
                    第二种写法(首字母大写)：School
                多个单词组成：
                    第一种写法(kebab-case命名)：my-school
                    第二种写法(CamelCase命名)：MySchool (需要Vue脚手架支持)
                备注：
                    (1).组件名尽可能回避HTML中已有的元素名称，例如：h2、H2都不行。
                    (2).可以使用name配置项指定组件在开发者工具中呈现的名字。

                2.关于组件标签:
                    第一种写法：<school></school>
                    第二种写法：<school/>
                    备注：不用使用脚手架时，<school/>会导致后续组件不能渲染。

                3.一个简写方式：
                    const school = Vue.extend(options) 可简写为：const school = options
		-->
		<!-- 准备好一个容器-->
		<div id="root">
			<h1>{{msg}}</h1>
			<school></school>
		</div>
	</body>

	<script type="text/javascript">
		Vue.config.productionTip = false
		
		//定义组件
		const s = Vue.extend({
			name:'atguigu',
			template:`
				<div>
					<h2>学校名称：{{name}}</h2>	
					<h2>学校地址：{{address}}</h2>	
				</div>
			`,
			data(){
				return {
					name:'尚硅谷',
					address:'北京'
				}
			}
		})

		new Vue({
			el:'#root',
			data:{
				msg:'欢迎学习Vue!'
			},
			components:{
				school:s
			}
		})
	</script>
</html>
```

#### 组件的嵌套

通常一个应用会以一棵嵌套的组件树的形式来组织：

![image-20220607192045039](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220607192045039.png)

**例1：**

![image-20220607193159689](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220607193159689.png)

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>嵌套组件</title>
    <script type="text/javascript" src="../js/vue.js"></script>

</head>
<body>

<div id="demo" style="background-color: cadetblue">
    <!--第三步：编写组件标签，使用组件-->
    <school></school>

</div>
</body>

<script type="text/javascript">
    Vue.config.productionTip = false

    //第一步：创建组件
    const stu = Vue.extend({
        //结构
        template: `
          <div>
          <h2>学生姓名：{{ studentName }}</h2>
          <h2>学生年龄：{{ age }}</h2>
          </div>
        `,
        data() {
            return {
                studentName: 'Allen',
                age: 18
            }
        }
    });
    const sc = Vue.extend({
        //结构
        template: `
          <div>
          <h2>学校名称：{{ schoolName }}</h2>
          <h2>学校位置：{{ address }}</h2>
          <student></student>
          </div>
        `,
        //组件的data一定要写为函数式 ===> 避免组件被复用时，数据存在引用关系。
        data() {
            return {
                schoolName: '西北大学',
                address: '西安'
            }
        },
        //嵌套组件  局部注册组件
        components: {
            student: stu
        }
    });

    //创建Vue实例
    new Vue({
        el: '#demo',
        data: {
            msg: 'GO'
        },
        //第二步：注册组件 （局部注册）
        components: {
            school: sc,
        }
    })
</script>
</html>
```

**例2：**

![image-20220607194548164](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220607194548164.png)

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>嵌套组件 app</title>
    <script type="text/javascript" src="../js/vue.js"></script>

</head>
<body>

<div id="demo" style="background-color: cadetblue">
    <!--第三步：编写组件标签，使用组件-->
    <app></app>

</div>
</body>

<script type="text/javascript">
    Vue.config.productionTip = false

    //第一步：创建组件
    const stu = Vue.extend({
        //结构
        template: `
          <div>
          <h2>学生姓名：{{ studentName }}</h2>
          <h2>学生年龄：{{ age }}</h2>
          </div>
        `,
        data() {
            return {
                studentName: 'Allen',
                age: 18
            }
        }
    });
    const sc = Vue.extend({
        //结构
        template: `
          <div>
          <h2>学校名称：{{ schoolName }}</h2>
          <h2>学校位置：{{ address }}</h2>
          <student></student>
          </div>
        `,
        //组件的data一定要写为函数式 ===> 避免组件被复用时，数据存在引用关系。
        data() {
            return {
                schoolName: '西北大学',
                address: '西安'
            }
        },
        //嵌套组件  局部注册组件
        components: {
            student: stu
        }
    });
    const hi = Vue.extend({
        //结构
        template: `
          <div><h2>{{ word }}</h2></div>
        `,
        data() {
            return {
                word: 'Hello, welcome to NWU',
            }
        }
    });
    //创建（定义）的最顶层组件，vue管理一个组件app
    const application = Vue.extend({
        template: `
          <div>
          <hello></hello>
          <school></school>
          </div>

        `,
        //局部注册组件
        components: {
            school: sc,
            hello: hi
        }
    });

    //创建Vue实例
    new Vue({
        el: '#demo',
        //局部注册组件
        components: {
            app: application
        }
    })
</script>
</html>
```

#### VueComponent

- 组件的本质是一个名为`VueComponent`的**构造函数**
- 我们只需要写`<school/>`或`<school></school>`，Vue解析时会帮我们创建school组件的实例对象，即Vue帮我们执行的：`new VueComponent(options)`。
- 每次调用`Vue.extend`，返回的都是一个全新的 `VueComponent`

**关于this指向：**

1. 组件配置中：

   data函数、methods中的函数、watch中的函数、computed中的函数 它们的this均是    🔥🔥`VueComponent`实例对象。

2. new Vue(options)配置中：

    data函数、methods中的函数、watch中的函数、computed中的函数 它们的this均是   🔥🔥 `Vue`实例对象。

![image-20220607203358674](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220607203358674.png)

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>组件的本质是什么</title>
    <script type="text/javascript" src="../js/vue.js"></script>

</head>
<body>
<!--
        关于VueComponent：
            1.school组件本质是一个名为VueComponent的构造函数，且不是程序员定义的，是Vue.extend生成的。

            2.我们只需要写<school/>或<school></school>，Vue解析时会帮我们创建school组件的实例对象，
                即Vue帮我们执行的：new VueComponent(options)。

            3.特别注意：每次调用Vue.extend，返回的都是一个全新的VueComponent！！！！

            4.关于this指向：
                    (1).组件配置中：
                                data函数、methods中的函数、watch中的函数、computed中的函数 它们的this均是【VueComponent实例对象】。
                    (2).new Vue(options)配置中：
                                data函数、methods中的函数、watch中的函数、computed中的函数 它们的this均是【Vue实例对象】。

            5.VueComponent的实例对象，以后简称vc（也可称之为：组件实例对象）。
                Vue的实例对象，以后简称vm。
		-->
<div id="demo" style="background-color: cadetblue">
    <!--第三步：编写组件标签，使用组件-->
    <school></school>

</div>
</body>

<script type="text/javascript">
    Vue.config.productionTip = false

    //第一步：创建组件
    const stu = Vue.extend({
        //结构
        template: `
          <div>
          <h2>学生姓名：{{ studentName }}</h2>
          <h2>学生年龄：{{ age }}</h2>
          <button @click="show">点我看组件中的this是什么</button>
          </div>
        `,
        data() {
            return {
                studentName: 'Allen',
                age: 18
            }
        },
        methods: {
            show() {
                console.log(this)
            }
        }
    });
    const sc = Vue.extend({
        //结构
        template: `
          <div>
          <h2>学校名称：{{ schoolName }}</h2>
          <h2>学校位置：{{ address }}</h2>
          <student></student>
          </div>
        `,
        //组件的data一定要写为函数式 ===> 避免组件被复用时，数据存在引用关系。
        data() {
            return {
                schoolName: '西北大学',
                address: '西安'
            }
        },
        //嵌套组件  局部注册组件
        components: {
            student: stu
        }
    });
    console.log(sc)
    console.log(stu)

    //创建Vue实例
    const vm = new Vue({
        el: '#demo',
        data: {
            msg: 'GO'
        },
        //第二步：注册组件 （局部注册）
        components: {
            school: sc,
        }
    });
    console.log(vm)
</script>
</html>
```

![image-20220607203955862](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220607203955862.png)

由上图可知：

- 组件的本质是一个名为`VueComponent`的**构造函数**
- 不同组件是**不同的**` VueComponent` 实例对象
- 组件配置中：data函数、methods中的函数、watch中的函数、computed中的函数 它们的this均是**VueComponent实例对象**。
- `Vue`管理着下面的 `VueComponent`

#### 一个重要的内置关系

1. 一个重要的内置关系：VueComponent.prototype.__proto__ === Vue.prototype
2. 为什么要有这个关系：让组件实例对象（vc）可以访问到 Vue原型上的属性、方法

待补充

### 单文件组件

ES6 中 export 的三种对外暴露的方式

#### App.vue

```html
<template>
  <div>
    <School></School>
    <Student></Student>
  </div>

</template>

<script>
//引入组件
import School from './components/School'
import Student from "./components/Student";
export default {
  name: "App",
  components: {
    School,
    Student
  }
}
</script>

<style scoped>
</style>
```

最高级组件，管理所有子组件

#### index.html

容器

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>容器</title>
</head>
<body>
<div id="demo"></div>
<script type="text/javascript" src="../js/vue.js"></script>
<script type="text/javascript" src="main.js"></script>

</body>
</html>
```

#### main.js

创建 vue 实例的地方，程序的入口

```js
import App from "./App";

new Vue({
    template: `
    <App></App>
    `,
    el: '#demo',
    components: {App},
    data: {}
})
```

#### School.vue

子组件School.vue

```vue
<template>
  <!--  组件的结构-->
  <div class="demo">
    <h2>学校名称：{{ name }}</h2>
    <h2>学校地址：{{ address }}</h2>
    <button @click="showName">点我提示学校名</button>
  </div>
</template>

<script>
// 组件交互相关的代码(数据、方法等)
export default {
  //在浏览器vue插件中的名字
  name: 'SchoolName',
  data() {
    return {
      name: '尚硅谷',
      address: '北京昌平'
    }
  },
  methods: {
    showName() {
      alert(this.name)
    }
  }
}
</script>

<style>
/*组件的样式*/
.demo {
  background-color: antiquewhite;
}
</style>
```

#### Student.vue

子组件Student.vue

```vue
<template>
  <div>
    <h2>学生姓名：{{ name }}</h2>
    <h2>学生年龄：{{ age }}</h2>
  </div>
</template>

<script>
export default {
  //在浏览器vue插件中的名字  写School会报错，因为不是大写驼峰
  name: "StudentName",
  data() {
    return {
      name: 'Allen',
      age: 18
    }
  }
}
</script>

<style scoped>
</style>
```

利用单文件编写vue前端工程，必须使用 Vue-CLI （Vue脚手架）才能展现界面。

## 使用Vue CLI 脚手架

### 初始化脚手架

#### 安装启动

> 如果你已经全局安装了旧版本的 `vue-cli` (1.x 或 2.x)，你需要先通过 `npm uninstall vue-cli -g` 或 `yarn global remove vue-cli` 卸载它。

可以使用下列任一命令安装这个新的包：

```
npm install -g @vue/cli
# OR
yarn global add @vue/cli
```

安装之后，你就可以在命令行中访问 `vue` 命令。你可以通过简单运行 `vue`，看看是否展示出了一份所有可用命令的帮助信息，来验证它是否安装成功。

你还可以用这个命令来检查其版本是否正确：

```
vue --version
```

创建 `vue-cli` 项目：

```
//切换到要存储的目录，创建名为 vue_test 的项目
vue  create vue_test
```

启动项目：

```
npm run serve  
```

**Vue 脚手架隐藏了所有 webpack 相关的配置，若想查看具体的 webpakc 配置，请执行：**

```
vue inspect > output.js  
```

#### 模板项目结构

```
.文件目录
├── node_modules 
├── public
│   ├── favicon.ico: 页签图标
│   └── index.html: 主页面
├── src
│   ├── assets: 存放静态资源
│   │   └── logo.png
│   │── component: 存放组件
│   │   └── Student.vue
│   │   └── School.vue
│   │── App.vue: 汇总所有组件
│   └── main.js: 入口文件
├── .gitignore: git版本管制忽略的配置
├── babel.config.js: babel的配置文件
├── package.json: 应用包配置文件 
├── README.md: 应用描述文件
└── package-lock.json: 包版本控制文件
```

##### index.html

```html
<!DOCTYPE html>
<html lang="">
  <head>
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width,initial-scale=1.0">
    <link rel="icon" href="<%= BASE_URL %>favicon.ico">
    <title><%= htmlWebpackPlugin.options.title %></title>
  </head>
  <body>
    <noscript>
      <strong>We're sorry but <%= htmlWebpackPlugin.options.title %> doesn't work properly without JavaScript enabled. Please enable it to continue.</strong>
    </noscript>
    <div id="app"></div>
    <!-- built files will be auto injected -->
  </body>
</html>
```

##### App.vue

```vue
<template>
  <div>
    <School></School>
    <Student></Student>
  </div>

</template>

<script>
//引入组件
import School from './components/School'
import Student from "./components/Student";
export default {
  name: "App",
  components: {
    School,
    Student
  }
}
</script>

<style scoped>

</style>
```

##### main.js

```js
import Vue from 'vue'
import App from './App.vue'

Vue.config.productionTip = false

new Vue({
    el: '#app',
    //将App组件放入容器中
    render: h => h(App),
})
```

##### Student.vue

```vue
<template>
  <div>
    <h2>学生姓名：{{ name }}</h2>
    <h2>学生年龄：{{ age }}</h2>
  </div>
</template>

<script>
export default {
  //在浏览器vue插件中的名字
  name: "StudentName",
  data() {
    return {
      name: 'Allen',
      age: 18
    }
  }
}
</script>

<style scoped>

</style>
```

##### School.vue

```vue
<template>
  <!--  组件的结构-->
  <div class="demo">
    <h2>学校名称：{{ name }}</h2>
    <h2>学校地址：{{ address }}</h2>
    <button @click="showName">点我提示学校名</button>
  </div>
</template>

<script>
// 组件交互相关的代码(数据、方法等)
export default {
  //在浏览器vue插件中的名字
  name: 'SchoolName',
  data() {
    return {
      name: '尚硅谷',
      address: '北京昌平'
    }
  },
  methods: {
    showName() {
      alert(this.name)
    }
  }
}
</script>

<style>
/*组件的样式*/
.demo {
  background-color: antiquewhite;
}
</style>
```

#####  render函数

```js
import Vue from 'vue'
import App from './App.vue'

Vue.config.productionTip = false

new Vue({
    el:'#app',
    // 简写形式
	render: h => h(App),
    // 完整形式
	// render(createElement){
	//     return createElement(App)
	// }
})
```

总结：

关于不同版本的函数：

1. vue.js 与 vue.runtime.xxx.js的区别：

   vue.js 是完整版的 Vue，包含：核心功能+模板解析器

   vue.runtime.xxx.js 是运行版的 Vue，只包含核心功能，没有模板解析器

2. 因为 vue.runtime.xxx.js 没有模板解析器，所以不能使用 template 配置项，需要使用 render函数接收到的createElement 函数去指定具体内容

![image-20220608101149247](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220608101149247.png)

#### 修改默认配置

- `vue.config.js` 是一个可选的配置文件，如果项目的（和 `package.json` 同级的）根目录中存在这个文件，那么它会被 `@vue/cli-service` 自动加载
- 使用 `vue.config.js` 可以对脚手架进行个性化定制，详见[配置参考 | Vue CLI](https://cli.vuejs.org/zh/config/#vue-config-js)

```js
module.exports = {
    pages: {
        index: {
            // 入口
            entry: 'src/index/main.js'
        }
    },
  // 关闭语法检查
  lineOnSave:false
}
```

### ref

ref属性的用处：

1. 被用来给元素或子组件注册引用信息（id的替代者）

2. 应用在html标签上获取的是**真实DOM元素**，应用在组件标签上获取的是**组件实例对象（vc）**

3. 使用方式：

   - 打标识：<h1 ref="xxx"></h1> 或 <School ref="xxx"></School>

   - 获取：this.$refs.xxx

```vue
<template>
  <div>
    <School ref="sch"/>
    <h1 v-text="msg" ref="title"></h1>
    <button ref="btn" @click="showDom">点我</button>
  </div>

</template>

<script>
//引入组件
import School from './components/School'
//对外暴露
export default {
  name: "App",
  components: {
    School,
  },
  data() {
    return {
      msg: '欢迎学习Vue'
    }
  },
  methods: {
    showDom() {
      console.log(this.$refs.btn)
      console.log(this.$refs.title)
      console.log(this.$refs.sch)
    }
  }

}
</script>

<style scoped>

</style>
```

### props

使用 props 将信息/数据从 **父组件传递到子组件**

🔥🔥**单向数据流：**

1. 所有的 prop 都使得其父子 prop 之间形成了一个**单向下行绑定**：父级 prop 的更新会向下流动到子组件中，但是反过来则不行。这样会防止从子组件意外变更父级组件的状态，从而导致你的应用的数据流向难以理解。
2. 额外的，每次父级组件发生变更时，子组件中所有的 prop 都将会刷新为最新的值。这意味着你**不应该在一个子组件内部改变 prop**。如果你这样做了，Vue 会在浏览器的控制台中发出警告。

#### 传递静态或动态Prop

```
<Student name="Allen Li" sex="male" age="18"></Student>
```

age的 18 会被解析为字符串

```
<!-- 即便 `42` 是静态的，我们仍然需要 v-bind 来告诉 Vue 这是一个 JavaScript 表达式而不是一个字符串。--> 
<Student name="Allen Li" sex="male" :age="18"></Student>
```

此时age中 18 为数字

App.vue

```vue
<template>
  <div>
    <Student name="Allen Li" sex="male" :age="18"></Student>
  </div>

</template>

<script>
//引入组件
import Student from './components/Student'
//对外暴露
export default {
  name: "App",
  components: {
    Student,
  }
}
</script>

<style scoped>

</style>
```

Student.vue

```vue
<template>
  <div>
    <h2>学生姓名：{{ name }}</h2>
    <h2>学生年龄：{{ age }}</h2>
    <h2>学生性别：{{ sex }}</h2>
  </div>
</template>

<script>
export default {
  //在浏览器vue插件中的名字
  name: "StudentName",
  data() {
    return {
      msg: '我是NWU的Rookie',
    }
  },
  // 简单声明接收
  // props: ['name', 'sex', 'age']

  // 接收的同时对数据进行类型限制
  // props: {
  //   name: String,
  //   age: Number,
  //   sex: String
  // }

  // 接收的同时对数据进行类型限制 + 指定默认值 + 限制必要性
  props:{
    name:{
      type:String,
      required:true,
    },
    age:{
      type:Number,
      default:99
    },
    sex:{
      type:String,
      required:true
    }
  }
}
</script>

<style scoped>

</style>
```

main.js

```javascript
import Vue from 'vue'
import App from './App.vue'

Vue.config.productionTip = false

new Vue({
    //绑定容器
    el: '#app',
    //将App组件放入容器中,在 index.html中
    render: h => h(App),
})
```

index.html

```html
<!DOCTYPE html>
<html lang="">
  <head>
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width,initial-scale=1.0">
    <link rel="icon" href="<%= BASE_URL %>favicon.ico">
    <title><%= htmlWebpackPlugin.options.title %></title>
  </head>
  <body>
    <noscript>
      <strong>We're sorry but <%= htmlWebpackPlugin.options.title %> doesn't work properly without JavaScript enabled. Please enable it to continue.</strong>
    </noscript>
    <div id="app"></div>
    <!-- built files will be auto injected -->
  </body>
</html>
```

### mixin

两个组件共享一个配置项

![image-20220609111343723](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220609111343723.png)

App.vue

```vue
<template>
  <div>
    <Student></Student>
    <MySchool></MySchool>
  </div>

</template>

<script>
//引入组件
import Student from './components/Student'
import MySchool from "@/components/School";
//对外暴露
export default {
  name: "App",
  components: {
    MySchool,
    Student,
  }
}
</script>

<style scoped>
</style>
```

Student.vue

```vue
<template>
  <div class="st">
    <h2 @click="showName">学生性别：{{ sex }}</h2>
  </div>
</template>

<script>
import {mixin} from "@/mixin";

export default {
  //在浏览器vue插件中的名字
  name: "StudentName",
  data() {
    return {
      name: 'Rookie',
      age: 18,
      sex: 'male'
    }
  },
  mixins: [mixin]


}
</script>

<style scoped>
.st{
  background-color: aqua;
}
</style>
```

School.vue

```vue
<template>
  <!--  组件的结构-->
  <div class="demo">
    <h2 @click="showName">学校地址：{{ address }}</h2>
  </div>
</template>

<script>
import {mixin} from "@/mixin";
// 组件交互相关的代码(数据、方法等)
export default {
  //在浏览器vue插件中的名字
  name: 'MySchool',
  data() {
    return {
      name: '尚硅谷',
      address: '北京昌平'
    }
  },
  mixins: [mixin]
}
</script>

<style>
/*组件的样式*/
.demo {
  background-color: antiquewhite;
}
</style>
```

mixin.js

```js
export const mixin = {
    methods: {
        showName() {
            alert(this.name)
        }
    }
}
```

 main.js

```javascript
import Vue from 'vue'
import App from './App.vue'

Vue.config.productionTip = false

new Vue({
    //绑定容器
    el: '#app',
    //将App组件放入容器中,在 index.html中
    render: h => h(App),
})
```

**效果:**

![image-20220609114521366](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220609114521366.png)

![image-20220609114540838](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220609114540838.png)

#### 选项合并

当组件和混入对象含有同名选项时，这些选项将以恰当的方式进行“合并”。

###### 数据对象、组件对象与mixin中冲突

1. 数据对象在内部会进行递归合并，并在发生冲突时**以组件数据优先**。
2. 值为对象的选项，例如 `methods`、`components` 和 `directives`，将被合并为同一个对象。两个对象键名冲突时，**取组件对象的键值对**。

例：组件中的 `methods`与`mixin`中的`methods `冲突

**App.vue**

```vue
<template>
  <div>
    <Student></Student>
    <MySchool></MySchool>
  </div>

</template>

<script>
//引入组件
import Student from './components/Student'
import MySchool from "@/components/School";
//对外暴露
export default {
  name: "App",
  components: {
    MySchool,
    Student,
  }
}
</script>

<style scoped>

</style>
```

**Student.vue**

```vue
<template>
  <!--  组件的结构-->
  <div class="demo">
    <h2 @click="showName">学校地址：{{ address }}</h2>
  </div>
</template>

<script>
import {mixin} from "@/mixin";
// 组件交互相关的代码(数据、方法等)
export default {
  //在浏览器vue插件中的名字
  name: 'MySchool',
  data() {
    return {
      name: '尚硅谷',
      address: '北京昌平'
    }
  },
  mixins: [mixin],
  methods: {
    showName()  {
      alert('Hahahahaha!!!')
    }
  }
}
</script>

<style>
/*组件的样式*/
.demo {
  background-color: antiquewhite;
}
</style>
```

**mixin.js**

```js
export const mixin = {
    methods: {
        showName() {
            alert(this.name)
        }
    }
}
```

**index.js**

```html
<!DOCTYPE html>
<html lang="">
  <head>
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width,initial-scale=1.0">
    <link rel="icon" href="<%= BASE_URL %>favicon.ico">
    <title><%= htmlWebpackPlugin.options.title %></title>
  </head>
  <body>
    <noscript>
      <strong>We're sorry but <%= htmlWebpackPlugin.options.title %> doesn't work properly without JavaScript enabled. Please enable it to continue.</strong>
    </noscript>
    <div id="app"></div>
    <!-- built files will be auto injected -->
  </body>
</html>
```

**main.js**

```js
import Vue from 'vue'
import App from './App.vue'

Vue.config.productionTip = false

new Vue({
    //绑定容器
    el: '#app',
    //将App组件放入容器中,在 index.html中
    render: h => h(App),
})
```

###### 钩子函数与与mixin中冲突

同名钩子函数将合并为一个数组，**因此都将被调用**。另外，混入对象的钩子将在组件自身钩子**之前**调用。

**App.vue**

```vue
<template>
  <div>
    <MySchool></MySchool>
  </div>

</template>

<script>
//引入组件
import MySchool from "@/components/School";
//对外暴露
export default {
  name: "App",
  components: {
    MySchool,
  }
}
</script>

<style scoped>

</style>
```

**School.vue**

```vue
<template>
  <!--  组件的结构-->
  <div class="demo">
    <h2>学校地址：{{ address }}</h2>
  </div>
</template>

<script>
import {mixin} from "@/mixin";
// 组件交互相关的代码(数据、方法等)
export default {
  //在浏览器vue插件中的名字
  name: 'MySchool',
  data() {
    return {
      name: '尚硅谷',
      address: '北京昌平'
    }
  },
  mixins: [mixin],
  mounted() {
    console.log('我是Student组件的的mounted函数')
  }
}
</script>

<style>
/*组件的样式*/
.demo {
  background-color: antiquewhite;
}
</style>
```

**mixin.js**

```js
export const mixin = {
    mounted() {
        console.log('我是mixin的mounted函数')
    }
}
```

**index.html**

```html
<!DOCTYPE html>
<html lang="">
  <head>
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width,initial-scale=1.0">
    <link rel="icon" href="<%= BASE_URL %>favicon.ico">
    <title><%= htmlWebpackPlugin.options.title %></title>
  </head>
  <body>
    <noscript>
      <strong>We're sorry but <%= htmlWebpackPlugin.options.title %> doesn't work properly without JavaScript enabled. Please enable it to continue.</strong>
    </noscript>
    <div id="app"></div>
    <!-- built files will be auto injected -->
  </body>
</html>
```

**main.js**

```js
import Vue from 'vue'
import App from './App.vue'

Vue.config.productionTip = false

new Vue({
    //绑定容器
    el: '#app',
    //将App组件放入容器中,在 index.html中
    render: h => h(App),

})
```

**效果：**

![image-20220609122141738](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220609122141738.png)

#### 全局混入

混入也可以进行全局注册。使用时格外小心！一旦使用全局混入，它将影响**每一个**之后创建的 Vue 实例。使用恰当时，这可以用来为自定义选项注入处理逻辑。

```js
// 为自定义的选项 'myOption' 注入一个处理器。
Vue.mixin({
  created: function () {
    var myOption = this.$options.myOption
    if (myOption) {
      console.log(myOption)
    }
  }
})

new Vue({
  myOption: 'hello!'
})
// => "hello!"
```

请谨慎使用全局混入，因为它会影响每个单独创建的 Vue 实例 (包括第三方组件)。大多数情况下，只应当应用于自定义选项，就像上面示例一样。推荐将其作为[插件](https://cn.vuejs.org/v2/guide/plugins.html)发布，以避免重复应用混入。

### 插件

插件通常用来为 Vue 添加全局功能。插件的功能范围没有严格的限制——一般有下面几种：

1. 添加全局方法或者 property。如：[vue-custom-element](https://github.com/karol-f/vue-custom-element)
2. 添加全局资源：指令/过滤器/过渡等。如 [vue-touch](https://github.com/vuejs/vue-touch)
3. 通过全局混入来添加一些组件选项。如 [vue-router](https://github.com/vuejs/vue-router)
4. 添加 Vue 实例方法，通过把它们添加到 `Vue.prototype` 上实现。
5. 一个库，提供自己的 API，同时提供上面提到的一个或多个功能。如 [vue-router](https://github.com/vuejs/vue-router)

**App.vue**

```vue
<template>
  <div>
    <School></School>
    <Student></Student>
  </div>

</template>

<script>
//引入组件
import School from "./components/School";
import Student from "./components/Student";

//对外暴露
export default {
  name: "App",
  components: {
    Student,
    School,
  }
}
</script>

<style scoped>

</style>
```

**Student.vue**

```vue
<template>
  <div class="st">
    <h2>学生性别：{{ sex }}</h2>
    <button @click="test">点我看插件添加的方法</button>
  </div>
</template>

<script>

export default {
  //在浏览器vue插件中的名字
  name: "StudentName",
  data() {
    return {
      name: 'Rookie',
      age: 18,
      sex: 'male'
    }
  },
  methods: {
    test() {
      this.hello()
    }
  }
}
</script>

<style scoped>
.st{
  background-color: aqua;
}
</style>
```

**School.vue**

```vue
<template>
  <!--  组件的结构-->
  <div class="demo">
    <!--使用了插件中的过滤器-->
    <h2>学校名称：{{ name | mySlice }}</h2>
  </div>
</template>

<script>
// 组件交互相关的代码(数据、方法等)
export default {
  //在浏览器vue插件中的名字
  name: 'MySchool',
  data() {
    return {
      name: '尚硅谷西安分谷',
      address: '北京昌平'
    }
  },
}
</script>

<style>
/*组件的样式*/
.demo {
  background-color: antiquewhite;
}
</style>
```

**Plugin.js**

```js
export default {
    install(Vue) {
        //全局过滤器
        Vue.filter('mySlice', function (value) {
            return value.slice(0, 4)
        })

        //定义混入
        Vue.mixin({
            data() {
                return {
                    x: 100,
                    y: 200
                }
            },
        })

        //给Vue原型上添加一个方法（vm和vc就都能用了）
        Vue.prototype.hello = () => {
            alert('你好啊,我是插件添加的方法')
        }
    }

}
```

**main.js**

```js
import Vue from 'vue'
import App from './App.vue'
import plugin from './plugin'

Vue.config.productionTip = false

//使用插件
Vue.use(plugin)

new Vue({
    //绑定容器
    el: '#app',
    //将App组件放入容器中,在 index.html中
    render: h => h(App),

})
```

### scope

`scoped`样式：

1. 作用：让样式在局部生效，防止冲突
2. 写法：`<style scoped>`

> `scoped`样式一般不会在`App.vue`中使用

**School.vue**

```vue
<template>
  <!--  组件的结构-->
  <div class="demo">
    <!--使用了插件中的过滤器-->
    <h2>学校名称：{{ name | mySlice }}</h2>
  </div>
</template>

<script>
// 组件交互相关的代码(数据、方法等)
export default {
  //在浏览器vue插件中的名字
  name: 'MySchool',
  data() {
    return {
      name: '尚硅谷西安分谷',
      address: '北京昌平'
    }
  },
}
</script>

<style scoped>
/*组件的样式*/
.demo {
  background-color: antiquewhite;
}
</style>
```

## Todo-List案例

### 效果图

<img src="https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220609165846698.png" alt="image-20220609165846698" style="zoom:67%;" />

### 目录结构

```shell
.
|-- App.vue
|-- components
|   |-- MyFooter.vue
|   |-- MyHeader.vue
|   |-- MyItem.vue
|   `-- MyList.vue
`-- main.js
```

### 同级组件的通信

利用父组件

![image-20220610115829496](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220610115829496.png)

初级做法

利用props将App.vue中的函数传给Header.vue

这里都是利用App.vue的props一级一级往下传

**MyFooter.vue**

```html
<template>
  <div class="todo-footer" v-show="todos.length">
    <label>
      <input type="checkbox" v-model="isAll"/>
    </label>
    <span>
          <span>已完成{{ doneTotal }}</span> / 全部{{ todos.length }}
    </span>
    <button class="btn btn-danger" @click="clearAll">清除已完成任务</button>
  </div>
</template>

<script>
export default {
  name: "MyFooter",
  props: ['todos', 'checkAllTodo', 'clearAllTodo'],
  computed: {
    doneTotal() {
      let i = 0
      this.todos.forEach((todo) => {
        if (todo.done === true) i++
      })
      return i
    },
    //控制全选框
    isAll: {
      //全选框是否勾选
      get() {
        return this.doneTotal === this.todos.length && this.todos.length > 0
      },
      //isAll被修改时set被调用
      set(value) {
        this.checkAllTodo(value)
      }
    }
  },
  methods: {
    /* checkAll(e){
      this.checkAllTodo(e.target.checked)
    } */
    //清空所有已完成
    clearAll() {
      this.clearAllTodo()
    }
  },
}
</script>

<style scoped>
/*footer*/
.todo-footer {
  height: 40px;
  line-height: 40px;
  padding-left: 6px;
  margin-top: 5px;
}

.todo-footer label {
  display: inline-block;
  margin-right: 20px;
  cursor: pointer;
}

.todo-footer label input {
  position: relative;
  top: -1px;
  vertical-align: middle;
  margin-right: 5px;
}

.todo-footer button {
  float: right;
  margin-top: 5px;
}
</style>
```

**MyItem.vue**

```vue
<template>
  <li>
    <label>
      <input type="checkbox" :checked="todo.done" @click="handleCheck(todo.id)"/>
      <span>{{ todo.title }}</span>
    </label>
    <button class="btn btn-danger" @click="handleDelete(todo.id)">删除</button>
  </li>
</template>

<script>
export default {
  name: "MyItem",
  props: ['todo', 'checkTodo', 'deleteTodo'],
  methods: {
    handleCheck(id) {
      //通知App组件将对应的todo对象的done值取反
      this.checkTodo(id)
    },
    handleDelete(id) {
      if(confirm('确定删除吗？')){
        //通知App组件将对应的todo对象删除
        this.deleteTodo(id)
      }
    }
  }

}
</script>

<style scoped>
/*item*/
li {
  list-style: none;
  height: 36px;
  line-height: 36px;
  padding: 0 5px;
  border-bottom: 1px solid #ddd;
}

li label {
  float: left;
  cursor: pointer;
}

li label li input {
  vertical-align: middle;
  margin-right: 6px;
  position: relative;
  top: -1px;
}

li button {
  float: right;
  display: none;
  margin-top: 3px;
}

li:before {
  content: initial;
}

li:last-child {
  border-bottom: none;
}

li:hover {
  background-color: #ddd;
}

li:hover button {
  display: block;
}
</style>
```

**MyHeader.vue**

```vue
<template>
  <div class="todo-header">
    <input type="text" placeholder="请输入你的任务名称，按回车键确认" v-model="title" @keyup.enter="add()"/>
  </div>
</template>

<script>
import {nanoid} from 'nanoid'

export default {
  name: "MyHeader",
  data() {
    return {
      title: '',
    }
  },
  //接收从App传递过来的addTodo
  props: ['addTodo', ],
  methods: {
    add() {
      //校验数据
      if (!this.title.trim()) return alert('输入不能为空')
      //将用户的输入包装成一个todo对象
      const todoObj = {id: nanoid(), title: this.title, done: false}
      //通知App组件去添加一个todo对象
      this.addTodo(todoObj)
      //清空输入
      this.title = ''
    }
  }
}
</script>

<style scoped>
/*header*/
.todo-header input {
  width: 560px;
  height: 28px;
  font-size: 14px;
  border: 1px solid #ccc;
  border-radius: 4px;
  padding: 4px 7px;
}

.todo-header input:focus {
  outline: none;
  border-color: rgba(82, 168, 236, 0.8);
  box-shadow: inset 0 1px 1px rgba(0, 0, 0, 0.075), 0 0 8px rgba(82, 168, 236, 0.6);
}
</style>
```

**MyList.vue**

```vue
<template>
  <ul class="todo-main">
    <!--:key="todoObj.id 唯一标识， v-bind="todoObj"为父组件MyList传递数据给子组件MyItem数据-->
    <MyItem v-for="todoObj in todos" :key="todoObj.id" :todo="todoObj" :checkTodo="checkTodo" :deleteTodo="deleteTodo"/>
  </ul>
</template>

<script>
import MyItem from "./MyItem";

export default {
  name: "MyList",
  components: {
    MyItem
  },
  //用props从父组件App.vue向子组件MyList.vue传递数据
  props: ['todos', 'checkTodo','deleteTodo']

}
</script>

<style scoped>
/*main*/
.todo-main {
  margin-left: 0px;
  border: 1px solid #ddd;
  border-radius: 2px;
  padding: 0px;
}

.todo-empty {
  height: 40px;
  line-height: 40px;
  border: 1px solid #ddd;
  border-radius: 2px;
  padding-left: 5px;
  margin-top: 10px;
}
</style>
```

**App.vue**

```vue
<template>
  <div id="root">
    <div class="todo-container">
      <div class="todo-wrap">
        <MyHeader :addTodo="addTodo"/>
        <!--:todos="todos"用props传给子组件MyList，v-bind 来告诉这是一个 JavaScript 表达式而不是一个字符串-->
        <MyList :todos="todos" :checkTodo="checkTodo" :deleteTodo="deleteTodo"/>
        <MyFooter :todos="todos" :checkAllTodo="checkAllTodo" :clearAllTodo="clearAllTodo"/>
      </div>
      '


    </div>
  </div>

</template>

<script>
//引入组件
import MyList from "./components/MyList";
import MyFooter from "./components/MyFooter";
import MyHeader from "./components/MyHeader";

//对外暴露
export default {
  name: "App",
  components: {
    MyHeader,
    MyFooter,
    MyList
  },
  data() {
    return {
      todos: [
        {id: '001', title: '抽烟', done: false},
        {id: '002', title: '喝酒', done: false},
        {id: '003', title: '烫头', done: false},
      ]
    }
  },
  methods: {
    ////添加一个todo
    addTodo(todoObj) {
      this.todos.unshift(todoObj)
    },
    //勾选或者取消一个todo
    checkTodo(id) {
      this.todos.forEach((todo) => {
        if (todo.id === id) todo.done = !todo.done
      })
    },
    //删除一个todo
    deleteTodo(id) {
      this.todos = this.todos.filter(todo => todo.id !== id)
    },
    //全选或者取消全选
    checkAllTodo(done) {
      this.todos.forEach((todo) => {
        todo.done = done
      })
    },
    //清除所有已经完成的todo
    clearAllTodo() {
      this.todos = this.todos.filter((todo) => {
        return !todo.done
      })
    }
  }
}
</script>

<style>
/*base*/
body {
  background: #fff;
}

.btn {
  display: inline-block;
  padding: 4px 12px;
  margin-bottom: 0;
  font-size: 14px;
  line-height: 20px;
  text-align: center;
  vertical-align: middle;
  cursor: pointer;
  box-shadow: inset 0 1px 0 rgba(255, 255, 255, 0.2), 0 1px 2px rgba(0, 0, 0, 0.05);
  border-radius: 4px;
}

.btn-danger {
  color: #fff;
  background-color: #da4f49;
  border: 1px solid #bd362f;
}

.btn-danger:hover {
  color: #fff;
  background-color: #bd362f;
}

.btn:focus {
  outline: none;
}

.todo-container {
  width: 600px;
  margin: 0 auto;
}

.todo-container .todo-wrap {
  padding: 10px;
  border: 1px solid #ddd;
  border-radius: 5px;
}


</style>
```

**组件化编码流程**

1 拆分静态组件：组件要按照功能点拆分，命名不要与html元素冲突 

2 实现动态组件：考虑好数据的存放位置，数据是一个组件在用，还是一些组件在用 

- 一个组件在用：放在组件自身即可 
- 一些组件在用：放在他们共同的父组件上（状态提升） 

3 实现交互：从绑定事件开始 

props适用于 

- a 父组件 ==> 子组件 通信 
- b 子组件 ==> 父组件 通信（要求父组件先给子组件一个函数） 

使用v-model时要切记：v-model绑定的值不能是props传过来的值，因为props**是不可以修改的** 

props传过来的若是对象类型的值，修改对象中的属性时Vue不会报错，但不推荐这样做

## 本地存储

存储内容大小一般支持 5MB 左右（不同浏览器可能还不一样） 

浏览器端通过`Window.sessionStorage和Window.localStorage`属性来实现本地存储机制 相关API 

`xxxStorage.setItem('key', 'value')`该方法接受一个键和值作为参数，会把键值对添加到存储中，如果键名存在，则更新其对应的值 

`xxxStorage.getItem('key')`该方法接受一个键名作为参数，返回键名对应的值 

`xxxStorage.removeItem('key')`该方法接受一个键名作为参数，并把该键名从存储中删除 

`xxxStorage.clear()`该方法会清空存储中的所有数据

 备注 

- SessionStorage存储的内容会随着浏览器窗口关闭而消失 
- LocalStorage存储的内容，需要手动清除才会消失 ○ xxxStorage.getItem(xxx)如果 xxx 对应的 value 获取不到，那么getItem()的返回值是null 
-  JSON.parse(null)的结果依然是null localStorage

### localStorage

```html
<h2>localStorage</h2>
<button onclick="saveDate()">点我保存数据</button><br/>
<button onclick="readDate()">点我读数据</button><br/>
<button onclick="deleteDate()">点我删除数据</button><br/>
<button onclick="deleteAllDate()">点我清空数据</button><br/>

<script>
  let person = {name:"JOJO",age:20}

  function saveDate(){
    localStorage.setItem('msg','localStorage')
    localStorage.setItem('person',JSON.stringify(person))
  }
  function readDate(){
    console.log(localStorage.getItem('msg'))
    const person = localStorage.getItem('person')
    console.log(JSON.parse(person))
  }
  function deleteDate(){
    localStorage.removeItem('msg')
    localStorage.removeItem('person')
  }
  function deleteAllDate(){
    localStorage.clear()
  }
</script>
```

### sessionStorage

```html
<h2>sessionStorage</h2>
<button onclick="saveDate()">点我保存数据</button><br/>
<button onclick="readDate()">点我读数据</button><br/>
<button onclick="deleteDate()">点我删除数据</button><br/>
<button onclick="deleteAllDate()">点我清空数据</button><br/>

<script>
  let person = {name:"JOJO",age:20}

  function saveDate(){
    sessionStorage.setItem('msg','sessionStorage')
    sessionStorage.setItem('person',JSON.stringify(person))
  }
  function readDate(){
    console.log(sessionStorage.getItem('msg'))
    const person = sessionStorage.getItem('person')
    console.log(JSON.parse(person))
  }
  function deleteDate(){
    sessionStorage.removeItem('msg')
    sessionStorage.removeItem('person')
  }
  function deleteAllDate(){
    sessionStorage.clear()
  }
</script>
```

### 使用本地存储优化Todo-List案例

```vue
<template>
	<div id="root">
		<div class="todo-container">
			<div class="todo-wrap">
				<MyHeader :addTodo="addTodo"/>
				<MyList :todos="todos" :checkTodo="checkTodo" :deleteTodo="deleteTodo"/>
				<MyFooter :todos="todos" :checkAllTodo="checkAllTodo" :clearAllTodo="clearAllTodo"/>
			</div>
		</div>
	</div>
</template>

<script>
	import MyHeader from './components/MyHeader'
	import MyList from './components/MyList'
	import MyFooter from './components/MyFooter.vue'

	export default {
		name:'App',
		components:{MyHeader,MyList,MyFooter},
		data() {
			return {
				// 🔴从本地存储中获得数据，null就创建空数组[]
				todos:JSON.parse(localStorage.getItem('todos')) || []
			}
		},
		methods: {
			//添加一个todo
			addTodo(todoObj){
				this.todos.unshift(todoObj)
			},
			//勾选or取消勾选一个todo
			checkTodo(id){
				this.todos.forEach((todo)=>{
					if(todo.id === id) todo.done = !todo.done
				})
			},
			//删除一个todo
			deleteTodo(id){
				this.todos = this.todos.filter( todo => todo.id !== id )
			},
			//全选or取消全选
			checkAllTodo(done){
				this.todos.forEach((todo)=>{
					todo.done = done
				})
			},
			//清除所有已经完成的todo
			clearAllTodo(){
				this.todos = this.todos.filter((todo)=>{
					return !todo.done
				})
			}
		},
    // 🔴数据发生改变就放到本地存储中，注意深度侦听，以及JSON转化为字符串
		watch: {
			todos:{
				deep:true,
				handler(value){
					localStorage.setItem('todos',JSON.stringify(value))
				}
			}
		},
	}
</script>
```

## 组件的自定义事件

1. 一种组件间通信的方式，适用于：**子组件 ===&gt; 父组件**

   ① 之前**使用props + 父组件的methods** 方法来配合实现 子组件向父组件传递数据，这里使用**例外两种方法**：

   ② 通过父组件给子组件绑定一个自定义事件实现子给父传递数据（使用@或v-on）<Student @atguigu="getStudentName" @demo="m1"/>

   ③ 通过父组件给子组件绑定一个自定义事件实现子给父传递数据（使用ref）<Student ref="student" @click.**native**="show"/> 

2. 使用场景：子组件想给父组件传数据，那么就要在**父组件中给子组件绑定自定义事件**（事件的回调在A中） 

3. 绑定自定义事件 

   - 第一种方式，在父组件中<Demo @事件名="方法"/>或<Demo v-on:事件名="方法"/> 

   - 第二种方式，在父组件中this.$refs.demo.$on('事件名',方法)

   - 若想让自定义事件只能触发一次，可以使用once修饰符，或$once方法 

   - ```vue
     <Demo ref="demo"/>
     ......
     mounted(){
        this.$refs.demo.$on('atguigu',this.test)
     }
     ```

4. 触发自定义事件this.$emit('事件名',数据) 

5. 解绑自定义事件this.$off('事件名') 

6. 组件上也可以绑定原生DOM事件，需要使用native修饰符 @click.native="show" 上面绑定自定义事件，即使绑定的是原生事件也会被认为是自定义的，需要加native，加了后就将此事件给组件的根元素 

7. 注意：通过this.$refs.xxx.$on('事件名',回调函数)绑定自定义事件时，回调函数要么配置在methods中，要么用箭头函数，否则 this 指向会出问题

### ① 子组件通过props给父子间传输数据

#### App.vue

```vue
<template>
  <div id="root">
    <h2>{{schoolName}}</h2>
    <School :getSchoolName="getSchoolName"/>
  </div>

</template>

<script>
//引入组件
import School from "@/components/School";

//对外暴露
export default {
  name: "App",
  components: {
    School,

  },
  data() {
    return {
      msg: '你好啊！',
      schoolName: ''
    }
  },
  methods: {
    getSchoolName(name) {
      console.log('App.vue父组件收到了School.vue子组件传来的学生名字：', name)
      this.schoolName = name
    }
  }
}
</script>

<style>

</style>
```

#### School.vue

```vue
<template>
  <!--  组件的结构-->
  <div class="student">
    <h2>学校名称：{{ name }}</h2>
    <h2>学校地址：{{ address }}</h2>
    <button @click="sendSchoolName">点我发送学校名给App.vue</button>
  </div>
</template>

<script>
// 组件交互相关的代码(数据、方法等)
export default {
  //在浏览器vue插件中的名字
  name: 'SchoolName',
  props: ['getSchoolName'],
  data() {
    return {
      name: '尚硅谷',
      address: '北京昌平'
    }
  },
  methods: {
    sendSchoolName() {
      this.getSchoolName(this.name)
    }

  }
}
</script>

<style scoped>
.student {
  background-color: pink;
  padding: 5px;
  margin-top: 30px;
}
</style>
```

### ② 通过父组件给子组件绑定一个自定义事件实现子给父传递数据（使用@或v-on）





















## 全局事件总线

任意组件间通信













## Vuex

看到108_搭建vuex环境的同学注意了，记得使用npm i vuex@3.6.2 来安装较低的版本，不然会有一堆奇怪警告和报错，这是因为vuex4.0及以上的版本不兼容Vue2。
