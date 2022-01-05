# 你不知道的javascript上

- 作用域
- this 四规则
- 类 在 js 中实现方案 
    - 委托 实现 及 代替 类
    - ES6 的 class 特点 及 弊端

## 一部分

### 一章 作用域

- 1.1 编译原理
- 1.2 理解作用域
    - LHS / RHS
- 1.3 作用域嵌套

```
编译原理
    过程
        1. 分词/词法分析        字符串 -> 词法单元
        2. 解析/语法分析        词法单元 -> 抽象语法树 (AST)
        3. 代码生成             AST -> 代码
    概念
        引擎                    负责整个JS程序编译及执行过程
        编译器                  负责语法分析及代码生成
        作用域                  收集维护声明标识符(变量)并控制访问权限
                                有作用域链未找到变量向上查找
    查询
        LHS (左侧)
                                查找容器本身
                                赋值左侧 / 形参定义
                                未声明 -- ( 非严: 全局创建 | 严: referenceError )
        RHS (非左侧)
                                得到某个值
                                未声明 -- ( referenceError )
                                声明但操作非法 -- ( typeError )
                                
var     
    重复定义会忽略
    
error
    referenceError              判别失败
    typeError                   判别成功操作非法
```

### 二章 词法作用域

- 1 词法阶段
- 2 欺骗词法

```
词法作用域
        特点
                静态检查(只由声明位置决定)
        查找
                逐级向上 
                都有最下层会屏蔽最上层 (非全局被屏蔽将无法访问)
        欺骗 (运行期修改词法作用域)   
            手段
                eval    
                        (非严) 则在运行时修改词法作用域(不会提升)  动态加入作用域标识符 (严格模式 无法修改作用域 eval 有自己的作用域)
                        类似 setTimeout() / setInterval() 第一个参数 new Function() 最后一个参数
                with (为避免重复引用对象 会把对象属性作为标识符加入到作用域中)
                        如果对象属性没有 被赋值时 可能创建全局作用域的变量
                        严格模式被禁止使用
            性能
                优化依赖词法静态分析 因为动态作用域无法做任何优化所以性能损失非常大
```

```js
/** eval 欺骗词法 */
function foo(str, a) {
    eval(str)       // 动态加入作用域标识符 (如果在严格模式 无法修改作用域 eval 有自己的作用域)
    console.log(a,b)
}

var b = 2
foo('var b = 3', 1) // 1,3
```

### 三章 函数作用域和块作用域

- 1. 函数作用域
- 2. 函数作用域隐藏内部实现
- 3. 函数 匿名 具名 IIFE
- 4. 块作用域

```
函数作用域
    说明
            常用创建子作用域的手段
    作用
            隐藏内部实现
                    为何隐藏    1. 最小特权原则
                                2. 规避冲突
                    手段
                                全局命名空间
                                模块管理
                    原理
                                函数作用域

函数 
    匿名
            缺点
                    1. 调试困难 追踪栈没有有意义函数名
                    2. 引用自身困难 要使用已过期的 argument.callee 
                    3. 绑定事件后无法解绑
                    3. 减少代码可读和可理解性
            解决
                    为匿名增加具名 (该具名只能内部使用)
            使用
                    函数表达式
                    立即执行表达式 (IIFE)
                            写法
                                    (function foo(){}())
                                    (function foo(){})()
                            应用场景
                                    1. 解决 undefined 标识符默认值被错误覆盖 (undefined 可以作为变量名)
                                    2. 倒置代码运行顺序 (UMD)

块作用域
    产生
        with
                with 对象创建的作用域仅with声明中可以访问
        try/catch
                catch 会常见一个块作用域声明变量仅 catch 内有效
        {let/const(锁定赋值操作)}
                let 会在 {} 创建一个块作用域
                let 不会声明提升 提前使用会暂存死区 referenceError
    用途
        闭包中垃圾回收
        let 处理循环    (for () 中let 声明不仅绑定在 for 循环块中并且每次迭代都是新赋值)
```

```js
/** 非块作用域 */
for(var i = 0; i < 10; i++){
    var m = 1
    bar () // bar 中修改了 作用域中的 i 会造成死循环
}

function bar(){
    i = 3       // 作用域查找 i 会找到外面作用域中的 i 
}
```

```js
/** 闭包时多余声明提前垃圾回收 */
function f() {
    {
        let some = {}   // 块作用域结束后 就被回收了
    }
    
    return fn(){}
}
```

### 四章 提升

```
- 变量(var)
  - 仅提升声明(不赋值)
  - 重复声明会忽略
- 函数
  - 提升函数声明(表达式不会参考变量提升)
  - 函数提升优先于变量
  - 重复声明会覆盖
- let     
  - 没有变量提升 提前使用会有TDZ 暂存死区  
```

```js
console.log(a)

var a = 2
```

### 五章 作用域闭包

- 闭包
- 模块

```
闭包    使函数是在当前词法作用域之外执行 (延长作用域生命周期)
        闭包会阻止垃圾回收
    使用
        返回函数结果
        传递函数参数 (回调)
        模块 (必须有外部封闭函数且必须调用一次 封闭函数必须返回至少一个内部函数)
```

```js
for (var i=1; i<=5; i++) {
    setTimeout( function timer() {
    console.log( i ); // 每秒 打印 6 
    }, i*1000 );
}

/** 解决 1. 修改 for (var) 为 let */
/** 解决 2 */
for (var i=1; i<=5; i++) {
    (function(j) {
        setTimeout( function timer() {
            console.log( j );
        }, j*1000 );
    })( i );
}
```

```js
/** 模块化 */
var foo = (function () {
    var a = ''
    function do(){}
    
    return {
        a,
        do
    }
})()

/** 现代化模块方案 */
var MyModules = (function Manager() {
    var modules = {};
    function define(name, deps, impl) {
        for (var i=0; i<deps.length; i++) {
            deps[i] = modules[deps[i]];
        }
        modules[name] = impl.apply( impl, deps );
    }
    
    function get(name) {
        return modules[name];
    }
    
    return {
        define: define,
        get: get
    };
})();

// 使用 
MyModules.define( "foo", ["bar"], function(bar) {
var hungry = "hippo";
function awesome() {
console.log( bar.hello( hungry ).toUpperCase() );
}
return {
awesome: awesome
};
} );
```

### 附录 

- 动态作用域
- 块作用域替代方案
- this词法

```
动态作用域      不关心声明位置 只关心调用位置 this
                js 不具有动态作用域

块作用域替代方案
                1. try{throw 1}catch(){}
                        性能糟糕单有优化
                2. IIFE
                        会改变代码段含义 this return break contine
                        
this词法
                胖箭头继承外部this绑定
```

```js
/** ES5之前使用 块作用域 with catch */
try{throw 2}catch(a){
    console.log(a)
}
```

## 二部分 

### 一章 关于this

```
this
        特点
                取决于函数调用方式(运行时)和函数声明位置没有关系(有关系的是词法作用域)
        好处
                隐式传递对象引用 API设计简洁易于复用
        坏处
                容易误解
                        并非指向函数自身
                        并非指向作用域
```

### 二章 this全面解析

```
/**
 * this 
 *      绑定规则
 *          1. 默认绑定     fn()            this -> window(非严) | undefined(严)
 *                              不是调用位置是否严格模式 而是 函数体是否处于严格模式中  
 *          2. 隐式绑定     obj.fn()        this -> obj
 *                              隐式丢失:       a = obj.fn  
 *          3. 显示绑定     fn.call(obj)    this -> obj
 *                              如果 obj 为 原始值 会转换为对象形式
 *                              如果 obj 为 null / undefined 则忽略 按 默认绑定 (所以传 null 时副作用是可能修改全局对象)
 *                              (bind作为柯里化使用时会传 null: foo.bind(null, val1) )
 *                              (apply作为数组参数展开为函数参数时:  foo.apply(null, [2,3]))
 *                              解决传 null 副作用: 创建一个 DMZ 代替 null 传入 
 *                                                  (完全空对象:  Object.create(null) 没有 prototype 委托)
 *                              也会绑定丢失
 *                              (解决办法: 硬绑定 bar = function(){fn.call(obj)} 强制绑定到obj上)
 *                                   
 *          4. new绑定      new fn()        this -> 新对象
                                new步骤
                                            1. 创建新对象
                                            2. 新对象执行原型链接
                                            3. 新对象绑定函数this
                                            4. 若没有返回则返回新对象
 *      优先级
 *          比较方式:
 *              隐式绑定 vs 显示绑定 obj1.fn.call(obj2) 看结果是 obj1 还是 obj2
 *              隐式绑定 vs new绑定  new obj.fn(val)    看 val 会写在 obj 上还是返回 对象上
 *              显示绑定 vs new绑定  new 需要接一个函数 fn.call 没有返回值 所以不能直接使用
 *                                  硬绑定法:       bar = fn.bind(obj1)     // fn(val){this.a = val}
 *                                                  bar(2)                  // 硬绑定调用
 *                                                  obj2 = new bar(3)       // new 绑定 / 硬绑定    
 *                                                  比较 obj1.a 和 ojb2.a 谁是 3 即可 
 *                                  new绑定优先原因: bind() 实现中有一个 this 判断是否被 new 调用若是就用new产生的this来硬绑定  
 *                                  bing添加new判断原因: bind()功能之一是柯理化 new 也可以使用                              
 *          比较结果:     
 *              默认绑定 < 隐式绑定 < 显示绑定 < new绑定
 *      其他
 *          间接引用:       (p.foo = o.foo)() 这里是默认绑定指向全局对象
 *                          a = o.foo; a()
 *          硬绑定:         bar = function foo(){fn.call(obj)} 之后调用 bar 就强制 把 foo 绑定到 obj 上
 *                          内置 bind 
 *          软绑定:         硬绑定增加 this 判断 ( fn.apply((!this||this ===(window||global))?obj:this), ... )
 *                          可以在替换默认绑定, 但不影响隐式和显示绑定
 *          胖箭头函数:     胖箭头函数根据外层作用域决定 this
 */

/** 优先级比较: 隐式绑定 vs 显示绑定 */
(function(){
    function foo() {
        console.log(this.a)
    }
    
    var obj1 = {
        a: 2,
        foo: foo
    }
    
    var obj2 = {
        a: 3,
        foo: foo
    }
    
    obj1.foo()
    obj2.foo()
    
    obj1.foo.call(obj2)
    obj2.foo.call(obj1)
})()

/** 优先级比较: 隐式绑定 vs new 绑定 */
(function(){
    function foo(val) {
        this.a = val
    }

    var obj1 = {
        foo
    }

    var obj2 = {}

    obj1.foo(2)
    console.log(obj1.a)

    obj1.foo.call(obj2, 3)
    console.log(obj2.a)

    var obj2 = new obj1.foo(4)
    console.log(obj1.a)
    console.log(obj2.a)
})()

/** 优先级比较: 显示绑定 vs new绑定 (bind硬绑定比较法) */
(function() {
    function foo(val) {
        this.a = val
    }

    var obj1 = {}

    var bar = foo.bind(obj1)
    bar(2)

    var obj2 = new bar(3)
    console.log(obj1.a)
    console.log(obj2.a)
})()

/** bind 实现的 polyfill */
(function(){
    if (!Function.prototype.bind) {
        Function.prototype.bind = function(oThis) {
            if (typeof this !== "function") {
                throw new TypeError(
                "Function.prototype.bind - what is trying " +
                "to be bound is not callable"
                );
            }

            var aArgs = Array.prototype.slice.call( arguments, 1 ),
            fToBind = this,
            fNOP = function(){},
            fBound = function(){    
                return fToBind.apply(
                    (this instanceof fNOP && oThis ? this : oThis),         // 这里判断了 this 是否是 new 生成的 this , 只有这样 bind 才能作为 new 的柯里化方法
                    aArgs.concat(Array.prototype.slice.call( arguments ))
                )
            }

            /**
             * 这里的 this 指的是调用 bind 方法的函数本身( fn.bind(obj) 中的 fn) 
             * 
             * new生成的this(fBound中的this)是 fBound 方法创建(bind返回的方法) 
             * 所以 它的 __proto__.__proto__ 是 fBound.prototype 即 new fNOP()
             * (new(this) -> fBound.prototype -> fNOP.prototype )
             * 
             * instanceof 是比较 obj.__proto__.__proto__.... === fn.prototype
             * 
             * 所以 this instanceof fNOP 为 true 
             */
            fNOP.prototype = this.prototype; 
            fBound.prototype = new fNOP();
            return fBound;
        };
    }
})()


/** bind 作为 new 的柯里化实践 */
(function(){
    function foo(p1, p2) {
        this.val = p1 + p2
    }

    var bar = foo.bind(null, 'p1')  // 柯里化 (柯里化时绑定一般传null)
    var baz = new bar('p2')
    
    console.log(baz.val)    // p1p2
})()
```

### 第三章 对象

```
类型
    主要类型
        基本类型
            string
            number
            boolean
            null
            nudefined
            symbol
        复杂类型
            object
                        创建:   声明形式 {}
                                构造形式 new Object()
                        内置对象:
                                String
                                Number
                                Boolean
                                Object
                                Function
                                Array
                                Date
                                RegExp
                                Error
                                            string / number / boolean 在必要时语言会自动把基本类型包装成对象
                                            null / undefined 没有构造形式 只有文字形式 
                                            Date只有构造形式没有文字形式
                                            Object / Array / Function / RegExp 无论文字形式还是构造形式都是对象   
                        属性访问: 
                                .操作符 属性访问    
                                                    满足标识符命名规范
                                                    访问不存在属性 是 undefined (不存在变量是 RefernceError)
                                ['']语法 键访问     
                                                    有特殊符号
                                                    属性名永远是字符串(会转为字符串 toString() )
                                                    ES6 增加 可计算属性名 (Symbol 的用法其实就是) 
                        函数: 
                                永远不属于一个对象(只是引用)
                                ES6 增加了 class 中函数 super 引用
                        数组:
                                数组也是对象    
                                                    可以添加属性 不触发 length 变化
                                                    但属性 是类数字 则或 变为下标
                        复制: 
                                浅复制:             Object.assign() (=号赋值 所以不会复制属性特性)
                                深拷贝:             
                                                    JSON.parse(JSON.stringify(obj))  
                                                    递归拷贝 (循环引用容易造成死循环)
                        描述符:
                            特性:
                                value
                                writable:可写
                                                    false 时 修改 (非严) 静默失败 (严格) typeError
                                enumerable:可枚举
                                                    true 时 属性会被 for...in...枚举到
                                                    检查是否可枚举 obj.propertyIsEnumerable(propName) (不含原型链)
                                configurable:可配置
                                                    false 时 单向操作 再使用 defineProperty 修改特性 typeError
                                                             但可以把 writable 由 true 改为 false
                                                             禁止删除 (静默失败)
                            修改特性: 
                                Object.defineProperty(obj, name, {...})
                            不变性: (都是浅不变)
                                Object.preventExtensions(obj)   
                                                    禁止扩展 无法创建新属性 (非严) 静默失败 (严) typeError
                                Object.seal(obj)
                                                    密封对象 无法创建新属性 不能重新配置 不能删除属性 可修改值
                                                            (调用 preventExtensions 再设置 configurable: false)
                                Object.freeze(obj)
                                                    冻结对象 无法创建新属性 不能重新配置 不能删除属性 不可修改值
                                                            (调用 seal 且所有数据访问属性 writable:false)
                        访问描述符: 
                            设置getter/setter后 由 value 和 writable 改为 set 和 get
                        属性存在性: 
                                properName in obj    
                                                    是否在对象及其原型链上(没有直接办法获取同类型所有属性数组)
                                obj.hasOwnProperty(propName)
                                                    只检查是否在对象中不检查链
                        属性遍历:
                            对象
                                for...in...
                                                    特性相关  enumerable: true
                                                    无法直接获取属性值
                                                    遍历数组时 还会包含所有可枚举属性(所以数组用for就行)
                                Object.keys(obj)
                                                    可枚举属性 仅自身
                                Object.getOwnPropertyNames(obj)
                                                    无论是否可枚举 仅自身
                            数组
                                arr.forEach()
                                arr.every()
                                arr.some()
                                for(var v of arr){}
                                                    实际使用的是迭代器对象 arr 内置了 it = arr[Symbol.iterator](); it.next(); 
                                                    (arr[Symbol.iterator] 是迭代器对象生成函数)
                                                    (可以为任意对象定义[Symbol.iterator]属性来使用for..of遍历)
    类型判断
        方法
            typeof val                              判断基本类型(除null)
            obj instanceof fn
            Object.prototype.toString.call(val)                
        注意
            typeof null == 'object'     // 底层二进制前三位 都是 0 会判定为 object 类型 null 都是 0
```

```js
/** 描述符 -> 访问性描述符 (添加 getter / setter) */
// 对象直接设置
var obj = {
    get a() {
        return this._a
    }
    set a(val) {
        this._a = val
    }
} 

// 特性设置
Object.defineProperty(obj, 'b', {
    get: function(){return this.a*2},
    enumerable: true
})
```

```js
/** 为任意对象添加 @@iterator (之后就可以使用 for...of...) */
var obj = {}

Object.defineProperty(obj, Symbol.iterator, {
    enumerable: false,
    writable: false,
    configurable: true,
    value: function(){
        var o = this;
        var idx = 0;
        var ks = Object.keys(o);
        return {
            next: function(){
                return {
                    value: o[ks[id++]],
                    done: (idx > ks.length)
                }
            }
        }
    }
})

// 手动遍历
var it = obj[Symbol.iterator]()
it.next()

// for..of遍历
for(var v of obj){}
```

### 第四章 混合对象类

```
类: 
    概念
        类:         结构分类
        继承:       不再重复定义
        多态:       重写父类行为
        构造函数:   实例由类的一个特殊方法构造 通常和类名相同 作用是 初始化实例需要的信息
    其他:
        多重继承:   (重复的定义会不确定)
        
混入
    显式混入        混入方法实现把没有属性创建并复制
    隐式混入        借用构造函数
```

```js
/** js 显式混入 方式 实现 继承 */
function mixin(sourceObj, targetObj) {
    for(var key in sourceObj) {
        if(!(key in targetObj)){        // 没有的属性才会 混入 进去 (实现了多重继承类似的属性拷贝 但并没有继承关系)
            targetObj[key] = sorceObj[key]  // 如果是 复杂类型 会影响其他混入的属性
        }
    }
    return targetObj
}

var Car = mixin({a:1}, {})
mixin({b: 2}, Car)          // Car 混入了两个对象属性
```

```js
/** js 隐式混入 方式 实现 继承 */
var something = {
    cool: function() {
        this.greeting = ""
        this.count = this.count ? this.count + 1: 1
    }
}

something.cool()
something.greeting
someting.count

var Another = {
    cool: function () {
        something.cool.call(this) // 隐式混入 (借用构造函数方式)
    }
}

Another.cool()
Another.greeting
Another.count
```

```js
/** js 寄生式 实现 继承 (即时显式也是隐式) */
function Vehicle() {
    this.engines = 1
}

Vehicle.prototype.ignition = function() {
    console.log('')
}
Vehicle.prototype.drive = function() {
    this.igition()
    console.log('')
}

function Car () {
    var car = new Vehicle()     // 先 从 Vehicle 构建实例
    car.wheels = 4              // 再 混入 自己的定义
    var vehDrive = car.drive
    car.drive = function() {    // 重写父类方法 (多态)
        VehDrive.call(this)         // 类似 类的 super()
        console.log('')
    }
    return car                  // 返回 符合对象 (new + 混入自己定义)
}

// 使用
var myCar = new Car()           // 这里不用 new 更好 会生成一个对象最后被丢弃       
myCar.drive()
```

### 第五章 原型

```
对象 
    obj.__proto__   原型链
        属性不在对象自身时 会 沿着原型链查找 全部找不到才会返回 undefined
        for...in 遍历对象可枚举包含原型链上属性
        name in obj 也会检查包含原型链上属性
        尽头:   所有普通的原型链尽头是 Object.prototype
        创建:   obj2 = Object.create(obj1)  ( obj2.prototype = obj1 )
        屏蔽:   对象自身属性定义时会屏蔽原型链属性
    属性赋值
        存在对象上                          直接赋值
        不存在对象 / 不存在原型链上         在对象上新建属性赋值
        不存在对象 / 存在原型链上           1.  原型链上属性标记为 writable: true 则  在对象上新建属性赋值  
                                            2.  原型链上属性标记为 writable: false 则 (非严)静默失败 (严) typeError
                                            3.  原型链上属性是访问属性 则 调用原型链上 setter (并不会重新定义)
                                            (若希望2,3同样新建屏蔽属性应该使用 Object.defineProperty()来添加)
    隐式屏蔽(不小心属性赋值造成屏蔽)
        obj.a++
类  
    js 中没有类 其他语言类的继承类似复制 js 对象不会复制 继承用 原型链来关联实现  
    new Fn      创建一个新对象把对象 的原型链指向 Fn 的原型上
                劫持函数用构造对象的形式调用它
构造函数
    fn.prototype                函数创建时就会有一个原型
    fn.prototype.constructor    函数的原型对象有关联到函数的属性
                                new创建对象也有关联到函数的属性(实际上不是对象本身而是对象的原型链上指向了函数的原型)
                                constructor 并不代表又谁构造(仅是对象和那个函数相关)
                                仅每个函数伴生prototype对象有该属性且指向函数
                                是可修改的 (所以不可靠)
继承
    原型继承    先借用构造函数再修复原型
    继承检查    obj instanceof Fn 
                                处理对象 和 函数
                                .bind() 硬绑定函数 没有 .prototype 属性 目标函数的 .prototype 会代替硬绑定函数的.prototype
                obj1.isPrototypeOf(obj2)
                                对象 和 对象(也可以处理函数 fn.prototype)
    获取        
                Object.getPrototypeOf( obj )
                obj.__proto__   (非标准方法)
                                不存在于实际对象上 存在于 内置的 Object.prototype 且更像 getter/setter
                                可设置属性 调用 setter
    关联
                Object.create(obj) 
                                创建关联对象的新对象
                                null 创造一个空对象 非常适合 存储数据
                                create 第二个参数可以添加属性(及特性)
                Object.setPrototypeOf( this, o );
                                创建对象的关联对象
```

```js
function Foo(){}
Foo.prototype.constructor === Foo // true   

var a = new Foo()
a.constructor === Foo // true  // a 上实际没有 construtor 属性 实际上查找到原型链(即Foo.prototype)上的 construtor

// 注意
Foo.prototype = {} // 创建一个新原型对象 
var a1 = new Foo()
a1.constructor === Foo // false     a1 上实际没有 construtor 属性 
a1.constructor === Object // true   {} 新对象没有 construtor 属性 会继续向原型链查找 直到找到 Object.prototype.construtor
```

```js
/** js 模拟 类 */
function Foo(name) {
    this.name = name
}

Foo.prototype.myName = function () {
    return this.name
}

// 使用
var a = new Foo('a')
a.myName()
```

```js
function Foo(){}
Foo.prototype = {} // 创建一个新原型对象(这个对象自身没有 constructor 属性) 

/** 修复 prototype 的 constructor 属性 */
Object.defineProperty( Foo.prototype, "constructor" , {
    enumerable: false,
    writable: true,
    configurable: true,
    value: Foo // 让 .constructor 指向 Foo
} );
```

```js
/** 定义 Foo 类 */
function Foo(name) {
    this.name = name
}

Foo.prototype.myName = function() {
    return this.name
}

/** 由 Foo 类 继承 的 Bar 类 */
function Bar(name, label) {
    Foo.call(this, name)    // 借用构造函数
    this.label = label
}

// 这里不能用 Bar.prototype = Foo.prototype     为 Bar 添加原型方法时 直接在 Foo 上添加了 不如直接使用 Foo
// 这里最好别 Bar.prototype = new Foo()         如果 Foo 有副作用 会引发副作用
Bar.prototype = Object.create(Foo.prototype) // 把原型的原型链链接到Foo的原型上

Bar.prototype.myLabel = function() {
    return this.label
}

var a = new Bar('a', 'obj a')

a.myName()
a.myLabel()
```

```js
/** __proto__ 实现 */
Object.defineProperty( Object.prototype, "__proto__", {
    get: function() {
        return Object.getPrototypeOf( this );
    },
    set: function(o) {
        // ES6 中的 setPrototypeOf(..)
        Object.setPrototypeOf( this, o );
        return o;
    }
} );
```

```js
/** Object.create 的 polyfill (第二个参数无法polyfill) */
if (!Object.create) {
    Object.create = function(o) {
        function F(){}
        F.prototype = o;
        return new F();
    }
}
```

### 第六章 委托

- 6.1 面向委托设计
    - 类的继承(复制)和多态(重写)
    - JS的类实现(委托)
    - 调试时chrome对对象构造方法名的追踪方式
    - 对比了用委托模拟继承不如直接使用委托(原型链)
    - 方法对象原型关系图
- 6.2 类于对象
    - es6的class语法糖 继承(extends) 多态调用父方法(super)
    - 以父子控件为例对比了模拟继承 / class继承 / 直接委托控件对象 
- 6.3 更简洁设计
    - 反类 即 把 原本设计为类的父类 设计为对象形式
- 6.4 更好的语法
    - 委托时创建的 Object.create(obj) 可以直接设置 Object.setPrototypeOf(obj1, obj2) (两个已有对象)
    - 对象中方法 { fn () {}} 代替 { fn:function f(){} } (有匿名函数缺点-不容易自我调用)
- 6.5 内省
    - instanceof 检查类型原理及弊端
    - isPrototypeOf 委托后判断方式

```
[[prototype]]       本质就是对象之间关联关系

类                  (其他语言)继承本质是复制
                    (JS) 没有类机制 继承 子类任务委托给父类方法
                    委托实现 来自于 [[prototype]]
                    
调试
                    chrome 中对象会显示构造函数名称
                            (其实是 obj.prototype.constructor.name)

ES6
                    class   类(语法糖)
                    extends 继承
                    super() 多态
                    { fn () {}} 代替 { fn:function f(){} } (有匿名函数缺点-不容易自我调用)

检查          
                    instanceof 
                            检查只要在原型链上即可 检查 对象 和 函数 (继承判断)
                    obj1.isPrototypeOf(obj2)
                            对象是否在对象原型链上 (直接委托判断)
```             

```js
/** 委托模拟实现 类 */
function Foo(who) {
    this.me = who;
}
Foo.prototype.identify = function() {
    return "I am " + this.me;
};

function Bar(who) {     // Bar 继承自 Foo
    Foo.call( this, who ); // 借用构造函数
}
Bar.prototype = Object.create( Foo.prototype ); // 修改原型链

Bar.prototype.speak = function() {
    alert( "Hello, " + this.identify() + "." );
};

var b1 = new Bar( "b1" );
var b2 = new Bar( "b2" );
b1.speak();
b2.speak();
```

```js
/** 直接使用委托 */
Foo = {                     // 直接把 Foo 作为一个可委托的基础对象
    init: function(who) {
        this.me = who;
    },
    identify: function() {
        return "I am " + this.me;
    }
};
Bar = Object.create( Foo ); // 直接使用委托

Bar.speak = function() {
    alert( "Hello, " + this.identify() + "." );
};
var b1 = Object.create( Bar );
b1.init( "b1" );
var b2 = Object.create( Bar );
```

```js
/** instanceof */
function Foo(){}
function Bar(){}
Bar.prototype = Object.create(Foo.prototype)
var b1 = new Bar()

b1 instanceof Foo // true
```

### 附录 ES6中class特点及弊端

- class
    - class 解决的问题
    - class 本质
    - class 问题

```
class
        特点
            杂乱的 prototype 设置
            可以使用 extends 扩展对象
            可以使用 super 实现相对多态(父方法实现一遍先)
            class 不能声明属性 只能声明方法
        本质
            语法糖
            修改父类方法 所有实例都会影响
            无法定义成员类属性(共享属性 只能使用 prototype)
        其他
            依然会意外屏蔽
            super 是静态绑定 (使用super的方法不适合应用于多个class)
            如果使用 .bind 函数硬绑定函数 这个函数不会被 extend 扩展到子类中
```

```js
class C {
    constructor(id) {
        // 噢，郁闷，我们的 id 属性屏蔽了 id() 方法
        this.id = id; // 实例中 id 属性 会屏蔽掉 id 方法
    }
    id() {
        console.log( "Id: " + id );
    }
}
var c1 = new C( "c1" );
c1.id(); // typeError
```

```js
/** super (静态确定)不适合再委托 */
class P {
    foo() { console.log( "P.foo" ); }
}
class C extends P {
    foo() {
        super();
    }
}
var c1 = new C();
c1.foo(); // "P.foo"
var D = {
    foo: function() { console.log( "D.foo" ); }
};
var E = {
    foo: C.prototype.foo
};
// 把 E 委托到 D
Object.setPrototypeOf( E, D );
E.foo(); // "P.foo"
```


-------------------------------------------

## 自总结

函数 使用 new:  但凡 return 为非 基础类型 都不会被隐式创建新对象替换掉  

```
// 对象

/**
 * obj.hasOwnProperty()     : 属性是否在操作符中    仅自身          包含不可迭代
 * name in obj              : 属性是否在操作符中    包含原型链      包含不可迭代
 * 
 * obj.propertyIsEnumerable() 属性是否可枚举        仅自身
 * 
 * for...in...              : 遍历键值              包含原型链      仅迭代
 * Object.keys()            : 属性列表              仅自身              仅迭代
 * Object.getOwnPropertyNames()                     仅自身          包含不可迭代
 * 
 */

// 特性
/**
 * writable
 * configurable
 * enumerable       /   obj.propertyIsEnumerable()
 * 
 * 修改: 
 * Object.defineProperty()
 * 
 * 
 * 不变性:
 * Object.preventExtensions()
 * Object.seal()
 * Object.freeze()
 * 
 * 
 */

// 迭代器
/**
 * for...of...
 * [Symbol.iterator] : () => {return {next: ()=>{return {value, done}}}}
 */

// 原型链
/**
 * 特性的影响: (解决只能用 defineProperty() 向对象添加属性 )
 * 原型链上 writable false 会影响创建屏蔽属性
 * 原型链上 setter 会影响创建屏蔽属性
 * 
 * Bar.prototype = Foo.prototype 这样还不如直接使用 Foo ,用 Bar 原型并非独立
 * Bar.prototype = new Foo() 这样会调用一次Foo如果Foo有副作用会有问题
 * Bar.prototype.__proto__ = Foo.prototype ES6之前
 * Bar.prototype = Object.create(Foo.prototype) 最常用(轻微性能 创建新对象抛弃原对象)
 * Object.setPrototypeof(Bar.prototype, Foo.prototype) ES6方案完美
 * 
 * 寻找祖先: 
 * obj instanceOf Foo           判断 对象  函数 
 * Object.getPrototypeOf(obj)   获取对象原型(ES6)
 * obj.__proto__                获取对象原型(ES6之前)(访问器属性)
 * 
 */
```

## 其他问题

如果使对象调用不存在属性返回某值?   
实现柯里化函数?  
js 实现继承方法?  

ES6 中的 class 关键字可以在内置的类型（比如 Array）上实现类似“子类”的功能 ?  
