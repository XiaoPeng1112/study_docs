# 面试记录

1. ​“你用React做C端项目时，遇到过最大的性能问题是什么？怎么解决的？”​​
    - 参考回答：“之前做商品列表页时，滑动卡顿明显（FPS只有40左右）。通过Chrome DevTools的Performance面板分析，发现是render函数中做了大量数据格式化（如时间戳转日期），导致重复计算。解决方案是用useMemo缓存格式化后的数据，配合React.memo缓存列表项组件，最终FPS提升到60，首屏加载时间减少30%。”

2. ​“React和RN的核心区别是什么？你在项目中如何利用它们的共性？”​​
    - 参考回答：“核心区别在于运行环境（浏览器vs原生容器）和渲染方式（DOM vs 原生组件）。共性是都基于React的组件化思想，状态管理和Hooks可复用。例如我在RN项目中封装了通用的fetch钩子，和Web端的请求逻辑完全一致，减少了重复编码；还共享了商品详情页的样式变量（如主色调、字体大小），保证了双端视觉一致性。”

3. ​“如果需求紧急，产品设计不清晰，你会如何处理？”​​
    - 参考回答：“首先主动对齐需求（拉产品/设计开短会），明确核心目标和优先级（如‘大促期间先保证功能可用，细节后续优化’）；然后用低保真原型快速验证（如用Figma画草图，和产品确认交互流程）；开发时预留扩展点（如用配置化方式控制按钮样式），降低后期修改成本。例如之前做一个限时活动页，设计稿延迟交付，我用临时占位图+注释的方式先开发框架，设计稿出来后2小时内完成了样式替换，保证了上线节点。”

4. ​“你如何保持前端技术的学习？”​​
    - 参考回答：“定期关注技术社区（如掘金、GitHub Trending），重点学习与业务相关的技术（如最近在研究React Server Components对C端首屏的影响）；参与技术分享（曾在团队内分享《React性能优化实战》），输出文档巩固知识；遇到问题时深入源码（如之前为了优化Redux性能，阅读了reselect的源码，理解了记忆化实现的原理）。”

- 2025年8月20日 下午三点 阿里 AI Business
    - 高德：使用高德的什么定位方式？    
    - ES分词搜索
        1. 之前APP的搜索是怎么做的？
        2. 对ES分词的原理有了解吗？
        3. 对于这块前端做的输入防抖和请求取消是什么？请求是不发还是中途取消
        4. 你知道network还可以发送之后再取消请求吗？
    - 埋点数据采集
        1. 这个是你自己做的？还是说在业务里面调用一些API
    - PWA功能
        1. 引入这个技术是为了什么？
    - RN
        1. 你是一直在用RN吗
        2. 你们RN是使用的Expo构建项目还是使用的原生框架？
        3. 你在RN开发过程中遇到最麻烦或者最有挑战性的地方？相对比开发Web，让你开发效率变低，开发的太不舒服
            - 高耦合代码、兼容性、库版本升级、安卓手机兼容、模拟器红屏
            - 怎么解决的？
                - Stack Overflow
                - 去开源的网站查询
    - 有没有使用AI Coding工具来编写代码或排错
        1. 输入自然语言进行上下文编写代码？
    - 基础问题
        1. TS的type和interface？
        2. JS的作用域和作用域链、包括闭包之类的概念？
        3. http和https协议？
        4. 你之前学习的计算机网络，说一下整个网络的架构以及怎么分层
        5. 你现在对于底层网络架构的构建能力可能不足，那现在和你前端相关的网络
        6. HPPTS怎么防止窃听和篡改数据有了解吗？
        7. 对于一些http跨域、缓存简单说一下，跨域的一个特性
        8. 怎么定义域名是同一个域或者是跨域的情况？
        9. 防抖和节流区别，并说明一下？

- 2025年9月4日 周四下午三点 阿里国际 Miravia 一面（已过）
    1. 
    使用hook方式实现一个倒计时

- 2025年9月5日 周五下午两点十五分 上海美团（大众点评）一面
    1. 热更新流程部署是怎么样的？
        - 概念：不重新发布整个应用，通过差量更新（JS/CSS/Bundle 等资源）快速下发到客户端。
        - 流程：
            1. 打包工具（Webpack/Vite/Metro）编译生成更新包。
            2. 上传到热更新服务端（比如 CodePush、自研 OTA）。
            3. 客户端启动时对比版本号 → 拉取增量包 → 解压覆盖。
            4. 下次启动直接运行更新后的 bundle。
        - 好处：无需 App Store 审核，快速修复 bug 或上线小功能。
    2. ES分词简单说一下？
    3. React Native视图是怎么渲染到原生的？以及有什么优势
        - 渲染原理：
            1. RN 写的 JS 代码在 JS 引擎里运行（以前是 JSCore，现在 Hermes 更多）。
            2. JS 通过 Bridge 把 UI 操作指令传给原生层。
            3. 原生层（iOS UIView / Android View）渲染真正的 UI。
        - 优势：一套 JS 代码多端复用（iOS/Android）、热更新能力、社区生态丰富（UI 组件、三方 SDK 封装）。
    4. 说一下轮播图、抽奖、弹幕这些组件封装过程？
        - 轮播图：封装成受控组件，props 包含数据源、自动播放间隔、切换动画。内部用 transform/translateX 或 swiper 实现滑动。
        - 抽奖：核心是 动画 + 状态管理，封装成转盘/九宫格，props 传奖品列表、中奖索引，内部用 CSS 动画或 requestAnimationFrame 实现转动。
        - 弹幕：本质是 队列管理，新消息进入队列，逐个从右到左滚动。需要定时器和队列调度，支持弹幕轨道复用。
    5. 组件通信？
        - 父子：props / 回调。
        - 兄弟：提升 state / Context / 状态管理库（MobX、Redux、Zustand）。
        - 跨层级：Context / 全局状态。
        - 非关系组件：EventBus / 自定义 hooks + 状态库。
    6. AI辅助工具有使用吗？以及Cursor为什么在你们APP项目中推进不下去？
        - AI 工具：
            - ChatGPT / Copilot 用于代码提示、文档生成。
            - AI 辅助测试/重构/代码规范化。
        - Cursor 推不下去的原因：
            - 团队成员 IDE 分散，迁移成本高。
            - 对 AI 上下文依赖大，熟悉 prompt 的门槛高。
            - 本地代码私有化 / 安全合规顾虑。
    7. 微任务和宏任务的区别？
        - 宏任务：整体脚本执行、setTimeout、setInterval、I/O、UI 渲染。
        - 微任务：Promise.then、MutationObserver、queueMicrotask。
        - 执行顺序：一个宏任务执行完 → 清空所有微任务 → 再进入下一个宏任务。
    8. http1、http2、http3有什么区别？
        - HTTP1.1：文本协议，队头阻塞，每次请求都要新建连接。
        - HTTP2：二进制协议，多路复用（一个 TCP 连接跑多个请求），头部压缩。
        - HTTP3：基于 QUIC（UDP），解决 TCP 队头阻塞，连接更快，弱网更稳。
    9. 讲一下虚拟DOM，优势在哪？以及是怎么比较更新的（diff差异在哪）
        - 虚拟 DOM：用 JS 对象模拟 DOM 节点，更新时通过 Diff 算法计算最小变更。
        - 优势：批量更新、跨平台渲染（Web/Native/SSR）。
        - Diff 策略：
            1. 同层对比，不跨层。
            2. key 标识，避免重复创建/销毁。
            3. O(n) 优化，而不是 O(n³)。
    10. 什么是Promise？
        - 概念：异步编程的一种解决方案，代表一个未来完成或失败的值。
        - 三种状态：pending → fulfilled / rejected。
        - 链式调用：then / catch / finally。
        - 优势：解决回调地狱，支持更清晰的异常捕获。
    11. 给定一个整数数组 nums 和一个整数目标值 target，请你在该数组中找出 和为目标值 target 的那 两个 整数，并返回它们的数组下标

- 2025年9月6日 周六下午两点 杭州谐云科技
    1. 说一下什么是跨域，有哪些场景会发生跨域？跨域的三个标准是什么？
        - 跨域指的是浏览器的同源策略限制下，协议/域名/端口有任意一个不同，就不能直接访问对方的资源。
        - 场景：
            1. 前端请求不同域名的 API 接口
            2. 主域相同但端口不同
            3. iframe 嵌套不同域的页面
            4. Cookie/localStorage 的访问

        - 跨域的三个标准（判断是否同源）：
            - 协议（protocol）、域名（host）、端口（port）三者完全相同才算同源。
    2. localStorage和sessionStorage的使用场景和区别？在同一域名下不同端口的两个页面localStorage能共享吗？会不会发生跨域？
        - 相同点：都存储在浏览器端、容量一般为 5MB 左右、只能存储字符串
        - 区别：
            - 生命周期
                - localStorage：永久存储，除非手动清理
                - sessionStorage：页面会话结束（关闭标签页/浏览器）后清空
            - 作用域
                - localStorage：同源页面都能共享
                - sessionStorage：仅当前标签页有效，不共享
        - 问题：同一域名下不同端口能否共享 localStorage？
            - 不能共享，因为不同端口视为不同源，会触发跨域。
    3. http1/http2/http3的区别？
        - HTTP/1.1
            - 基于文本协议
            - 支持长连接（Connection: keep-alive）
            - 队头阻塞（同一连接中请求排队）
        - HTTP/2
            - 二进制协议
            - 多路复用（一个 TCP 连接里并发多个请求）
            - 头部压缩（HPACK）
            - 服务端推送
        - HTTP/3
            - 基于 UDP + QUIC 协议
            - 消除了 TCP + TLS 握手的延迟
            - 天然支持多路复用，没有 TCP 队头阻塞
    4. js数组有哪些方法？forEach能中断吗？为什么不能中断？
        - 常用方法：
            - 增删改查：push / pop / shift / unshift / splice / slice
            - 遍历：forEach / map / filter / reduce / every / some / find / findIndex
            - 排序：sort / reverse / flat / concat / join / includes
        - forEach 能中断吗？
            - 不能，它没有返回值控制，只能遍历完。想中断用 for / for...of / some / every。
            - 强制中断可以抛出一个throw Error异常，因为forEach接收的是一个函数，故不能中断
    5. js怎么给数组添加一个自定义方法？（Array.prototype）
    ```
    Array.prototype.sum = function() {
        return this.reduce((a, b) => a + b, 0);
    };
    console.log([1, 2, 3].sum()); // 6
    ```
    6. React有哪些常用的Hooks？
        - useState、useEffect、useContext、useRef、useMemo、useCallback、useReducer、useLayoutEffect
        - useTransition / useDeferredValue（并发特性）
    7. 高阶函数HOC解释一下，常用的场景？
        - 定义：一个函数，接收一个组件并返回一个增强后的新组件。
        - 场景：权限控制、日志记录、复用逻辑（如主题、埋点、数据请求封装）。
    8. forwardRef是什么函数？
        - 高阶函数
        - 定义：React 提供的 API，用来转发 ref，使父组件能获取子组件内部的 DOM 或 ref。
    9. 浏览器输入url到展示的过程？
        1. DNS 解析
        2. 建立 TCP/QUIC 连接（三次握手 / TLS 握手）
        3. 发送 HTTP 请求
        4. 服务端处理返回响应
        5. 浏览器解析 HTML（DOM Tree）
        6. 解析 CSS（CSSOM Tree）
        7. 合并成渲染树
        8. 布局（Reflow）
        9. 绘制（Paint）
        10. 合成（Composite Layers）
    10. flex: 1 由哪些字段构成？
        - 等价于 flex: 1 1 0%;
        - flex-grow: 1 (放大比例)、flex-shrink: 1 (缩小比例)、flex-basis: 0% (初始大小)
    11. 水平垂直居中使用flex布局怎么实现？
    12. setState经历哪些过程？怎么避免过时闭包问题？
        - 流程：触发更新 → React 批量处理 → Fiber diff → 更新 DOM。
        - 过时闭包：函数中引用了旧的 state 值。
        - 解决办法：使用函数式更新。
    13. 什么是闭包？
        - 定义：函数能够“记住”它创建时作用域中的变量，即使在外部调用时依然可以访问。
        - 场景：状态保持、防抖节流、模块化。
    14. 什么是Promise？
        - 定义：异步编程的解决方案，表示一个未来可能完成或失败的值。
        - 三种状态：pending → fulfilled / rejected。
    15. 说说async/await和Promise的关系？async/await是基于Promise实现的吗？
        - 关系：async/await 是 Promise 的语法糖。
        - 执行原理：await 会暂停函数执行，等待 Promise 结果。
        - 本质：基于 Promise 实现。
    16. JS中 0.1+0.2=0.3 吗？
        - 不等，精度丢失

- 2025年9月8日 周一上午十点半 阿里国际 Miravia 二面 业务Leader
    1. React Native的原理说一下？
    2. 为什么Native可以去用JS的组件（为什么JS可以被Native解析出来）？
    3. 你用过Fluter吗？有什么区别？为什么你说Fluter会比RN性能快？
    4. 工作中你觉得最有挑战性或者最难的事情是什么？
    5. 有没有遇到一些技术难题？除了API的应用层面这些的难题？
    6. 合作过程有遇到过什么难题吗？