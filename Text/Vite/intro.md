# 打包工具

1. Vite常用功能：（按需编译）
    - 路径别名 (resolve.alias)
    - 网络代理 (server.proxy)
    - 插件 (plugins)
    - 构建输出路径 (build.outDir)
    - 源映射 (build.sourcemap)

2. Webpack常用功能：（打包整个项目，慢）
    - 文件入口/出口 (entry, output)
    - Loader 处理 JS/TS/CSS/图片
    - Plugin 插件扩展功能，如 HTML、清理输出目录
    - 路径别名 (resolve.alias)
    - 开发服务器 (devServer)

3.  Vite 和 Webpack 的区别和应用场景：
    - Vite 启动快，HMR 极速，适合现代 React/Vue 开发；Webpack 配置灵活，适合大型项目定制打包。
    - 常用 Vite 配置包括路径别名、开发代理、插件和构建输出；
    - Webpack 常用配置包括入口输出、Loader、Plugin、别名和 devServer 配置。
    在实际项目中，我使用 Vite 做开发环境优化，提高启动和热更新速度，同时熟悉 Webpack 打包优化方案，例如 Tree Shaking 和代码分割。”