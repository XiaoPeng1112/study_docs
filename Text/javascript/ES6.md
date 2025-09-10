
1. 新增特性
    - let/const块级作用域
    - 结构赋值
    - 模板字符串
    - 箭头函数
    - 展开运算符
    - Promise
    - 对象结构新增Set、Map

2. 全局对象
    - var：在全局作用域声明，会成为window的属性
    ```
    var globalVar = 'I am everywhere';
    console.log(window.globalVar) // I am everywhere
    ```
    - let/const：不会成为window的属性
    ```
    var globalLet = 'I am everywhere';
    console.log(window.globalLet) // undefined
    ```