原生ajax实现：前端向后端异步取数据而无需刷新页面的技术

```js
//1、创建ajax核心
let xmlhttp = new XMLHttpRequest();
//2、创建请求（get/post 请求地址 请求异步）
xmlhttp.open("get","my.php?user=982395099&psd=123456",true);
//3、发送请求参数，key=value形式的字符串，多个参数用&连接。
//其中get请求请求参数不能写在send里面，要写在请求地址后面，以？连接，send里传null
xmlhttp.send(null);
//如果请求方式为post，需要设置请求头
//xmlhttp.setRequestHeader("Content-Type","application/x-www-form-urlencoded")
//4、接收响应
xmlhttp.onreadystatechange=function(){
  //readyState (请求的状态) 1(尚未初始化) 2(正在请求) 3(正在响应) 4(响应完毕)
  // status(服务端返回的状态码) 404 500 200(ok) 301 304
  if(xmlhttp.readyState==4&&xmlhttp.status==200){
    //responseText,可以获取服务器端返回的文本格式的数据
    var data=xmlhttp.responseText
    }
}
```

