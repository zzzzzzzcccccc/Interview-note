## 1.写 React / Vue 项目时为什么要在列表组件中写 key，其作用是什么？

 * 绑定key一定要唯一，这样在修改数据的时候才能驱动虚拟dom更新
 * 某些情况下不绑定key在修改数据后dom渲染的速度是比加key要快的(dom结构必须简单)，这种就是diff的<font color="red">就地复用</font>所以比加key要快
 * 一般是要加key，在vue里不加key只有黄字警告，但是在react的diff和vue的diff稍有区别，react循环渲染key是必须要加，不然在某一些情况下setState了数据视图没有更新
 
## 2.['1', '2', '3'].map(parseInt) 执行后是输出什么，为什么输出这个?

* map函数式回调形式返回参数一共有三个参数：value, index, array 当前值 当前下标 当前遍历的数组
* parentInt是内置函数一共有两个参数: string radix 需要转化成整形的值 需要返回的进制数
  
```js
  parseInt('1', 0); // 0 默认是10进制所以返回 1
  parseInt('2', 1); // 1 是1进制最大值为1 无法计算返回 NaN
  parseInt('3', 2); // 2 是2进制最大值为2 无法计算返回 NaN
```

* 所以答案是[1, NaN, NaN],不是['1', '2', '3']

## 3.什么是防抖和节流？有什么区别？如何实现？
* 防抖：动作绑定事件，动作发生后一定时间后触发事件，在这段时间内，如果该动作又发生，则重新等待一定时间再触发事件

```js
/**
* debounce
* @param {Function} fn
* @param {Number} delay
* @returns {Function}
*/
function debounce(fn, delay) {
  let timeout = null;
  return function() {
    clearTimeout(timeout)
    timeout = setTimeout(() => {
      fn.apply(this, arguments);
    }, delay)
  }
}
```
* 节流: 动作绑定事件，在规定时间内不管事件怎么触发，事件始终执行一次
```js
/**
* throtte
* @param fn
* @param time
* @returns {Function}
*/
function throtte(fn, time) {
  let activeTime = 0;
  return function() {
    const current = Date.now();
    if (current - activeTime > time) {
      fn.apply(this, arguments);
      activeTime = Date.now();
    }
  }
}
```

## 4.Set、Map、WeakSet 和 WeakMap 的区别？
* Set
  1. 成员不能重复
  2. 只有值，没有建名，类似数组
  3. 可以遍历，有add，delete，has, clear方法
* Map
  1. 是集合、字典可以储存不重复的值
  2. 集合石已[value1, value2]形式储存，字段是已[key, value]形式储存
* WeakSet
  1. 只能存储对象引用，不能存放值，Set是可以的
* WeakMap
  1. 是一种键值对的集合(null除外)，其中建是弱引用对象，而值可以是任意
  2. 不能遍历，有get, set, has, delete方法

## 5.深度优先遍历、广度优先遍历
* 深度优先遍历：假设某视图未被访问，则从某一个顶点v出发，依次访问下去直到访问不到为止，然后再访问另外一个未被访问的顶点v，依次执行下去。（其实就是多个tree递归的集合）
* 广度优先遍历：假设某视图未被访问，则从某一个顶点v出发访问完毕后再寻找另外一个顶点v直到找不到顶点v为止，然后在访问第一个访问过的顶点v的下级顶点，依次循环下去。(多个tree都先遍历顶部，遍历完所有tree的顶部，再遍历所有的下级，依次循环下去)

## 6.ES5/ES6 的继承除了写法以外还有什么区别？
* function声明变量会提升, class不会。
```js
const bar = new Bar(); // 这里不会报错，正常的new出来了
function Bar() {
  this.foo = 1;
}
```
```js
const bar = new Bar(); // bar is not defined;
class Bar {
  constructor() {
    this.foo = 1;
  }
}
```
* class声明会默认内部开启严格模式
* class所有方法都是不可枚举的
```js
function Bar() {
  this.foo = 1;
}
Bar.prototype.answer = function() {
  return 1314;
}
Object.keys(Bar.prototype) // ['answer']
```
```js
class Bar {
  constructor() {
    this.foo = 1;
  }
  static answer() {
    return 1314;
  }
}
Object.keys(Bar.prototype) // []
```
* class的所有方法没有原型prototype,不能用new调用
```js
function Bar() {
  this.foo = 1;
}
Bar.prototype.answer = function () {
  return 1314;
}
const bar = new Bar();
const answer = new bar.answer(); // 正常
```
```js
class Bar {
  constructor(){
    this.foo = 1;
  }
  answer() {
    return 1314;
  }
}
const bar = new Bar();
const answer = new bar.answer(); // 报错
```
* class使用前必须要new
* class内部不能重写类名
```js
function Bar() {
  Bar = 'Baz'; // 正常
  this.foo = 1;
}
```
```js
class Bar {
 constructor() {
   Bar = 'Baz'; // 报错
   this.foo = 1;
 }
}
Bar = 'Baz' // 正常
```

## 7.setTimeout、Promise、Async/Await 的区别
* 此题需要基础非常优秀，不然太容易混淆，我个人理解是宏观上的任务列队与微观任务列队的区别
* 例如经常会考的这样的题目问执行console的顺序
```js
console.log(1);
const promise = new Promise((resolve) => {
  console.log(2);
  resolve();
  console.log(3);
}).then(() => {
  console.log(4);
})
setTimeout(() => {
  console.log(5);
}, 0)
console.log(6)

/**
* 输出顺序 1 -> 2 -> 3 -> 6 -> 4 -> 5
**/
```
* 又例如关于async/await的
```js
async function function1() {
  console.log(2);
  await function2();
  console.log(3)
}
async function function2() {
  console.log(4);
}
console.log(1);
function1();
console.log(5);

/**
* 输出顺序 1 -> 2 -> 4 -> 5 -> 3
**/
```
* 4个混合使用 (头条面试题)
```js
async function async1() {
  console.log(1);
  await async2();
  console.log(2);
}

async function async2() {
  console.log(3);
}

console.log(4);

setTimeout(() =>{
  console.log(5)
}, 0);

async1();

new Promise((resolve) => {
  console.log(6);
  resolve();
}).then(() => {
  console.log(7);
});
console.log(8);

/**
* 输出顺序 4 -> 1 -> 3 -> 6 -> 8 -> 2 -> 7 -> 5
**/
```
* 在js中任务为同步任务和异步任务，不同任务在主线程上执行，而异步任务只要有了执行结果就会放入任务队列中，一旦主线程任务执行完，系统就会读取任务队列将异步任务添加到可执行任务队列中，开始执行。任务执行流程图如下:
![](./lib/image/1.jpeg)

* Promise异步是提现在then和catch的，所以在then之前的是同步执行的，await是等待而在任务队列里是让出一个线程的意思await后的同步任务会先执行，但是await的任务会在任务队列里去了

## 8.资源预加载，用于活动页面，游戏开始前加载
```js
/**
* 单张图片加载
* @param src
* @returns {Promise<any>}
*/
function loadImg(src='') {
  return new Promise((resolve, reject) => {
    const img = new Image();
    img.src = src;
    img.onload = () => {
      resolve(src)
    };
    img.onerror = () => {
      reject()
    }
  })
}
const imgArray = ['http://pic37.nipic.com/20140113/8800276_184927469000_2.png', 'http://pic25.nipic.com/20121205/10197997_003647426000_2.jpg', 'http://img.redocn.com/sheji/20141219/zhongguofengdaodeliyizhanbanzhijing_3744115.jpg', 'http://pic40.nipic.com/20140331/9469669_142840860000_2.jpg', 'http://pic39.nipic.com/20140321/18063302_210604412116_2.jpg'];
const imgLen = imgArray.length;
let loadSuccessCount = 0;
for (let i = 0; i < imgLen; i++) {
  loadImg(imgArray[i]).then(() => {
    loadSuccessCount++;
    console.log(`当前加载到${loadSuccessCount}张图片，当前进度${(loadSuccessCount / imgLen * 100).toFixed(2)}%`);
  });
}
```

## 9.算法题
> 已知如下数组: [ [1, 2, 2], [3, 4, 5, 5], [6, 7, 8, 9, [11, 12, [12, 13, [14] ] ] ], 10]; 编写一个函数把数组扁皮化，最终得到一个不重复且升序的数组;
```js
// 解法1 递归
function formatArray1(realArray) {
  let nowArr = [];
  function genArray(arr) {
    if (!Array.isArray(arr)) {
      nowArr.push(arr);
      return;
    }
    let len = arr.length;
    for (let i = 0; i < len; i++) {
      genArray(arr[i]);
    }
  }
  genArray(realArray);
  
  return Array.from(new Set(nowArr)).sort((a, b) => a - b);
}
// 解法2 es6
function formatArray2(realArray) {
  return Array.from(new Set(realArray.flat(Infinity))).sort((a, b) => a - b);
}
// 解法3 论坛上找的很秀
function formatArray3(realArray) {
  return realArray.toString().split(',').sort((a, b) => a - b).map(Number)
}
```

## 10.如何实现一个new方法
```js
function _new(fn, ...arg) {
  const obj = Object.create(fn.prototype);
  const ret = fn.apply(obj, arg);
  
  return ret instanceof Object ? ret : obj;
}
```

## 11.Vue 和 React 的主要生命周期
* Vue
  1. new Vue() 创建一个vue实例
  2. beforeCreate 实例创建前
  3. created 实例已被创建
  4. beforeMount 实例已创建渲染虚拟dom前
  5. mounted 虚拟dom渲染完毕
  6. beforeUpdate data数据变化更新前
  7. updated data数据变化更新完毕
  8. beforeDestory 实例销毁前
  9. destory 实例已销毁
* React
  1. getDefaultProps 载入默认的props
  2. getInitialState 载入state
  3. componentWillMount 虚拟dom渲染前
  4. render 渲染
  5. componentDidMount 虚拟dom渲染完毕
  6. props变化触发componentWillReceiveProps再触发shouldComponentUpdate
  7. setState触发shouldComponentUpdate
  8. shouldComponentUpdate返回true则触发componentWillUpdate -> render -> componentDidUpdate
  9. componentWillUnmount 组件销毁前

## 12.TCP 三次握手与四次握手
* 三次握手
  1. 你好我是A
  2. 收到，我是B
  3. 那么我们连接了
* 四次握手
  1. 你好我是A, 我要挂了
  2. 收到我是B, 请你等等我还有一些数据没有发给你
  3. 收到，我随时准备挂了
  4. 给你了，你可以挂了
  
## 13.React setState 笔试题，下面的代码输出什么？ 
```js
class Example extends React.Component {
    constructor() {
      super();
      this.state = {
        val: 0
      };
    }
    
    componentDidMount() {
      this.setState({val: this.state.val + 1});
      console.log(this.state.val);    // 第 1 次 log
  
      this.setState({val: this.state.val + 1});
      console.log(this.state.val);    // 第 2 次 log
  
      setTimeout(() => {
        this.setState({val: this.state.val + 1});
        console.log(this.state.val);  // 第 3 次 log
  
        this.setState({val: this.state.val + 1});
        console.log(this.state.val);  // 第 4 次 log
      }, 0);
    }
  
    render() {
      return null;
    }
  };

/**
* 输出顺序 0 -> 0 -> 2 -> 3
*/
```
* 第一次和第二次都是在 react 自身生命周期内，触发时 isBatchingUpdates 为 true，所以并不会直接执行更新 state，而是加入了 dirtyComponents，所以打印时获取的都是更新前的状态 0。

* 两次 setState 时，获取到 this.state.val 都是 0，所以执行时都是将 0 设置成 1，在 react 内部会被合并掉，只执行一次。设置完成后 state.val 值为 1。

* setTimeout 中的代码，触发时 isBatchingUpdates 为 false，所以能够直接进行更新，所以连着输出 2，3。

## 14.重绘和回流，如何优化它们
* 浏览器渲染机制
  1. 浏览器是流式布局模型
  2. 浏览器会被html解析成dom，把css解析成cssom，dom和cssom组合形成了renderTree
  3. 有了renderTree就能遍历节点，计算它们在页面上的位置，最后绘制到页面上
  4. 在这种布局中，一般的dom遍历一次就能完成，但是table可能需要遍历多次, 所以table元素慎用
* 重绘：由于dom节点几何属性属性发生变化、由于样式发生变化而改变而不会影响布局的，称为重绘。重绘的代价是高昂的，因为浏览器必须验证dom树上的其他节点的可见性
* 回流: 布局、几何属性需要改变称为回流。回流是影响浏览器性能的关键元素，因为涉及到部分页面、整个页面的布局更新。一个dom元素的回流可能导致其他所有子元素以及其中紧随其后的节点、祖元素的随后回流。
* 回流必定会触发重绘，重绘不一定引发回流
* 以下属性改变会触发浏览器强制更新
  1. offsetTop、offsetLeft、offsetWidth、offsetHeight
  2. scrollTop、scrollLeft、scrollWidth、scrollHeight
  3. clientTop、clientLeft、clientWidth、clientHeight
  4. width、height
  5. getComputedStyle()
  6. getBoundingClientRect()
* 减少重绘与回流
  1. 使用 transform 替代 top
  2. 使用 visibility 替代 display: none
  3. table布局慎用
  4. 尽量在dom数的末端更改class，这样触发回流的dom能降到最少
  5. css选择器结构尽量简单，不要使用css表达式
  6. 动画效果运用在带有 position: absolute || fixed 时候可以使用requestAnimationFrame来执行动画
  7. 将频繁重绘或回流的节点设置成视图层，视图层能够阻止该节点渲染行为影响到别的节点。例如will-change、video、ifame等标签，浏览器自动视为视图层
  8. css3带有GPU的硬件加速，可以尽量使用css3替代以前的css
  9. 不要使用js频繁读取同一个dom，尽量用一个变量存储起来
  10. 对具有复杂动画的dom使用绝对定位，使它脱离文档流，否则会引起父元素的回流
  11. 频繁修改操作dom，创建一个documentFragment，最后在添加到文档上

## 15. Vuex、mobx、redux
* Vuex和redux思想是基本一致
  1. 统一维护应用状态
  2. 状态只有一个可信数据来源
  3. 操作更新方式同意，并且可控，通常以action方式提供更新状态途径
  4. Dispatcher接收所有的action，触发view
* mobx思想与上面两者就有区别了
  1. 他是面向对象的思想，而上面两种是面向函数的
  2. 没有时间回溯能力，因为数据只有一份引用
  3. 没有这样的烦恼，数据流动由函数调用一气呵成，便于调试
  4. 虽然他在报错的时候没有回溯，但是配合typescript一起使用则大大减少了报错的可能性, redux对typescript支持不太友好
  5. 它颗粒度较细，没有redux的中间件的思想，所以没有action分发，直接到view

## 16.什么是BFC,请解释一下它
  1. BFC是web里盒模型的css渲染模式，是指独立的一个渲染区或是一个独立的隔离独立容器
  2. B -> block、F -> formatting、C -> context
  3. 当display: block、list-item、table元素会产生bfc
  4. 什么情况下会触发BFC
    1. float且不为none
    2. position:absolute || fixed
    3. display: inline-block || table-cell || table-caption || flex || inline-flex
    4. overflow属性不为visible
  5. BFC元素的特点
    1. 盒子从顶端一个个排列
    2. 盒子的垂直方向由margin决定，同一个BFC的盒子margin会重叠
    3. 每个盒子左边边缘会碰到父元素，父元素的border在padding和margin都为0时，会重叠
    4. 如果父元素没有设定高度，但盒子里有子盒子有浮动元素，那么BFC在计算高度时候，会算上子盒子浮动的高度
```html
<style>
.father {
    width: 150px;
    border: 1px solid red;
}

.son1, .son2 {
    width: 50px;
    height: 50px;
    background-color: pink;
}

.son2 {
    background-color: purple;
}
</style>
<div class="father">
     <div class="son1"></div>
     <div class="son2"></div>
</div>
```   
正常情况下的样子是上下块撑开父元素的高度 

![](./lib/image/2.png)

当给两个子元素设置了float属性之后，子元素不再占据父元素的空间，此时父元素的高度就为0

![](./lib/image/3.png)


当设置父元素bfc后，此时就清除了子元素浮动带来的影响，什么影响呢，就是不撑开父元素的高度的影响，那么父元素的高度就是子元素的高度为父元素添加overflow:hidden;

![](./lib/image/4.jpg)

## 17.介绍下观察者模式和订阅-发布模式的区别，各自适用于什么场景
* 观察模式：一个被观察的对象的状态发现变化，则会通知所有依赖它的对象

  例子：去餐厅吃饭时候，经常遇到人很多的情况，餐厅会给你发一个号码让你排队，餐厅则被视为观察者，一旦餐厅有位置了，则会通知所有排队的客人。
```js
class Subject {
  constructor() {
    this.observers = [];
  }
  
  addObserver = (obj) => this.observers.push(obj);
  
  notify = () => {
    for (let obj of this.observers) {
      if (obj.update && typeof obj.update === 'function') {
        obj.update();
      }
    }
  }
}
const subject = new Subject();
const subA = {
  update: () => {
    console.log('update subA')
  }
};
const subB = {
  update: () => {
    console.log('update subB')
  }
};
// 添加观察者
subject.addObserver(subA);
subject.addObserver(subB);
// 通知所有观察者
subject.notify();
```
* 发布模式：发布者状态更新时，发布某些类型的通知，只通知订阅了相关类型的订阅者，发布者和订阅者是没有直接关联的

  例子：去餐厅吃饭时候，经常遇到人很多的情况，餐厅会给你发一个号码让你排队(订阅了)，我们在排队过程中干其他的事情去了，到我们的号码的时候餐厅则会通知我们去就餐
```js
class Publish {
  constructor() {
    this.topics = {};
  }
 
  subscribe = (type, fn) => { // 订阅
    if (!this.topics[type]) {
      this.topics[type] = [];
    }
    this.topics[type].push(fn);
  };
  
  doPublish = (type, ...args) => { // 发布指定类型的主题
    if(!this.topics[type]) {
      return;
    }
    for (let fn of this.topics[type]) {
      fn(args);
    } 
  };
}
const publish = new Publish();
const subA = {type: 'typeA'};
const subB = {type: 'typeB'};
const subC = {type: 'typeC'};
// 订阅
publish.subscribe(subA.type, () => console.log(`subscribe ${subA.type}`));
publish.subscribe(subB.type, () => console.log(`subscribe ${subB.type}`));
publish.subscribe(subC.type, () => console.log(`subscribe ${subC.type}`));
// 发布通知
publish.doPublish(subA.type);
```

## 18.算法题
> 请把两个数组 ['A1', 'A2', 'B1', 'B2', 'C1', 'C2', 'D1', 'D2'] 和 ['A', 'B', 'C', 'D']，合并为 ['A1', 'A2', 'A', 'B1', 'B2', 'B', 'C1', 'C2', 'C', 'D1', 'D2', 'D']

```js
function formatArray(arr1, arr2) {
  let formatArr2 = arr2.map(v => v + 3);
  let arr = [];
  
  arr1.concat(formatArr2).sort().map(v => {
    if (v.indexOf('3') !== -1) {
      v = v.split('')[0]
    }
    arr.push(v);
  });
  
  return arr;
}
```

## 19. 改造下面的代码，使之输出0 - 9，写出你能想到的所有解法。
```js
for (var i = 0; i< 10; i++){
  	setTimeout(() => {
  		console.log(i);
      }, 1000)
  }
```

```js
for (let i = 0; i < 10; i++) {
	setTimeout(() => {
  		console.log(i);
    }, 1000)
}
```

```js
for (var i = 0; i < 10; i++) {
  ((i) => {
    setTimeout(() => console.log(i), 1000)
  })(i)
}
```

```js
for (var i = 0; i < 10; i++) {
  setTimeout(i => {
    console.log(i)
  }, 1000, i);
}
```

## 20.实现一个sleep函数
```js
function sleep(time) {
  return new Promise((resolve) => {
    setTimeout(() => {
      resolve()
    }, time)
  })
}
```

## 21.实现 (5).add(3).minus(2) 功能
```js
Number.prototype.add = function(num) {
  if (typeof num !== 'number' || isNaN(num)) {
    throw new Error(`must be num number`);
  }
  
  return this + num;
};

Number.prototype.minus = function(num) {
  if (typeof num !== 'number' || isNaN(num)) {
      throw new Error(`must be num number`);
  }
  
  return this - n;
};
```

## 22.冒泡排序如何实现，时间复杂度是多少， 还可以如何改进？
```js
// 原始冒泡 O(n^2)
function buddleSort(arr) {
  const array = arr;
  const len = array.length;
  for (let i = 0; i < len - 1; i++) {
    for (let j = 0; j < i + 1; j++) {
      if (array[i] > array[j]) {
        let temp = array[i];
        array[i] = array[j];
        array[j] = temp;
      }
    }
  }
  
  return array;
}

// 优化版本
function buddleSort1(arr) {
  const array = arr;
  const len = array.length;
  let isSort = true;
  
  for (let i = 0; i < len - 1; i++) {
    for (let j = 0; j < i + 1; j++) {
      if (array[i] > array[j]) {
        let temp = array[i];
        array[i] = array[j];
        array[j] = temp;
        
        isSort = false;
      }
    }
    if (isSort) {
      break;
    }
  }
  
  return array;
}
```

## 23.某公司 1 到 12 月份的销售额存在一个对象里面
> 如下：{1:222, 2:123, 5:888}，请把数据处理为如下结构：[222, 123, null, null, 888, null, null, null, null, null, null, null]。
```js
function format(obj) {
  return Array.from({ length: 12 }).map((item, index) => obj[i + 1] || null);
}
```

## 24.要求设计 LazyMan 类，实现以下功能。
LazyMan('Tony');

> // Hi I am Tony
  
LazyMan('Tony').sleep(10).eat('lunch');

> // Hi I am Tony
  
> // 等待了10秒...
  
> // I am eating lunch
  
LazyMan('Tony').eat('lunch').sleep(10).eat('dinner');

> // Hi I am Tony

> // I am eating lunch

> // 等待了10秒...

> // I am eating diner
  
LazyMan('Tony').eat('lunch').eat('dinner').sleepFirst(5).sleep(10).eat('junk food');

>  // Hi I am Tony
  
>  // 等待了5秒...
  
>  // I am eating lunch
  
>  // I am eating dinner
  
>  // 等待了10秒...
  
>  // I am eating junk food
```js
  class LazyMan {
    constructor(name) {
      console.log(`Hi I am ${name}`);
      this.taskList = [];
      this.name = name;

      setTimeout(() => this.next(), 0)
    }

    eat = (food) => {
      let that = this;
      const fn = (function (f) {
        return () => {
          that.next();
          console.log(`I am eating ${f}`)
        }
      })(food);
      this.taskList.push(fn);
      return this;
    };

    sleepFirst = (time) => {
      let that = this;
      const fn = (function (t) {
        return () => {
          setTimeout(() => {
            console.log(`等待了${t}秒...`);
            that.next();
          }, time * 1000);
        }
      })(time);
      this.taskList.unshift(fn);
      return this;
    };

    sleep = (time) => {
      let that = this;
      const fn = (function (t) {
        return () => {
          setTimeout(() => {
            console.log(`等待了${t}秒...`);
            that.next();
          }, time * 1000);
        }
      })(time);
      this.taskList.push(fn);
      return this;
    };

    next = () => {
      let fn = this.taskList.shift();
      fn && fn();
    }
  }

  function lazyMan(name) {
    return new LazyMan(name);
  }
  lazyMan('哈哈哈').sleepFirst(5).eat('水果').sleep(10);
```

## 25.输出以下代码运行结果
```js
// example 1
var a={}, b='123', c=123;  
a[b]='b';
a[c]='c';  
console.log(a[b]);
```
```js
// example 2
var a={}, b=Symbol('123'), c=Symbol('123');  
a[b]='b';
a[c]='c';  
console.log(a[b]);
```

```js
// example 3
var a={}, b={key:'123'}, c={key:'456'};  
a[b]='b';
a[c]='c';  
console.log(a[b]);
```

* 对象的建必须是字符串或symbol
* 第一题输出是c，a[c]隐式转换把123转换成了'123'，所以会覆盖a[b]故输出c
* 第二题输出b，因为是用symbol定义的
* 第三题输出c，b和c是对象隐式转换成了'[object object]'，所以会覆盖a[b]故输出c

## 26.实现Promise
```js
const PENDING = 'PENDING';
const FULFILLED = 'FULFILLED'
const REJECTED = 'REJECTED'
class _Promise {
  constructor(fn) {
    this.status = PENDING;
    this.value = undefined;
    this.reason = undefined;
    
    let resolve = val => {
      if (this.status === PENDING) {
        this.status = FULFILLED;
        this.value = val;
      }
    };
    
    let reject = err => {
      if (this.status === PENDING) {
        this.status = REJECTED;
        this.reason = err;
      }
    };
    
     // 自动执行函数
     try {
       fn(resolve, reject)
     } catch (e) {
       reject(e);
     }
  }
  
  then(onFulfilled, onRejected) {
    switch (this.status) {
      case FULFILLED:
        onFulfilled();
        break;
      case REJECTED:
        onRejected();
        break;
      default: return null;  
    }
  }
}
```

## 26.call,apply,bind的区别如何实现它们

* call方法第一个参数是要绑定this的值，后面传入是一个参数列表。当地一个参数为null,undefined时候默认指向window
```js
// 一个简单的使用例子
const obj = {
  message: 'My name is:'
};
function getName(firstName, lastName) {
  console.log(`${this.message}${firstName}-${lastName}`);
}
getName.call(obj, '张三', '李四');

// 实现一个call
Function.prototype._call = function(context) {
  if (typeof this !== 'function') {
    throw new TypeError('not funciton')
  }
  
  const ctx = context || window; // 若给null undefined则指向window
  
  ctx.fn = this; // 重新绑定this
  
  const args = [...arguments].slice(1); // 获取方法里传递的所有参数,第0个除外
  
  const res = ctx.fn(...args);
  
  // 删除改方法，不然会污染被调用的对象
  delete ctx.fn;
  
  return res;
}
```

* apply接收两个参数，第一个参数是给绑定给this的值，第二个参数是一个参数数组。当第一个参数为null,undefined时候则默认指向window
```js
// 使用例子
const obj = {
  message: 'My name is:'
};
function getName(firstName, lastName) {
  console.log(`${this.message}${firstName}-${lastName}`);
}
getName.apply(obj, ['张三', '李四']); // 其实apply和call最后目标是一直，只是后面参数传递方式的区别，apply是数组

// 实现一个apply
Function.prototype._apply = function(context) {
  if (typeof this !== 'function') {
    throw new TypeError('not funciton')
  }
    
  const ctx = context || window;
  
  ctx.fn = this;
  
  const res = arguments[1] ? ctx.fn(arguments[1]) : ctx.fn();
  
  delete ctx.fn;
  
  return res;
}
```

* bind和call非常相似，第一个参数是this指向，从第二个参数开始接受的参数列表。区别在与bind方法返回值是函数以及bind接受到的参数列表，bind方法不会立刻执行，而是返回了一个改变上下文this的函数，原函数不变化
```js
// 使用例子
const obj = {
    message: 'My name is:'
};
function getName(firstName, lastName) {
  console.log(`${this.message}${firstName}-${lastName}`);
}
const fn = getName.bind(obj, '张三', '李四');
fn();

// 实现一个bind
Function.prototype._bind = function(context) {
  if (typeof this !== 'function') {
    throw new TypeError('not funciton')
  }
  
  let that = this;
  let args = [...arguments].slice(1);
  
  return function _F() {
    if (this instanceof _F) {   // 处理函数使用new的情况
      return new that(...args, ...arguments)
    } else {
      return that.apply(context, [...args, ...arguments])
    }
  }
}
```

## 27.关于对象拷贝
```js
// 实现浅拷贝
function copyObj(obj) {
  if (!obj) {
    return obj;
  }
  return Object.assign({}, obj);
}

// 实现一个基本的深拷贝
function deepCopy(obj) {
  let target = obj instanceof Array ? [] : {};
  for (let key in obj) {
    if (obj.hasOwnProperty(key)) {
      target[key] = typeof obj[key] === 'object' ? deepCopy(obj[key]) : obj[key]
    }
  }
  
  return target;
}
```
## 28.实现一个EventEmitter
```js
class EventEmitter {
  constructor() {
    this.events = this.events || new Map()
  }
  // 添加监听
  addListener(type, fn) {
    if (!this.events.get(type)) {
      this.events.set(type, fn);
    }
  }
  // 接收监听
  emit(type) {
    const handle = this.events.get(type);
    handle.apply(this, [...arguments].splice(1))
  }
  // 移除监听
  removeListener(type) {
    this.events.delete(type)
  }
}
```

## 29.react hook
```js
import React, { useState } from 'react'

/**
* 随机颜色函数
* @param colors
* @param initialColor
* @returns {*[]}
*/
const useRandomColor = (colors=[], initialColor='#fff') => {
  const lenColor = colors.length;
  const [color, setColor] = useState(initialColor);
  
  const changeColor = () => {
    const index = Math.floor(Math.random() * lenColor);
    const activeColor = colors[index];
    setColor(activeColor);
  };
 
  return [color, changeColor]
};

const colorBanner = () => {
  const colors = ['red', 'blue', 'green'];
  const [color, changeColor] = useRandomColor(colors, 'red');
  
  return(
     <div style={{ textAlign: 'center', padding: '20px 0', backgroundColor: color }}>
       <h2>点击更换背景色</h2>
       <br />
       <button onClick={changeColor}>change</button>
     </div>
  )
};
```

## 30.最简洁方法实现m的n次方结果

```js
/**
* @param {number} number     被次方数 
* @param {number} powNumber  次方数
*/
const mathPow = (number, powNumber) => number ** powNumber
```

## 31.写一个 mySetInterVal(fn, a, b),每次间隔 a,a+b,a+2b, ...a + nb 的时间，然后写一个 myClear，停止上面的 mySetInterVal

```js
function LoopFn(fn, a, b) {
  this.a = a;
  this.b = b;
  this.deepCount = 0;
  this.timer = null;

  this.doFn = () => {
    this.timer = setTimeout(() => {
      fn();
      this.deepCount++;
      this.doFn();
    }, this.a + (this.b * this.deepCount));
  };

  this.clear = () => {
    if (this.timer) {
      clearTimeout(this.timer);
      this.timer = null;
      this.deepCount = 0;
    }
  };
}

var loopFn = null;
function mySetInterVal(fn, a, b) {
  loopFn = new LoopFn(fn, a, b);
  loopFn.doFn();
}

function myClear() {
  if (loopFn) {
    loopFn.clear();
    loopFn = null;
  }
}
```

## 32.合并二维有序数组成一维有序数组，归并排序的思路

```js
function listMerge(arr) {
  return arr.toString().split(',')
}
```
