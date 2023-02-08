# 1、判断图片是否进入可视区域

1. `window.innerHeight`是浏览器可视区域的高度
2. `document.body.scrollTop || document.documentElement.scrollTop`是浏览器滚动过的距离
3. `img.offsetTop`是元素顶部距离文档顶部的高度，包括滚动条的距离
4. 内容到达显示区域的条件是`img.offsetTop < window.innerHeight + document.body.scrollTop`

![image-20230109173631164](https://raw.githubusercontent.com/Rainchen0504/picture/master/202301091736437.png)



# 2、大文件上传（切片上传加断点续传）

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

### （2）后端部分

接收每一个切割文件，并在接收成功后，存储到指定位置，并告诉前端接收成功

收到合并信号后，将所有的切割文件排序，合并，生成最终的大文件，然后删除掉切割的小文件，并返回前端大文件地址

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



## 2、将文件转为二进制

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



## 3、大文件切片

获取到二进制后就进行切块分片。但是考虑到大文件的合并，切片文件必须有规律方便合并，为了避免同一个文件（改名）多次上传，引入`spark-md5`根据具体内容生成hash值

```json
// 每一个切片命名都改成了`hash-1、hash2`这种形式。
const buffer = await this.filepParse(file,'buffer')
const sparkMD5 = new SparkMD5.ArrayBuffer()
sparkMD5.append(buffer)
this.hash = sparkMD5.end()
```

分割大文件的时候，可以采用**定切片数量**，**定切片大小**两种方式，这里采用了定切片数量举例：

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



## 4、创建切片请求

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



## 6、断点续传

点击暂停的时候，停止上传。点击继续上传就上传剩下的请求

```js
if (res.data.code === 0) {
    this.count += 1;
    // 传完的切片我们把它移除掉
    this.partList.splice(index, 1);
}
```

如果上传成功，就将其从请求数组中剥离出去，就可以保证剩下的就是待上传的。