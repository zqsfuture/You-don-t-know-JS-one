# 你不知道的JS-下

## 第一部分

### 第一章 深入编程

```
程序
        源码 或 代码:       一组特殊指令 指示计算机完成那些任务
        计算机语言/语法:    指令的格式和组合规则
        语句:               一组特定任务的 单词 数字 和 运算符
        变量:               保存值的替代符
        字面量:             值
        运算符:             值或变量执行的动作
        表达式:             变量或值的应用 或与运算符的组合
        调用表达式:         函数调用
执行程序
        解释器/编译器:      代码翻译成计算机理解的命令 
        代码解释:           运行时翻译 (JS 是解释型-动态编译-编译后立即执行) 
        代码编译:           预先编译
        
实践
        输出
            console.log
            alert
        输入
            val = prompt('tips:')

运算符
        赋值            =
        声明            var
        算术            + - * /
        复合赋值        += -= *= /=
        递增/递减       ++a a++ --a a--
        对象属性访问    obj.property    obj[property]
        相等            == === != !==
        比较            > < >= <=
        逻辑            && || 
        
值和类型
        类型            值的分类
        类型转换        值 类型转换
        显式转换        Number() String() Boolean()
        隐式转换        a+b +a a>b a==b
        
注释
        作用            解释为什么 而非 是什么
        避免            没有注释
                        过多注释
        类型            单行注释
                        多行注释    ( 不能出现在多行注释中内容是 */ )

变量
        作用            存放指定类型的值
        静态类型        变量有类型(类型强制)
        弱类型          变量没有类型值才有类型(动态类型)(js)
            隐式类型转换
                        console.log()   需要把 val 隐式转换为 字符串

块
        作用            一系列步骤合集

条件判断
        if..else
        switch

循环
        while
        do...while
        for
        
函数
        function 
        参数
        作用域
```

### 第二章 深入 javascript

```
类型
    内置类型
        字符串
        数字
        布尔值
        null
        undefined
        对象
        符号
    检查
        typeof
            结果
                undefined
                string
                number
                boolean
                object
                undefined
                function

对象
    属性访问
        obj.a
        obj['a']
    特殊对象
        数组        键值是数字索引
        函数        也可以拥有属性
    基本类型封装
        字符串      new String()
        数字        new Number()
        布尔        new Boolean()


类型转换
    类型
        显式
        隐式
    布尔值
        false
                ''
                0 / -0 / NaN
                null
                undefined
                false
    
类型比较 (结果应当是布尔值)
    等性
        ==      类型不匹配时会有隐式转换
        ===     类型不同则断为不同
        !==
        !=
    不等关系    (转为基本值 若都是 字符串 比较字符顺序 否则 转为数字 比较)
        >
        >=
        <
        <=

变量
    作用域
        提升    声明会提升
        嵌套    内层可以访问外层 (访问不可访问的 referenceError)

条件判断
    if..else
    switch(a) {case n: break}
                break 省略后会运行之后 case 语句 (已经表示已通过)

严格模式
                不运行省略 var 的隐式自动全局变量

函数
    特性
                可以作为值
    匿名函数    (一般作为值用时可以)
    命名函数
    立即调用(IIFE)
    闭包
    
this
                通常指向一个对象 由函数如何被调用决定

原型
    prototype 
                行为委托给另一个对象

兼容
    polyfiling  补丁-旧语法 编写 适配新语法 的相同功能
    transpiling 新语法 编写 由 编译器 编译成符合旧语法的 代码
```

### 第三章 深入 你不知道JS 系列

```
你不知道-上
    一部分
        作用域
        闭包
    二部分
        this
        对象原型

你不知道-中
    一部分
        类型
        语法
    二部分
        异步
        性能

你不知道-下
    一部分
        基础讲解
    二部分
        ES6
        更新版本
```

## 第二部分

### 第一章 ES 现在与未来

```
版本
        ES5     2015年
        之后根据年份迭代

兼容
        polyfill        补丁
        transpiler      工具编译兼容
```

### 第二章 语法

```
块作用域
    创建        
                函数声明(IIFE)
                { let }
                        let 没有提升
                        提前使用 暂存死区(TDZ) referenceError
                                typeof 的安全防范机制可以规避 未声明 但 暂存死区依然报错
                        for 循环头部的 let (for中隐藏了两层作用域)
                { const }

es6
    展开运算符 (...)
                代替 apply() 把数组作为参数传递 foo(...[1,2,3])
                代替 arr.concat() 合并数组  [1,2,...a]
    收集运算符 (...)
                代替 arguments 接收剩余参数
                        argument 只是类数组
    默认参数值
                只设置 undefined 的值
                剩余参数不能设置默认值
                最右侧
                表达式是惰性调用
    解构
        特性    完成值是右侧的值
                可以用来解构参数 function( [x, y],  {m, n}){}
        数组
                [a, b, c] = [1,2,3]
                        不必是变量标识符 赋值表达式都可以
                        可以丢弃一部分
                        多余的会赋予 undefined
                        可以使用收集运算符
                        可以使用默认值
        对象
                {x, y: newY} = {x: 1, y: 2}
                        注意 解构中 目标反转
                        注意 {} 不能作为行首 (会被认为是块语法)
                        不必是变量标识符 赋值表达式都可以
                        var {} 声明 且赋值
                        可以使用默认值
    对象字面量
        简洁属性        {x}
        简洁方法        {f () {}}   
                                简洁方法支持 super
                                * foo() 生成器也可以
                                简洁方法不支持具名函数 
    getter/setter
                        o = { __id:0, get id(){}, set id(val) {} }
    计算属性名
                        o[a + b] = val
                        o = { [a + b]: val}  // ES6 之前不可以这么用
    原型
                        Object.setPrototypeOf() // 之前使用 obj.__proto__
    super对象
                        o = { foo () { super.foo() } } // 会调用 o.__proto__ 上的 foo 方法
                        对象中只允许简洁方法中出现 且只能使用 super.xxx 而不是类中 super()
    模板字面量
                `hello${name}`
                        可是用 ${} 插值
                        可以分散多行
                        与函数组合成特殊调用 标签函数 foo`hell${name}` 
                                参数: 所有普通字符串数组, 插值作为后面剩余参数
                                raw: 原始字符串
    箭头函数
                        总是函数表达式
                        单行省略 return
                        继承父层 this
                        继承父层 arguments
                        继承父层 super
                        继承父层 new.target
    for...of
                        迭代器(值必须是 iterable - 底层是  symbol.iterator)
                                默认提供的: Array, String, Generators, typedArrays , Collections
                        循环的是值
                        可以使用 break, continue return 提前终止
    正则
            unicode 标识 (/.../u)
                        之前 BMP 模式中 有些特殊字符 (双位) 匹配失败
            定点标识 (/.../y)
                        test() 总是起始开始匹配不更新lastIndex 
                        顶线模式限制 只能从 lastIndex 开始匹配
            flags
                        reg.flags 来获取 所有标识符 
    数字字面量
            0o52        八进制 (严格模式不支持字面量形式) 之前 是 052
            0x2a        十六进制
            0b101010"   二进制
    Unicode字符
                        ES6之前 用 Unicode 转义符 指定字符 `\uXXXX` 四个十六进制字符 (只能标识 BMP字符集 额外的 可能需要替代对 即两个特别的转义字符)
                        ES6之后 有 Unicode 码点转义 `\u{XXXX}` 
                        可以用于标识符名
            影响
                长度    ES6处理 非 BMP字符集 长度获取 [...gclef].length / Array.from(gclef).length / s1.normalize().length  ( 都有缺陷 - 字素族界限算法 )
                位置    ES6获取某位字符 charAt() 不支持 unicode 可使用 [...s1.normalize()][2] 取第二位字符
                        ES6获取某位字符码 codePointAt(2) 支持 之后再使用 String.fromCodePoint( 0x107 ) 反向
                正则    ES6 支持 unicode 标识模式
    Symbol
                        没有字面量形式 
                        不是构造器不能使用 new 
                        参数是描述符
                        typeof 输出 symbol
                        symbol 不是 Symbol 实例
                        封装方法是 Object(sym) 而不是 Symbol(sym)
                        符号内部值(名称)代码无法出现且获得
            Symbol.for()    
                        全局符号注册 (通过描述来确定唯一)
                Symbol.keyFor()
                        提取全局符号的 描述
            对象属性
                        常作为私有属性
                        不再一般属性枚举中 若要取  Object.getOwnPropertySymbols(obj)
                    

小技巧
                默认空函数 Function.prototype 代替 ( a = function(){} - 避免资源浪费和创建过程)
```

```js
if(something) {
    function foo() {console.log(1)}
}else {
    function foo() {console.log(2)}
}

foo() // ES6 之前 --> 2 ; ES6 报错(foo提升在块内) ;  目前 能按正常理解解析 
```

```js
function foo(x, y = 4) { // y 使用默认参数
    // x = x || 11;        // 之前设置默认值 (但若传递的 0 '' .... 也会被替代)
    x = (x !== undefined) ? x : 11 // 修正后设置默认值 (现在可以使用 x = x ?? 11)
    
}
```

```js
function bar(val) {
    console.log('use bar')
    return y + val
}

function foo(x = y + 3, z = bar (x)) { // 运行时 才调用 bar() 惰性调用
    console.log(x, z)
}

var y = 5
foo()       // use bar ; 8, 13
foo(10)     // use bar ; 10, 15
y = 6
foo(undefined, 10) // 9 10
```

```js
var w = 1, z = 2
function foo(x = w + 1, y = x + 1, z = z + 1) { // z 在 () 中声明了, 但还没有初始化 TDZ错误
    console.log(x, y, z)
}
foo()
```

```js
// 数组解构
var o = {}
[o.a, o.b, o.c] = foo()

var p = []
[p[1], p[0]] = foo()        // 数组重排

[x, y] = [y, x]             // 交换变量

[,b] = foo()                // 丢弃一部分

[a, b, c, d] = foo()        // d 多的被赋值 undefined

[a ,...b] = foo()           // 使用收集运算符

[a = 1] = foo()             // 默认值

[a, [b,c]] = [1, [2,5]]     // 嵌套解构

// 对象解构
var o = {}
( { x: o.x, y: o.y, z: o.z } = bar()  )         // 对象属性的赋值

var which = 'x'
var o = {}
(  { [which]: o[which] } = bar() )              // 计算出来的属性

var p = []
( { a: p[0], b: p[1] } = bar() )

var {a: x, a: y} = bar()                        // 重复赋值 x , y 都是 a 值

var { a: {x:X, y: Y}, a } = { a: {x: 1} }       // 解构子对象
var { a: X, a: Y, a: [ Z ] } = { a: [ 1 ] }     // 解构子数组

var { a = 5} = bar()                            // 默认值

{x: {y: {z:w}}} = {x: {y: {z: 6}}}              // 嵌套解构

// 完成值 
var p = {a, b, c} = o                           // p === o 完成值是右侧值 (可以链式解构)

// 解构默认值 + 参数默认值
function ({x = 10} = {}, {y} = {y: 10}){ ... }
```

```js
// es6 计算 非BMP 字符集长度 
var gclef = '\uD834\uDD1E' // ES6 可以 \u{1D11E}
gclef.length    // 2
[...gclef].length // 1
Array.from(gclef).length // 1

// es6 计算 非BMP字符集长度 normalize 法
var s1 = '\xE9'
var s2 = 'e\u0301' 
s1.length       // 1
s2.length       // 2
[...s2].length  // 2 (上面方法处理不了)
s2.normalize().length // 1
s2.normalize() === s1 // true  normalize 接收序列后规范化

// normalize 问题
var s1 = 'e\u0301\u0330' // 多个组合符号修改了单个字符
s1.normalize().length // 2
```

### 第三章 代码组织

```
迭代器
            有序, 连续, 基于拉取用于消耗数据的组织方式
        iterator
            next()      下一个
            return()    完成
            throw()     异常终止
        iteratorResult
            value
            done
        for...of
                        创建一个迭代器产生消耗
                        需要一个 iterable
        自定义迭代器
                        obj = {[Symbol.iterator]() { return { next () { return {value, done} } } }}
        与展开运算符 (...)
                        [...a] 也是完全消耗迭代器

生成器  
            function * foo(){}
            结果是一个迭代器 函数内部执行过程是 迭代器执行过程 用 yield 分割 步骤
            提前终止 it.return() it.throw()
        迭代器
            next()      执行代码 到下一个yield 
                        参数传给上一个 yield 作为返回结果
        yield
                        代码间隔
                        不要把 yield 放在 finally 子句里, 会延后 trow 后完成调用
        yield *     
                        迭代器委托
        模拟    
                        使用状态机模拟
                        
模块
        旧
            (module(){ return {...} })()       旧版使用模块方式
        AMD
        CMD
        UMD
        commonJS
        es6
            import
                        不允许双向绑定
                        所有导入绑定都是不可变的 (修改 typeerror)
                        import 结果声明是提升的
                import {} from ''
                import * as foo from ''
            export
                default
                        每个模块只能定义 一个 default 到处
                        export default function foo() {}        导出 foo() 函数本身 
                        export {foo as default}                 导出 foo 标识符(可能内部修改 foo)
            模块循环依赖
                        先分析导出 
            浏览器
                        <script type="module">

类
        class
                        js 中类只是模拟 一种原型的委托
                        class 的名称必须调用 new
                        class 不被提升
                        不会创建全局对象属性
            constructor()   
                        构造函数
                        不是必须的
                        默认子构造器自动调用父类构造器传递所有参数
                        子构造器若有写必须调用 super() 之后才能使用 this
            extends
                        继承
            super
                        指向父构造器
                        super 不是动态的 是声明时内部建立的引用
            new foo()
            new.target
                        总是指向 new 实际上调用的构造器 (即使子类调用 super 使用的父类)
                        一般用于调用静态方法
            static
                        静态属性
                        static get xxx() {} getter 属性
            Symbol.species
                        getter构造器
                        static get [Symbol.species]() {return Array}    (子类仍然可以使用 new this.constructor(..) 来创建自身实例)
            扩展原生类
                        class myArr extends Array {}  (之前需要手动创建对象并连接 prototype 且不具备自动更新 length 值)
```

```js
class Foo { 
 // 推迟species为子构造器
 static get [Symbol.species]() { return this; } 
 spawn() { 
 return new this.constructor[Symbol.species](); 
 } 
} 
class Bar extends Foo { 
 // 强制species为父构造器
 static get [Symbol.species]() { return Foo; } 
} 
var a = new Foo(); 
var b = a.spawn(); 
b instanceof Foo; // true 
var x = new Bar(); 
var y = x.spawn(); 
y instanceof Bar; // false 
y instanceof Foo; // true
```

### 第四章 异步流控制

```
Promise
            不是回调替代, 而是可靠机制管理回调
            事件监听者(只处罚一次)
            看做未来值
        new Promise()
            then()
            catch()
        reject()
        resolve()
        all([])
        race([])
    
生成器+prmise
```


### 第五章 集合

```
TypedArray
        buffer
            new ArrayBuffer(32)
            buffer 本身除了查看byteLength 属性外不支持其他交互 (需要挂载视图上操作这里相当于只分配内存)
            buffer 用来放置到视图上 typedArray 上 new Uint16Array(buf, offset, length)
            buffer 可以关联多个视图
        大小端
            多字节数字中低字节(8位)位于这个数字字节表示中的右侧(大端)还是左侧(小端)
            web 目前使用小端表示法
        数组构造器
            Int8Array / Uint8Array
            Int16Array / Uint16Array
            Int32Array / Uint32Array
            Float32Array                    32浮点 IEEE-754
            Float64Array                    64浮点 IEEE-754
                    元素限制在声明位数大小中, 超出赋值会折回适用位宽
                    相互转换使用 typedArray.from(a, function(val){...})
                    sort() 使用数字排序法 (注意数组中数字排序不一样)
Map
            创建无序键/值对数据结构 (对象键必须是字符串 Map 不必)
            解决对象间对应关系
        new Map()
                    参数 可以是 [ [key, value] ] 键值对数组
                    参数 可以是 iterable (迭代器)
        get()
        set()       不能使用方括号设置或获取值 使用 get() set()
        delete()    删除不是使用 delete 运算符 而是使用 delete()
        clear()
        size
        values()    返回一个值的迭代器
        entries()   返回一个迭代器
        keys()      键迭代器
        has()       是否有给定的键
WeakMap
            Map 变体 只接受对象作为键 
            作为键的对象是弱持有 (对象本身被回收 weakMap 也会被移除, 但 map 会阻止 回收)
            只弱持有键
Set
            唯一值的集合 (不会强制转换 1 和 '1' 不同值)
            set 和 Map 用 add 代替 set 且不需要 get 
        new Set()
                    参数 接受 数组(值列表) / 或另一个 set 
                    参数 接受 iterable 
        has()       比较算法和 Object.is() 一样 但不会区分 -0  和 0
WeakSet
            对值弱持有
```

```
3085
十六进制:   00001100 00001101   // 基于 16 视图
大端:       00001100 00001101   // 基于 8 位视图时 大端
小端:       00001101 00001100   // 基于 8 位视图时 小端 如果小端数据用大端建立视图 得到的值会是 3340
```

```js
// 监测是大端 false 还是 小端 true
var littleEndian = (function() { 
 var buffer = new ArrayBuffer( 2 ); 
 new DataView( buffer ).setInt16( 0, 256, true ); 
 return new Int16Array( buffer )[0] === 256; 
})()
```

### 第六章 新增API

```
Array
        Array.of()      
                        解决new Array() 数字时构造器陷阱
                        接受任意个参数都是作为项
                        Array构建子类后通过子类创建数组时需要
        Array.from()
                        类数组 (有一个 length 属性 且大于等于 0)
                        ES6之前 类数组转换 Array.prototype.slice.call(arrLike)
                        ES6之前 复制数组 arr.slice()
                        永远不会产生空槽位 (但会是 undefined)
                        第二个参数 是映射回调 同 arr.map
                        第三个参数 是 第二个参数的 this 绑定
        arr.copyWithin()
                        复制一部分到同数组另一个位置 覆盖原值
                        参数: ( 目标位置索引, 复制源开始位置索引, 复制源结束位置索引 ) 
                        负值作为相对数组结束相对值
                        不会增加数组长度 到达结尾就会停止 
                        范围重叠会重复复制
        arr.fill(val, start, end)
                        填充数组
        arr.find(fn, this)
                        indexof: 返回 -1 或索引  / some(fn): 找到后返回 boolean / find(fn): 找到后返回找到的内容
                        内部方法 接收 return boolean 表示找到没找到 但整体 返回找到的内容
        arr.findIndex(fn)
                        indexof: 返回 -1 或索引 且 比较是 === 严格相等
                        findIndex: 返回 索引位置
        arr.entries()
        arr.values()
        arr.keys()
Object
        Object.is()
                        比 === 更严格 区分 +0 / -0  ES6之前判断-0 ( x == 0 && 1/x === -Infinity ) 
                        NaN 判定相同
        Object.getOwnPropertySymbols()
                        对象属性特性
        Object.setPrototypeOf()
                        设置原型 
        Object.assign()
                        浅复制
Math
        cosh()
        acosh()
        sinh()
        asinh()
        tanh()
        atanh()
        hypot()         平方和的平方根 (勾股定理)
        cbrt()          立方根
        clz32()
        expm1()
        log2()
        log10()
        log1p()
        imul()          32位整数乘法
        sign()
        trunc()         数字整数部分
        fround()
Number
        Number.parseInt()   指向全局的 parseInt
        NumberparseFloat()  指向全局的 parseFloat
        Number.EPSILON      两数最小差 (0.99... 和 1)
        Number.MAX_SAFE_INTEGER
        Number.MIN_SAFE_INTEGER
        Number.isNaN()      只对 NaN 返回 true
                            全局的 isNaN(): 对非数字返回 true 
        Number.isFinite()
        Number.isInteger(val)   检查小数部分是否非 0 
                                x === Math.floor(x) 不能判断 NaN 和 Infinity
        Number.isSafeInteger()  检查确保整数且在安全范围内
String
    Unicode函数
        String.fromCodePoint()
        str.codePointAt()
        str.normalize()         unicode 规范化
        String.raw()            或缺的不使用任何转义序列原始串 (常当 模板字符串标签函数使用)
        srt.repeat()            重复字符串
        str.startsWith()
        str.endsWith()
        str.includes()
```

### 第七章 元编程

```
元编程
            指操作目标是程序本身行为特性的编程
            代码查看自身, 修改自身, 修改默认语言特性
    函数名称 ( fn.name )
            用于递归栈调用
            用于开发栈追踪显示
            没有词法名称函数 
                    匿名                没有
                    class的构造函数     class名称
                    属性简写            属性名
                    symbol属性          [描述]
                    export default      defalut
                    foo.bind(0)         bound foo
                    new Function()      anonymous
            Object.defineProperty()来修改
    new.target
            指向能够指向调用 new 目标构造器
            用于内省 或 静态属性访问
    公开符号 (symbol)
        Symbol.iterator
            用于...展开 和 for...of
        Symbol.toStringTag
            影响 Object.prototype.toString() 结果  [object xxxx]
        Symbol.hasInstance
            方法 接收对象值 判断这个值是否实例
        Symbol.species
            修改控制构造器
        Symbol.toPrimitive
            隐式转换中必须被强制转换为一个原生类型值
        Symbol.isConcatSpreadable
            传个数组 concat 是否应该展开
        Symbol.unscopables
            使用 with 时哪些属性可以或不可以暴露为词法变量 ( return {a:true, b: false} )
    正则符号
        Symbol.match (默认匹配算法)
            代替 str.match 方法
        Symbol.replace
        Symbol.search
        Symbol.split
    代理
        Proxy
            局限性
                obj == pobj 不会被代理
            取消代理
                Proxy.revocable(obj, handlers) => {proxy:代理, revoke:取消代理方法}
            代理在后
                用代理作为最后保障
                思路: 设置原型 Object.setPrototypeOf(obj, pobj)
                作用: 希望对象不存在属性做其他操作
        reflect
            get
            set
            deleteProperty
            apply
            construct
            getOwnPropertyDescriptor
            defineProperty
            getPrototypeOf
            setPrototypeOf
            preventExtensions
            isExtensible            返回值总被转换为 boolean
            ownKeys
            enumerate
            has
    对象属性排序
            1. 数字上升排序 2 按创建顺序枚举字符串属性名 3 创建顺序枚举符号属性
            Reflect.ownKeys() Object.getOwnPropertyNames() Object.getOwnPropertySymbols() 按上述顺序
            Reflect.enumerate() Object.keys() for...in 可能不一样
    归优化
        尾递归 (TCO)
        非TCO  (trampolining)
```

```js
// 非 TCO 优化
function trampoline( res ) { 
 while (typeof res == "function") { 
 res = res(); 
 } 
 return res; 
} 
var foo = (function(){ 
 function _foo(acc,x) { 
 if (x <= 1) return acc; 
 return function partial(){ 
 return _foo( (x / 2) + acc, x - 1 ); 
 }; 
 } 
 return function(x) { 
 return trampoline( _foo( 1, x ) ); 
 }; 
})(); 
foo( 123456 ); // 3810376848.5
```

### 第八章 ES6之后

```
异步函数
    async funciton
            本质是 生成器 + promise + run() 语法糖
幂运算
    a ** b
arr.includes()
```