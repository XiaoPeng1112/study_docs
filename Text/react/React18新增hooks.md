# React 18 新提供Hooks

1. useId（React 18 新提供Hooks）
    - React 保证 id 在服务端和客户端一致

2. 解释一下 React 18 的并发特性
    - React 18之前的渲染是同步、阻塞式的，React 18 并发渲染过程是可中断、可调度的，提升复杂程度操作下的用户体验。

3. useTransition（标记不那么紧急的更新）
    - 核心：控制状态更新的“时机”或“优先级”，避免阻塞高优先级交互。
    - 允许将状态更新标记为“过渡（Transition）”，降低其优先级。
    - 返回 isPending（布尔值，表示过渡是否待处理）和 startTransition（函数，用于包裹低优先级状态更新）
    ```
    <!-- 使用场景：搜索大型列表 -->
    import React, { useState, useTransition } from 'react';
    function App(){
        const [inputValue, setInputValue] useState(''); //管理输入框的值
        const [searchTerm, setSearchTerm] useState(''); //保存搜索的关键字
        const [isPending, startTransition] useTransition();

        const handleInputValue = (e) => {
            setInputValue(e.target.value); //高优先级
            startTransition(()=>{
                setSearchTerm(e.target.value); //低优先级
            });
        };

        return (
            <div>
                <input type='text' value={inputValue} onChange={handleInputValue} />
                { isPending && <p> 加载中...</p>}
                <!-- 展示与搜索相关的内容 -->
                {/* <MyList searchTerm={searchTerm} /> */}
            </div>
        )
    }
    export default App;
    ```

4. useDeferredValue（获取一个延迟的值）
    - 核心：提供一个值的“副本”，此副本的更新被推迟，以避免阻塞主线程渲染。
    - 仅返回一个延迟后的值
    ```
    <!-- 外部数据源的图表/可视化接收频繁更新的props，导致重绘耗时，引发卡顿 -->
    import React, { useState, useDeferredValue } from 'react';
    function SlowListComponent({ text }) {
        const deferredText = useDeferredValue(text);
        // 基于 deferredText 进行耗时渲染
        return <div>显示内容：{deferredText}</div>;
    }
    function App() {
        const [text, setText] = useState('你好');
        const handleChange = (e) => {
            setText(e.target.value);
        };
        return (
            <div>
                <input type='text' value={text} onChange={handleChange} />
                <SlowListComponent text={text} />
            </div>
        );
    }
    export default App;
    ```

5. useTransition 和 useDeferredValue 的不同点
    - useTransition：允许包裹状态更新的逻辑（setState）。明确指定哪个更新是低优先级的。
    - useDeferredValue：允许包裹一个值（通常是props或派生状态）。关注的是值的延迟版本，而非更新过程。

6. 何时使用 useTransition 和 useDeferredValue
    - useTransition：当你能控制导致性能问题的状态更新代码，并希望明确标记这些更新为低优先级时。
    - useDeferredValue：当你无法直接控制值的更新源头（如props），但希望基于此值的组件渲染能延迟执行时。

7. React 并发：useTransition 与 useDeferredValue 解析
    - 解决UI卡顿，优化用户体验，面试热点：并发特性与性能优化

8. 使用场景：编辑器草稿与实时预览
    - 用户输入内容（高优先级），旁边的预览窗口根据输入实时渲染（低优先级）。
    - 两者皆有可能，取决于状态组织方式：
        - useTransition：若编辑器和预览内容是分别的状态，用它包裹更新预览内容的状态。
        - useDeferredValue：若预览组件直接接收编辑器内容做prop，用它延迟预览组件的渲染。

9. React.memo VS  并发特性的Hooks
    - 可以协同工作：例如：列表项组件用 memo 优化，整个列表的更新用 useTransition 调度。
    - React.memo：通过 props 比较，避免组件不必要的重新渲染。
    - useTransition/useDeferredValue：处理必要的但耗时过长的渲染，调度期‘时机’和‘优先级’。

10. useQuery
