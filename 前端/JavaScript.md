## 声明函数

```javascript
function logger() {
    console.log('Hello')
}

logger();


function fruitProcessor(apples, oranges) {
    console.log(apples, oranges);
    const juice = `Juice with ${apples} apples and ${oranges} oranges`;
    return juice;
}


const appleOrangeJuice = fruitProcessor(10, 20);
console.log(appleOrangeJuice);
```

使用可以放在声明前

## 匿名函数

```javascript
/* ES6 新式的匿名函数的写法   */
const calcAge = (birthYear) => {
    return 2022 - birthYear;
}

console.log(calcAge(1990)) 
```

使用不可以放在声明前

## DOM

![image-20220711192903827](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220711192903827.png)

![image-20220711192846579](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220711192846579.png)

## Class

Project：PIG GAME

![image-20220711215522034](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220711215522034.png)











## 理论

### JavaScript引擎

![image-20220711220213224](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220711220213224.png)

![image-20220711220322760](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220711220322760.png)

![image-20220711220512866](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220711220512866.png)

![image-20220711220721862](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220711220721862.png)

#### [客户端 JavaScript 中的 API](https://developer.mozilla.org/zh-CN/docs/Learn/JavaScript/Client-side_web_APIs/Introduction#客户端javascript中的api)

客户端 JavaScript 中有很多可用的 API — 他们本身并不是 JavaScript 语言的一部分，却建立在 JavaScript 语言核心的顶部，为使用 JavaScript 代码提供额外的超强能力。他们通常分为两类：

- **浏览器 API**内置于 Web 浏览器中，能从浏览器和电脑周边环境中提取数据，并用来做有用的复杂的事情。例如[Geolocation API](https://developer.mozilla.org/en-US/docs/Web/API/Geolocation_API)提供了一些简单的 JavaScript 结构以获得位置数据，因此您可以在 Google 地图上标示您的位置。在后台，浏览器确实使用一些复杂的低级代码（例如 C++）与设备的 GPS 硬件（或可以决定位置数据的任何设施）通信来获取位置数据并把这些数据返回给您的代码中使用浏览器环境；但是，这种复杂性通过 API 抽象出来，因而与您无关。
- **第三方 API**缺省情况下不会内置于浏览器中，通常必须在 Web 中的某个地方获取代码和信息。例如[Twitter API](https://dev.twitter.com/overview/documentation) 使您能做一些显示最新推文这样的事情，它提供一系列特殊的结构，可以用来请求 Twitter 服务并返回特殊的信息。

![img](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/browser.png)

#### [JavaScript，API 和其他 JavaScript 工具之间的关系](https://developer.mozilla.org/zh-CN/docs/Learn/JavaScript/Client-side_web_APIs/Introduction#javascript，api和其他javascript工具之间的关系)

如上所述，我们讨论了什么是客户端 JavaScript API，以及它们与 JavaScript 语言的关系。让我们回顾一下，使其更清晰，并提及其他 JavaScript 工具的适用位置：

- JavaScript — 一种内置于浏览器的高级脚本语言，您可以用来实现 Web 页面/应用中的功能。注意 JavaScript 也可用于其他象[Node](https://developer.mozilla.org/en-US/docs/Learn/Server-side/Express_Nodejs/Introduction)这样的的编程环境。但现在您不必考虑这些。
- 客户端 API — 内置于浏览器的结构程序，位于 JavaScript 语言顶部，使您可以更容易的实现功能。
- 第三方 API — 置于第三方普通的结构程序（例如 Twitter，Facebook），使您可以在自己的 Web 页面中使用那些平台的某些功能（例如在您的 Web 页面显示最新的 Tweets）。
- JavaScript 库 — 通常是包含具有[特定功能](https://developer.mozilla.org/en-US/docs/Learn/JavaScript/Building_blocks/Functions#custom_functions)的一个或多个 JavaScript 文件，把这些文件关联到您的 Web 页以快速或授权编写常见的功能。例如包含 jQuery 和 Mootools
- JavaScript 框架 — 从库开始的下一步，JavaScript 框架视图把 HTML、CSS、JavaScript 和其他安装的技术打包在一起，然后用来从头编写一个完整的 Web 应用。

#### [API 可以做什么？](https://developer.mozilla.org/zh-CN/docs/Learn/JavaScript/Client-side_web_APIs/Introduction#api可以做什么？)

在主流浏览器中有大量的可用 API，您可以在代码中做许多的事情，对此可以查看[MDN API index page](https://developer.mozilla.org/en-US/docs/Web/API)。

#### [常见浏览器 API](https://developer.mozilla.org/zh-CN/docs/Learn/JavaScript/Client-side_web_APIs/Introduction#常见浏览器api)

特别地，您将使用的最常见的浏览器 API 类别（以及我们将更详细地介绍的）是：

- ❗❗❗**操作文档的 API**内置于浏览器中。最明显的例子是[DOM（文档对象模型）](https://developer.mozilla.org/zh-CN/docs/Web/API/Document_Object_Model)API，它允许您操作 HTML 和 CSS — 创建、移除以及修改 HTML，动态地将新样式应用到您的页面，等等。每当您看到一个弹出窗口出现在一个页面上，或者显示一些新的内容时，这都是 DOM 的行为。 您可以在在[Manipulating documents](https://developer.mozilla.org/zh-CN/docs/Learn/JavaScript/Client-side_web_APIs/Manipulating_documents)中找到关于这些类型的 API 的更多信息。
- **从服务器获取数据的 API** 用于更新网页的一小部分是相当好用的。这个看似很小的细节能对网站的性能和行为产生巨大的影响 — 如果您只是更新一个股票列表或者一些可用的新故事而不需要从服务器重新加载整个页面将使网站或应用程序感觉更加敏感和“活泼”。使这成为可能的 API 包括[`XMLHttpRequest`](https://developer.mozilla.org/zh-CN/docs/Web/API/XMLHttpRequest)和[Fetch API](https://developer.mozilla.org/zh-CN/docs/Web/API/Fetch_API)。您也可能会遇到描述这种技术的术语**Ajax**。您可以在[Fetching data from the server](https://developer.mozilla.org/zh-CN/docs/Learn/JavaScript/Client-side_web_APIs/Fetching_data)找到关于类似的 API 的更多信息。
- **用于绘制和操作图形的 API**目前已被浏览器广泛支持 — 最流行的是允许您以编程方式更新包含在 HTML元素中的像素数据以创建 2D 和 3D 场景的[Canvas](https://developer.mozilla.org/en-US/docs/Web/API/Canvas_API)和[WebGL](https://developer.mozilla.org/en-US/docs/Web/API/WebGL_API)。例如，您可以绘制矩形或圆形等形状，将图像导入到画布上，然后使用 Canvas API 对其应用滤镜（如棕褐色滤镜或灰度滤镜），或使用 WebGL 创建具有光照和纹理的复杂 3D 场景。这些 API 经常与用于创建动画循环的 API（例如[`window.requestAnimationFrame()`](https://developer.mozilla.org/zh-CN/docs/Web/API/Window/requestAnimationFrame)）和其他 API 一起不断更新诸如动画和游戏之类的场景。
- **音频和视频 API**例如[`HTMLMediaElement`](https://developer.mozilla.org/zh-CN/docs/Web/API/HTMLMediaElement)，[Web Audio API](https://developer.mozilla.org/zh-CN/docs/Web/API/Web_Audio_API)和[WebRTC](https://developer.mozilla.org/zh-CN/docs/MDN/Doc_status/API/WebRTC)允许您使用多媒体来做一些非常有趣的事情，比如创建用于播放音频和视频的自定义 UI 控件，显示字幕字幕和您的视频，从网络摄像机抓取视频，通过画布操纵（见上），或在网络会议中显示在别人的电脑上，或者添加效果到音轨（如增益，失真，平移等） 。
- **设备 API**基本上是以对网络应用程序有用的方式操作和检索现代设备硬件中的数据的 API。我们已经讨论过访问设备位置数据的地理定位 API，因此您可以在地图上标注您的位置。其他示例还包括通过系统通知（参见[Notifications API](https://developer.mozilla.org/zh-CN/docs/Web/API/Notifications_API)）或振动硬件（参见[Vibration API](https://developer.mozilla.org/zh-CN/docs/Web/API/Vibration_API)）告诉用户 Web 应用程序有用的更新可用。
- **客户端存储 API**在 Web 浏览器中的使用变得越来越普遍 - 如果您想创建一个应用程序来保存页面加载之间的状态，甚至让设备在处于脱机状态时可用，那么在客户端存储数据将会是非常有用的。例如使用[Web Storage API](https://developer.mozilla.org/zh-CN/docs/Web/API/Web_Storage_API)的简单的键 - 值存储以及使用[IndexedDB API](https://developer.mozilla.org/zh-CN/docs/Web/API/IndexedDB_API)的更复杂的表格数据存储。

### Scope And Scope Chain

![image-20220717173821289](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220717173821289.png)

![image-20220717174451935](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220717174451935.png)

紫色是Block Scope，黄色是Function Scope，两个是平级关系。

他们之间不能互相访问

let 和 const是块变量，只能在其范围内使用

![image-20220717175407615](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220717175407615.png)

str 是Block Scope，出了这个范围使用就会报错

换成 var就可以，var是Function Scope的。

![image-20220717180216142](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220717180216142.png)

![image-20220717180947460](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220717180947460.png)

### this

![image-20220717181400221](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220717181400221.png)

```js
'use strict';

// // 这里的this指的是window object
// console.log(this);


// // 在严格模式下，this是undefined；在非严格模式下，this是window object
// const calAge = function (birthYear) {
//     console.log(this);
//     return 2022 - birthYear;
// }

// calAge(1990);

// // this是window object
// const calAgeArrow = (birthYear) => {
//     {
//         console.log(this);
//         return 2022 - birthYear;
//     }

//     calAgeArrow(1996);
// }


// const jonas = {
//     year: 1990,
//     // 这里的this指的是jonas object
//     calAge: function () {
//         console.log(this);
//         return 2022 - this.year;
//     },
//     // 这里的this指的是window object
//     calAgeArrow: () => {
//         console.log(this);
//     }
// }

// jonas.calAge();
// jonas.calAgeArrow();


// //this指向调用该方法的对象，这里this指的是allen object,而不是jonas object
// const allen = {
//     year: 2000,
// }
// allen.calAge = jonas.calAge;
// allen.calAge();


// //这里是找不到year的，这里的this指的是f函数，它没有year属性
// const f = jonas.calAge;
// f();


//普通函数和箭头函数的用法
const jonas = {
    year: 1990,
    // 这里的this指的是jonas object
    calAge: function () {
        console.log(this);
        console.log(2022 - this.year);

        // ❌ 这里的this指的是 isMillenial function,没有year属性
        // const isMillenial = function () {
        //     console.log(this.year >= 1981 && this.year <= 1996);
        // }
        // isMillenial();

        // solution 1: bind this to jonas object
        // const self = this;
        // const isMillenial = () => {
        //     console.log(self.year >= 1981 && self.year <= 1996);
        // }
        // isMillenial();

        // solution 2: use arrow function  箭头函数向上调用
        const isMillenial = () => {
            console.log(this.year >= 1981 && this.year <= 1996);
        }
        isMillenial();

    }
}

jonas.calAge();
```

### Array

...  扩展运算符，除了适用于数组，还适用于所有可迭代的（iterables） => Arrayy Set String， 没有数组

不能用取值运算符中 【 `` 】

### The call and apply Methods

call()、apply()、bind() 都是用来重定义 this 这个对象的！

call 、bind 、 apply 这三个函数的第一个参数都是 this 的指向对象，第二个参数差别就来了：

call 的参数是直接放进去的，第二第三第 n 个参数全都用逗号分隔，直接放到后面 **obj.myFun.call(db,'成都', ... ,'string' )**。

apply 的所有参数都必须放在一个数组里面传进去 **obj.myFun.apply(db,['成都', ..., 'string' ])**。

bind 除了返回是函数以外，它 的参数和 call 一样。

当然，三者的参数不限定是 string 类型，允许是各种类型，包括函数 、 object 等等！

```js
'use strict';

const lufthansa = {
    airline: 'Lufthansa',
    iataCode: 'LH',
    booking: [],
    book(flightName, name) {
        console.log(`${name} booked a seat on ${this.airline} flight ${this.iataCode} ${flightName}`);
        this.booking.push(`${this.iataCode} ${flightName}`);
    }
}

lufthansa.book('LH1234', 'John');
lufthansa.book('LH1235', 'Jane');

const eurowrings = {
    airline: 'Eurowings',
    iataCode: 'EW',
    booking: [],
}

const book = lufthansa.book;
// do not works 
// book('LH1236', 'Jack');

lufthansa.book.call(eurowrings, 'CN666', 'Allen');

lufthansa.book.apply(eurowrings, ['CN666', 'Allen']);

//bind返回的是一个新的函数，必须加'（）'调用才能执行
lufthansa.book.bind(eurowrings, 'CN666', 'Allen')();
```

### Immediately Invoked Function Expressions

🎉🎉async / await中会使用  ==> **异步调用**





### 闭包

 

## DOM

![image-20220718121625201](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220718121625201.png)

![image-20220718121950819](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220718121950819.png)

## Events

HTML 事件是发生在 HTML 元素上的事情。当在 HTML 页面中使用 JavaScript 时， JavaScript 可以触发这些事件。

[事件参考](https://developer.mozilla.org/zh-CN/docs/Web/Events)

```js
const click = function (e) {
  alert('Xixixiixix!');
}

document.querySelector('h1').addEventListener('click', click);
setTimeout(() => {
  document.querySelector('h1').removeEventListener('click', click);
}, 3000);
```

### Bubbling and Capturing

![image-20220718160614689](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220718160614689.png)

事件触发在子元素上，但父元素也会触发（冒泡到父元素）

![image-20220718161240319](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220718161240319.png)

可以使用 `e.stopPropagation()`来停止向上传播

## 遍历DOM



## prototype   对象原型

通过原型这种机制，JavaScript 中的对象从其他对象继承功能特性；这种继承机制与经典的面向对象编程语言的继承机制不同。

JavaScript 常被描述为一种**基于原型的语言 (prototype-based language)**——每个对象拥有一个**原型对象**，对象以其原型为模板、从原型继承方法和属性。原型对象也可能拥有原型，并从中继承方法和属性，一层一层、以此类推。这种关系常被称为**原型链 (prototype chain)**，它解释了为何一个对象会拥有定义在其他对象中的属性和方法。

准确地说，这些属性和方法定义在 Object 的构造器函数 (constructor functions) 之上的`prototype`属性上，而非对象实例本身。

在传统的 OOP 中，首先定义“类”，此后创建对象实例时，类中定义的所有属性和方法都被复制到实例中。在 JavaScript 中并不如此复制——而是在对象实例和它的构造器之间建立一个链接（它是__proto__属性，是从构造函数的`prototype`属性派生的），之后通过上溯原型链，在构造器中找到这些属性和方法。
