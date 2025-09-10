
1. 类型推断
    ```
    let num = 10
    num = '10' // 会类型报错
    ```

2. 类型注解
    ```
    let num: number = 10
    num = 100 // 类型正常
    ```

3. 类型断言
    ```
    let numArr = [1, 2, 3];
    const result = numArr.find(item => item > 2) as number // 确保numArr里面是number类型
    console.log(result * 5) // 15 正常打印
    ```

4. 基础类型和联合类型
    ```
    <!-- 基础类型 -->
    let v1: string = 'abc'
    let v2: number = 10
    let v3: boolean = true
    let v4: null = null
    let v5: undefined = undefined

    <!-- 联合类型 -->
    let v6: string | null = null
    let v7: 1 | 2 | 3 = 2 //类型验证正确
    let v7: 1 | 2 | 3 = 5 //类型验证错误
    ```

5. 数组、元组、枚举
    ```
    <!-- 数组 -->
    let arr: number[] = [1, 2, 3];
    let arr1: Array<string> = ['1', '2', '3'];

    <!-- 元组 -->
    let t1: [number, string, number] = [1, '2', 3]
    t1[0] = 'a' //报错
    let t1: [number, string, number?] = [1, '2']

    <!-- 枚举 -->
    enum MyEnum {
        A,
        B,
        C
    }
    console.log(MyEnum.A)
    console.log(MyEnum[0])
    ```

6. 函数
    ```
    function MyFn(a = 10, b:string, c?: boolean, ...rest: number[]): number {
        return 100
    }
    const f = MyFn(20, 'a', true, 1, 2, 3)
    ```

7. 接口 interface
    ```
    interface ObjType {
        name: string,
        age: number
    }
    const obj: ObjType = {
        name: 'bruce',
        age: 18
    }
    ```

8. 类型别名 type
    ```
    type UserName = string | number
    let Name: UserName = 'bruce'
    ```

9. 泛型 <>
    ```
    function myFn<T>(a: T, b: T): T[] {
        return [a, b]
    }
    myFn<number>(1, 2)
    myFn<string>('a', 'b')
    myFn('a', 'b')
    myFn('a', 1) //报错，会按第一次推断的值进行验证
    ```