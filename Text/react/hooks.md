# Hooks
1. useState 的函数式更新有什么好处？
    - 避免出现过时闭包的情况，可以拿到最新的state数据，保证数据的准确性。
    - 在批量操作、异步更新时优势明显

2. setState 执行的流程？同步还是异步？
    - “setState 并不是立即更新的，它会先放到更新队列里，React 会在合适的时机进行批量更新和渲染。在 React 事件和生命周期里是异步批量的，而在 setTimeout、原生事件这些环境下会是同步的。这样设计的目的是为了性能优化。”

3. useEffect 的执行时机？和 useLayoutEffect 的区别？
    - useEffect异步执行、浏览器渲染之后执行，useLayoutEffect是同步执行、浏览器渲染之前执行、可解决画面闪烁问题

4. useEffect 依赖项（每次渲染都会重新生成函数）
    - 不提供依赖项：effect会在每次组件渲染（包括首次渲染和所有更新）后执行
    - 提供一个空数组：effect 只会在组件首次加载（mount）后执行一次，其返回的清理函数会在组件卸载（unmount）时执行一次
    - 提供一个包含依赖项的数组：首次挂载后执行，后续的每一次渲染后，React会使用 Object.is() 算法去比较数组中每一个依赖项的当前值和上一次渲染时的值

5. useEffect 依赖项优化手段
    - 依赖项是函数：配合 useCallback，依赖项是复杂对象或数组配合 useMemo

6. useEffect 如何发起异步请求
    - 在useEffect内编写异步函数执行即可

7. useEffect 和竞态条件

8. useEffect 中的竞态条件
    - 核心问题：依赖项改变触发新异步请求，旧的异步请求过慢，导致UI显示过时数据。
    - 另一情况：组件卸载之前异步未完成，回掉更新状态导致警告或者内存泄漏。
    - 解决思路：识别忽略过时的结果，取消不再需要的异步操作。

9. useEffect 如何处理竞态条件
    - 解决方案：1、布尔变量标记模式：在useEffect内部维护一个布尔值初始条件为true，在异步回调中检查布尔值，为true才更新状态，在useEffect的清理函数中将该标记为false，阻止过时回调。
    - 2 AbortController API（针对fetch）（可以真正的终止请求）
    ```
    useEffect(()=>{
        const controller = new AbortController();
        const signal = controller.signal;
        fetch('/api/data',{ signal})
            .then(res => res.json())
            .then(data => {/* setState */})
            .catch(err => {
                if(err.name === 'AbortError') {
                    console.log('Fetch Aborted);
                }
            });
        return () => controller.abort();    
    },[dependency])
    ```

10. 描述一下 useContext 性能问题
    - 核心问题：Provider 的value更新，所有消费 useContext的组件均会被重新渲染，即便依赖数据未变。
    - 原因：useContext 订阅的是整个Context对象，React通过比较value的引用来判断变更。
    - 机制缺陷：不像 Redux useSelector 等允许订阅部分数据或进行更细致的比较

11. 如何优化 useContext 导致的性能问题？
    - 拆分Context，如用户认证、主题配置等
    - 使用 useMemo 包裹需要通过 Provider 传递给组件的value，确保 value的引用只在依赖项变更时进行更新

12. useState VS useReducer
 面试回答
    - 避免绝对化性能：useReducer 并非在所有情况下性能都优于 useState。
    - 区分 useReducer 与 Redux：useReducer 主要用于组件内部或有限跨组件共享，非全局状态管理方案。
    - 强调 useReducer 是处理组件内部复杂状态的利器。

13. useMemo 与 useCallback
    - useMemo：解决**值计算的重复开销**。
        - 缓存计算结果，依赖项发生改变才重新计算，否则返回缓存值。
        - 场景：
            - 复杂计算：比如大数据列表中的统计、筛选、排序。
            - 避免子组件重复渲染：把处理好的数据通过 props 传递给子组件时，确保引用不变。
    - useCallback：解决**函数引用的重复创建**。
        - 缓存函数本身（函数的引用）
        - 场景：
            - 传递回调给子组件：子组件用 React.memo 包裹，如果父组件每次 render 都生成新函数，子组件会白白重渲。
    - useCallback(fn,deps) 在功能上等价于 useMemo(() => fn, deps)，主要是为了语义上的清晰，表明正在记忆化一个函数。

14. useRef 是什么？主要的用途？
    - 调用 useRef(initialValue) 返回一个可变的ref对象，此对象有重要的属性：.current。
    - useRef 返回的ref对象在组件整个生命周期内保持不变，是持久的。
    - 核心：当修改myRef.current 的值，React不会触发组件的重新渲染。useState是会触发组件的重新渲染的
    - 用途：访问和操作DOM元素（获取焦点、触发动画、测量尺寸），持久化数据但不触发渲染（定时器、上次的props/state）

15. forwardRef 和 useImperativeHandle 是解决什么问题的？
    - forwardRef（高阶函数HOC） 解决父组件无法获取子组件DOM元素的问题，让父组件 ref 安全穿越函数组件边界（函数组件不提供实例），适用于焦点管理、动画控制或集成第三方库。
    - useImperativeHandle 避免暴露完整的子组件实例、控制父组件可以访问的方法，接收三个参数（ref、回掉函数、依赖数组）

16. 如何用 useContext + useReducer 实现一个轻量级的状态管理器？
    1. 表达：为何选择 useContext + useReducer？
        - 强强联合：
            - useReducer：集中管理状态逻辑，使状态变更可预测。
            - useContext：将 state 和 dispatch 函数全局注入，避免逐层传递。
        - 优势：
            - 轻量级：无需引入第三方库，React 内置支持。
            - 清晰：状态逻辑与UI分离。
            - 可维护性：对于中小型应用，比 Redux 等状态管理库更容易上手和维护。
            - 性能：useContext 本身可能导致不必要的重新渲染，但可通过 React.memo 或拆分 Context优化。
    2. 核心实现步骤
        1. 创建Context：用于共享 state 和 dispatch。
            - const MyContext = React.createContext();
        2. 定义 reducer 和 initialState：管理状态逻辑。
        3. 创建 Provider 组件：
            - 内部使用 useReducer 初始化 state 和 dispatch。
            - 通过 MyContext.Provider 将其作为 value 传递下去。
        4. 在组件中使用：通过 useContext 获取 state 和 dispatch。
    3. 代码示例