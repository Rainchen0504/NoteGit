# 一、nginx代理配置

<img src="https://raw.githubusercontent.com/Rainchen0504/picture/master/202303051622860.png" alt="image-20230305162239487" style="zoom: 50%;" />

server配置项：

1. listen是监听的端口
2. Server_name是监听的主机名
3. location表示服务器上文件入口地址



# 二、判断图片是否进入可视区域

1. `window.innerHeight`是浏览器可视区域的高度
2. `document.body.scrollTop || document.documentElement.scrollTop`是浏览器滚动过的距离
3. `img.offsetTop`是元素顶部距离文档顶部的高度，包括滚动条的距离
4. 内容到达显示区域的条件是`img.offsetTop < window.innerHeight + document.body.scrollTop`

![image-20230109173631164](https://raw.githubusercontent.com/Rainchen0504/picture/master/202301091736437.png)



# 三、大文件上传（切片上传加断点续传）

## 1、思路

### （1）前端部分

#### 1.1、大文件上传：

- 将大文件转换成二进制流的形式
- 利用流可以切割的属性，将二进制流切割成多份
- 组装和分割块同等数量的请求块，并行（采用web worker）或串行的形式发出请求
- 待监听到所有的请求都发送成功后，给服务器端发送一个合并信号

#### 1.2、断点续传

- 为每个文件切割块添加不同的标识
- 上传成功的部分记录上成功的标识
- 当暂停或发送失败后，可以重新发送没有上传成功的切割文件



```vue
<script>
  methods: {
    // 提交文件后触发
    changeFile () {
      this.filepParse()
      this.createSendQeq()
      this.sendQeq()
      this.mergeUpload
    },
    // 将文件变成二进制，方便后续分片
    filepParse () {},
    // 创建切片请求
    createSendQeq () {},
    // 将每一个切片 并行/串行 的方式发出
    sendQeq () {},
    // 发送代码合并请求
    mergeUpload () {}
  }
</script>
```



### （2）后端部分

接收每一个切割文件，并在接收成功后，存储到指定位置，并告诉前端接收成功

收到合并信号后，将所有的切割文件排序，合并，生成最终的大文件，然后删除掉切割的小文件，并返回前端大文件地址



## 2、实现方法

### （1）搭建页面

使用`element-ui`搭建页面，使用`el-upload`组件

```vue
<template>
  <div id="app">
    <!-- 上传组件 -->
    <el-upload action drag :auto-upload="false" :show-file-list="false" :on-change="handleChange">
      <i class="el-icon-upload"></i>
      <div class="el-upload__text">将文件拖到此处，或<em>点击上传</em></div>
      <div class="el-upload__tip" slot="tip">大小不超过 200M 的视频</div>
    </el-upload>

    <!-- 进度显示 -->
    <div class="progress-box">
      <span>上传进度：{{ percent.toFixed() }}%</span>
      <el-button type="primary" size="mini" @click="handleClickBtn">{{ upload | btnTextFilter}}</el-button>
    </div>

    <!-- 展示上传成功的视频 -->
    <div v-if="videoUrl">
      <video :src="videoUrl" controls />
    </div>
  </div>
</template>
```



### （2）将文件转为二进制

#### 2.1、方式一

js常见二进制格式有Blob和ArrayBuffer，这里转换成ArrayBuffer的格式。因为解析的过程时间较长，采用promise异步处理方式

```js
filepParse(file, type) {
  const caseType = {
    'base64': 'readAsDataURL',
    'buffer': 'readAsArrayBuffer'
  }
  const fileRead = new FileReader()
  return new Promise(resolve => {
    fileRead[caseType[type]](file)
    fileRead.onload = (res) => {
      resolve(res.target.result)
    }
  })
}
```



#### 2.2、方式二（匹配页面）

转成 ArrayBuffer 是因为后面要用 SparkMD5 这个库生成 hash 值，对文件进行命名

```js
async handleChange (file) {
  const fileObj = file.raw
  try {
    const buffer = await this.fileToBuffer(fileObj)
  } catch (e) {
    console.log(e)
  }
}
```

`before-upload` 函数和 `on-change` 函数的参数都有 `file`，但是 `on-change` 中的 `file` 不是 `File` 对象，要获取 `File` 对象需要通过 `file.raw`。 这里用到 FileReader 类将 File 对象转 ArrayBuffer 对象，因为是异步过程，所以用 Promise 封装:

```js
fileToBuffer (file) {
  return new Promise ((resolve, reject) => {
    const fr = new FileReader()
    fr.onload = e => {
      resolve(res.target.result)
    }
    fr.readAsArrayBuffer(file)
    fr.onerror = () => {
      reject(new Error('转换文件格式发生错误'))
    }
  })
}
```



## 3、大文件切片

#### 3.1、方式一

获取到二进制后就进行切块分片。但是考虑到大文件的合并，切片文件必须有规律方便合并，为了避免同一个文件（改名）多次上传，引入`spark-md5`根据具体内容生成hash值

```js
// 每一个切片命名都改成了`hash-1、hash2`这种形式。
const buffer = await this.filepParse(file,'buffer')
const sparkMD5 = new SparkMD5.ArrayBuffer()
sparkMD5.append(buffer)
this.hash = sparkMD5.end()
```

分割大文件的时候，可以采用<font color=deepred>**定切片数量**</font>，**定切片大小**两种方式，这里采用了定切片数量举例：

```js
const partSize = file.size / 10
let current = 0
const partList = []
for (let i = 0 ;i < 10 ;i++) {
  let reqItem = {
    chunk: file.slice(current, current + partSize),
    filename: `${this.hash}_${i}.${suffix}`
  }
  current += partSize
  partList.push(reqItem)
}
this.partList = partList
```

大文件切割完成后，将切割的数据存储到一个数组变量中



#### 3.2、方式二（匹配页面）

切片文件可以通过固定大小或固定数量把一个文件分成好几个部分，为了避免由于js使用的IEEE745二进制浮点数计算标准可能导致的误差，这里采用<font color=deepred>**定切片大小**</font>的方式对文件进行切割，设定每个切片的大小为2M，2M = 21024KB = 21024*1024B = 2097152B。切割文件用到的是`Blob.slice()`。

```js
const chunkSize = 2097152, //切片大小
      chunkList = [], //保存所有切片数组
      chunkListLength = Math.ceil(fileObj.size / chunkSize), //计算总共多少个切片
      suffix = /\.([0-9A-z]+)$/.exec(fileObj.name)[1] //文件名后缀

//根据文件内容生成hash值
const spark = new SparkMD5.ArrayBuffer()
spark.append(buffer)
const hash = spark.end()

//生成切片，这里根据后端要求传递参数为字节数据块（chunk）和每个数据块的文件名（fileName）
let curChunk = 0 //切片时的位置
for (let i = 0; i < chunkListLength; i++) {
  const item = {
    chunk: fileObj.slice(curChunk, curChunk + chunkSize),
    fileName: `${hash}_${i}.${suffix}` // 文件名规则按照 hash_1.jpg 命名
  }
  curChunk += chunkSize
  chunkList.push(item)
}
```



## 4、创建切片请求

#### 4.1、方式一

发送的数据采用的是`FormData`数据格式

```js
createSendQeq() {
  const reqPartList = []
  this.partList.forEach((item,index) => {
    const reqFn = () => {
      const formData = new FormData();
      formData.append("chunk", item.chunk);
      formData.append("filename", item.filename);
      return axios.post("/upload",formData,{
        headers: {"Content-Type": "multipart/form-data"}
      }).then(res => {
        console.log(res)
      })
    }
    reqPartList.push(reqFn)
  })
  return reqPartList
}
```



## 5、串行/并行请求

#### 5.1、方式一

目前切片完成，并且请求也已经包装好了，采用串行或并行请求发送数据，下面用串行举例：

每成功的发出去一个请求，对应的下标就加一证明发送成功。当 i 下标和切片数相同的时候，默认发送成功，触发合并（merge）请求

```js
sendQeq() {
  const reqPartList = this.createSendQeq()
  let i  = 0 
  let send = async () => {
    if (i >= reqPartList.length) { 
      // 上传完成
      this.mergeUpload()
      return 
    }
    await reqPartList[i]()
    i++
    send()
  }
  send()
}
```

缺点是没有串行快



#### 5.2、方式二

使用串行举例，每个切片都新建一个请求，为了能实现断点续传，将请求封装到函数 `fn` 里，用一个数组 `requestList` 来保存请求集合，然后封装一个 `send` 函数，用于请求发送，这样一旦按下暂停键，可以方便的终止上传

```js
sendRequest() {
  const requestList = [] // 请求集合
  this.chunkList.forEach(item => {
    const fn = () => {
      const formData = new FormData()
      formData.append('chunk', item.chunk)
      formData.append('filename', item.fileName)
      return axios({
        url: '请求地址',
        method: 'post',
        headers: { 'Content-Type': 'multipart/form-data' },
        data: formData
      }).then(res => {
        if (res.data.code === 0) { // 成功
          if (this.percentCount === 0) {
            this.percentCount = 100 / this.chunkList.length
          }
          this.percent += this.percentCount // 改变进度
          his.chunkList.splice(index, 1) // 一旦上传成功就删除这一个 chunk，方便断点续传
        }
      })
    }
    requestList.push(fn)
  })
  
  let i = 0 // 记录发送的请求个数
  const send = async () => {
    // if ('暂停') return
    if (i >= requestList.length) {
      // 发送完毕
      return
    } 
    await requestList[i]()
    i++
    send()
  }
  send() // 发送请求
}
```



## 6、切片发送成功

根据后端接口需要再发送一个 get 请求并把文件的 hash 值传给服务器，定义一个 `complete` 方法来实现，这里假定发送的为视频文件：

```js
const complete = () => {
  axios({
    url: '/merge',
    method: 'get',
    params: { hash: this.hash }
  }).then(res => {
    if (res.data.code === 0) { // 请求发送成功
      this.videoUrl = res.data.path
    }
  })
}
```

这样就能在文件发送成功后在页面浏览到发送的视频文件了



## 7、断点续传

#### 7.1、方式一

点击暂停的时候，停止上传。点击继续上传就上传剩下的请求

```js
if (res.data.code === 0) {
    this.count += 1;
    // 传完的切片我们把它移除掉
    this.partList.splice(index, 1);
}
```

如果上传成功，就将其从请求数组中剥离出去，就可以保证剩下的就是待上传的。



#### 7.2、方式二

暂停按钮文字的处理，用了一个过滤器，如果 `upload` 值为 `true` 则显示“暂停”，否则显示“继续”：

```js
filters: {
  btnTextFilter(val) {
    return val ? '暂停' : '继续'
  }
}
```

当按下暂停按钮，触发 `handleClickBtn` 方法

```js
handleClickBtn() {
  this.upload = !this.upload
  // 如果不暂停则继续上传
  if (this.upload) this.sendRequest()
}
```

在发送切片的 `send` 方法的开头添加 `if (!this.upload) return`，这样只要 `upload` 这个变量为 `false` 就不会继续上传了。为了在暂停完后可以继续发送，需要在每次成功发送一个切片后将这个切片从 `chunkList` 数组里删除 `this.chunkList.splice(index, 1)`



方式二代码汇总

```vue
<template>
  <div id="app">
    <!-- 上传组件 -->
    <el-upload action drag :auto-upload="false" :show-file-list="false" :on-change="handleChange">
      <i class="el-icon-upload"></i>
      <div class="el-upload__text">将文件拖到此处，或<em>点击上传</em></div>
      <div class="el-upload__tip" slot="tip">大小不超过 200M 的视频</div>
    </el-upload>

    <!-- 进度显示 -->
    <div class="progress-box">
      <span>上传进度：{{ percent.toFixed() }}%</span>
      <el-button type="primary" size="mini" @click="handleClickBtn">{{ upload | btnTextFilter}}</el-button>
    </div>

    <!-- 展示上传成功的视频 -->
    <div v-if="videoUrl">
      <video :src="videoUrl" controls />
    </div>
  </div>
</template>

<script>
  import SparkMD5 from "spark-md5"
  import axios from "axios"
  
  export default {
    name: 'App3',
    filters: {
      btnTextFilter(val) {
        return val ? '暂停' : '继续'
      }
    },
    data() {
      return {
        percent: 0,
        videoUrl: '',
        upload: true,
        percentCount: 0
      }
    },
    methods: {
      async handleChange(file) {
        if (!file) return
        this.percent = 0
        this.videoUrl = ''
        // 获取文件并转成 ArrayBuffer 对象
        const fileObj = file.raw
        let buffer
        try {
          buffer = await this.fileToBuffer(fileObj)
        } catch (e) {
          console.log(e)
        }
        
        // 将文件按固定大小（2M）进行切片，注意此处同时声明了多个常量
        const chunkSize = 2097152,
          chunkList = [], // 保存所有切片的数组
          chunkListLength = Math.ceil(fileObj.size / chunkSize), // 计算总共多个切片
          suffix = /\.([0-9A-z]+)$/.exec(fileObj.name)[1] // 文件后缀名
          
        // 根据文件内容生成 hash 值
        const spark = new SparkMD5.ArrayBuffer()
        spark.append(buffer)
        const hash = spark.end()

        // 生成切片，这里后端要求传递的参数为字节数据块（chunk）和每个数据块的文件名（fileName）
        let curChunk = 0 // 切片时的初始位置
        for (let i = 0; i < chunkListLength; i++) {
          const item = {
            chunk: fileObj.slice(curChunk, curChunk + chunkSize),
            fileName: `${hash}_${i}.${suffix}` // 文件名规则按照 hash_1.jpg 命名
          }
          curChunk += chunkSize
          chunkList.push(item)
        }
        this.chunkList = chunkList // sendRequest 要用到
        this.hash = hash // sendRequest 要用到
        this.sendRequest()
      },
      
      // 发送请求
      sendRequest() {
        const requestList = [] // 请求集合
        this.chunkList.forEach((item, index) => {
          const fn = () => {
            const formData = new FormData()
            formData.append('chunk', item.chunk)
            formData.append('filename', item.fileName)
            return axios({
              url: '/single3',
              method: 'post',
              headers: { 'Content-Type': 'multipart/form-data' },
              data: formData
            }).then(res => {
              if (res.data.code === 0) { // 成功
                if (this.percentCount === 0) { // 避免上传成功后会删除切片改变 chunkList 的长度影响到 percentCount 的值
                  this.percentCount = 100 / this.chunkList.length
                }
                this.percent += this.percentCount // 改变进度
                this.chunkList.splice(index, 1) // 一旦上传成功就删除这一个 chunk，方便断点续传
              }
            })
          }
          requestList.push(fn)
        })
        
        let i = 0 // 记录发送的请求个数
        // 文件切片全部发送完毕后，需要请求 '/merge' 接口，把文件的 hash 传递给服务器
        const complete = () => {
          axios({
            url: '/merge',
            method: 'get',
            params: { hash: this.hash }
          }).then(res => {
            if (res.data.code === 0) { // 请求发送成功
              this.videoUrl = res.data.path
            }
          })
        }
        const send = async () => {
          if (!this.upload) return
          if (i >= requestList.length) {
            // 发送完毕
            complete()
            return
          } 
          await requestList[i]()
          i++
          send()
        }
        send() // 发送请求
      },
      
      // 按下暂停按钮
      handleClickBtn() {
        this.upload = !this.upload
        // 如果不暂停则继续上传
        if (this.upload) this.sendRequest()
      },
      
      // 将 File 对象转为 ArrayBuffer 
      fileToBuffer(file) {
        return new Promise((resolve, reject) => {
          const fr = new FileReader()
          fr.onload = e => {
            resolve(e.target.result)
          }
          fr.readAsArrayBuffer(file)
          fr.onerror = () => {
            reject(new Error('转换文件格式发生错误'))
          }
        })
      }
    }
  }
</script>

<style scoped>
  .progress-box {
    box-sizing: border-box;
    width: 360px;
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-top: 10px;
    padding: 8px 10px;
    background-color: #ecf5ff;
    font-size: 14px;
    border-radius: 4px;
  }
</style>
```



