
### 使用vite2创建

* 创建项目
    ```powershell
    npm init vite@latest
    ```

* 选择参数
    * project name: 项目名称
    * select a framework: 项目框架
    * select a variant: 脚本语言

* 创建完成
    * cd 项目名称: 进入项目所在文件夹
    * npm i: 下载项目所需依赖包
    * npm run dev: 运行项目

```json
{
  "scripts": {
    "dev": "vite",            // 启动开发服务器，别名：`vite dev`，`vite serve`
    "build": "vite build",    // 为生产环境构建产物
    "preview": "vite preview" // 本地预览生产构建产物
  }
}
```

### 使用`@vue/cli`创建

* 下载 `@vue/cli`
    ```powershell
    npm install @vue/cli -g
    ```

* 创建项目
    ```powershell
    npm init vue@latest
    ```

* 选择参数
    * project name: 项目名称
    * typeScript: 是否支持ts
    * JSX support: 是否支持JSX
    * Vue Router: 是否使用vue router
    * Pinia: 是否使用Pinia 状态管理工具
    * Vitest: 单元测试
    * Cypress: 自动化测试
    * Eslint:
    * Prettier: 格式化工具

* 创建完成
    * cd 项目名称: 进入项目所在文件夹
    * npm i: 下载项目所需依赖包
    * npm run dev: 运行项目
