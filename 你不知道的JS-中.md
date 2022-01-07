# 你不知道的JS 中

## 第一部分

- 类型
- 类型转换 

### 第一章 类型

```
内置类型
            null            判断: !val && typeof val === 'object'
            undefined
            boolean
            number
            string
            object
            symbol

typeof 
        结果
            undefined
            boolean
            number
            string
            object
            symbol
            function
        特性
            typeof 检查的是值 (变量没有类型, 值才有 变量可以随时持有任何类型的值)
            typeof 总是返回一个字符串
            typeof 针对 undeclared(未声明) 返回 undefined (使用未声明变量会报 referenceError - 常用于避免抛出错误 或 判断是否polyfill (也可以用 window.xxx) )

函数
        特性
            object 子类型
            可调用对象 内部有属性 [[Call]] 使其可被调用
            依然可以拥有自己属性
                length 其声明参数个数
        判断    
            typeof fn === 'function'
        
数组
        特性
            object 子类型
            按数字顺序索引 length 是元素个数

undeclared
        特性
            直接使用会报错 referenceError   
                安全防范机制
                        typeof 针对 undeclared(未声明) 返回 undefined (常用于避免抛出错误 或 判断是否polyfill)
                        window.xxx 

依赖注入
        依赖项通过参数传递进去
```

### 第二章 值

```
null        空值 曾赋值过 目前没值
            不能作为标识符
undefined   从未赋值过
            (非严)可以作为标识符 (所以会造成 undefined 不安全-void 0获取) (严)作为标识符会typeError
            void 运算符 返回都是 undefined (惯例 void 0)

数组
        特性
            可以容纳任何类型值 
            声明后可加入值不需要预先设定大小
            delete元素安抚可以删除 (但 length 不变 - 会变成稀疏数组)
            也是对象可以包含字符串属性(不在长度计算范围内 若属性名可强制转为数字会当数字索引 改变长度)
        稀疏数组 (含有空白或空缺单元的数组)
        类数组 (数字索引的列表 但不是数组)
            实例
                    DOM查询DOM元素列表
                    arguments对象
                    字符串
            转换为数组
                    Array.prototype.slice.call(arguments)
                    Array.from(arguments)

字符串
        特性
            类数组 有 length 以及 indexOf() concat()
            字符串不可变(数组可变)
        常用
            获取字符    
                        a[1] (老IE不可用)
                        a.chartAt(1) 
            字符串反转
                        Array.prototype.reverse.call(a) (只适合简单字符串-unicode不适用)
            浅复制
                        a.slice()

数字
        特性
            基于 IEEE 754 标准实现(即浮点数) 双精度格式(即64位二进制)
            . 优先被视为 数字常量一部分
            基本类型值不可更改(即便封装成对象)
        特殊值
            NaN                     表达不是一个数字
                                    NaN != NaN (唯一一个非自反)
                                    检查
                                            isNaN()         检查是否不是NaN 也不是数字 (非数字也为 true)
                                            Number.isNaN()  是否为 NaN
                                            n !== n         (NaN 是唯一自反)
            Infinity
                                    1 / 0 == Infinity
                                    Infinity / Infinity => NaN
                                    5 / Infinity => 0
                                    数学运算超出范围
                                    溢出为无穷数无法再得到有穷数
            -0
                                    0 / -3
                                    0 * -3
                                    加减无法得到 -0
                                    字符串化得到 '0' (字符串转数字可以得到 -0)
                                    0 === -0 => true
                                    意义:   表示方向
                                    判断
                                            Object.is(a, -0)    可以分清 +0 和 -0
        常用
            a.toPrecision(4)        显示有效位数 ( 小数部分四舍五入 正数位指数表示 ) => string
            a.toFixed(4)            保留多少位小数 (不足 0 补齐) => string
            Number.EPSILON          机器精度误差
            Number.MAX_VALUE        能呈现最大浮点数(308位)
            Number.MIN_VALUE        最小浮点数(小数点324位)
            Number.MAX_SAFE_INTEGER 安全呈现最大整数(53位)
            Number.MIN_SAFE_INTEGER 安全呈现最小整数
            Number.isInteger(a)     检查是否整数()
            Number.isSafeInteger(a) 检查是否安全整数
        位操作 (32位)
            a | 0                   数值转为 32 位有符号整数 (超出会忽略 NaN / Infinity 会转为 +0)

值和引用
        基本类型值       
            通过复制的方式赋值和传递 null, undefined , string, number, boolean, symbol
        复合值
            通过引用复制方式赋值和传递
```

```js
/** 数字 */
var a = .42 // 0.42 可省略 0
var b = 42. // 42.0 可省略 0

var c = 1E3 // 指数表达 1 * 10 ^ 3
var d = 0xf3 // 十六进制 0X 开头也是
var e = 0363 // 八进制 (ES6开始 严格模式不再支持)
var f = 0o363 // 八进制 (ES6开始)
var g = 0b110 // 二进制 (ES6开始)

42.toFixed(3)   // syntaxError
(42).toFixed(3) // 有效语法
0.42.toFixed(3) // 有效语法
42..toFixed(3) // 有效语法
42 .toFixed(3) // 有效语法

a.toPrecision(4)    // 显示有效位数 ( 小数部分四舍五入 正数位指数表示 )

// NaN
NaN | 0 == 0

// Infinity
1 /0 == Infinity
Number.MAX_VALUE + Math.pow(2, 969) == Number.MAX_VALUE // true (超出处理范围 采用就近取整)
Number.MAX_VALUE + Math.pow(2, 970) == Infinity // (超出处理范围 采用就近取整)

// -0
0 / -3 == -0
0 * -3 == -0
+'-0' == -0
Number('-0') == -0
JSON.parse('-0') == -0
-0 == 0 // true
-0 === 0 // true
Object.is(0, -0)    // false (唯一判断 -0 的方法)

// 0.1+0.2
0.1 + 0.2 == 0.3 // false (机器精度误差 - 2 ^ -52 记录为 Number.EPSILON)
// 如何正确判断 在机器精度误差范围内即可
function numbersCloseEnoughToEqual(a, b){
    return Math.abs(a - b) < Number.EPSILON
}
numbersCloseEnoughToEqual(0.1 + 0.2, 0.3)

// isInteger polyfill
if (!Number.isInteger) {
 Number.isInteger = function(num) {
 return typeof num == "number" && num % 1 == 0;
 };
}

// isSafeInteger polyfill
if (!Number.isSafeInteger) {
 Number.isSafeInteger = function(num) {
 return Number.isInteger( num ) &&
 Math.abs( num ) <= Number.MAX_SAFE_INTEGER;
 }
}

// number.isNaN polyfill
if (!Number.isNaN) {
 Number.isNaN = function(n) {
 return (
 typeof n === "number" &&
 window.isNaN( n )
 ); 
 };
}

// Object.is() polyfill (可以判断 -0)
if (!Object.is) {
 Object.is = function(v1, v2) {
 // 判断是否是-0
 if (v1 === 0 && v2 === 0) {
 return 1 / v1 === 1 / v2;
 }
 // 判断是否是NaN
 if (v1 !== v1) {
 return v2 !== v2;
 }
 // 其他情况
 return v1 === v2;
 };
}
```

```js
function foo(x) {
    x = x + 1   // 这里 x + 1 时拆封了得到 3 , x = 时 把对象引用替换了 数字的基本类型引用 
    console.log(x) // 3
}

var a = 2
var b = new Number(a)

foo(b)
console.log(b) // 2 (即便传递的是引用副本, 也不能改变基本类型的值-基本类型值不可更改)
```

### 三章 原生函数

```
原生函数
        常用
                String()            new String() 封装基本类型 / String() 强制转换 
                Number()            new Number() 封装基本类型 / Number() 强制转换 
                Boolean()           new Boolean() 封装基本类型 / Boolean() 强制转换 
                Array()             new Array       带不带 new 都可以
                Object()            new Object()    没有必要使用 无法像字面量一样一次设置多个属性 
                                                    Object(val) 可以用来封装一个基本类型 (null, undefined 和 Object() 返回一个正常对象)
                Function()          new Function()  极少使用(动态定义函数参数和函数体时) 基本不会
                RegExp()            new RegExp()    语法复杂转义两个斜杠 偶尔有用(动态定义正则-正则中使用参数时)
                Date()              new Date()      原始构造函数 没有常量形式替代
                                    Date.now() ES5添加 原来需要 (new Date()).getTime()
                Error()             new Error()     带不带 new 都可以
                                    至少包含一个 message 属性
                Symbol()            不能带new 静态形式出现
                                    symbol之前用下划线开头表示私有和特殊属性
        类型判断
                typeof val
                val instanceof Fn   
                Object.prototype.toString.call(val)  => '[object String]' (内部的[[Class]]属性)

基本类型
        封装
            方法
                基本类型的方法 `new String(str)`
                `Object(str)` 同上
            影响
                基本值但提前封装反而会减低执行效率(浏览器已做过优化)
                会影响 typeof 判断
                会影响 真假性 `if(new Boolean(false))`
            拆封
                `obj.valueOf()` (需要用到封装对象基本类型值得地方会隐式拆封)

数组
        创建
            `val = []`
            `val = new Array(1,2,3)`    (可以不带 new 会自动补上效果一致)
                                        只带一个参数且是数字时 会预设长度 而非元素 (所以不太推荐new方法创建数组)
        稀疏数组    (至少包含一个空单元) 尽量不要使用
            创建稀疏
                `new Array(2)`
                `[,,]`
                `a=[]; a.length = 2`
                `a = [1]; delete a[0]`
            空元素 与 undefined
                join()      通过length属性值来遍历 空元素也算 undefined 也算
                map()       空元素不参与遍历 (但map返回结果与原数组长度一致)
        创建包含undefined单元数组
                Array.apply(null, {length: 3}) // [undefined, undefined, undefined]

原生原型
        特殊
            Function.prototype  空函数 ( 常用于作为函数的默认值 )
                                typeof => function
            Array.prototype     []
            RegExp.prototype    /(?:)/
                用途 用于默认值, 避免每次都需要创建且真正创建后(改引用)还需要垃圾回收 (但如果需要改内容 就不要使用)
        
        
```

```js
/** 基本类型 封装 影响 */
var a = new String('')       // 基本类型的封装对象 但提前封装反而会减低执行效率(浏览器已做过优化)
typeof a                            // 'object'
a instanceof String                 // true
Object.prototype.toString.call(a)   // '[object String]' 内部 [[Class]]
if(a){}                             // 会当做 truely 判断
```

```js
/** 稀疏数组 */
// 创建稀疏数组
new Array(2)    // => [empty * 2]
[,,]            // => [empty * 2] (注意 这里是两个逗号 ES5 规范开始运行列表末尾多加一个逗号)
var a = []; a.length = 2    // => [empty * 2]
var b = [1]; delete b[0]    // 删除后也为稀疏数组

// 非稀疏数组 undefined
Array.apply(null, {length: 3}) // [undefined, undefined, undefined]
```

### 四章 强制类型转换

```
值类型转换  (一种类型转换为另一种类型)
    隐式    (隐式强制类型转换)
            强制类型转换总是返回基本类型值(结果总是基本类型)
            JS 类型转换发生在运行时 (都是强制类型转换 分 隐式 显式) (其他语言 强制类型转化发生在运行时 类型转换发生在编译阶段)
    toString 
            显式方法
                String()
                obj.toString()
                a + b       两边有一个为对象或字符串时 (但会先调用 valueOf() 而 String() 则直接调用 toString() )
            其他类型
                null -> 'null'
                undefined -> 'undefined'
                true -> 'true'
                number -> number | 指数(极大极小)
                object -> object.toString()
                            对象    [object Object]
                            数组    toString改写过 (逗号连接后字符串化)
                            JSON.stringify() 也会用到 obj.toString()
    toNumber
            显式方法
                Number()
                `a - 0`  / `a * 1` / `a / 1`
                +a / -a     一元操作符 ( - 会发展符号位, 撤销翻转 使用 `- -'3.14'` 注意多加一个空格 因为 --会作为递减运算符)
                            +new Date               -> 时间戳 (用于旧版 Date.now() 的polyfill)
                            new Date().getTime()    -> 时间戳
                            Date.now()              -> 时间戳
                ~a          非运算符 (转换为32位整数 对字符位翻转 补码 == -(a+1) 使用 `~~a` 翻转回32位整数 )
                            特殊意义:   `if(str.indexOf('x') !== -1)` 可以简写为 `if(~str.indexOf('x'))` ( ~a 能够得到 0 的 a 值是 -1 即 ~-1 结果是 +0 )
                                        ~~a 常用来截除小数部分
                                                Math.floor() 是向下取整 (负数也是)
                                        ~~a 常用来解除为一32位整数
                                                a | 0 也可以做到 (优先级抵于 * /)
                parseInt()  / parseFloat()
                            参数是字符串 (非字符串隐式转换为字符串)
                            截断到非数字格式
                            (ES5之前 parseInt 没有第二个参数时 第一个参数0开头会转为八进制-将第二个参数设置为10即可)
                            意义:       常用于把其他进制字符串转为 10 进制
            其他类型
                true        -> 1
                false       -> 0
                undefined   -> NaN
                null        -> 0
                obj         -> valueOf() 若基本类型 再转数字 若不是 toString() 再转数字 若都不是基本类型 则 typeError
                                (Object.create(null) 即没有 valueOf 也没有 toString() 所以为 typeError )
                symbole     -> typeError symbol 无法转化为数字
                string          '' ' ' '\n' -> 0
    toBoolean
            显式方法
                Boolean()
                !a          一元运算符 (翻转  常用 !!a)
            假值判断    (其他转换都是 true 包括假值得封装对象)
                undefined
                null
                false
                +0 / -0
                NaN
                ""
                document.all    (经常用来判断是否旧版IE, 旧版IE为true)
            转换
                if()
                for(..;..;..) 第二个
                while()/do..while() 条件
                ?: 条件
                `||` `&&` 左边操作数
            特点
                注意 == 若两边不是同类型是先转换为 基本类型再判断 ( new Number(0) == false )
                而 > < 是直接判断是否为假值判断等同于 Boolean ( if(new Number(0)){} - 这里做了 true 判断 )
                Symbol 可以转换为 boolean 都是 true

逻辑运算符 
    ||      (返回两个操作数中间一个值)
            空值合并运算符
    &&      (返回两个操作数中间一个值)
            守护运算符
            短路操作

JSON
    stringify()
            特点
                    第二个参数 是 数组或函数 定义序列化过程需要处理的属性
                                        数组    需要处理的键
                                        函数    (key, val) => {}    (对象调一次 每个属性调一次, 若要忽略返回 undefined 否则返回 val 对 val 转义)
                    第三个参数是 指定输出缩进格式 (数字表示空格个数, 或字符串用于填充缩进)
            流程
                    若对象定义了 toJSON() 方法则调用得到返回值(安全的JSON)用返回值 序列化
                    若 字符串, 数字, boolean, null 则 与 toString 一致
            不安全的JSON    
                    undefined   (自动忽略 数组返回 null)
                    function    (自动忽略 数组返回 null)
                    symbol      (自动忽略 数组返回 null)
                    循环引用    报错
                解决办法
                    定义 toJSON() 转化为安全的JSON再序列化
                    第二个参数 是 数组或函数 定义序列化过程需要处理的属性

隐式强制转换
    流程
            1. 调用对象的 valueOf() -> 基本类型 -> 转换类型
            2.                      -> 非基本类型 -> toString() -> 字符串 -> 转换类型
    常见
            a + b
                    若任意一个是字符串 则为 拼接
                    否则按加法处理 两边都是 数字 null nudefined boolean
                    `{} + []` => 0 ( {} 作为代码块 +[] 作为一元操作符使用了 )
            a > b   
                    两边都为字符串 则为 比较字符编码
            a == b
                    两边都为字符串 则为 比较字符 且不会 null / undefined 否则 比较数字 (a , b 非同类型)
                    基本类型    封装会解封 ( null, undefined 与 原值不等 等同于 创建新对象 NaN 不等是因为 NaN != NaN )
                                解封是调用 valueOf() 所以 new Number(2) 但是修改 valueOf() 方法时可以得到其他值 
```

```js
/** 杂项 */
d = +new Date       // JS 构造函数没有参数时 可以不用带 () | 一元运算符 + 把对象转换为数字 (这里把 date.valueOf() )
parseInt(1/0, 19)   // => 18 parseInt(Infinity, 19) I 是 19 位之一 n 不是
parseInt(parseInt, 16) // 15 parseInt('function...', 16) f 是 16 位之一

~~null // 0
~~undefined // 0
~~Infinity // 0
~~NaN   // 0

!!new Boolean(false) // true
Boolean(new Boolean(false)) // true
new Boolean(false) == false // true

{} + [] // 0
[] + {} // [ojbect object]

[] == ![] // true
0 == [] // true
'' == [] // true
false == [] // true
'' == [null] // true
0 == '\n' // true 
```

```js
var a = {b: 42}
var b = {b: 43}

a < b // false
a == b // false
a > b // false

a<=b // true
a>=b // true
```

```js
// valueOf 修改解封值
Number.property.valueOf = function() {
    return 3
}
new Number(2) == 3 // true

// 同时等于2 且等于 3??
var i = 2
Number.prototype.valueOf = function() {
    return i++
}
var a = new Number(42)
if(a == 2 && a == 3){}
```

```js
/** 位运算 */
0 | -0          // 0
0 | NaN         // 0
0 | Infinity    // 0
0 | -Infinity   // 0
```

```js
/** 技巧 */

// indexOf 判断
if(~str.indexOf('x')){
    // str 中有 x 时的操作
}

// 如果仅有一个为true
function onlyOne() {
    var sum = 0
    for(var i=0; i< arguments.length, i++) {
        sum += Number(!!argument[i])
    }
    return sum === 1
}
```

### 五章 语法

```
语句    
    类型
        声明语句    `var a`
        赋值语句    `a = b`
        语句表达式  `b`
    特点
        语句都有一个结果值 
            var 的结果值是 undefined
            {} 代码块 结果值是最后一个语句结果
                        (语法不运行获取语句结果值并赋值给另一个变量 可以用 eval得到 a = eval('if(true){b=4+38}') // a 42 )
                        (es7 可以用 do 表达式获取到 a = do {if(true){b = 4 + 38}}  // a 42)
            ( , )  返回最后一个表达式的值
            delete 返回 boolean
        有副作用的表达式
            函数 (如果修改其他作用域的值)
            a++ / a-- 
                        ++a++ 会产生 referenceError
                        b = (a++) 不能得到 副作用后的值 除非在 ++ 之后再次运算 可以用 , 运算符 b = (a++, a)
            a = b  赋值也是副作用
                        var a = b = 42 会创建全局作用域的 b 
        JS 没有 else if (但 if 和 else 只包含单条语句时可省略 {} 所以实际上应该是 else{ if (){} } )

{} 语法
    在 RHS 中表示 对象
    在 语句开头 表示代码块 {foo:bar()} (这里合法是因为 foo: 当做标签语法了 (js 没有 goto) 但可以使用 continue break 跳转到标签)
                continue 只能用于循环
                break    可以用于非循环代码块
    {a} = b 解构语法(ES6)

JSON
    是JS语法一个子集 但不是合法的JS语法
    JSONP
                将 JSON 数据封装为函数调用 foo({"a":42})

运算符           
    运算符优先级
        , 永远最低
        `,` < `=` < `?:` < `||` < `&&`
    右关联
        ?:
        =
        
ASI(自动补齐分号)
    do {} while(a);
    break
    continue
    return
    yield
    
error
    syntaxError     
                42 = a  (赋值对象必须是一个标识符)
                function (a, a) {"use strict"} (参数重名 严格模式)
                {b:1, b:2} (对象属性重名)
    referenceError
                a = 2;let a; (TDZ 暂存死区 let 没有变量提升)
                function(a = 42, b = a + b){} (b 被提前使用 也是 TDZ)

函数参数
    不带参数
                有默认值使用默认值
                arguments 数组为空
    undefined   
                有默认值使用默认值
                arguments 数组会有 undefined 单元 而不是默认值
                (非严) arguments 在被传递参数时 会建立 形参 和 arguments 关联 (严格不会)
                
try...catch...finally
    try 可以和 catch 和 finally 配对使用
    finally
                总是最后执行 但一定会被调用 (但在整个语句完成之前)
                finally 抛出异常 外部函数会终止 抛弃掉 try 中 return 
                但 与 yield (generator暂停执行 但未结束) 不是立即执行 
                会覆盖 try catch 中的 return 

switch(a){case 2: ...}
    if..else 简化版
    特点
                a 和 case 匹配算法 和 === 相同 (a 和 case 其实做了一个 真假判断)
                case 会将表达式计算结果比较 (但任然是严格相等比较)
                default 是可选的 break 对 default 仍然适用  
                
```

```js
// 标签为foo的循环
foo: for (var i=0; i<4; i++) {
 for (var j=0; j<4; j++) {
 // 如果j和i相等，继续外层循环
 if (j == i) {
 // 跳转到foo的下一个循环
 continue foo; // 不是跳转到 foo 而是 foo循环的下一轮
 }
 // 跳过奇数结果
 if ((j * i) % 2 == 1) {
 // 继续内层循环（没有标签的）
 continue; 
 }
 console.log( i, j );
 }
}
// 1 0
// 2 0
// 2 1
// 3 0
// 3 2
```

```js
// 标签为foo的循环
foo: for (var i=0; i<4; i++) {
 for (var j=0; j<4; j++) {
 if ((i * j) >= 3) {
 console.log( "stopping!", i, j );
 break foo; // 内循环跳转到外循环 不是跳转到 foo 位置 而是 跳出 foo 所在循环块
 }
 console.log( i, j );
 }
}
// 0 0
// 0 1
// 0 2
// 0 3
// 1 0
// 1 1
// 1 2
// 停止！ 1 3
```

```js
// 标签为bar的代码块
function foo() {
 bar: {
 console.log( "Hello" );
 break bar; // break 用于非循环代码块
 console.log( "never runs" );
 }
 console.log( "World" );
}
foo();
// Hello
// World
```

```js
var a = 42;
var b = "foo";
var c = false;
var d = a && b || c ? c || b ? a : c && b : a;  // `?:` < `||` < `&&`
d; // 42

true || false && false; // true
(true || false) && false; // false
true || (false && false); // true

true ? false : true ? true : true // ?: 是右关联 等同  true ? false : (true ? true : true)

a = b = c = 42 // = 也是右关联 等同 a = b = c = 42
```

```js
function foo() {
     try {
        throw 42; 
     }
     finally {
        console.log( "Hello" );
     }
     console.log( "never runs" );
}
console.log( foo() );
// Hello (在异常捕获之前)
// Uncaught Exception: 42
```

```js
for (var i=0; i<10; i++) {
 try {
 continue; 
 }
 finally {
 console.log( i ); // 在 try 之后运行 但 continue 作用起效之前 运行 
 }
}
// 0 1 2 3 4 5 6 7 8 9 (注意有 0 是在 i++ 执行之前)
```

```js
function foo() {
     bar: {
         try {
            return 42;
         } 
         finally {
             // 跳出标签为bar的代码块
             break bar;
         }
     }
     console.log( "Crazy" )
     return "Hello";
}
console.log( foo() );
// Crazy
// Hello
```

```js
var a = 10;
switch (a) {
     case 1:
     case 2:
     // 永远执行不到这里
     default:
        console.log( "default" );
     case 3:
         console.log( "3" );
         break;
     case 4:
        console.log( "4" );
}
// default
// 3
```

### 附录

```
混合环境 js
    浏览器常见差异
            1. 非严格模式 允许 八进制 常量存在 (0123 => 83)
            2. window.escape() window.unescape() 转移和回转 %分隔符的十六进制字符串
            3. String.prototype.substr (第二个参数是结束索引不含) String.prototype.substring (第二个参数是长度)   
    保留字也不能用来作为对象常量中的属性名称或者键值(ES5之前 - 现在可以 但不能作为 标识符 且不推荐)

浏览器扩展
    RegExp 扩展
        RegExp.$1...$9 
        RegExp.lastMatch / RegExp['$&']
    Function 扩展
        Function.prototype.arguments
        Function.caller

宿主对象
    行为差异
        无法访问正常的 object 内建方法 toStirng() 等
        无法写覆盖
        包含预定义的只读属性
        包含无法将 this 重载为其他对象的方法
        console 由宿主对象提供 (浏览器输出到控制台 node 输出到 stdout stderr)

全局DOM变量
    创建带有ID属性的DOM元素也会创建同名全局变量
    不要扩展原生方法 即便扩展 加入判断条件
    按规范 polyfill
    
<script>
    外部引入文件 和 内联 JS 共享 window 命名空间运行 并相交 但全局变量作用域提升机制边界不适用
    如果 发生错误 独立的文件或内联会停止 但其他 代码会接着运行 不受影响
    在内联代码中不可以出现 </script> 字符串
    内联代码的 script 标签没有 charset 属性
    
现实限制
    字符串常量中允许的最大字符数
    可以作为参数传递到函数的数据大小
    函数声明参数个数
    未经优化的调用栈最大层数 函数调用链最大长度
    JS 程序阻塞最长运行时间
    变量名最大长度
```

```js
/** 动态加载 scrpit */
var greeting = "Hello World";
var el = document.createElement( "script" ); // el.src 的值设置为一个文件 URL 可加载外部文件
el.text = "function foo(){ alert( greeting );\
 } setTimeout( foo, 1000 );";
document.body.appendChild( el );
```

```html
<script>
// 用于不支持时 (已不再推荐)
<!--//--><![CDATA[//><!--
alert( "World" );
//--><!]]>
</script>
```

```
保留字的诗:  
Let this long package float,
Goto private class if short.
While protected with debugger case,
Continue volatile interface.
Instanceof super synchronized throw,
Extends final export throws.
Try import double enum?
-False, boolean, abstract function, 
Implements typeof transient break!
Void static, default do,
Switch int native new.
Else, delete null public var
In return for const, true, char 
...Finally catch byte
```

---------------------------------

## 第二部分 异步和性能

### 第一章 现在与将来

```
将来执行块(异步机制)
    必要性
        阻塞程序行为(并阻塞用户交互)
    解决办法
        回调函数
    其他示例
        console.log (并非立即输出 I/O有延迟 所以调试依赖 断点 不要依赖 console 或 输出 JSON.stringify 快照)
        setTimeout (完成后加入循环队列等待事件循环加入调用栈要等待前面任务完成所以精度不高)
    异步机制
        事件循环        监听 事件循环队列 和 调用栈 当调用栈完毕之后 把 事件循环队列 第一个任务加入到调用栈
        事件循环队列    setTimeout...
        任务队列        挂载事件循环队列每个任务之后的一个队列 promise...
        调用栈

并行
    进程
        独立运行 可能同时运行
    线程
        独立运行 可能同时运行
        多个线程共享单个进程的内存
    js
        事件循环
            异步事件交替调度
            并发的一种形式
        没有并行好处
            竞态条件 多个线程修改共同内存时相同方法运行事件差的问题可能得到不同结果 (js 从不跨线程共享数据)
```

### 第二章 回调

```
回调
        异步回调
                代码难以理解追踪调试和维护 (大脑是顺序的)
        嵌套回调
                同步异步 流程可能不一样 
                其他步骤无法复用
        执行块交给第三方 (控制反转 - 造成信任链断裂)
            调用过早
            调用过晚
            调用次数太少/太多
            参数未传递
            吞掉异常
        解决
            error-first (nodeJS 错误优先模式)
                第一个回调参数表示状态 (依然没有解决信任问题)
            增加判断
                代码臃肿
```

### 第三章 promise

```
未来值
        可能 成功 失败 未决议
        回调处理方式: 如果多个语句之间 都要添加判断其他完成状态
        promise: 封装了 状态 且改变后不可再修改
                一种封装和组合未来值易于复用的机制
完成事件
        回调:       提供回调函数 完成后调用 (反控制)
        侦听:       提供一个方法 接收完成后通知 (反控制反转)
        promise:    提供了 resolve  reject 来接收完成通知 

promise
        兼容
                    旧版 promise 实现策略不一样但都实现了 then (用 鸭子辩证法判断是否 promise)
        信任
                    调用过早: 即便已决议也是异步的
                    调用过晚: 决议后 then 注册回调有会依次调用 (两个独立的promise链顺序无法预测)
                    调用次数: 没有任何东西能阻止 promise 决议后调用 且只能被决议一次
                    未传参数: promise 至多有一个决议值 ( resolve, reject 多个参数会忽略)
                    吞噬异常: promise 会捕获异常
        链式流
                    每次 then 都是一个新的 promise
                    无论返回值是什么都会设置为 下一链的 完成值 (promise thenable 都是) 没有值会隐式 undefined
        最佳实践
                    总是以 cath 结束
        API
                    new Promise(fn)
                            必须提供一个函数回调 
                            函数调用是同步的
                    Promise.resolve()
                            也会展开 thenable 值 (有then方法的)
                    Promise.reject()
                    pro.then()
                    pro.catch()
                    pro.finally()
                            依然会返回一个新的 promise
                    Promise.all([])
                            如果传入空数组会立即完成
                    Promise.race([])
                            如果传入空数组会挂住永远不会决议
        其他
                    错误会顺着链一直向下传递直到捕获为止 但如果自身做了错误处理就不会
                    只能由一个完成值或拒绝理由 (单一值)
                    只能被决议一次
                    一旦创建但未决议没法从外部停止它的进程
        性能
                    相比裸回调会慢一些
```

```js
/**
    promise 各种变体的实现 ?
        none()  所有都拒绝才会拒绝
        any()   有一个成功 则 成功
        first() 
        last()  最后一个结果
*/
```

```js
/** 多值合并一个值 后 解构 (非解构语法) */
function spread(fn) {
    return Function.apply.bind(fn, null)
}

Promise.all( foo(10, 20) )
    .then(spread(function(x, y){
        console.log(x, y)
    }))
```

```js
/** ajax 转换为 promise 方式 处理回调 */
// 原代码
function foo(x, y, cb) {
    ajax(`url?x=${x}&y=${y}`, cb)
}

foo(1,2, function(err, text){
    if(err) {} else {}
})

// 改造
if(!Promise.wrap){
    Promise.wrap = function(fn) {
        return function () {
            var args = [].slice.call(arguments) // 把 类数组转为数组
            return new Promise(function(resolve, reject){
                fn.apply(null, args.concat(function(err, v){
                    if(err) {reject(err)}
                    else {resolve(v)}
                }))
            })
        }
    }
}

var request = Promise.wrap(ajax)

request(url)
    .then(val => {})
    .catch(err => {})
```

### 第四章 生成器

```
回调
        缺陷    
                不符合大脑对任务步骤规划方式
                控制反转回调不可信任不可组合

promise
        优势
                反转了控制反转 恢复可信任可组合性
                
generator
        优势
                看似同步的异步流程控制风格
        特点
                打破完整运行 可以一次或多次启动或停止 不一定非得完成
                yield 相当于断点 next() 相当于继续执行下一段 
                yield / next() 相互传值 但 next() 第一个没有参数
                可以构造多个迭代器实例 且相互独立 
                可以通过 yield 暂停 达到多个迭代器相互交替执行 
                可以安全迭代无限循环
        用处
                自定义对象 [Symbol.iterator] 用来实现 for...of 迭代
        next()
                返回值 是 {value: any, done: boolean}
        
迭代器
        特点
                generator 方法返回一个迭代器
                迭代器可以直接使用 for..of 遍历 (但不会传递参数 并且拿到的结果是解封后 value 直到 done 为 false为止 )
                不要用 for..of 遍历无限循环的迭代器 (可以使用 break 跳出)
        iterable
                包含 [Symbol.iterator]函数能返回一个 迭代器  具有 next() 方法
        停止
                break
                it.return('...') 
                抛出异常
        异常
                外向内抛出异常  it.throw('xxx')
                内向外抛出异常  throw new Error('xxx')

生成器+promise
        生成器
                主要把代码分段 执行
                yield 返回一个 promise ( `yield promise` )
        promise
                用 then 来调用 it.next()

生成器委托
        语法
                yield * foo() 把当前 迭代器 委托到 *foo() 的迭代器上 当 *foo() 完成后再回到 当前迭代器 
        特点
                不一定要转到另一个生成器 可以转到 iterable
                目的是组织代码 可以把任意多委托 按顺序连接在一起 
                异常也会被委托
                委托给自己 生成器递归

生成器并发
        特点
                迭代器可以相互调用 等待 暂停 组合
```

```js
/** generator */
function * foo (a) { // * 前后空格都可有可无
    let b = a + (yield 1);    // 只有暂停的 yield 才接受 值 (所以从 第二个 .next(val) 开始才接收值)
    let c = b + (yield 2);    //
    return b + c;           // 作为最后一个 next() 的返回值
}

var it = foo(10)          // iterator(迭代器) 只是构造了 迭代器 并没有执行 把 a 赋值 了 10

let x = it.next()         // 开始执行 到 yield 暂停 (首个next 从 函数开始运行不需要插值地方 - 如果传递会默默丢弃) x = { value: 1, done: false}
let y = it.next(20)       // 从上个 暂停地方开始 (并把参数作为上个yield语法的结果) 到下一个 yield 暂停 y = { value: 2, done: false }
let ret = it.next(30)     // 最后一段 从 return 结果 (函数都有默认 return 返回 undefined) ret = {value: 90, done: true}

// 可以直接用 for..of 遍历迭代器 (只是无法传值)
for(let r of it){
    console.log(r)
}
```

```js
/** 自定义的标准的迭代器接口 */
var something = (function(){ 
 var nextVal; 
 return { 
 // for..of循环需要
 [Symbol.iterator]: function(){ return this; }, 
 // 标准迭代器接口方法
 next: function(){ 
 if (nextVal === undefined) { 
 nextVal = 1; 
 } 
 else { 
 nextVal = (3 * nextVal) + 6; 
 } 
 return { done:false, value:nextVal }; 
 } 
 }; 
})(); 
```

```js
/** 迭代器 外部终止 */
function * something(){
    try {
        var nextVal
        while(true) {
            if(nextVal === undefined) {
                nextVal = 1
            }else{
                nextVal = (3 * nextVal) + 6
            }
            yield nextVal
        }
    }
    finally {
        console.log('cleaning up')
    }
}

var it  = something()
for(var v of it) {
    console.log(v)
    if(v > 500) {
        // 都会调用 finally
        // break    // 这里可以退出
        console.log(it.return('hello world').value) // it.return() 也可以退出 把 done 设置为 false
    }
}
```

```js
// 使用回调异步
function foo(cb) {
    setTimeout(() => {
        cb('ret 1')
    }, 1000)
}

foo(val=> {
    console.log(val)
})

/** generator 实现异步流程控制 */
let it

function foo() {
    setTimeout(()=>{
        it.next('ret 1')
    },1000)
}

function * main () {
    try {
        var text = yield foo() // 这里就可以像同步一样使用了
        console.log(text)
    }catch(err) { // try catch 无法捕获异步异常, 但 yield 暂停了执行 也可以捕获了 
        console.log(err)
    }
}

it = main()
it.next()

/** generator + promise */
function foo() {
    return new Promise((resolve, reject) =>{
        setTimeout(()=>{
            resolve('ret 1')
        }, 1000)
    })
}

var it = main()
var p = it.next().value
p.then(text => {
    it.next(text)
}).catch(err => {
    it.throw(err)
})
```

```js
/** 遍历异步 (利用 promise + generator) - async /await 替代方案 */
function run(gen) {
    var args = [].slice.call(arguments, 1), it;
    it = gen.apply(this, args)
    
    return Promise.resolve()
        .hten(function handleNext(value){
            var next = it.next(value)
            
            return (function handleResult(next){
                if(next.done) {
                    return next.value
                } else {
                    return Promise.resolve(next.value)
                        .then(handleNext, function handleErr(err){
                            return Promise.resolve(it.throw(err))
                                .then(handleResult)
                        })
                }
            })(next)
        })
}

// 使用
run(main) // 使 main 中代码按 同步方式运行
```

```js
/** 生成器委托 */
function * foo () {
    console.log('foo start')
    yield 1
    yield 2
    yield 3
    console.log('foo end')
    return 4    // return 会把 done 设置为 false 4 为 最后的值 但 for of 不会遍历 到这个最终值
}

function * bar () {
    console.log('bar start')
    yield 'a'
    yield 'b'
    yield * foo()
    yield 'c'
    console.log('bar end')
    return 'd'
}

let it = bar()
for(let i of it){
    console.log(i) // a b 1 2 3 c 
}
```

```js
/** 生成器委托 异常 */
function *foo() { 
 try { 
 yield "B"; 
 } 
 catch (err) { 
 console.log( "error caught inside *foo():", err ); 
 } 
 yield "C"; 
 throw "D"; 
} 

function *bar() { 
 yield "A"; 
 try { 
 yield *foo(); 
 } 
 catch (err) { 
 console.log( "error caught inside *bar():", err ); 
 } 
 yield "E"; 
 yield *baz(); 
 // 注：不会到达这里！
 yield "G"; 
} 

function *baz() { 
 throw "F"; 
} 

var it = bar(); 
console.log( "outside:", it.next().value ); 
// outside: A 
console.log( "outside:", it.next( 1 ).value ); 
// outside: B 
console.log( "outside:", it.throw( 2 ).value ); 
// error caught inside *foo(): 2 
// outside: C 
console.log( "outside:", it.next( 3 ).value ); 
// error caught inside *bar(): D 
// outside: E 
try { 
 console.log( "outside:", it.next( 4 ).value ); 
} 
catch (err) { 
 console.log( "error caught outside:", err ); 
} 
// error caught outside: F 
```

### 第五章 程序性能

```
web worker
        特性
                独立线程
                统一文件创建多个Worker 都是各自独立的 worker
                可以访问 ajax, websockets, 定时器
                可以访问 web 的 navigator, location, json, applicationCache 副本
        消息
            数据
                序列化字符串 (双倍内存)
                结构化克隆算法 (双倍内存 但 可处理循环引用)
                transferable 对象 (数据权限转移-原线程不可用)
                            uint8Array 类型数组就是
            api
                (web) w1.addEventListener('message', (evt) => {})
                (web) w1.postMessage('msg') 
                (worker)  addEventListener('message', (evt) => {})
                (worker)  postMessage('msg')   
        应用
                密集型数学计算
                大数据排序
                数据处理(压缩,音频分析, 图形处理)
                高流量网络通信
        共享的worker
                `new SharedWorker('xxx.js')` 整个站点都可以共享的 worker
```

```js
/** web worker 使用 */
var w1 = new Worker('xxx.js') // 这个JS 奖杯加载到独立线程

// web 线程中
w1.addEventListener('message', function(evt){ // 消息接收: worker -> web
    ...
})

w1.postMessage('msg') // 消息发送: web -> worker

w1.terminate() // 关闭 worker

// worker 线程中
importScripts('xx.js','xx.js')  // 加载额外的脚本(同步-加载完再执行下面脚本)

addEventListener('message', function(evt) { // 消息接收: web -> worker
    ...
})

postMessage('msg') // 消息发送: worker -> web
```

```js
/** shared worker 使用 */
var w1 = new SharedWorker('xx.js')

// web 中
w1.port.start() // 初始化链接端口
w1.port.addEventListener('message', hadleMessages)
w1.port.postMessage('xxx')

// worker 中
addEventListener('connect', function (evt){
    var port = evt.ports[0]
    
    port.addEventListener('message', function(evt) {
        port.postMessage('')
    })
    
    port.start()
})
```

### 第六章 性能测试与调优

```
性能测试
        受环境影响 
测试
    Benchmark.js
        测试代码性能库 (需要不同环境测试)
        
引擎的优化
        引擎会针对一些场景做优化处理
        所以不需要执迷于微观性能 更应该写出语义明确的代码
        过早优化是万恶之源

尾调用优化
        函数结尾处的其他函数调用 (这个调用结束后没有其余事情 除了返回结果)
    原理
        调用新函数需要额外的内存来管理栈 但尾调用则可以使用旧函数已有的栈 速度更快 节省内存
```

```js
// 之前使用方法 
var start = new Date().getTime() // 获取时间戳也会有延误
... // 每次运行时间都不稳定
var end = (new Date()).getTime()
console.log(end - start)    // 浏览器精度不够 IE 至少运行 15ms 以上才不会是 0ms
```

```js
var x = '42'
var y = x /2
var y = parseInt(x, 0) / 2 // 最慢
var y = Number(x) / 2 // 其次慢 需要函数调用机制
var y = +x / 2
var y = (x | 0) / 2
```

```js
function bar () {
    return 1 + foo()    // 非尾调用 还有计算
}
```