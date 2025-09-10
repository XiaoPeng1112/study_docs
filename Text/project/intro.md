
## 项目

- “假设需要为美团外卖的‘秒杀活动页’开发前端功能，你会重点关注哪些技术点？请结合你的经验说明。”
    - ​​参考话术（技术深度+业务关联）​​：“秒杀活动页是典型的C端交易场景，核心目标是‘高并发下保障用户体验与交易成功率’。我会关注以下四点：
    1. ​首屏加载优化​​：
        - 关键资源优先加载（如活动标题、秒杀按钮），非关键资源（如推荐商品）用React.lazy+Suspense延迟加载；
        - 图片用webp格式+lazyload（首屏只加载可视区域图片），结合Service Worker缓存活动页静态资源（如CSS/JS），减少接口调用（加载时间从2.2s→0.8s）。
    2. ​倒计时准确性​​：
        - 避免前端setInterval误差（累计误差超1s），改用服务端返回‘活动结束时间戳’，前端用requestAnimationFrame实时计算剩余时间（精度≤100ms）；
        - 服务端同步：每5分钟请求一次活动状态（是否开始/结束），避免前端时间与服务器不同步（如用户本地时间错误）。
    3. 库存扣减提示​​：
        - 实时显示剩余库存（通过WebSocket或长轮询监听服务端库存变更）；
        - 库存为0时，禁用‘立即抢购’按钮并提示‘已售罄’，避免用户重复点击（实测减少30%无效请求）。
    4. ​异常处理​​：
        - 网络异常：用error boundary捕获组件错误，展示友好提示（如‘网络开小差，点击重试’）；
        - 接口失败：重试机制（retry库）+ 本地缓存（如活动未开始时缓存倒计时），确保用户感知流畅。

- 问题场景​​：在展业工具2.0的机具列表页（RN开发），用户反馈滑动时卡顿（FPS 45），尤其快速滑动时明显。
    - ​排查过程​​：
        - 使用Flipper的Performance面板抓取主线程耗时，发现renderItem函数执行时间过长（单次120ms）；
        - 通过React DevTools分析组件树，发现ItemComponent嵌套了8层View（包括自定义的Divider、Badge等），且未做React.memo缓存；
        - 日志显示onScroll事件触发频率过高（每秒60次），每次触发都调用了setState更新滚动位置状态（即使位置未变化）。
    - ​解决方案​​：
        - ​减少渲染层级​​：将ItemComponent的8层View扁平化为3层（合并Divider和Badge为单个View，用absolute positioning替代嵌套）；
        - ​缓存组件​​：用React.memo包裹ItemComponent，并自定义areEqual函数（仅比较item.id和item.status），避免相同数据重复渲染；
        - ​节流滚动事件​​：对onScroll事件使用lodash.throttle（500ms），并结合useRef记录上次滚动位置，仅当位置变化超过50px时才触发setState；
        - ​优化图片加载​​：机具logo图片从本地require改为react-native-fast-image（支持缓存和渐进式加载），首屏图片加载时间从800ms降至200ms。

- 建盛材APP的搜索功能是商家找货的核心入口，但原方案因前端本地过滤+多次接口请求，导致平均响应时间1.8s，用户反馈“搜索慢”。
    - 目标是将响应时间降至1s内，同时提升搜索结果的准确性（如“瓷砖”能匹配到“仿古砖”“抛光砖”等细分类型）。
    - 行动：
        - 发现服务端分词支持更复杂的语义匹配（如“800x800的地板”能识别“尺寸”和“品类”），但需要解决接口延迟问题；
        - 性能优化：用debounce（300ms）+AbortController取消重复请求，减少无效网络开销；
        - 缓存策略：对高频搜索词（如“瓷砖”）的结果用localStorage缓存（30分钟过期），命中缓存时直接展示；
        - 语义扩展：与后端合作，在搜索接口中增加“同义词映射”（如“地板”→“地砖”“瓷砖”），提升结果覆盖率。
        - ​R（结果）​​：响应时间降至0.4s，搜索结果准确率从65%提升至85%，用户日均搜索次数从5000次增长至8000次。
        - ​L（学习）​​：通过这个项目，我深入理解了“服务端分词”和“前端缓存”的权衡。
    - ES 基本原理
        - 基于倒排索引（Inverted Index）实现高效文本检索。
        - 支持全文检索、模糊匹配、分词、高亮、聚合等功能。
        - 通过 REST API 提供查询接口，前端只需要发送查询条件即可。
    - 前端请求逻辑
        - 用户输入关键字 → 前端做防抖处理 → 请求后端搜索 API。
        - 后端根据关键词查询 ES 索引 → 返回结果（含高亮和分页）。
        - 前端接收结果 → 渲染列表或表格。
    - 分词/匹配
        - ES 会自动进行中文分词（如使用 IK 分词器）。
        -支持前缀匹配、模糊匹配、多字段联合查询。

- PWA是什么怎么提高DAU（用户）留存率？
    - PWA（Progressive Web App）本质是 给 Web 加上原生 App 的一些能力，有 **Service Worker** 缓存机制，支持离线访问
    - 传统 Web App 一旦网络断开，就会白屏或报错。
        - 引入 PWA 后：
            1. 静态资源（HTML、CSS、JS、图片）会被缓存到本地
            2. 关键接口数据也可以通过 IndexedDB / Cache Storage 缓存一份
            3. 用户断网时，系统不会直接崩，而是展示缓存数据 + 友好提示
    - 为什么能提升 DAU、留存率
        1. 不容易流失用户
            - 用户断网时还能继续用，避免了“卡死 → 关掉 → 不再打开”的情况。
        2. 打开速度快
            - 有缓存，二次进入就能“秒开”，体验接近原生。
        3. 增强用户黏性
            - 可添加到桌面图标，减少用户流失（不用每次去浏览器找）。
- 什么是 Service Worker
    - 本质：运行在浏览器后台的一个 独立线程（不依赖页面生命周期）。
    - 特点：可以把它理解成：前端的“代理层”，帮你管理缓存和网络请求。
        1. 没有直接操作 DOM 的能力（与页面隔离）
        2. 通过 postMessage 与页面通信
        3. 常用于拦截网络请求、缓存资源、推送消息等
    - Service Worker 能做什么
        1. 请求拦截 + 缓存
            - 可以缓存静态资源（HTML、CSS、JS、图片）
            - 可以缓存接口数据（JSON 响应）
            - 用户离线时返回缓存，保证页面可用
        2. 离线体验
            - 网络断了也能访问应用，不会白屏
        3. 消息推送 (Push Notifications)
            - 即使页面关闭，仍能在后台接收并展示通知（需要浏览器支持）
        4. 后台同步 (Background Sync)
            - 用户离线提交表单，待网络恢复后自动发送
    - Service Worker 生命周期
        - 有几个关键阶段：
            1. 注册（register）：页面第一次访问时注册
            2. 安装（install）：缓存静态资源
            3. 激活（activate）：清理旧缓存
            4. 拦截（fetch）：监听网络请求，走缓存或发起请求

- H5支付与微信环境支付的实现详解​​
    - H5支付是指通过浏览器（含微信内置浏览器）完成的在线支付，核心是通过支付平台（如支付宝、微信支付）提供的H5接口或JS-SDK实现。以下是​​通用H5支付流程​​和​​微信环境特殊处理​​的详细说明，结合技术实现与项目实践（如电商H5页面、小程序外的微信支付）。
    - H5支付的通用实现（以支付宝、微信支付为例）​​
        - H5支付的本质是通过浏览器调用支付平台的接口，完成“订单生成→支付请求→结果回调”的流程。不同支付平台的实现细节略有差异，但核心步骤一致。
    - 支付宝H5支付实现流程​​：支付宝H5支付支持两种模式：​​跳转支付网关​​（用户跳转到支付宝页面完成支付）和​​H5 SDK直连​​（通过支付宝JS SDK在当前页面完成支付）。
    - 流程：
        - 步骤1：后端生成预支付订单​​
            - 用户选择商品后，前端调用后端接口（如/api/pay/alipay/create），后端生成支付宝的​​预支付交易会话标识（out_trade_no）​​和​​支付参数（biz_content）​​，并返回给前端。
            - ​关键参数​​：out_trade_no：商户订单号（唯一）；total_amount：支付金额（元，如99.99）；subject：商品标题（如“XX瓷砖”）；product_code：支付宝产品码（固定为QUICK_MSECURITY_PAY）。
        - ​步骤2：前端构造支付请求​​
            - 前端根据后端返回的参数，构造支付宝的支付链接（alipay://协议）或调用支付宝JS SDK的tradePay接口。
        - ​步骤3：用户跳转支付宝支付​​
            - ​方式1：跳转支付网关​​（兼容所有浏览器）：前端通过window.location.href跳转到支付宝的支付页面，参数通过URL Query传递：
            ```
            const alipayUrl = `https://openapi.alipay.com/gateway.do?${queryString}`;
            window.location.href = alipayUrl;
            ```
            - 其中queryString需包含后端返回的app_id、method、charset、sign_type、timestamp、version、biz_content、sign等参数（需后端生成并签名）。
        - ​步骤4：支付结果回调​，用户支付完成后，支付宝会通过​​同步跳转​​（return_url）或​​异步通知​​（notify_url）告知商户支付结果：
            - ​同步跳转​​：用户支付成功后，支付宝跳转到商户设置的return_url（如https://your-domain.com/pay/success），前端通过URL参数（如trade_status=TRADE_SUCCESS）判断支付状态；
            - ​异步通知​​：支付宝向商户的notify_url（后端接口）发送POST请求，包含加密的支付结果（需后端用支付宝公钥验签）。
        - 步骤5：前端更新订单状态​​，前端通过轮询return_url的参数或监听后端的WebSocket通知，获取支付结果后更新页面（如显示“支付成功”）。

- 微信环境支付的特殊注意事项​​
    - 微信内置浏览器（X5内核）的支付实现需额外处理以下问题：
        1. 微信JS-SDK的签名验证​​
            - 微信JS-SDK的wx.config需要后端生成正确的签名，签名算法为：signature = SHA1('jsapi_ticket=' + ticket + '&noncestr=' + nonceStr + '&timestamp=' + timestamp + '&url=' + url)
            - 其中：
                1. jsapi_ticket：通过微信/cgi-bin/ticket/getticket接口获取（需access_token）；
                2. url：当前页面的完整URL（含查询参数，需编码）；
                3. nonceStr：随机字符串（长度16）；
                4. timestamp：当前时间戳（秒级）。
        2. 微信支付的安全限制​​
            - ​域名白名单​​：微信支付商户平台需配置支付页面的域名（如https://your-domain.com），否则无法唤起支付；
            - ​支付场景限制​​：微信支付仅支持“商户号绑定”的APPID（需在微信支付商户平台关联）；
            - ​签名防篡改​​：后端需对package、timestamp等参数进行签名，防止前端篡改支付金额。
        3. 微信内浏览器的兼容性问题​​
            - ​X5内核的缓存问题​​：微信内置浏览器可能缓存旧版JS-SDK，需在页面URL中添加随机参数（如?v=1.0.1）强制刷新；
            - ​支付弹窗被拦截​​：若页面在iframe中加载，微信可能拦截支付弹窗，需确保支付页面在顶层窗口打开。

- 响应式布局
    - 核心是 流式布局、弹性盒子(Flexbox)、网格布局(Grid) 以及 媒体查询(Media Query)。
    - 方式：
        1. 媒体查询(针对不同屏幕设置样式)
        ```
        /* 屏幕宽度小于768px时 */
        @media (max-width: 768px) {
            .container {
                flex-direction: column;
            }
        }
        ```
        2. flex布局
        ```
        .container {
            display: flex;
            justify-content: space-between;
            flex-wrap: wrap; /* 自动换行 */
        }
        ```
        3. 网格布局grid
        ```
        .grid {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
            gap: 16px;
        }
        ```
        4. 相对单位
            - 使用 rem、em、vw、vh 代替固定 px。
            - 结合 @media 让组件在不同设备上缩放。

- 前端全局搜索改造为 Elasticsearch（ES）后端搜索
    - 之前我们的全局搜索逻辑完全在前端做，数据量一多就卡顿，而且复杂查询（模糊匹配、多字段、高亮等）不好实现。为了提高性能和可扩展性，我们将搜索逻辑迁移到后端，使用 Elasticsearch 作为搜索引擎。”
    - 把搜索逻辑迁移到后端，用 Elasticsearch 建索引，前端输入关键词经过防抖后请求后端 API，后端利用倒排索引做多字段匹配、模糊搜索和高亮返回结果，前端接收渲染列表。
    - ES 基本原理
        - 基于倒排索引（Inverted Index）实现高效文本检索。
        - 支持全文检索、模糊匹配、分词、高亮、聚合等功能。
        - 通过 REST API 提供查询接口，前端只需要发送查询条件即可。
    - 前端请求逻辑
        - 用户输入关键字 → 前端做防抖处理 → 请求后端搜索 API。
        - 后端根据关键词查询 ES 索引 → 返回结果（含高亮和分页）。
        - 前端接收结果 → 渲染列表或表格。
    - 分词/匹配
        - ES 会自动进行中文分词（如使用 IK 分词器）。
        -支持前缀匹配、模糊匹配、多字段联合查询。

- AI 助手功能：
    - 前端框架：React + TypeScript + Ant Design / @ant-design/x 组件。
    - 消息流渲染：
        - 用户输入内容，点击发送 → 添加到 messageList。
        - 前端调用 sendMessageStream，向后端发送请求。
        - 后端通过 SSE（Server-Sent Events）或者返回 ReadableStream，流式返回 AI 回复内容。
        - 前端用 ReadableStream.getReader() + TextDecoder 持续读取数据块。
        - 解析每一块 JSON 数据，拼接到对应消息对象中，实现逐字/逐句渲染。
    - UI 组件：
        - Bubble 显示消息气泡，左侧助手、右侧用户。
        - Sender 固定底部输入框，支持提交、附件上传。
        - Conversations 管理会话列表。
    - 总结：
        - “在我们项目中，AI 助手的消息是流式返回的，我在前端通过 ReadableStream + TextDecoder 解析 SSE 数据，每读到一块就拼接到消息数组里，然后用 Bubble 气泡组件渲染，实现逐字显示效果。这样用户输入问题后，不必等待整段内容返回，就能看到 AI 逐步输出，提高交互感。我还结合 Sender 组件固定输入框，支持附件上传，并在切换会话或中途取消时正确清理流和状态。此外，我们前端还做了防抖和请求取消优化，保证高频输入下不会出现重复请求或渲染卡顿。”

1. 行为监控
    - Sentry 配合PostHog进行统计

2. 前端如何判断 token 过期 + 如何自动刷新 token
    - token 过期：
        1. 看后端返回的过期时间字段
            - 登录成功后，后端一般会返回 accessToken 和 expiresIn（过期时间戳）。
            - 前端可以在本地存下这个时间，每次请求前判断是否快过期。
        2. 根据 Token 自身（JWT）解析过期时间
            - 如果是 JWT，里面有 exp 字段，前端可以直接用 atob 解码 payload，判断是否过期。
        3. 根据接口返回
            - 如果请求时后端返回 401 Unauthorized，说明 token 已失效，需要刷新或重新登录。
    - 自动刷新token
        1. 后端设计
            - accessToken：短期有效（如 2h）
            - refreshToken：长期有效（如 7d）
            - 提供一个 刷新 token 的接口：/refreshToken

        2. 前端逻辑
            - 在请求拦截器里带上 accessToken。
            - 如果发现 token 过期（本地判断或后端返回 401）：
                - 用 refreshToken 去换一个新的 accessToken。
                - 更新本地缓存，再重新发起原来的请求。
            - 如果 refreshToken 也过期，就强制跳转登录页。

3. 在 RN 中引入高德 SDK 的步骤
    1. 获取高德 SDK Key
        - 去 高德开放平台 注册开发者。
        - 创建应用，获取对应平台（Android/iOS）的 API Key。
    2. Android 集成
        - 在 android/app/src/main/AndroidManifest.xml 配置权限：
        ```
        <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>
        <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION"/>
        <uses-permission android:name="android.permission.ACCESS_LOCATION_EXTRA_COMMANDS"/>
        <uses-permission android:name="android.permission.INTERNET"/>
        <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
        ```
        - 在 AndroidManifest.xml 中添加高德 key：
        ```
        <meta-data
            android:name="com.amap.api.v2.apikey"
            android:value="你的高德API_KEY"/>
        ```
        - 在 build.gradle 添加高德依赖：
        ```
        implementation 'com.amap.api:location:latest.integration'
        ```
    3. iOS 集成
        - 在 ios/Podfile 里添加高德 SDK：
        ```
        pod 'AMap3DMap', '~> 9.7.0'
        pod 'AMapLocation', '~> 2.9.0'
        ```
        然后执行 pod install。
        - 在 AppDelegate.m / AppDelegate.swift 初始化：
        ```
        [AMapServices sharedServices].apiKey = @"你的高德API_KEY";
        ```
    4. 封装 Native Module
        - 在 Android 新建一个 Java 类：
        ```
        public class AMapModule extends ReactContextBaseJavaModule {
            public AMapModule(ReactApplicationContext reactContext) {
                super(reactContext);
            }

            @Override
            public String getName() {
                return "AMapModule";
            }

            @ReactMethod
            public void getCurrentLocation(Callback successCallback, Callback errorCallback) {
                AMapLocationClient locationClient = new AMapLocationClient(getReactApplicationContext());
                AMapLocationClientOption option = new AMapLocationClientOption();
                option.setOnceLocation(true);
                locationClient.setLocationOption(option);

                locationClient.setLocationListener(location -> {
                    if (location != null && location.getErrorCode() == 0) {
                        successCallback.invoke(location.getLatitude(), location.getLongitude());
                    } else {
                        errorCallback.invoke(location.getErrorInfo());
                    }
                });
                locationClient.startLocation();
            }
        }
        ```
        - 在 JS 里调用：
        ```
        import { NativeModules } from 'react-native';
        const { AMapModule } = NativeModules;

        AMapModule.getCurrentLocation(
            (lat, lng) => console.log('定位成功:', lat, lng),
            (err) => console.error('定位失败:', err)
        );
        ```