

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





