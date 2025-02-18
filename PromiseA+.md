```javascript
class MyPromise {
    constructor(executor) {
        this.state = "pending"; // 初始状态
        this.value = undefined; // 成功值
        this.reason = undefined; // 失败原因
        this.onFulfilledCallbacks = []; // 成功回调队列
        this.onRejectedCallbacks = []; // 失败回调队列
        
        const resolve = (value) => {
            if (this.state === "pending") {
                this.state = "fulfilled";
                this.value = value;
                this.onFulfilledCallbacks.forEach((callback) => callback());
            }
        };
        
        const reject = (reason) => {
            if (this.state === "pending") {
                this.state = "rejected";
                this.reason = reason;
                this.onRejectedCallbacks.forEach((callback) => callback());
            }
        };
        
        try {
            executor(resolve, reject);
        } catch (error) {
            reject(error);
        }
    }
    
    then(onFulfilled, onRejected) {
        // 如果 onFulfilled 不是一个函数，则将其设置为一个默认函数，该函数返回传入的值
        onFulfilled = typeof onFulfilled === "function" ? onFulfilled : (value) => value;
        
        // 如果 onRejected 不是一个函数，则将其设置为一个默认函数，该函数抛出传入的原因。
        onRejected = typeof onRejected === "function" ? onRejected : (reason) => { throw reason };
    	
        // 链式调用
    	const promise2 = new MyPromise((resolve, reject) => {
            if (this.state === "fulfilled") {
                setTimeout(() => {
                 try {
					const x = onFulfilled(this.value);
                    resolvePromise(promise2, x, resolve, reject);
                 } catch (error) {
                      reject(error)  
                 }
                }, 0);
            } else if (this.state === "rejected") {
                setTimeout(() => {
                 try {
					const x = onRejected(this.reason);
                 	resolvePromise(promise2, x, resolve, reject);
                    } catch (error) {
						reject(error) 
                    }
                }, 0);
            } else if (this.state === "pending") {
                this.onFulfilledCallbacks.push(() => {
                    setTimeout(() => {
                        try {
                            const x = onFulfilled(this.reason);
                            resolvePromise(promise2, x, resolve, reject);
                        } catch (error) {
							reject(error) 
                        }
                    }, 0);
                });
                
                this.onRejectedCallbacks.push(() => {
                    setTimeout(() => {
                        try {
							const x = onRejected(this.reason);
                            resolvePromise(promise2, x, resolve, reject);
                        } catch (error) {
							reject(error) 
                        }
                    }, 0);
                });
            }
        });
        
        return promise2;
    }
}

function resolvePromise(promise2, x, resolve, reject) {
    // 解决循环引用
    if (promise2 === x) {
        return reject(new TypeError("Chaining cycle detected for promise"));
    }
    
    let called = false; // 定义一个标志变量 called，用于确保 resolve 或 reject 只被调用一次。
    
    if (x !== null && (typeof x === "object" || typeof x === "function")) {
        try {
            const then = x.then;
            if (typeof then === "function") {
                // 调用 then 方法
                then.call(x, (y) => {
                    if (called) return;
                    called = true;
                    resolvePromise(promise2, y, resolve, reject); // y 可能也是一个 promise 情况
                }, (r) => {
                    if (called) return;
                    called = true;
                    reject(r);
                })
            } else {
                resolve(x);
            }
        } catch (error) {
            if (called) return;
            called = true;
            reject(error);
        }
    } else {
        resolve(x);
    }
}
```



**`使用示例：`**

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

