# JavaScript

- 什是原型和原型链
    - 原型：函数都有prototype属性，称之为原型，也叫原型对象
        - 原型可以放一些属性和方法，共享给实例对象使用
        - 原型可以做继承
        ```
        const arr = new Array(1, 4, 3, 2)
        arr.sort() // 排序
        arr.reverse() //翻转
        ```
    - 原型链：对象都有_proto_属性，这个属性指向它的原型对象，原型对象也是对象，也有_proto_，指向原型对象的原型对象，这样一层一层形成的链式结构称为原型链，最顶层找不到则返回null

- 什么是继承
    - 继承的本质是：子对象可以访问父对象的属性和方法。
    - JS 继承有两种方式：
        - 原型继承：通过原型链访问父对象属性。
            - 每个 JavaScript 对象都有一个内部属性 [[Prototype]]（通常通过 __proto__ 或 Object.getPrototypeOf 访问）。
        - 类继承（ES6）：通过 extends 和 super() 更直观实现。
            - extends 表示继承父类。
            - super() 调用父类构造函数，必须在子类构造函数中先调用。
    - 优点：代码复用、逻辑组织清晰、支持多态（方法重写）。

1. 说说你对JavaScript作用域的理解？
    - **作用域简单说就是变量能“存活”的范围。** 比如在函数里声明的变量，只能在函数内部用（函数作用域）；如果在全局声明，整个文件都能用（全局作用域）。ES6之后有了块级作用域（用let/const声明），比如在if{}或for{}里的变量，出了这个块就不能用了。
    - **作用域链**的话，就是变量找的时候会往上找父级作用域，直到全局，找不到就报错。

2. 闭包是什么？有什么用？会导致什么问题？​
    - **闭包就是函数“记住”它外面变量的能力。**比如一个内部函数，即使跑到了外面执行，还能访问原来函数里的变量。
    - 场景：延迟执行（比如定时器里用外部变量）
    - 弊端：要是闭包一直不被垃圾回收，变量就永远占内存，可能导致内存泄漏。比如如果把变量counter存起来，一直不用但不销毁，count就一直占着内存。
    ```
    function outer() {
        let count = 0;
        return function inner() {
            count++; 
            console.log(count);
        };
        }
    const counter = outer();
    counter(); // 1
    counter(); // 2（因为inner记住了outer里的count）
    ```
3. 说说事件循环（Event Loop），宏任务和微任务的区别？​​
    - **事件循环就是JS处理任务的机制，**因为JS是单线程的，得排队干活。任务分两种：同步任务（立刻执行）和异步任务（等回调）。异步任务又分宏任务（比如setTimeout、script整体、DOM事件）和微任务（比如Promise.then、MutationObserver）。
    - 执行顺序：
        1. 先执行所有同步任务；
        2. 同步完，看微任务队列，一个个执行；
        3. 微任务清完，再执行一个宏任务；
        4. 宏任务执行完，再看微任务队列有没有新的，循环下去。
    ```
        console.log('1'); // 同步
        setTimeout(() => { 
        console.log('2'); // 宏任务
        }, 0);
        Promise.resolve().then(() => { 
        console.log('3'); // 微任务
        });
        console.log('4'); // 同步
        // 1 4 3 2
    ```

4. 用过Promise吗？说说它的作用和常用方法？​
    - 经常用 Promise 来处理异步，它的核心作用就是使用链式调用的方式，把回调写法变得更清晰，避免回调地狱。
    - Promise就是解决回调地狱的，把异步操作用同步的方式写。
    - 常用的方法：
        - new Promise((resolve, reject) => {}); 创建一个 Promise 实例，resolve是成功的回调，reject是失败的回调。
        - .then()：处理成功结果，.catch()：处理失败结果，.finally()：不管成功or失败都会执行。
        - Promise.all()：等所有的Promise都成功才返回结果（有一个失败就整体失败）。
        - Promise.race()：哪个Promise先完成（无论成功or失败）就返回它。
    - 一个例子
        比如要依次：

        1. 请求用户数据

        2. 再用用户 id 请求订单数据

        3. 再用订单 id 请求商品数据

        如果用回调函数来写，就会变成这样：
    ```
    <!-- 回调地狱 -->
    getUser((user) => {
        getOrders(user.id, (orders) => {
            getProducts(orders[0].id, (products) => {
            console.log('最终结果:', products);
            });
        });
    });
    ```
    ```
    <!-- Promise 可以把它改写成链式调用： -->
    getUser()
    .then(user => getOrders(user.id))
    .then(orders => getProducts(orders[0].id))
    .then(products => {
        console.log('最终结果:', products);
    })
    .catch(err => console.error(err));
    ```
5. 说说let、const和var的区别？​
    - var：函数作用域，会变量提升，可以重复声明（在声明前使用会是undefined）。
    - let:块级作用域（{}内有效），没有变量提升，不能重复声明（声明前使用会报错）。
    - const：和let类似，但声明时必须赋值，不能重新赋值（基本类型值不能变，对象/数组的属性可以改）。
    ```
    // var的问题
    console.log(a); // 不报错，输出undefined（变量提升）
    var a = 1;

    // let的块级作用域
    if (true) {
    let b = 2;
    }
    console.log(b); // 报错，b不在这个作用域

    // const的特性
    const obj = { name: '张三' };
    obj.name = '李四'; // 可以改属性
    obj = { name: '王五' }; // 报错，不能重新赋值整个对象
    ```

6. 如何实现一个深拷贝？要注意什么？​
    - 深拷贝就是复制一个对象，修改新对象不影响原对象。
    - 浅拷贝只复制第一层，深拷贝要复制所有层级的属性。
    - 简单实现可以用递归，或者用JSON.parse(JSON.stringify(obj))（但有局限性，比如函数、Date、RegExp这些类型会丢）。
        - JSON.parse(JSON.stringify(obj))
        - structuredClone()：首选现代Web API推荐
            - 不能拷贝函数和Error对象/DOM节点
    ```
    function deepClone(obj, map = new WeakMap()) {
        if (obj === null || typeof obj !== 'object') return obj; // 基本类型直接返回
        if (map.has(obj)) return map.get(obj); // 处理循环引用（比如obj.a = obj）
        // 处理特殊类型（比如Date、RegExp）
        if (obj instanceof Date) return new Date(obj);
        if (obj instanceof RegExp) return new RegExp(obj);
        const clone = Array.isArray(obj) ? [] : {};
        map.set(obj, clone); // 记录已拷贝的对象，防止循环引用
        for (let key in obj) {
            if (obj.hasOwnProperty(key)) { // 只拷贝自身属性，不拷贝原型链的
            clone[key] = deepClone(obj[key], map);
            }
        }
        return clone;
    }
    ```

    - 浅拷贝
        - Object.assign()
        ```
        const original = { a: 1, b: { c: 2}};
        const Copy = Object.assign({},original);
        original.a = 10;//修改第一层
        original.b.c = 20;//修改第二层

        console.log(original.a) // 1 (未受到影响)
        console.log(original.b.c) // 20（受到影响）
        ```
        - ... 扩展运算符
        ```
        const original = { a: 1, b: { c: 2}};
        const Copy = {...original};
        original.a = 10;//修改第一层
        original.b.c = 20;//修改第二层

        console.log(original.a) // 1 (未受到影响)
        console.log(original.b.c) // 20（受到影响）
        ```

7. this指向问题，在函数/对象方法/构造函数里有什么不同？​​
    - this的指向是“运行时”决定的，不是定义时，主要看调用方式：
    - 方式：
        1. 普通函数调用​​：this指向全局对象（浏览器是window，严格模式下是undefined）。
            - 例：function fn() { console.log(this); } fn(); // window
        2. 对象方法调用​​：this指向调用它的对象。
            - 例：const obj = { fn() { console.log(this); } }; obj.fn(); // obj
        3. 构造函数调用​​：this指向新创建的实例对象（用new调用时）。
            - 例：function User(name) { this.name = name; } const u = new User('张三'); // this是u
        4. 箭头函数​​：this继承自外层作用域（定义时的上下文）。
            - 例：const obj = { fn() { const arrow = () => console.log(this); arrow(); } }; obj.fn(); // obj（因为arrow在obj的fn里，继承了fn的this）

8. 数组的map、forEach、filter有什么区别？​
    - ​​forEach​​：遍历数组，没有返回值（或者说返回undefined），适合纯遍历操作（比如打印每个元素）。
        - 不能使用break打断（因为forEach接收的是一个函数），可以抛出异常进行强制打断，
    - map​​：遍历数组，返回一个新数组，新数组的元素是原数组每个元素执行回调后的结果。
        - 例：[1,2,3].map(item => item*2); // [2,4,6]
    - filter​​：遍历数组，返回一个新数组，新数组是原数组中满足回调条件（返回true）的元素。
        - 例：[1,2,3].filter(item => item>1); // [2,3]
    - 核心：
        - 分清可变性：splice修改原数组，slice/map/filter返回新数组。
        - map vs forEach：核心区别在于返回值和用途。
        - reduce：注意initialValue对启动行为和空数组的影响

9. 说说async/await和Promise的关系？​
    **async/await是基于Promise**
    - async/await是**Promise+Generators**的语法糖，让异步代码看起来像同步代码，更易读。
    - ​async函数​​：用async声明的函数，返回值一定是一个Promise（如果返回的不是Promise，会自动包装成Promise.resolve()）。
    - ​await​​：只能在async函数里用，它会等待右边的Promise解决（resolve），然后返回结果；如果Promise reject，会抛出错误（需要用try/catch捕获）。

    ```
    // 用Promise链式调用
    function fetchData() {
    return fetch('/api/data')
        .then(res => res.json())
        .then(data => data.list);
    }
    // 用async/await
    async function fetchData() {
        try {
            const res = await fetch('/api/data'); // 等待fetch完成（返回Promise）
            const data = await res.json(); // 等待res.json()完成（也是一个Promise）
            return data.list; // 返回的结果会被包装成Promise.resolve()
        } catch (err) {
            console.error('出错了', err);
        }
    }
    ```

10. 数据类型和typeof陷阱
    - 7种原始类型（string、number、boolean、null、undefined、symbol、bigint）
    - 1种对象类型 object（包含Array、Function等）
    ```
    //类型判断
    <!-- 1. 如何判断null？ -->
    const number = 1
    console.log(number === null) // false
    console.log(typeof number); // object
    <!-- 2. 如何判断数组 Array？ -->
    const arr = []
    console.log(Array.isArray(arr)); // true
    console.log(typeof arr); // object
    ```
    - Object.prototype.toString.call()：是最精确的类型检查方法。

11. 高阶函数和柯里化


12. 节流与防抖
    - 节流
        - 固定频率执行
        - 保证一定时间内至少执行一次
    - 防抖
        - 事件停止后执行（从新开始）
        - 保证一段空闲时间后只执行一次
    ```
    <!-- 节流 -->
    function throttle(fn, interval = 300) {
        let last = 0;
        return function (...args: any) {
            const now = Date.now();
            if (now - last > interval) {
                last = now;
                fn.apply(this, args);
            }
        };
    }
    <!-- 防抖 -->
    function debounce(fn, delay = 300) {
        let timer: any;
        return function (...args: any) {
            clearTimeout(timer);
            timer = setTimeout(() => fn.apply(this, args), delay);
        };
    }
    ```

13. bind/call/apply
    - 是Function.prototype上的方法，用于显示指定函数执行时的this值


14. 数组扁平化
    ```
    const flat = (arr: any[]) =>
         arr.reduce((acc, val) => 
            acc.concat(Array.isArray(val) ? flat(val) : val)
        ,[]);
    ```
