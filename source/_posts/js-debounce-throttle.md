---
title: 理解节流与防抖
date: 2019-05-29 11:21:31
tags:
 - js
 - 前端
categories:
  - 前端
---

我们知道，鼠标滚动（scroll）、调整窗口大小（resize）、敲击键盘（keyup）这类事件在触发时往往频率极高。这时候事件对应的回调函数也会在极短时间内反复执行。想象一下，如果这些回调函数内的逻辑涉及复杂的计算，或者对 DOM 操作非常频繁，从而造成大量布局操作、绘制操作，影响性能。

如上的情况，我们会使用的优化手段往往是使用节流（throttle）或者防抖（debounce）来进行处理。

**节流和防抖总是一起出现，那么它们有什么不同呢？**

<!-- more -->

首先要知道它们解决的问题相同，方向类似：**两者并不会减少事件的触发，而是减少事件触发时回调函数的执行次数**。为了达成这个目的，节流和防抖采用的手段有所差别。

- 节流：顾名思义，就是将短时间的函数调用以一个固定的频率间隔执行，这就如同水龙头开关限制出水口流量。
- 防抖：抖动现象本质就是指短时间内高频次触发。因此，我们可以把短时间内的多个连续调用合并成一次，也就是只触发一次回调函数。


## 节流(throttle)
函数节流指的是某个函数在一定时间间隔内（例如 3 秒）只执行一次，在这 3 秒内 **无视后来产生的函数调用请求**。可以理解为是缩减执行频率

### 原理及实现
函数节流非常适用于函数被频繁调用的场景，例如：window.onresize() 事件、mousemove 事件、上传进度等情况。

可以通过两种方案实现节流：
- 第一种是用时间戳来判断是否已到执行时间，记录上次执行的时间戳，然后每次触发事件执行回调，回调中判断当前时间戳距离上次执行时间戳的间隔是否已经达到时间差（Xms） ，如果是则执行，并更新上次执行的时间戳，如此循环。
- 第二种方法是使用定时器，比如当 scroll 事件刚触发时，打印一个 hello world，然后设置个 1000ms 的定时器，此后每次触发 scroll 事件触发回调，如果已经存在定时器，则回调不执行方法，直到定时器触发，handler 被清除，然后重新设置定时器。

根据第一种时间戳方式来实现：
```js
// fn 是需要执行的函数
// wait 是时间间隔，单位毫秒
const throttle = (fn, wait = 50) => {
  // 上一次执行 fn 的时间
  let previous = 0
  // 将 throttle 处理结果当作函数返回
  return function(...args) {
    // 获取当前时间，转换成时间戳，单位毫秒
    let now = +new Date()
    // 将当前时间和上一次执行函数的时间进行对比
    // 大于等待时间就把 previous 设置为当前时间并执行函数 fn
    if (now - previous > wait) {
      previous = now
      fn.apply(this, args)
    }
  }
}

// 执行 throttle 函数返回新函数
const betterFn = throttle(() => console.log('fn 函数执行了'), 1000)
// 每 10 毫秒执行一次 betterFn 函数，但是只有时间差大于 1000 时才会执行 fn
setInterval(betterFn, 10)
```

第二种方法是使用定时器来实现：
```js
const throttle = function(fn,delay) {
  let timer = null

  return function() {
    const context = this
    let args = arguments
    if(!timer) {
      timer = setTimeout(() => {
        fn.apply(context,args) 
        clearTimeout(timer) 
      },delay)
    }
  }
}
```

## 防抖(debounce)
防抖函数的作用就是控制函数在一定时间内的执行次数。防抖意味着N秒内函数只会被执行一次，如果N秒内再次被触发，则重新计算延迟时间。

### 应用场景及实现
#### 防抖函数常用场景：
- 搜索框输入查询，如果用户一直在输入中，没有必要不停地调用去请求服务端接口，等用户停止输入的时候，再调用，设置一个合适的时间间隔，有效减轻服务端压力。
- 表单验证
- 按钮提交事件。
- 浏览器窗口缩放，resize事件等。

#### 防抖函数实现
滚动scroll事件，不停滑动滚轮会连续触发多次滚动事件，从而调用绑定的回调函数，我们希望当我们停止滚动的时，才触发一次回调，这时可以使用函数防抖。
```html
<style>
    * {
        padding: 0;
        margin: 0;
    }

    .box {
        width: 800px;
        height: 1200px;
    }
</style>
<body>
    <div class="container">
        <div class="box" style="background: tomato"></div>
        <div class="box" style="background: skyblue"></div>
        <div class="box" style="background: red"></div>
        <div class="box" style="background: yellow"></div>
    </div>
    <script>
        window.onload = function() {
            const decounce = function(fn, delay) {
                let timer = null

                return function() {
                    const context = this
                    let args = arguments
                    clearTimeout(timer) // 每次调用debounce函数都会将前一次的timer清空，确保只执行一次
                    timer = setTimeout(() => {
                        fn.apply(context, args)
                    }, delay)
                }
            }

            let num = 0

            function scrollTap() {
                num++
                console.log(`看看num吧 ${num}`)
            }
            // 此处的触发时间间隔设置的很小
            document.addEventListener('scroll', decounce(scrollTap, 500))
            // document.addEventListener('scroll', scrollTap)
        }
    </script>
</body>
```
此处的触发时间间隔设置的很小，如果匀速不间断的滚动，不断触发scroll事件，如果不用debounce处理，可以发现num改变了很多次，用了debounce函数防抖，num在一次上时间的滚动中只改变了一次。

补充：浏览器在处理setTimeout和setInterval时，有最小时间间隔。 
setTimeout的最短时间间隔是4毫秒； 
setInterval的最短间隔时间是10毫秒，也就是说，小于10毫秒的时间间隔会被调整到10毫秒。

---
封装完善的防抖与节流函数

防抖
```js
/**
 * 空闲控制 返回函数连续调用时，空闲时间必须大于或等于 wait，func 才会执行
 *
 * @param  {function} func        传入函数，最后一个参数是额外增加的this对象，.apply(this, args) 这种方式，this无法传递进函数
 * @param  {number}   wait        表示时间窗口的间隔
 * @param  {boolean}  immediate   设置为ture时，调用触发于开始边界而不是结束边界
 * @return {function}             返回客户调用函数
 */
const debounce = function(func, wait, immediate) {
    let timeout, args, context, timestamp, result;

    const later = function() {
        // 据上一次触发时间间隔
        let last = Number(new Date()) - timestamp;

        // 上次被包装函数被调用时间间隔last小于设定时间间隔wait
        if (last < wait && last > 0) {
            timeout = setTimeout(later, wait - last);
        } else {
            timeout = null;
            // 如果设定为immediate===true，因为开始边界已经调用过了此处无需调用
            if (!immediate) {
                result = func.call(context, ...args, context);
                if (!timeout) {
                    context = args = null;
                }
            }
        }
    };

    return function(..._args) {
        context = this;
        args = _args;
        timestamp = Number(new Date());
        const callNow = immediate && !timeout;
        // 如果延时不存在，重新设定延时
        if (!timeout) {
            timeout = setTimeout(later, wait);
        }
        if (callNow) {
            result = func.call(context, ...args, context);
            context = args = null;
        }

        return result;
    };
};
```

节流

```js
**
 * 频率控制 返回函数连续调用时，func 执行频率限定为 次 / wait
 *
 * @param  {function}   func      传入函数
 * @param  {number}     wait      表示时间窗口的间隔
 * @param  {object}     options   如果想忽略开始边界上的调用，传入{leading: false}。
 *                                如果想忽略结尾边界上的调用，传入{trailing: false}
 * @return {function}             返回客户调用函数
 */
const throttle = function(func, wait, options) {
    let context, args, result;
    let timeout = null;
    // 上次执行时间点
    let previous = 0;
    if (!options) options = {};
    // 延迟执行函数
    let later = function() {
        // 若设定了开始边界不执行选项，上次执行时间始终为0
        previous = options.leading === false ? 0 : Number(new Date());
        timeout = null;
        result = func.apply(context, args);
        if (!timeout) context = args = null;
    };
    return function(..._args) {
        let now = Number(new Date());
        // 首次执行时，如果设定了开始边界不执行选项，将上次执行时间设定为当前时间。
        if (!previous && options.leading === false) previous = now;
        // 延迟执行时间间隔
        let remaining = wait - (now - previous);
        context = this;
        args = _args;
        // 延迟时间间隔remaining小于等于0，表示上次执行至此所间隔时间已经超过一个时间窗口
        // remaining大于时间窗口wait，表示客户端系统时间被调整过
        if (remaining <= 0 || remaining > wait) {
            clearTimeout(timeout);
            timeout = null;
            previous = now;
            result = func.apply(context, args);
            if (!timeout) context = args = null;
            //如果延迟执行不存在，且没有设定结尾边界不执行选项
        } else if (!timeout && options.trailing !== false) {
            timeout = setTimeout(later, remaining);
        }
        return result;
    };
};
```

---
参考文章：

[深入浅出节流函数 throttle](https://juejin.im/post/5cedd320f265da1ba77c85be)

[函数节流与函数防抖的区别](https://blog.csdn.net/qq_35585701/article/details/81392174)