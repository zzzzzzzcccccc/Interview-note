## 1.写 React / Vue 项目时为什么要在列表组件中写 key，其作用是什么？
 * 绑定key一定要唯一，这样在修改数据的时候才能驱动虚拟dom更新
 * 某些情况下不绑定key在修改数据后dom渲染的速度是比加key要快的(dom结构必须简单)，这种就是diff的<font color="red">就地复用</font>所以比加key要快
 * 一般是要加key，在vue里不加key只有黄字警告，但是在react的diff和vue的diff稍有区别，react循环渲染key是必须要加，不然在某一些情况下setState了数据视图没有更新
 
## 2.['1', '2', '3'].map(parseInt) 执行后是输出什么，为什么输出这个?

* map函数式回调形式返回参数一共有三个参数：value, index, array 当前值 当前下标 当前便利的数组
* parentInt是内置函数一共有两个参数: string radix 需要转化成整形的值 需要返回的进制数
```js
  parseInt('1', 0); // 0 默认是10进制所以返回 1
  parseInt('2', 1); // 1 是1进制最大值为1 无法计算返回 NaN
  parseInt('3', 2); // 2 是2进制最大值为2 无法计算返回 NaN
```

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
