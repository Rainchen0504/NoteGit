# 一、vue3移动端音乐项目

1、移动端常用控制缩放方法：在public目录下的index.html文件中meta标签设置`user-scalable=no`

```html
<meta 
  name="viewport"
  content="width=device-width,initial-scale=1.0,maximum-scale=1.0,minimum-scale=1.0,user-scalable=no"
/>
```





# 二、Vue3+TS组件库

## 1、defineComponent

​	vue项目使用typescript语法编写时，defineComponent()函数是定义 Vue 组件时提供类型推导的辅助函数。

​	源码定义位置：runtime-core/src/apiDefineComponent.ts文件中对defineComponent进行了重载，有四种写法类型，一般建议使用第四种，即以对象形式单独声明props。

​	defineComponent接收参数范型定义了props、setup函数的返回值、data、computed、mixin、extend关键字等实例属性的类型。最后生成组件实例的返回值对象包括`$、$data、$props、$attrs、$refs、$slot`等属性。



## 2、渲染函数

​	使用setup写渲染函数的组件中，setup函数中的内容只会执行一次，事件操作产生数据变化影响的是return出的内容，所以渲染函数需要把闭包变量要声明在return中。

### （1）h函数

​	在vue文件中，模板中的内容其实不是像普通html一样，写完直接渲染的，而是最后被编译成JS代码执行渲染到页面上。



可以通过官方提供的h函数实现一样的原理，比如实现App.vue中的模板内容：

```vue
<template>
  <div id="app">
  	<p class="red">你好</p>
  </div>
</template>
```

使用h函数编写的App.tsx文件如下：

```tsx
import { createApp, defineComponent, h } from 'vue' // 通过h函数可以创建节点

const App = defineComponent({
	render() {
		return h('div', {id: 'app'}, [
			h('p', {
				class: 'red'
			})
		])
	}
})
```

h函数接收三个参数，**节点类型**，**节点属性**，**子节点**。

模板编译出来的JS大致就是这个样子，我们接着来看看其他节点怎么写：

```tsx
<template>
  <div id="app">
  	<img src="../......xxxxxxx.png"/>
  	<hello-app></hello-app> // 组件
  </div>
</template>

import { createApp, defineComponent, h } from 'vue' 
import helloApp from '.......'
// 这里可能eslint会不允许ts使用require语法，所以要禁用这行的eslint检查 eslint-disable-line
const img = require('./../xxx.png') 
const App = defineComponent({
	render() {
		return h('div', {id: 'app'}, [
			h('img', {
				src: img // 在js中无法直接写字符串的地址，所以要引入
			}),
			h(helloApp)
		])
	}
})
```



### （2）在setup中写h函数

```tsx
const App = defineComponent({
	setup() {
		const str = ref('a')
		return () => { // 通过闭包可以获取外面的变量
			return h('div', {id: 'app'}, [
				h('p', str )
			])
		}
	}
})
```

注意一个闭包的问题：

```tsx
const App = defineComponent({
	setup() {
		const str = ref('a')
		const outSide = str.value // 因为setup制作组件创建的时候执行一次，所以outSide是不具备响应式的
		return () => { // 通过闭包可以获取外面的变量
			const inSide = str.value // 因为闭包的缘故，str发生了改变，闭包内的函数内容会重新执行，所以inSide会更新
			return h('div', {id: 'app'}, [
				h('p', str )
			])
		}
	}
})
```



### （3）createVNode

通过查看源码，h函数就是对`createVNode`进行了一层封装，所以`createVNode`也可以直接代替h函数。

```tsx
import { createApp, defineComponent, createVNode } from 'vue' // 通过h函数可以创建节点

const App = defineComponent({
	render() {
		return createVNode ('div', {id: 'app'}, [
			createVNode ('p', {
				class: 'red'
			})
		])
	}
})
```



### （4）JSX

用h函数去写html其实还是有点繁琐的，可以通过JSX更加便捷的书写html还能发挥JS的使用。

vue3推荐使用一个babel库去安装JSX：

```shell
npm i @vue/babel-plugin-jsx -D
```

然后在babel.config.js中配置：

```js
plugins: ['@vue/babel-plugin-jsx']
```

在App.vue同级中建个App.jsx：

```tsx
import { defineComponent } from "vue";

export default defineComponent ({
    setup() {
        return () => {
            return (
                <div id="app">123</div>
            )
        }
    },
})
```

然后在main.js中把vue文件替换成Jsx文件：

```ts
import { createApp } from "vue";
import App from "./App";

createApp(App).mount("#app");
```



## 3、json-schema

### （1）说明

​	Json Schema定义了一套词汇和规则，这套词汇和规则用来定义Json元数据，定义了Json数据需要满足的规范，包括成员、结构、类型、约束等。可以类比成json的增强interface。



### （2）安装

使用ajv这个基于json-schema标准实现的库

```shell
npm i ajv -S
```



### （3）使用流程

可以新建个src同级文件夹，里面放json-schema的测试文件，比如json-schema/test.js：

```js
// 官方案例
const Ajv = require("ajv");
const ajv = new Ajv(); // options can be passed, e.g. {allErrors: true}

const schema = {
  type: "object", // 类型
  properties: {
    // 对象上的属性（特征）
    foo: { type: "number" },
    bar: { type: "string" },
  },
  required: ["foo"], // 必输入项
  additionalProperties: false,
};

const validate = ajv.compile(schema);

const data = {
  foo: 1,
  bar: 1, // 故意输错
};

const valid = validate(data);
if (!valid) console.log(validate.errors);
```

cd到文件夹下直接node test.js跑起来，会打印出：

```js
{
  instancePath: '/bar',
  schemaPath: '#/properties/bar/type',
  keyword: 'type', // 类型错误
  params: { type: 'string' },
  message: 'must be string' // 错误信息
}
```

可能在写的过程中，eslint会报错，需要在src同级下创建个.eslintignore文件用来忽略schema-tests文件下的检测：

```js
schema-tests
```



#### 3.1、自定义校验规则addFormat

​	可以理解为ajv内置的一些字符串和数字的校验规则，参考文档https://ajv.js.org/guide/formats.html#string-formats，使用addFormat方法可以自定义format规则。



#### 3.2、自定义关键字addKeyword

​	类似上面的type、properties等就是关键字。如何自定义关键字的内部逻辑有多种方案。

##### （1）validate方案：

```js
const Ajv = require("ajv");
const ajv = new Ajv();

const schema = {
  type: "object",
  properties: {
    input: { type: "string", fnCheck: true },
  },
};

ajv.addKeyword("fnCheck", {
  validate(schema, data) { // addKeyword内部逻辑的现实有多种，先看看validate方案
    // schema为新增fnCheck关键字的值，为true，data为添加这个关键字的对象的值，input为'1234'
    if (data === "123") return true;
    else return false;
  },
});
const validate = ajv.compile(schema);

const data = {
  input: "1234",
};

const valid = validate(data);
if (!valid) console.log(validate.errors);
```

直接运行就会提示没通过fnCheck的校验

```js
{
   instancePath: '/input',
   schemaPath: '#/properties/input/fnCheck',
   keyword: 'fnCheck',
   params: {},
   message: 'must pass "fnCheck" keyword validation'
}
```



##### （2）compile方案：

```js
const Ajv = require("ajv");
const ajv = new Ajv();

const schema = {
  type: "object",
  properties: {
    input: { type: "string", fnCheck: true },
  },
};

ajv.addKeyword("fnCheck", {
  compile(sch, parentSchema) {
    // sch 为新增key的值，例如fnCheck的true。parentSchema取的是新增key所在的父级内容，也就是{ type: "string", fnCheck: true }
    return () => true; // 注意的是要返回一个函数，如果你想返回值需要通过返回的函数再返回出去
  },
  metaSchema: {
    type: "boolean", // 定义新增key值的类型
  },
});
const validate = ajv.compile(schema);

const data = {
  input: "1234",
};

const valid = validate(data);
if (!valid) console.log(validate.errors);
```



##### （3）macro方案：

```js
const Ajv = require("ajv");
const ajv = new Ajv();

const schema = {
  type: "object",
  properties: {
    input: { type: "string", fnCheck: true, minLength: 10, maxLength: 20 },
  },
};

ajv.addKeyword("fnCheck", {
  macro() {
    // 会把返回的内容全部加到新增key的下面
    return {
      minLength: 10,
      maxLength: 20,
    };
  },
});
const validate = ajv.compile(schema);

const data = {
  input: "1234",
};

const valid = validate(data);
if (!valid) console.log(validate.errors);
```



#### 3.3、中文化错误信息

上面的错误信息提示都是底层自动报出来的，而且是英文的，如果想转化成中文，需要安装相关库：

```shell
npm i ajv-i18n -S
```



```js
const Ajv = require("ajv");
const ajv = new Ajv();
const localize = require("ajv-i18n"); // 引入这个库

const schema = {
  type: "object", // 类型
  properties: {
    // 对象上的属性（特征）
    foo: { type: "number" },
    bar: { type: "string" },
  },
  required: ["foo"], // 必输入项
  additionalProperties: false,
};

const validate = ajv.compile(schema);

const data = {
  foo: 1,
  bar: 1, // 故意输错
};

const valid = validate(data);
if (!valid) {
  localize.zh(validate.errors); // 转化成中文
  console.log(validate.errors);
}
```

执行结果为：

```js
[
  {
    instancePath: '/bar',
    schemaPath: '#/properties/bar/type',
    keyword: 'type',
    params: { type: 'string' },
    message: '应当是 string 类型'
  }
]
```



#### 3.4、自定义错误信息

```js
const Ajv = require("ajv");
const ajv = new Ajv();

const schema = {
  type: "object",
  properties: {
    input: { type: "string", fnCheck: true },
  },
};

ajv.addKeyword("fnCheck", {
  validate: function fn(schema, data) {
    fn.errors = [ // 通过修改的方式去自定义
      {
        instancePath: "/input",
        schemaPath: "#/properties/input/fnCheck",
        keyword: "fnCheck",
        params: {},
        message: "世上无难事，只要肯放弃",
      },
    ];
    return false;
  },
});
const validate = ajv.compile(schema);

const data = {
  input: "1234",
};

const valid = validate(data);
if (!valid) {
  console.log(validate.errors);
}
```

可以通过使用库简化流程：

```shell
npm i ajv-errors -S
```

```js
const Ajv = require("ajv");
const ajv = new Ajv({ allErrors: true, jsonPointers: true }); // 注意：new的时候需要设置
require("ajv-errors")(ajv); // 把ajv扔进去处理

const schema = {
  type: "object",
  properties: {
    input: {
      type: "string",
      minLength: 10,
      maxLength: 20,
      errorMessage: {
        // 针对不同要求设置不同提示
        type: "必须是字符串", // 对类型的提示
        minLength: "最小长度不能小于10",
        maxLength: "最大长度不能大于20",
      },
      //   errorMessage: "必须是字符串", 这种方式无论什么类型都报这个错误
    },
  },
};

const validate = ajv.compile(schema);

const data = {
  input: "1",
};

const valid = validate(data);
if (!valid) {
  console.log(validate.errors);
}
```

输出结果为：

```js
[
  {
    instancePath: '/input',
    schemaPath: '#/properties/input/errorMessage',
    keyword: 'errorMessage',
    params: { errors: [Array] },
    message: '最小长度不能小于10'
  }
]
```

缺点是对自定义关键字无法使用中文报错提示。



## 4、tsx使用monaco

### （1）说明

​	monaco是一个可提供在线编辑器的库。



### （2）安装

```shell
npm i monaco-editor -D 
```

安装这个库后还需要安装实现用js去编写css的库jss，这里安装使用匹配vue的：

```shell
npm i vue-jss -S 
// 以及vue-jss的依赖包 
npm i jss jss-preset-default -S
```



### （3）使用流程

#### 3.1、创建一个monaco组件的tsx文件

```tsx
import { defineComponent, ref, onMounted, watch, onBeforeUnmount, shallowReadonly, shallowRef } from 'vue'

import * as Monaco from 'monaco-editor'

import type { PropType, Ref } from 'vue'
import { createUseStyles } from 'vue-jss'

const useStyles = createUseStyles({
  container: {
    border: '1px solid #eee',
    display: 'flex',
    flexDirection: 'column',
    borderRadius: 5
  },
  title: {
    backgroundColor: '#eee',
    padding: '10px 0',
    paddingLeft: 20,
  },
  code: {
    flexGrow: 1
  }
})

export default defineComponent({
  props: {
    code: {
      type: String as PropType<string>,
      required: true
    },
    onChange: {
      type: Function as PropType<(value: string, event: Monaco.editor.IModelContentChangedEvent) => void>,
      required: true
    },
    title: {
      type: String as PropType<string>,
      required: true
    }
  },
  setup(props) {
    // must be shallowRef, if not, editor.getValue() won't work
    const editorRef = shallowRef()

    const containerRef = ref()

    let _subscription: Monaco.IDisposable | undefined
    let __prevent_trigger_change_event = false

    onMounted(() => {
      const editor = editorRef.value = Monaco.editor.create(containerRef.value, {
        value: props.code,
        language: 'json',
        formatOnPaste: true,
        tabSize: 2,
        minimap: {
          enabled: false,
        },
      })

      _subscription = editor.onDidChangeModelContent((event) => {
        console.log('--------->', __prevent_trigger_change_event)
        if (!__prevent_trigger_change_event) {
          props.onChange(editor.getValue(), event);
        }
      });
    })

    onBeforeUnmount(() => {
      if (_subscription)
        _subscription.dispose()
    })

    watch(() => props.code, (v) => {
      const editor = editorRef.value
      const model = editor.getModel()
      if (v !== model.getValue()) {
        editor.pushUndoStop();
        __prevent_trigger_change_event = true
        // pushEditOperations says it expects a cursorComputer, but doesn't seem to need one.
        model.pushEditOperations(
          [],
          [
            {
              range: model.getFullModelRange(),
              text: v,
            },
          ]
        );
        editor.pushUndoStop();
        __prevent_trigger_change_event = false
      }
      // if (v !== editorRef.value.getValue()) {
      //   editorRef.value.setValue(v)
      // }
    })

    const classesRef = useStyles()

    return () => {

      const classes = classesRef.value

      return (
        <div class={classes.container}>
          <div class={classes.title}><span>{props.title}</span></div>
          <div class={classes.code} ref={containerRef}></div>
        </div>
      )
    }
  }
})
```



#### 3.2、在App.tsx中引用组件

```tsx
import { defineComponent, Ref, ref } from "vue";

import { createUseStyles } from "vue-jss"; // 引入开源项目，用js写css

import MonacoEditor from "./components/MonacoEditor"; // 引入别人用monaco写的组件

const toJson = (params: any) => {
  return JSON.stringify(params, null, 2); // 保留格式的json转换
};

const schema = {
  // schema规则
  type: "string",
};

const useStyles = createUseStyles({
  // 写样式
  editor: {
    minHeight: 400,
  },
});

export default defineComponent({
  setup() {
    const schemaRef: Ref<any> = ref(schema); // 取得响应式的schema
    const handleCodeChange = (code: string) => {
      // 当在页面修改了code后会触发
      let schema: any;
      try {
        schema = JSON.parse(code); // 转换回来
      } catch (e) {}
      schemaRef.value = schema; // 转换回来
    };

    const classesRef = useStyles(); // 初始化

    return () => {
      const classes = classesRef.value;
      const code = toJson(schemaRef.value);
      return (
        <div id="app">
          <MonacoEditor
            code={code}
            onChange={handleCodeChange}
            title="Schema"
            class={classes.editor}
          />
        </div>
      );
    };
  },
});
```

效果如下

![image-20221114002648007](https://raw.githubusercontent.com/Rainchen0504/picture/master/202211140026645.png)

#### 3.3、注意⚠️

如果出现无法使用的报错信息即找不到对应的monaco插件，安装：

```shell
npm i monaco-editor-webpack-plugin
```

然后在vue.config.js中：

```js
const MonacoWebpackPlugin = require("monaco-editor-webpack-plugin");
module.exports = {
  chainWebpack(config) {
    config.plugin("monaco").use(new MonacoWebpackPlugin());
  },
};
```



# 三、vite项目

## 批量引入文件

### 1、vite构建工具

vite不支持require的引入方式，需要采用`import.meta.globEager(文件目录)`的方式。

```js
//其中*通配符代表此目录下的每一个文件
const files = import.meta.globEager("../components/*.js")
const modules = {};
for (const key in files) {
    if (Object.prototype.hasOwnProperty.call(files, key)) {
        modules[key.replace(/(\.\/|\.js)/g, '')] = files[key].default
    }
}
export default modules;
```



### 2、webpack构建工具

webpack就使用的require导入读取文件的方式注册`require.context(文件路径, 是否查找子目录, 匹配文件的正则)`

```js
const files = require.context('.', false, /\.ts$/);
const modules = {};
files.keys().forEach((key) => {
     if (key === './index.ts') { return; }
     modules[key.replace(/(\.\/|\.ts)/g, '')] = files(key).default;
});
export default modules;
```

