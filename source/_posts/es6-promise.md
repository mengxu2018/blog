---
title: es6 promise
date: 2019-10-26 11:35:01
tags: javascript
---

The arguments to then are optional, and catch(failureCallback) is short for then(null, failureCallback). You might see this expressed with arrow functions instead:

https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Using_promises

下面这个代码可以直接控制台执行
```
new Promise((resolve, reject) => {
 console.log('Initial');
 throw new Error('Something failed');
 // resolve();
})
.then(() => {
 // throw new Error('Something failed'); 
 console.log('Do this');
})
.then(null,
 () => {
 // throw new Error('Something failed'); 
 console.log('Do that');
 }
 )
.then(() => {
 console.log('Do this, no matter what happened before');
});
```
上面这个代码等同于
```
new Promise((resolve, reject) => {
 console.log('Initial');
 throw new Error('Something failed');
 // resolve();
})
.then(() => {
 // throw new Error('Something failed'); 
 console.log('Do this');
})
.catch(() => {
 console.error('Do that');
})
.then(() => {
 console.log('Do this, no matter what happened before');
});
```



When you return something from a then() callback, it's a bit magic. If you return a value, the next then() is called with that value. However, if you return something promise-like, the next then() waits on it, and is only called when that promise settles (succeeds/fails).
```
Promise.resolve('foo').
 then(() => {return "bar"}).
 then((v) => console.log(v))
Promise.resolve('foo').
 then(() => {return Promise.resolve('bar')}).
 then((v) => console.log(v))
```

https://stackoverflow.com/questions/34094806/return-from-a-promise-then