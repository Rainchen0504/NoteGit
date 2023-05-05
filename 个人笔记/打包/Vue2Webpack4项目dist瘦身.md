项目内容过大会导致构建速度慢，构建产物提及庞大等问题。

优化前项目大小207.8mb



### 1、字体文件大小优化

不推荐：

- eot：IE系列专属字体方案；
- ttf：兼容性好，但是文件较大；
- svg：不是真字体，存在很多兼容问题，已经被大部分浏览器抛弃；

推荐：

- woff：W3C标准，兼容性好；
- woff2：W3C标准，但是不兼容IE

优化效果：29.5mb --> 18.1mb



### 2、图片优化

- url-loader：转换图片为base64，减少文件请求次数，但是该方式适合较小的图片资源，如果图片很大又会导致构建出的`.js`文件变大，导致加载缓慢；
- image-webpack-loader：使用url-loader的limit属性控制指定大小图片转base64，然后用image-webpack-loader压缩超过limit阀值的大图片，即小图片转base64、大图片压缩到指定路径中；

```js
configureWebpack: {
  module: {
    rules: [
      {
        test: /\.(png|jpe?g|gif|webp)(\?.*)?$/i,
        exclude: /node_modules/,
        include: path.resolve('src'),
        use: [
          {
            loader: 'url-loader',
            options: {
              esModule: false,
              name: 'img/[name].[hash:2].[ext]',
              limit: 1024 * 12
            }
          },
          {
            loader: 'image-webpack-loader',
            options: {
              mozjpeg: { progressive: true, quality: 50 }, // 压缩JPEG图像
              optipng: { enabled: true }, // 压缩PNG图像
              pngquant: { quality: [0.5, 0.65], speed: 4 } // 压缩PNG图像
              gifsicle: { interlaced: false }, // 压缩GIF图像
              webp: { quality: 75 } // 压缩webp
            }
          }
        ]
      }
    ]
  }
}
```

优化效果：**dist包体积压缩提升60%**



### 3、前端开启Gzip压缩

- Compression-webpack-plugin（Gzip压缩）

nginx可以开启静态压缩和动态压缩，该插件可以打包文件压缩为.gz格式（最终发布生产环境时需要nginx配置开启gzip才能生效）

```js
configureWebpack: {
  plugins: [
    // gzip压缩
    new CompressionWebpackPlugin({
      test: /\.(js|css|json|txt|html|ico|svg)(\?.*)?$/i,
      threshold: 10240, //对10kb以上的数据进行压缩
      deleteOriginalAssets: true
    })
  ]
}
```

优化效果：207.8mb --> 56.8mb



### 4、nginx开启Gzip压缩

前端项目配置gzip压缩，删除原文件后部署访问会返回404，需要在nginx配置开启gzip

- nginx配置

  ```nginx
    #开启gzip功能
    gzip on; 
    #开启gzip静态压缩功能
    gzip_static on; 
    #gzip缓存大小
    gzip_buffers 4 16k;
    #gzip http版本
    gzip_http_version 1.1;
    #gzip 压缩级别 1-10 r
    gzip_comp_level 5;
    #gzip 压缩类型
    gzip_types text/plain application/javascript text/css application/xml text/javascript application/x-httpd-php image/jpeg image/gif image/png;
    #配置禁用gzip条件，支持正则。此处表示ie6及以下不启用gzip（因为ie低版本不支持
    gzip_disable   "MSIE [1-6]\.";
  ```

- gzip_static的作用

  nginx在设置了gzip on 后就已经打开了gzip压缩功能，不过这时候nginx所使用的是**动态压缩**。在动态压缩的情况下nginx会自动的将文件压缩成.gz文件，这时候就算不配置vue也能达到一样的效果。但是动态压缩无疑会占用服务器的资源来进行此操作。

  nginx也提供了**静态压缩模式**，也就是**gzip_static**，在这个模式下nginx会去寻找对应文件的.gz文件，只有.gz文件不存在的时候才会去压缩对应文件，这样就不会过度占用服务器资源。

观察请求`Content-Encoding：gzip`表示开启成功



### 5、合理使用缓存cache-loader

Webpack4还可以使用cache-loader实现和Webpack5相似的持久化缓存功能。cache-loader 缓存的是对应loader的处理结果，将结果缓存到磁盘里，显著提升二次构建速度。

用法是**在一些性能开销较大的 loader 之前添加此 loader**

```js
configureWebpack: {
  module: {
    rules: [
      {
        test: /\.(png|jpe?g|gif)(\?.*)?$/,
        use: [
          {
            loader: 'url-loader',
            options: {
              name: 'img/[name].[hash:6].[ext]',
              limit: 10 * 1024
            }
          },
          {
            loader: 'cache-loader'
          },
          {
            loader: 'image-webpack-loader'
          }
        ]
      }
    ]
  },
},
```

优化效果：打包速度来到了23s，相比上面的的250s **打包速度提升了约90%**