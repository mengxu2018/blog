---
title: node.js module.export
tags:
---

## module.export

The exports variable is available within a module's file-level scope, and is assigned the value of module.exports before the module is evaluated.

It allows a shortcut, so that module.exports.f = ... can be written more succinctly as exports.f = .... However, be aware that like any variable, if a new value is assigned to exports, it is no longer bound to module.exports:

```
module.exports.hello = true; // Exported from require of module
exports = { hello: false };  // Not exported, only available in the module
```

When the  `module.exports` property is being completely replaced by a new object, it is common to also reassign `exports`:

```bash
module.exports = exports = function Constructor() {
  // ... etc.
};
```

简单说就是exports如果被新的object覆盖掉了，那就必须是modules.exports = {}， 否则可以直接简写 exports.xxx={}

## sample code

*foo.js*
```bash
const circle = require('./circle.js');
console.log(`The area of a circle of radius 4 is ${circle.area(4)}`);
```

*circle.js*
```bash
const { PI } = Math;
module.exports.area = (r) => PI * r ** 2;   // the module can be ignored here
module.exports.circumference = (r) => 2 * PI * r;
```

**上面这个例子里， 因为`exports`没有被赋新的object， 所以`modules`关键字可以省略**

*bar.js*
```bash
const Square = require('./square.js');
const mySquare = new Square(2);
console.log(`The area of mySquare is ${mySquare.area()}`);
```

*square.js*
```bash
// assigning to exports will not modify module, must use module.exports
// the module can not be ignored here
module.exports = class Square {
    constructor(width) {
      this.width = width;
    }
  
    area() {
      return this.width ** 2;
    }
  };
```
 **上面这个例子里， 因为`exports`被赋新的object， 所以`modules`关键字不可以省略**
