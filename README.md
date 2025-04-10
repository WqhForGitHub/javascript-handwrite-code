





# call 方法

```javascript
Function.prototype.myCall = function (thisArg, ...args) {
    if (typeof this !== "function") {
        throw new TypeError(this + " is not a function");
    }
    
    thisArg = thisArg || globalThis;
    
    const fnSymbol = Symbol();
    
    thisArg[fnSymbol] = this;
    
    const result = thisArg[fnSymbol](...args);
    
    delete thisArg[fnSymbol];
    
    return result;
}
```





# apply 方法

```javascript
Function.prototype.myApply = function (thisArg, argsArray) {
    if (typeof this !== "function") {
        throw new TypeError(this + " is not a function");
    }
    
    thisArg = thisArg || globalThis;
    
    const fnSymbol = Symbol();
    
    thisArg[fnSymbol] = Symbol();
    
    const result = thisArg[fnSymbol](...argsArray);
    
    delete thisArg[fnSymbol];
    
    return result;
}
```





# bind 方法

```javascript
Function.prototype.myBind = function (context, ...args) {
    const fn = this;
    
    return function (...newArgs) {
        return fn.apply(context, args.concat(newArgs));
    }
}
```









# typeof 

```javascript
function myTypeof(obj) {
    return Object.prototype.toString.call(obj).slice(8, -1).toLowerCase();
}
```









# 模拟 instanceof 

```javascript
function myInstanceof(left, right) {
    const prototype = right.prototype;
    let proto = Object.getPrototypeOf(left);
    
    while(proto !== null) {
        if (proto === prototype) {
            return true;
        }
        
        proto = Object.getPrototypeOf(proto);
    }
    
    return false;
}
```







# 函数所有参数求和

```javascript
function sum(...args) {
    return args.reduce((acc, curr) => acc + curr, 0);
}
```







# 模拟 new 操作

```javascript
function myNew(constructor, ...args) {
    const obj = {};
    
    Object.setPrototypeOf(obj, constructor.prototype);
    
    const result = constructor.apply(obj, args);
    
    return result !== null && (typeof result === "object" || typeof result === "function") ? result : obj;
}
```





# setTimeout 模拟 setInterval

```javascript
function customInterval(callback, interval) {
    function intervalFunction() {
        callback();
        setTimeout(intervalFunction, interval);
    }
    
    setTimeout(intervalFunction, interval);
}
```





# 防抖

```javascript
function debounce(func, delay) {
    let timeoutId;
    
    return function (...args) {
        if (timeoutId) {
            clearTimeout(timeoutId);
        }
        
        timeoutId = setTimeout(() => {
            func.apply(this, args);
        }, delay)
    }
}
```



# 节流

```javascript
function throttle(func, apply) {
    let lastExecutionTime = 0;
    
    return function (...args) {
        const now = Date.now();
        
        if (now - lastExecutonTime >= delay) {
            lastExecutionTime = now;
            func.apply(this, args);
        };
    }
}
```



# 深拷贝

```javascript
function deepCopy(obj, hash = new WeakMap()) {
    // 基本数据类型直接返回
    if (obj === null || typeof obj !== "object") {
        return obj;
    }
    
    // 处理循环引用
    if (hash.has(obj)) {
        return hash.get(obj);
    }
    
    // 处理数组
    if (Array.isArray(obj)) {
        const arrCopy = [];
        hash.set(obj, arrCopy);
        obj.forEach((item, index) => {
            arrCopy[index] = deepCopy(item, hash);
        });
        
        return arrCopy;
    }
    
    // 处理对象
    const objCopy = {};
    hash.set(obj, objCopy);
    Object.keys(obj).forEach((key) => {
        objCopy[key] = deepCopy(obj[key], hash);
    });
    
    return objCopy;
    
}
```

# promise

```javascript
class MyPromise {
  constructor(executor) {
    this.state = 'pending';
    this.value = undefined;
    this.reason = undefined;
    this.onFulfilledCallbacks = [];
    this.onRejectedCallbacks = [];

    const resolve = (value) => {
      if (this.state === 'pending') {
        this.state = 'fulfilled';
        this.value = value;
        this.onFulfilledCallbacks.forEach(fn => fn(value));
      }
    };

    const reject = (reason) => {
      if (this.state === 'pending') {
        this.state = 'rejected';
        this.reason = reason;
        this.onRejectedCallbacks.forEach(fn => fn(reason));
      }
    };

    try {
      executor(resolve, reject);
    } catch (error) {
      reject(error);
    }
  }

  then(onFulfilled, onRejected) {
    return new MyPromise((resolve, reject) => {
      if (this.state === 'fulfilled') {
        try {
          const result = onFulfilled(this.value);
          resolve(result);
        } catch (error) {
          reject(error);
        }
      }

      if (this.state === 'rejected') {
        try {
          const result = onRejected(this.reason);
          resolve(result);
        } catch (error) {
          reject(error);
        }
      }

      if (this.state === 'pending') {
        this.onFulfilledCallbacks.push(() => {
          try {
            const result = onFulfilled(this.value);
            resolve(result);
          } catch (error) {
            reject(error);
          }
        });

        this.onRejectedCallbacks.push(() => {
          try {
            const result = onRejected(this.reason);
            resolve(result);
          } catch (error) {
            reject(error);
          }
        });
      }
    });
  }
}

// 使用示例
const promise = new MyPromise((resolve, reject) => {
  setTimeout(() => {
    resolve('成功');
  }, 1000);
});

promise.then(
  (value) => {
    console.log('fulfilled:', value);
  },
  (reason) => {
    console.log('rejected:', reason);
  }
);
```

**`使用示例`**
```javascript
const promise = new MyPromise((resolve, reject) => {
    setTimeout(() => {
        resolve("success");
    }, 1000);
});

promise.then((value) => {
    console.log(value); // 输出 success
    return "next success";
}).then((value) => {
    console.log(value); // 输出 "next success"
}).catch((error) => {
    console.error(error);
});
```







# promise



# 实现 Promise.all

# 实现 Promise.finally

# 实现 promise.allSettled

# 实现 promise.race

# promise.any

# promise.resolve

# promise.reject

# 数组去重

# 数组扁平化

# forEach

# reduce

# map

# filter

# every

# some

# find、findIndex

# indexOf

# sort

# 防抖节流

# 深拷贝

# new 

# 继承

# object.create

# call

# bind

# apply

# 实现柯里化

# ajax

# jsonp

# set

# map

# es6 的 class

# instanceof

# 实现千分位分隔符

# 实现数据类型判断函数

# 实现数组转数

# 实现 sleep 函数

# 实现发布订阅模式

# 把一个JSON对象的key从下划线形式（Pascal）转换到小驼峰形式（Camel）



# 解析 URL 参数为对象











