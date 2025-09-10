# React 哲学思想

- 若面试官追问技术细节（如“React的Diff算法”“RN的新架构”），需展示你对底层原理的理解，并结合项目中的实际应用。
    - ​示例（追问“React的Diff算法”）：​​
    - ​原理​​：React的Diff算法基于“同级比较”，通过key标识节点唯一性，减少不必要的重渲染。具体分为三步：
        1. 树比较：仅在同层级比较节点（跨层级会直接卸载重建）；
        2. 组件比较：若组件类型不同（如div→span），直接替换；若类型相同（如Button→Button），保留组件实例并更新props；
        3. 列表比较：通过key匹配新旧子节点，仅更新变化的节点（无key时按顺序比较，可能导致错误）。
    - ​实践​​：在银风科技的“抽奖列表”中，曾因未设置key导致列表更新时所有项重新渲染（FPS从60降至30）。添加唯一key（如lotteryItem.id）后，仅更新变化的项，FPS恢复至60。后续我还通过React.memo缓存列表项组件，进一步减少了重渲染次数（从每秒5次降至1次）。
- 说一下react中的 Fiber 架构
    1. 背景：
        - React 15 之前用的是 Stack Reconciler，也就是递归遍历 Virtual DOM。问题是
            - 渲染一旦开始，中途不能打断。
            - 如果组件树很大，会长时间占用主线程，导致页面卡顿（比如输入、动画卡住）。
        - 所以 React 16 引入了 Fiber 架构，核心目标是 可中断、可恢复的异步渲染。
    2. 什么是Fiber：
        - 可以理解为 React 自己实现的一套任务调度系统。
        - 每个 React 元素对应一个 Fiber 节点，本质是一个 JS 对象，里面记录了：
            - 组件类型（函数/类/HostComponent）
            - props、state
            - 父子兄弟关系（形成链表结构）
            - DOM 节点引用
            - 副作用标记（比如需要插入/更新/删除）
        - 这样 React 就能把整棵 Virtual DOM 转换成 Fiber 链表，方便拆分、打断和恢复。
    3. 总结
        - “React 16 引入了 Fiber 架构，本质上是把 Virtual DOM 转换成 Fiber 节点链表，让渲染变成可中断、可恢复的过程。它把渲染分成 Reconciliation 和 Commit 两个阶段，前者可中断，后者必须同步。这样 React 就能在合适的时机暂停渲染去响应用户输入，避免页面卡顿，也为后来的并发模式打下基础。”

1. 对于UI = f(state)的理解
    - “我理解的 UI = f(state)，就是界面是由状态计算出来的。以前要手动改 DOM，现在只要维护好数据，UI 会自动跟着变化，逻辑更直观，bug 也更少。”

2. 虚拟DOM（Virtual DOM）是什么？解决了哪些问题？
     - “虚拟 DOM 就是把页面结构先用 JS 对象表示，更新时先 diff 出差异，再去更新真实 DOM。这样能减少不必要的 DOM 操作，提高性能，也让 React 这种跨平台框架成为可能。”

3. Vite相比Webpack快在哪里？为什么新项目首选Vite？还有Umi？
    - “Vite 开发时不需要像 Webpack 那样把整个项目都打包一遍，它基于浏览器原生 ESM，按需编译，加上 esbuild 做依赖处理，所以启动特别快。新项目首选 Vite 基本是因为开发体验比 Webpack好太多。”
    - “Vite 是轻量级构建工具，启动快、热更新快，适合新项目和 H5/PC 项目。Umi 是企业级 React 框架，内置路由、权限和插件体系，适合中后台系统。项目里，如果是电商首页这种轻量模块，我会选择 Vite；如果是企业后台管理系统，我会用 Umi，省去大量框架搭建成本。”

4. 类组件和函数组件的本质区别？
    - 类组件提供可以实现（错误边界）捕获的函数
    - hooks 和闭包
    - 类组件：
        - 基于 class，通过 this.state 和生命周期方法管理状态。
        - 可以作为 错误边界（componentDidCatch）。
    - 函数组件：
        - 基于函数和 Hooks，用 useState/useEffect 管理状态和副作用。
        - 更轻量，没有 this，逻辑更清晰，复用性强。
    - 区别本质：
        - 类组件靠继承 + 生命周期管理状态；
        - 函数组件靠闭包 + Hooks 管理状态。
    - 口语化回答：
        - “类组件主要靠继承和生命周期来管理状态，还能作为错误边界；函数组件用 Hook 和闭包来管理状态，更简洁，逻辑复用也更方便，所以现在推荐用函数组件。

5. React 为什么强调 Props 不可改变？
    - 核心原因：
        1. 保证 单向数据流，父组件控制子组件，数据流动清晰。
        2. 提高可预测性：组件的输出只依赖于 props 和 state，不会被意外篡改。
        3. 方便优化：React 可以通过 shouldComponentUpdate 或 memo 判断 props 是否变化。
    - 口语化回答：
        - “Props 相当于父组件给子组件的参数，就像函数参数一样应该保持不可变。这样保证了单向数据流，逻辑更清晰，React 才能做性能优化。如果 props 能随意改，整个组件树的数据就会乱套。”

6. 如何理解组件的“单一职责”？
    - 目的：提高内聚性（Cohesion），降低耦合性（Coupling）。一个组件只做一件事。

7. SRP的重要性
    - 提高可维护性：修改一个功能，只影响到负责该功能的组件。
    - 增强可复用性：功能单一的组件更容易在不同场景下复用。
    - 代码更清晰：易于理解和团队协作。

8. React 中组合优于集成的原则
    - React 组件核心和UI单元，继承可能导致不直观的组件树，难以追踪props、state的来源和传递。
    - 通过继承复用组件逻辑，往往不如自定义Hooks或高阶组件HOCS清晰和灵活。官方文档明确推荐使用组合方式编写代码。

9. 组合在React中的实践
    - 通过 props 传递特定的行为或内容、props.children、高阶组件（函数接收一个组件，返回一个新的增强组件）。

10. 阐述“组合优于继承”
    - 核心观点：React 明确指出组合是实现代码复用和构建灵活组件结构的首选。
    - 原因：
        1、React组件本质上更像函数，组合更符合这种模型。
        2、组合提供了更好的灵活性，可维护性，降低了组件间的耦合度。
        3、避免了传统继承可能带来的层级过深，方法覆盖混乱的问题。

11. React 的错误边界（ErrorBoundaries）
    - 核心功能：捕获其子组件树中发生的JS错误。目的：记录这个错误，展示一个降级的UI，而不是让整个组件报错。
    - 核心方法：getDerivedStateFromError（更新state以渲染降级UI），componentDidCatch（记录错误）。
    - 重要性：提升用户体验，增强应用健壮性。
    - 无法捕获的情况：1、事件处理器（Event handlers）。2、异步代码（setTimeout）或requestAnimation回调。3、服务端渲染（Server side rendering）。4、错误边界自身抛出的错误（而非其子组件）

12. 设计一个组件库考量哪些方面？
    - 用户中心：开发者体验（DX）和最终用户体验（UX）并重。
    - 系统思维：不仅仅是单个组件，更是整体的解决方案。
    - 长期规划：考虑可扩展性、可维护性和社区生态。
    - 迭代进化：根据反馈和需求持续优化。

13. 谈谈你对React中原子设计（Atomic Design）的理解
    - 原子设计；是一种从基础到复杂的UI构建方法论。
    - 五个层级：原子（Atoms）、分子（Molecules）、组织（Organisms）、模板（Templates）、页面（Pages）
        - 原子（Atoms）：不可再拆分，如按钮、输入框、标签、图标、字体等。关注点：自身的状态和样式
        - 分子（Molecules）：由原子组合而成的功能单元，如搜索框（输入框原子 + 按钮原子）。关注点：原子之间的协作，构成一个有意义的整体。
        - 组织（Organisms）：由分子和原子组合而成的相对复杂的UI部分，构成一个独立的区域，如产品卡片列表（多个产品卡片分子）、侧边栏。关注点：不同分子和原子如何协同工作，形成一个功能区域。
        - 模板（Templates）：页面级别的骨架，关注内容布局，使用占位符来表示实际的内容，将组织及其他组件组合成一个完整的页面结构。关注点：内容的排布、组件的相对位置，例如产品列表页模板。
        - 页面（Pages）：模板的具体实例，用真实的动态内容替换掉模板中的占位符，用户最终看到的交互界面。
    - 原子设计的优缺点
        - 优点：高度模块化，易于管理和扩展，代码复用性高，清晰的职责分离；
        - 缺点：初期学习曲线和搭建成本可能较高

14. React 的状态提升与边界
    1. 场景驱动：兄弟组件间共享状态，子组件需要修改父组件或祖先组件的状态，保持多个视图的数据同步。
    2. React 核心原则：单向数据流（自项而下），唯一数据源（Single Source of Truth）。
    3. 缺点：
        - Prop Drilling（属性逐层传递）：
            - 状态可能需要通过许多中间层组件传递。
            - 中间组件被迫接收并传递其本身并不需要的的props。
        - 父组件膨胀：最近的共同父组件可能承载过多无关状态和逻辑，变得臃肿。
        - 潜在性能问题：父组件状态更新可能导致所有子组件（包括未使用该状态的中间组件）重新渲染，需要配合React.memo等hooks去优化
    4. 使用useContext + useReducer 可以避免组件树中深层次的props传递（Prop Drilling）的问题

15. 服务端状态 VS 客户端状态
    1. 核心区别：
        - 所有权：客户端拥有 VS 服务端拥有
        - 持久性：浏览器会话 VS 数据库持久存储
        - 同步性：通常同步 VS 必然异步
        - 控制权：UI直接控制 VS 通过API 间接控制
        - 复杂度：服务端状态管理通常更复杂（缓存、同步、过期、错误处理等）
    2. 什么时候该考虑使用SWR/React Query？
        - 频繁的异步数据交互：当应用大量依赖API获取和展示数据时。
        - 需要缓存和后台同步：希望提升性能，确保数据新鲜度。
        - 追求更好的用户体验：通过乐观更新、后台无感知刷新等。
    3. SWR/React Query
        - 简化服务端状态管理的利器，提供缓存、自动刷新、错误处理等强大功能。

16. React如何实现用户持久化存储？
    1. 使用 localStorage、useState、useEffect实现数据持久化
        ```
        import React, { useState, useEffect } from 'react'
        function PersistentCounter() {
            // 1. 初始化 state 时尝试从 localStorage 读取
            const [count, setCount] =useState(()=>{
             const saveCount = localStorage.getItem('myCounter');
             if(saveCount !== null){
                return JSON.parse(saveCount);//localStorage存储的是字符串
             }
             return 0; //默认值
            })
        }
        // 2. 在 count变化时将其存入localStorage
        useEffect(()=>{
            localStorage.setItem('myCounter',JSON.stringify(count))
        },[count])

        const increment = () => setCount(prevCount => prevCount + 1);
        const rest = () => setCount(0);

        return (
            <>
                <div onClick={increment}>count {count}</div>
                <div onClick={rest}></div>
            </>
        )
        ```

17. React 受控组件和非受控组件？
    - 受控组件：React State是唯一数据源，适用于需要实时控制的场景。
        - 动态校验、根据输入实时联动等（每次输入会导致重渲染）
    - 非受控组件：DOM是数据源，用useRef读取，适用于简单、一次性的场景。
        - 比如搜索框、一次性提交表单、第三方库封装的组件（）。

18. React 如何处理CSS的？
    - CSS Modules：不是一个库，独立的.css文件，是Vite/Webpack构建项目时的处理方案，生成唯一的类名。
    - CSS-in-js：用JS来编写和管理CSS。
        - 具体实现；通过库（styled-components）
        - 核心：在组件内部创建、附加和管理样式。
        ```
        const Title = styled.div`
            font-size:1.5em;
            text-align:center;
        `
        function Component() {
            return <Title>Hello World!</Title>
        }
        ```
        - 优点：组件级样式隔离，强大的动态样式能力（基于props），样式与组件逻辑高度内聚，无需.css文件，纯js环境。
        - 缺点：运行时开销大，CSS文件无法被浏览器缓存，对SSR（服务端渲染）支持不一。
    - Tailwind CSS：
        - 优点：
            - 极高的开发效率，不用在JS和CSS间切换
            - 样式约束性强，有利于团队协作
            - 无需担心命名问题，体积小
        - 缺点：
            - HTML（JSX）结构变的臃肿，可读性不高
            - 需要记忆大量的缩写类名
            - 样式和结构耦合
