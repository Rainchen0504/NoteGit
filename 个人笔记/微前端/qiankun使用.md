​	微前端是一种类似于微服务的架构，它将微服务的理念应用于浏览器，将web应用由单体应用转变为多个应用聚合的应用。每个应用都可以**<font color=pink>独立运行、独立开发、独立部署</font>**。



## 优点

- 应用自治：只需要遵循统一的规范或框架，以便系统集成到一起，相互之间是不存在依赖关系的。
- 单一职责：每个前端应用可以只关注于自己的需要完成的功能；
- 技术栈无关：不同应用可以使用不同的技术栈；



## 缺点

- 应用的拆分基础依赖于基础设施的构建，一旦大量应用依赖于同一基础设施，维护难度增大；
- 拆分颗粒度越小，架构变得复杂，维护成本变高；
- 技术栈一旦多样化，便意味着技术栈混乱，管理难度加大；



## 模块组成

当前微前端主要采用组合式路由方案，核心是"主从"思想，即<font color=pink>包括一个基座应用</font>和<font color=pink>若干个微应用</font>，基座应用大多数是一个前端SPA项目，主要负责应用注册，路由映射，消息下发等，而微应用是独立前端项目，这些项目不限于采用React，Vue，Angular或者JQuery开发，每个微应用注册到基座应用中，由基座进行管理，但是如果脱离基座也是可以单独访问。



## 基本流程图

![image-20230318150707850](https://raw.githubusercontent.com/Rainchen0504/picture/master/202303181507642.png)



## 微前端落地方案

当前微前端落地方案分为三类：自组织模式、基座模式以及模块加载模式。

![image-20230318151017201](https://raw.githubusercontent.com/Rainchen0504/picture/master/202303181510543.png)



## qiankun使用实例

### （1）项目架构

```shell
├── base    # 主服务 - 可以是任意框架，这里使用vue2技术栈
   ├── public   # 资源
   └── src  # 主要code
           └──  main.js # 配置注册微服务信息，以及给子应用传参等...
├── app1-vue      # 微服务app1 - vue
   ├── public   # 资源
   ├── src  # 主要code
              └── main.js  # 配置 注册信息 bootstrap/mount/unmount 方法声明
   └── vue.config.js  # 配置允许跨域，以及端口
├── app2-react    # 微服务app2 - react
   ├── public   # 资源
   └── src  # 主要code
            └── index.js # 配置 注册信息 bootstrap/mount/unmount 方法声明
    └── config-overrides.js  # 配置允许跨域，以及端口
```



### （2）搭建过程

#### 2.1、创建vue2主应用

这里使用vue2技术栈作为主应用

1. 脚手架创建项目；

2. 创建完项目后安装qiankun（`npm install qiankun -S`）；

3. 在main.js文件中添加配置

   ```js
   import Vue from "vue";
   import App from "./App.vue";
   import router from "./router";
   import ElementUI from "element-ui";
   import "element-ui/lib/theme-chalk/index.css";
   
   Vue.use(ElementUI);
   
   import { 
     registerMicroApps, 	// 注册应用
     start	 //开启方法
   } from "qiankun";
   
   const apps = [
     {
       name: "vue2App", //应用名字，微应用app1
       entry: "//localhost:10000", //默认会加载这个html 解析里面的js(这里使用的fetch) 动态的执行(子应用必须支持跨域)
       container: "#vue2", //容器名 挂载元素
       activeRule: "/vue2", // 激活路径 激活规则，当访问 /vue时就会把这个应用挂载到 #vue上
       props: {
         a: 1, //实现主应用给子应用传参
       },
     },
     {
       name: "reactApp", //应用名字，微应用app2
       entry: "//localhost:20000",
       container: "#react",
       activeRule: "/react",
       props: {
         b: 2,
       },
     },
     {
       name: "vue3App",
       entry: "//localhost:30000",
       container: "#vue3",
       activeRule: "/vue3",
       props: {
         c: 3,
       },
     },
   ];
   
   registerMicroApps(apps); //注册子应用
   start({
     prefetch: false, // 取消预加载
   });
   
   Vue.config.productionTip = false;
   
   new Vue({
     router,
     render: (h) => h(App),
   }).$mount("#app-base"); //修改挂载节点，防止跟app1-vue节点重名
   ```

4. 修改主应用`public/index.html`文件中的跟节点`id`

   ```html
   <div id="app"></div>
   <!-- 修改为 -->
   <div id="app-base"></div>
   ```

5. 修改`src/App.vue`中的跟节点`id`

   ```vue
   <template>
   	<div id="app"></div>
   </template>
   <!-- 修改为 -->
   <template>
     <div id="app-base">
       <el-menu default-active="/" :router="true" mode="horizontal">
         <el-menu-item index="/">基类项目</el-menu-item>
         <el-menu-item index="/vue2">vue2应用</el-menu-item>
         <el-menu-item index="/react">react项目</el-menu-item>
         <el-menu-item index="/vue3">vue3项目</el-menu-item>
       </el-menu>
       <router-view></router-view>
       <div id="vue2"></div>
       <div id="react"></div>
       <div id="vue3"></div>
     </div>
   </template>
   ```



#### 2.2、创建vue2子应用

1. 脚手架创建项目；

2. 修改`vue.config.js`配置文件

   ```js
   module.exports = defineConfig ({
     transpileDependencies: true,
     devServer: {
       port: 10000,	//和父应用配置文件中的端口应保持一致
       headers: {
         //qiankun内部使用fetch请求资源，子应用必须跨域
         "Access-Control-Allow-Origin": "*",
       },
     },
     configureWebpack: {
       output: {
         //打包路径
         library: "vue2App",
         libraryTarget: "umd",
       }
     }
   })
   ```

3. 配置`src/main.js`文件，父应用要加载子应用，子应用必须暴露三个生命周期方法：`bootstrap、mount、unmount`

   ```js
   import Vue from "vue";
   import App from "./App.vue";
   
   // 创建子应用实例
   let instance = null;
   function render() {
     instance = new Vue({
       render: (h) => h(App),
     }).$mount("#app"); //这里挂在到自己的html中
   }
   
   // 动态添加publicPath
   // if (window.__POWERED_BY_QIANKUN__) {
   //   __webpack_public_path__ = window.__INJECTED_PUBLIC_PATH_BY_QIANKUN__;
   // }
   
   // 默认独立运行
   if (!window.__POWERED_BY_QIANKUN__) {
     render();
   }
   
   // 基类应用加载子应用，子应用必须暴露三个接口：bootstrap,mount,unmount
   
   /**
   * 必须存在
   * bootstrap 只会在微应用初始化时调用一次，下次微应用重新进入时会直接调用 mount 钩子函数，不会再重复触发 bootstrap。
   * 通常这里可以做一些全局变量的初始化，比如不会在unmount阶段被销毁的应用级别的缓存等
   */
   export async function bootstrap(props) {
     console.log("vue2子应用app bootstraped", props);
   }
   
   // 必须，每次进入调用mount方法
   export async function mount(props) {
     console.log("vue2子应用mount", props);
     render(props);
   }
   
   // 必须，每次切出/卸载都会调用的方法，卸载应用实例
   export async function unmount(props) {
     console.log("vue2子应用unmount", props);
     instance.$destroy();
     instance.$el.innerHTML = "";
     instance = null;
   }
   
   Vue.config.productionTip = false;
   ```

   

#### 2.3、创建vue3子应用

1. 使用脚手架创建项目；

2. 在`main.js`文件中添加生命周期

   ```js
   import { createApp } from "vue";
   import App from "./App.vue";
   
   let instance = null;
   function render() {
     instance = createApp(App).mount("#vue3-app");
   }
   
   // 动态添加publicPath
   // if (window.__POWERED_BY_QIANKUN__) {
   //   __webpack_public_path__ = window.__INJECTED_PUBLIC_PATH_BY_QIANKUN__;
   // }
   // 默认独立运行
   if (!window.__POWERED_BY_QIANKUN__) {
     render();
   }
   
   // 父应用加载子应用，子应用必须暴露三个接口：bootstrap,mount,unmount
   
   /**
    * required
    * bootstrap 只会在微应用初始化时调用一次，下次微应用重新进入时会直接调用mount钩子，不会再重复触发，bootstrap.
    * 通常我们可以在这里做一些全局变量的初始化，比如不会在unmount 阶段被销毁的应用级别的缓存等。
    *
    */
   
   export async function bootstrap(props) {
     console.log("bootstrap:", props);
   }
   /**
    * required
    * 应用每次进入都会调用mount方法，通常我们在这里触发应用渲染方法
    */
   export async function mount(props) {
     console.log("mount:", props);
     render(props);
   }
   
   /**
    * required
    * 应用每次 切出/卸载 都会调用的方法
    * 通常我们会卸载微应用的应用实例
    */
   export async function unmount(props) {
     console.log("unmount:", props);
     instance.$destroy?.();
     instance = null;
   }
   /**
    * not required
    * 仅使用 loadMicroApp 方式加载微应用时生效
    */
   export async function update(props) {
     console.log("update props", props);
   }
   ```

3. 修改`vue.config.js`文件配置项

   ```js
   const { defineConfig } = require("@vue/cli-service");
   module.exports = defineConfig({
     transpileDependencies: true,
     devServer: {
       port: 30000,
       headers: {
         "Access-Control-Allow-Origin": "*",
       },
     },
     configureWebpack: {
       output: {
         //打包路径
         library: "vue3App",
         libraryTarget: "umd",
       },
     },
   });
   ```

4. 修改`public`目录下`index.html`文件中根元素

   ```html
   <div id="vue3-app"></div>
   ```

   

#### 2.4、创建react18子应用

1. 使用脚手架创建项目;

2. src/index.js中配置挂载信息，暴露三个接口：bootstrap、mount、unmount

   ```js
   import React from "react";
   import ReactDOM from "react-dom/client";
   import "./index.css";
   import App from "./App";
   import reportWebVitals from "./reportWebVitals";
   reportWebVitals();
   let root;
   function render(props = {}) {
     let { container } = props;
     root = ReactDOM.createRoot(
       container
         ? container.querySelector("#root")
         : document.getElementById("root")
     );
     root.render(<App />);
   }
   // 独立运行
   if (!window.__POWERED_BY_QIANKUN__) {
     render({});
   }
   export async function bootstrap(props) {
   }
   
   export async function mount(props) {
     // 如果没传props,会样式丢失掉!
     render(props);
   }
   
   export async function unmount(props) {
     let { container } = props;
     // react17的!!! (已被弃用,但是依然可以从reactdom包里导入)
     // ReactDOM.unmountComponentAtNode(
     //   container ? container.querySelector('#root') : document.getElementById('root')
     // )
     // react18!!!
     root.unmount(
       container
         ? container.querySelector("#root")
         : document.getElementById("root")
     );
   }
   ```

   注意⚠️：react18中<font color=pink>**卸载DOM的方法使用`unmount`**</font>

3. 引入配置依赖`npm install react-app-rewired`

   通过react脚手架`create-react-app`创建了项目 ，不执行`eject`命令是没有配置文件的，此时就需要用`react-app-rewired`插件，在根目录下添加`config-overrides.js`文件设置配置属性。

   ```js
   module.exports = {
     webpack: (config) => {
       config.output.library = "reactApp";
       config.output.libraryTarget = "umd";
       config.output.publicPath = "http://localhost:20000"; // 该应用的端口号
       return config;
     },
     devServer: (configFunction) => {
       return function (proxy, allowedHost) {
         const config = configFunction(proxy, allowedHost);
         config.port = "20000";
         config.headers = {
           "Access-Control-Allow-Origin": "*",
         };
         return config;
       };
     },
   };
   ```

4. 修改`package.json`文件中项目启动端口

   ```json
   "scripts": {
     "start": "PORT=20000 react-app-rewired start",
     "build": "react-scripts build",
     "test": "react-scripts test",
     "eject": "react-scripts eject"
   }
   ```



### （3）运行项目

主项目子项目均启动后就可以内嵌访问了