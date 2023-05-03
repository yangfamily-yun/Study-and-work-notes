

















# 安装nodemon

```
npm install -g nodemon
```

解决每修改一次node.js就要重开服务器的问题

以管理员身份启动服务端：nodemon.cmd server.js

[TOC]



# 一、全局刷新和局部刷新

####  （1）全局刷新

全局刷新：整个浏览器被新的数据覆盖。 在网络中传输大量的数据。 浏览器需要重新加载，渲染页面。

登录请求处理：index.jsp 发起登录请求--------LoginServlet--------result.jsp
**发起请求 request  阶段***： 
浏览器现在内存中是 index 页面的内容和数据 ：

![image-20220428135518314](C:\Users\ydy\AppData\Roaming\Typora\typora-user-images\image-20220428135518314.png)

**服务器端应答结果阶段：**
sevlet 返回后把数据全部覆盖掉原来 index 页面内容， result.jsp 覆盖了全部的浏览器内存数据。 整个浏览器数据全部被刷新。重新在浏览器窗口显示数据，样式，标签等

![image-20220428135622905](C:\Users\ydy\AppData\Roaming\Typora\typora-user-images\image-20220428135622905.png)

##### **全局刷新原理**

1) 必须由浏览器亲自向服务端发送请求协议包。
2) 这个行为导致服务端直接将【响应包】发送到浏览器内存中
3) 这个行为导致浏览器内存中原有内容被覆盖掉
4) 这个行为导致浏览器在展示数据时候，只有响应数据可以展示

####  （2）局部刷新

局部刷新： 在浏览器的内部，发起请求，获取数据，改变页面中的部分内容。其余的页面无需加载和渲染。在网络中数据传输量少，提高网页加载速度，实现按需加载（懒加载） 给用户的感受好。

浏览器在展示数据时，此时在窗口既可以看到本次的响应数据， 同时又可以看到浏览器内存中原有数据

##### 局部刷新原理

1. 不能由浏览器发送请求给服务端

2. 浏览器委托浏览器内存中一个脚本对象代替浏览器发送请求.

3. 这个行为导致导致服务端直接将【响应包】发送脚本对象内存中

4. 这个行为导致脚本对象内容被覆盖掉，但是此时浏览器内存中绝大部分内容没有收
    到任何影响.

5. 这个行为导致浏览器在展示数据时候,同时展示原有数据和响应数据

   ![image-20220428135832054](C:\Users\ydy\AppData\Roaming\Typora\typora-user-images\image-20220428135832054.png)

##### 局部刷新的核心对象

局部刷新的**核心对象**是 **异步对象**（XMLHttpRequest）
 这个异步对象是存在浏览器内存中的 ，使用javascript语法创建和使用XMLHttpRequest对象。

##### 异步请求对象

在局部刷新，需要创建一个对象，代替浏览器发起请求的行为，这个对象存在浏览器内存中。代替浏览器发起请求并接收响应数据。这个对象叫做异步请求对象。

这个异步对象用于在后台与服务器交换数据。XMLHttpRequest 就是我们说的异步对象。
XMLHttpRequest 对象能够：
•  在不重新加载页面的情况下更新网页

•  在页面已加载后向服务器请求数据
•  在页面已加载后从服务器接收数据
所有现代浏览器 (IE7+、Firefox、Chrome、Safari 以及 Opera) 都内建了 XMLHttpRequest对象。通过一行简单的 JavaScript 代码，我们就可以创建 XMLHttpRequest 对象
创建 XMLHttpRequest 对象的语法（xhr）：

```
var xmlhttp=new XMLHttpRequest();
```

#### （3）全局刷新与异步刷新的区别

1、原理不同（具体见上）

2、全局刷新是同步行为， 局部刷新是异步行为[浏览器数据没有全部更新]

# 二、HTTP

HTTP（hypertext transport protocol）协议『超文本传输协议』，协议详细规定了浏览器和万维网服务器之间互相通信的规则。
约定, 规则

### 1. 请求报文

重点是格式与参数

```
行      POST（请求方式）  /s?ie=utf-8（url）  HTTP/1.1 （协议版本）
头      Host: atguigu.com
        Cookie: name=guigu
        Content-type: application/x-www-form-urlencoded(请求体类型)
        User-Agent: chrome 83
空行
体      username=admin&password=admin（如果是get请求，体就是空的，如果是post请求，体可以不为空）
```

### 2.响应报文

```
行      HTTP/1.1  200（响应状态码）  OK（响应状态字符串）
头      Content-Type: text/html;charset=utf-8
        Content-length: 2048
        Content-encoding: gzip
空行    
体      <html>       主要的返回结果
            <head>
            </head>
            <body>
                <h1>尚硅谷</h1>
            </body>
        </html>
```

* 404 找不到
* 403 禁止
* 401 未授权
* 500 内部错误
* 200

# 三、Ajax

无刷新的获取数据

ajax是一种做局部刷新的新方法（2003左右），不是一种语言。 ajax包含的技术主要有javascript,dom,css, xml等等。 核心是javascript 和 xml 。

ajax:Asynchronous JavaScript and XML（异步的 JavaScript 和 XML）。

  Asynchronous: 异步的意思
  JavaScript：javascript脚本，在浏览器中执行。负责创建异步对象， 发送请求， 更新页面的dom对象。 ajax请求需要服务器端的数据。
  and : 和
  xml :  网络中的传输的数据格式。现在已经被 JSON 取代了。

​             XML 可扩展标记语言。
​             XML 被设计用来传输和存储数据。HTML 展示数据
​             XML 和 HTML 类似，不同的是 HTML 中都是预定义标签，而 XML 中没有预定义标签，全都是自定义标签，用来表示一些数据

```
   <数据>
      <数据1>宝马1</数据1>
  <数据2>宝马2</数据2>
  <数据3>宝马3</数据3>
  <数据4>宝马4</数据4>
  </数据>
```

####  2.1 ajax的核心以及核心对象

Ajax的核心是用来做局部刷新的。

AJAX /局部刷新的核心对象是 **异步对象**（XMLHttpRequest）

#### 2.2 Ajax的特点

##### 2.2.1 优点

1) 可以无需刷新页面与服务器端进行通信。（网页不刷新的情况下，发送HTTP请求）
2) 允许你根据用户事件来更新部分页面内容。

##### 2.2.2 缺点

1) 没有浏览历史，不能回退
2) 存在跨域问题（Ajax默认同源）
3) SEO （搜索引擎优化）不友好---》不能爬虫

#### 2.3 ajax中使用XMLHttpRequest对象

#####  2.3.1 创建异步对象

```
 var xmlHttp = new XMLHttpRequest();
```

##### 2.3.2 初始异步请求对象以及添加url参数

异步对象的方法open().
  xmlHttp.open(请求方式get|post, "服务器端的访问地址", 同步|异步请求（默认是true，异步请求）)

```
  //请求行信息（请求类型   url   HTTP协议版本）
  xmlHttp.open("get", "loginServlet?name=zs&pwd=123",true);
```

##### 2.3.3 Ajax设置自定义/预定义 请求头(可有可无)

**注意：设置请求头必须在open后，send以前**

客户端：

```
  //Content-Type：请求体内容类型   值固定
  xhr.setRequestHeader('Content-Type', 'application/x-www-form-urlencoded');//预定义请求头，固定的
  xhr.setRequestHeader('name', 'atguigu');//自定义请求头
```

自定义请求头如果没有在服务端声明，浏览器的安全机制就会提醒报错

服务端：

```
//all：可以接收任意类型的请求（解决option请求预检测，否则请求依然会报错，option请求就是一次效验（检查请求头），后面还有一次才是真正的请求
app.all('/server', (request, response) => {
    //设置响应头  设置允许跨域
    response.setHeader('Access-Control-Allow-Origin', '*');
    //响应头 
    response.setHeader('Access-Control-Allow-Headers', '*');//与自定义请求头相配合
    //设置响应体
    response.send('HELLO AJAX POST');
});

```

###### 注意：

自定义请求头要传英文不能传中文，不然会报错，要使用base64编码啥的

左边headers了还不显示请求头的 右边servers可以加上一行response.setHeader(‘A***w—Method’,’*’);

###### 总结：

如果需要添加自定义请求头，则需要（1）在服务端添加response.setHeader('Access-Control-Allow-Headers', '*');（2）同时还要解决option请求预检测---》路由规则应为 app.all

##### 2.3.4 使用异步对象发送请求

```
  //请求体信息（Get请求没有请求体，只有post请求可以有请求体）
  xmlHttp.send()
```

  获取服务器端返回的数据， 使用异步对象的属性 responseText .
  使用例子：

```
  xmlHttp.responseText 
```

  回调：当请求的状态变化时，异步对象会自动调用onreadystatechange事件对应的函数。

##### 2.3.5 给异步对象绑定事件

onreadystatechange ：当异步对象发起请求或获取了数据都会触发这个事件。这个事件需要指定一个函数， 在函数中处理状态的变化。

    例如1：
    xmlHttp.onreadystatechange= function(){
       处理请求的状态变化。
    	 if(xmlHttp.readyState == 4 && xmlHttp.status== 200 ){
           //网络请求成功并且异步对象把接收的数据处理完成后，更新当前页面
    		  var data = xmlHttp.responseText;
    		  document.getElementById("name").value= data;
    	 }
    }
    （1）创建异步对象 （2）给异步对象绑定事件（3）初始异步请求对象（4）使用异步对象发送请求
      为什么这里的三四步在第二部之后？
      回调：当请求的状态变化时，异步对象会自动调用onreadystatechange事件对应的函数。
    
    例如2：
    //获取button元素
            const btn = document.getElementsByTagName('button')[0];
            const result = document.getElementById("result");
            //绑定事件
            btn.onclick = function(){
                //1. 创建对象
                const xhr = new XMLHttpRequest();
                //2. 初始化 设置请求方法和 url
                xhr.open('GET', 'http://127.0.0.1:8000/server?a=100&b=200&c=300');
                //3. 发送
                xhr.send();
                //4. 事件绑定 处理服务端返回的结果
                // on  when 当....时候
                // readystate 是 xhr 对象中的属性, 表示状态 0 1 2 3 4
                // change  改变
                xhr.onreadystatechange = function(){
                    //判断 (服务端返回了所有的结果)
                    if(xhr.readyState === 4){
                        //判断响应状态码 200  404  403 401 500
                        // 2xx 成功
                        if(xhr.status >= 200 && xhr.status < 300){
                            //处理结果  行 头 空行 体
                            //响应 
                            // console.log(xhr.status);//状态码
                            // console.log(xhr.statusText);//状态字符串
                            // console.log(xhr.getAllResponseHeaders());//所有响应头
                            // console.log(xhr.response);//响应体
                            //设置 result 的文本
                            result.innerHTML = xhr.response;
                        }else{
    
                        }
                    }
                }
            }
       （1）给元素绑定事件（2）在事件的处理函数里创建异步对象 （3）初始异步请求对象（4）使用异步对象发送请求（5）给异步对象绑定事件
     异步对象的属性 readyState： 表示异步对象请求的状态变化
     0：创建异步对象时未初始化， new XMLHttpRequest();
     1: 初始异步请求对象， xmlHttp.open()
     2：发送请求， xmlHttp.send()
     3: 从服务器端获取了数据，此时3， 注意3是异步对象内部使用， 获取了原始的数据。
     4：异步对象把接收的数据处理完成后。 此时开发人员在4的时候处理数据。
        在4的时候，开发人员做什么 ？  更新当前页面。
    
    异步对象的status属性：表示网络请求的状况的，  200（请求成功）， 404（没访问到服务器）， 500（服务端代码出错）， 需要是当status==200
     时，表示网络请求是成功的。

**xhr.status**：状态码
**xhr.statusText**：状态字符串
**xhr.getAllResponseHeaders()**)：所有响应头
**xhr.response**：响应体

##### 2.3.5 服务端-设置响应头、响应体

(1)在终端初始化--- npn init --yes    (2)下载express---- npm i express

 **response.setHeader**:设置响应头  设置允许跨域 
 **response.send**:设置响应体

```
//1. 引入express
const express = require('express');
//2. 创建应用对象
const app = express();
//3. 创建路由规则
// request 是对请求报文的封装
// response 是对响应报文的封装
//这是get请求
app.get('/server', (request, response) => {
    //设置响应头  设置允许跨域
    response.setHeader('Access-Control-Allow-Origin', '*');
    //设置响应体
    response.send('HELLO AJAX - 2');
});
//4. 监听端口启动服务
app.listen(8000, () => {
    console.log("服务已经启动, 8000 端口监听中....");
});
```

#### 2.4 Ajax发送get请求

设置get请求行参数

```
 xmlHttp.open("get", "loginServlet?name=zs&pwd=123",true);
```

 在url后添加 ？ 在通过键值对的方式添加参数，若有多个参数，则用 & 连接

**注意：get请求没有请求体，只有post请求可以有请求体**

#### 2.5 Ajax发送post请求

客户端：

```
     //获取元素对象
        const result = document.getElementById("result");
        //绑定事件
        result.addEventListener("mouseover", function () {
            //1. 创建对象
            const xhr = new XMLHttpRequest();
            //2. 初始化 设置类型与 URL
            xhr.open('POST', 'http://127.0.0.1:8000/server');
            //设置请求头
            xhr.setRequestHeader('Content-Type', 'application/x-www-form-urlencoded');
            xhr.setRequestHeader('name', 'atguigu');
            //3. 携带请求体发送（get请求没有请求体，只有post请求可以有请求体）
            xhr.send('a=100&b=200&c=300');
            // xhr.send('a:100&b:200&c:300');
            // xhr.send('1233211234567');

            //4. 事件绑定
            xhr.onreadystatechange = function () {
                //判断
                if (xhr.readyState === 4) {
                    if (xhr.status >= 200 && xhr.status < 300) {
                        //处理服务端返回的结果
                        result.innerHTML = xhr.response;
                    }
                }
            }
        });
```

服务端：

```
//这是post请求
app.post('/server', (request, response) => {
    //设置响应头  设置允许跨域
    response.setHeader('Access-Control-Allow-Origin', '*');
    //设置响应体
    response.send('HELLO AJAX - post');
});
```

##### 2.5.1 设置post请求体参数

```
 xhr.send('a=100&b=200&c=300');
// xhr.send('a:100&b:200&c:300');
// xhr.send('1233211234567');
```

4.json使用

  ajax发起请求-------servlet（返回的一个json格式的字符串 { name:"河北", jiancheng:"冀","shenghui":"石家庄"}）
  json分类：1. json对象 ，JSONObject ,这种对象的格式   名称:值， 也可以看做是 key:value 格式。
            2. json数组， JSONArray, 基本格式  [{ name:"河北", jiancheng:"冀","shenghui":"石家庄"} , { name:"山西", jiancheng:"晋","shenghui":"太原"} ]

  为什么要使用json ：
    1. json格式好理解
	 2. json格式数据在多种语言中，比较容易处理。 使用java， javascript读写json格式的数据比较容易。
	 3. json格式数据他占用的空间下，在网络中传输快， 用户的体验好。

  处理json的工具库： gson（google）； fastjson（阿里），jackson， json-lib

  在js中的，可以把json格式的字符串，转为json对象， json中的key，就是json对象的属性名。

### 3、处理响应数据

#### 3.1 自动转换

服务端发送的响应体内容必须是字符串格式。如果内容是字符串，需要通过JSON.stringify(data)转为格式的字符串作为响应体

服务端：

```
//JSON 响应
app.all('/json-server', (request, response) => {
    //设置响应头  设置允许跨域
    response.setHeader('Access-Control-Allow-Origin', '*');
    //响应头
    response.setHeader('Access-Control-Allow-Headers', '*');
    //响应一个数据
    const data = {
        name: 'atguigu'
    };
    //对对象进行字符串转换
    let str = JSON.stringify(data);
    //设置响应体
    response.send(str);//发送的只能是字符串
});
```

客户端：

```
 const result = document.getElementById('result');
        //绑定键盘按下事件
        window.onkeydown = function(){
            //发送请求
            const xhr = new XMLHttpRequest();
            //设置响应体数据的类型
            xhr.responseType = 'json';
            //初始化
            xhr.open('GET','http://127.0.0.1:8000/json-server');
            //发送
            xhr.send();
            //事件绑定
            xhr.onreadystatechange = function(){
                if(xhr.readyState === 4){
                    if(xhr.status >= 200 && xhr.status < 300){
                        // 2. 自动转换
                        console.log(xhr.response);
                        result.innerHTML = xhr.response.name;
                    }
                }
            }
        }
```

#### 3.2 手动转换

客户端：

```
  //事件绑定
            xhr.onreadystatechange = function(){
                if(xhr.readyState === 4){
                    if(xhr.status >= 200 && xhr.status < 300){
                        //
                        // console.log(xhr.response);
                        // result.innerHTML = xhr.response;
                        // 1. 手动对数据转化
                        // let data = JSON.parse(xhr.response);
                        // console.log(data);
                        // result.innerHTML = data.name;
                    }
                }
            }
```

### 4、ie缓存问题

异步对象初始化时，为传入的url添加时间戳，这样每次发起请求的路径都是不同的，每次请求就能得到相应的的对象

```
script>
        const btn = document.getElementsByTagName('button')[0];
        const result = document.querySelector('#result');
        btn.addEventListener('click', function(){
            const xhr = new XMLHttpRequest();
            xhr.open("GET",'http://127.0.0.1:8000/ie?t='+Date.now());
            xhr.send();
            xhr.onreadystatechange = function(){
                if(xhr.readyState === 4){
                    if(xhr.status >= 200 && xhr.status< 300){
                        result.innerHTML = xhr.response;
                    }
                }
            }
        })
```

### 5、取消Ajax请求

#### 5.1 Ajax请求超时和网络异常处理---（自动）

时间到了之后，自动请求Ajax请求

```
   btn.addEventListener('click', function(){
            const xhr = new XMLHttpRequest();
            //超时设置 2s 设置
            xhr.timeout = 2000;
            //超时回调
            xhr.ontimeout = function(){
                alert("网络异常, 请稍后重试!!");
            }
            //网络异常回调
            xhr.onerror = function(){
                alert("你的网络似乎出了一些问题!");
            }
            xhr.open("GET",'http://127.0.0.1:8000/delay');
            xhr.send();
            xhr.onreadystatechange = function(){
                if(xhr.readyState === 4){
                    if(xhr.status >= 200 && xhr.status< 300){
                        result.innerHTML = xhr.response;
                    }
                }
            }
        })
```

#### 5.2 手动取消Ajax请求

通过异步对象的abort方法

```
script>
        //获取元素对象
        const btns = document.querySelectorAll('button');
        let x = null;//只有这样才可以让两个函数都可以访问异步对象
        btns[0].onclick = function(){
            x = new XMLHttpRequest();
            x.open("GET",'http://127.0.0.1:8000/delay');
            x.send();
        }
        // abort
        btns[1].onclick = function(){
            x.abort();
        }
```

### 6、Ajax请求重复发送问题

```
  //获取元素对象
        const btns = document.querySelectorAll('button');
        let x = null;
        //标识变量
        let isSending = false; // 是否正在发送AJAX请求

        btns[0].onclick = function(){
            //判断标识变量
            if(isSending) x.abort();// 如果正在发送, 则取消该请求, 创建一个新的请求
            x = new XMLHttpRequest();
            //修改 标识变量的值
            isSending = true;
            x.open("GET",'http://127.0.0.1:8000/delay');
            x.send();
            x.onreadystatechange = function(){
                if(x.readyState === 4){
                    //修改标识变量
                    isSending = false;
                }
            }
        }
```

# 四、在jQuery中发送Ajax请求

#### 1）引入jQuery 

在bootcdn中搜索jQuery，复制script标签引入

![image-20220429205520307](C:\Users\ydy\AppData\Roaming\Typora\typora-user-images\image-20220429205520307.png)

#### 2）给按钮绑定事件

服务端：

```
//jQuery 服务
app.all('/jquery-server', (request, response) => {
    //设置响应头  设置允许跨域
    response.setHeader('Access-Control-Allow-Origin', '*');
    response.setHeader('Access-Control-Allow-Headers', '*');
    // response.send('Hello jQuery AJAX');
    const data = { name: '尚硅谷' };
    response.send(JSON.stringify(data));
});
```

#### 3）通过jQuery发送get\post（Ajax）请求

客户端：

```
<div class="container">
        <h2 class="page-header">jQuery发送AJAX请求 </h2>
        <button class="btn btn-primary">GET</button>
        <button class="btn btn-danger">POST</button>
        <button class="btn btn-info">通用型方法ajax</button>
    </div>
```

get,post请求都可以参入四个参数：

（1）参数为服务端路径；（2）第二个参数为传入的url请求行参数；（3）第三个为成功的回调；（4）第四个为传输的数据格式，请求体类型，与xhr.respondType相似，自动类型转换，得到的响应体为对象，而非json格式的数据

get请求

```
  $('button').eq(0).click(function(){
            $.get('http://127.0.0.1:8000/jquery-server', {a:100, b:200}, function(data){
                console.log(data);
            },'json');
        });

```

post请求

```
  $('button').eq(1).click(function(){
            $.post('http://127.0.0.1:8000/jquery-server', {a:100, b:200}, function(data){
                console.log(data);
            });
        });

```

语法：$.get(url,data,function(data,status,xhr),dataType)
url 必需。规定您需要请求的 URL。
data 可选。规定连同请求发送到服务器的数据。
function(data,status,xhr)可选。当请求成功时运行的函数。data,status,xhr 是自定义形参名。
参数说明：
       data - 包含来自请求的结果数据
       status - 包含请求的状态（"success"、"notmodified"、"error"、"timeout"、"parsererror"）
       xhr - 包含 XMLHttpRequest 对象
dataType 可选。规定预期的服务器响应的数据类型。默认地，jQuery 会智能判断。可能的
类型：
"xml" - 一个 XML 文档
"html" - HTML 作为纯文本
"text" - 纯文本字符串
"json" - 以 JSON 运行响应，并以对象返回

#### 4）jQuery的通用方法（Ajax）发送Ajax请求

```

        $('button').eq(2).click(function () {
            $.ajax({
                //url
                url: 'http://127.0.0.1:8000/jquery-server',
                //请求行参数
                data: { a: 100, b: 200 },
                //请求类型
                type: 'GET',
                //响应体结果数据格式
                dataType: 'json',
                //成功的回调
                success: function (data) {
                    console.log(data);
                },
                //超时时间
                timeout: 2000,
                //请求超时，网络异常的失败的回调
                error: function () {
                    console.log('出错啦!!');
                },
                //头信息
                headers: {
                    c: 300,
                    d: 400
                }
            });
        });

```

datatype属性值：

"xml" - 一个 XML 文档
"html" - HTML 作为纯文本
"text" - 纯文本字符串
"json" - 以 JSON 运行响应，并以对象返回

#### 5）get/post和通用方法Ajax比较

（1）通用方法Ajax功能性较强一些，可以为请求报文的任何位置设置参数

（2）get/post更加简洁

（3）根据需要选择使用哪种方法

# 五、axios发送Ajax请求

#### 1）引入axios

在bootcdn中搜索axios，复制script标签引入

#### 2）给按钮绑定事件

#### 3）get/post请求

客户端：

```
<body>
    <button>GET</button>
    <button>POST</button>
    <button>AJAX</button>

    <script>
        // https://github.com/axios/axios
        const btns = document.querySelectorAll('button');

        //配置 baseURL
        axios.defaults.baseURL = 'http://127.0.0.1:8000';

        btns[0].onclick = function () {
            //GET 请求
            axios.get('/axios-server', {
                //url 参数
                params: {
                    id: 100,
                    vip: 7
                },
                //请求头信息
                headers: {
                    name: 'atguigu',
                    age: 20
                }
            }).then(value => {
                console.log(value);
            });
        }

        btns[1].onclick = function () {
            axios.post('/axios-server', {
            //请求体
                username: 'admin',
                password: 'admin'
            }, {
                //url 
                params: {
                    id: 200,
                    vip: 9
                },
                //请求头参数
                headers: {
                    height: 180,
                    weight: 180,
                }
            });
        }
    </script>
</body>

```

post请求第一个参数为服务端路径；第二参数为请求体（在post中会将对象当作请求体，所以不能和行、头一起设置，如果一起设置会将行、头全当作请求体）；第三个参数为行、头

注意：get请求没有请求体，所以不能设置，但可以为post请求设置请求体

#### 4）axios的方式（Ajax函数）发送Ajax请求

```
btns[2].onclick = function(){
            axios({
                //请求方法
                method : 'POST',
                //url
                url: '/axios-server',
                //url参数
                params: {
                    vip:10,
                    level:30
                },
                //头信息
                headers: {
                    a:100,
                    b:200
                },
                //请求体参数
                data: {
                    username: 'admin',
                    password: 'admin'
                }
            }).then(response=>{
                //响应状态码
                console.log(response.status);
                //响应状态字符串
                console.log(response.statusText);
                //响应头信息
                console.log(response.headers);
                //响应体
                console.log(response.data);
            })
        }
```

# 五、使用fetch函数发送Ajax请求

fetch函数属于全局对象的，可以直接使用。

fetch() 用于发起获取资源的请求。它返回一个 promise，这个 promise 会在请求响应后被 resolve，并传回 Response 对象。

服务端

```
//fetch 服务
app.all('/fetch-server', (request, response) => {
    //设置响应头  设置允许跨域
    response.setHeader('Access-Control-Allow-Origin', '*');
    response.setHeader('Access-Control-Allow-Headers', '*');
    // response.send('Hello jQuery AJAX');
    const data = { name: '尚硅谷' };
    response.send(JSON.stringify(data));
});
```

客户端

```
       
        const btn = document.querySelector('button');

        btn.onclick = function(){
            fetch('http://127.0.0.1:8000/fetch-server?vip=10', {
                //请求方法
                method: 'POST',
                //请求头
                headers: {
                    name:'atguigu'
                },
                //请求体
                body: 'username=admin&password=admin'
            }).then(response => {
                // return response.text();
                return response.json();//如果服务端返回的是json转化后的数据，则使用这个
            }).then(response=>{
                console.log(response);
            });
        }
```

**注意：如果服务端返回的是json转化后的数据，则使用response.json();**

# 六、Ajax跨域

Ajax遵循同源策略

### 1 .同源策略

同源策略(Same-Origin Policy)最早由 Netscape 公司提出，是浏览器的一种安全策略。
同源： 协议、域名、端口号 必须完全相同。
违背同源策略就是跨域

服务端

```
const express = require('express');

const app = express();

app.get('/home', (request, response) => {
    //响应一个页面
    // 不加下划线dirname就是绝对路径，加了是相对路径
    response.sendFile(__dirname + '/index.html');
});

app.get('/data', (request, response) => {
    response.send('用户数据');
});

app.listen(9000, () => {
    console.log("服务已经启动...");
});
```

客户端

```
   btn.onclick = function(){
            const x = new XMLHttpRequest();
            //这里因为是满足同源策略的, 所以 url 可以简写
            x.open("GET",'/data');
            //发送
            x.send();
            //
            x.onreadystatechange = function(){
                if(x.readyState === 4){
                    if(x.status >= 200 && x.status < 300){
                        console.log(x.response);
                    }
                }
            }
        }
```

重点： 

（1）在创建路由规则时响应一个页面： response.sendFile(__dirname + '/index.html');或在客服端通过open with liver server打开浏览器（错误，可能默认端口和服务端口不一致）

（2）在浏览器输入url（服务端）达到同源的目的，非直接运行代码打开网页

### 2. 如何解决跨域（Ajax实现跨域请求）

因为script引入的是js文件，而js文件是解析js代码的，所以你在服务端返回给客户端的代码必须是能让script解析出的js代码

#### 2.1 JSONP

1) JSONP 是什么
JSONP(JSON with Padding)，是一个非官方的跨域解决方案，纯粹凭借程序员的聪明才智开发出来，**只支持 get 请求**。
2) JSONP 怎么工作的？
    在网页有一些标签天生具有跨域能力，比如：img link iframe script。JSONP 就是利用 script 标签的跨域能力来发送请求的。
3) **script可以跨域所以用它引入服务器响应**。
4) **利用script的src向服务端发送请求**，获取了一段js代码，srcipt标签能解析这段代码（不能解析字符串），所以期望返回的结果：函数的调用（js代码，在客户端定义函数），函数中的实参就是服务端想要给客户端返回的结果数据，该函数需要提前声明（**注意script标签的顺序，客户端代码在前，引入服务端的script标签在后**）
5) 在该函数中对数据进行处理（渲染）

注意：**end函数中的符号为斜点，不是单引号**

客户端

```
 <script>
        //处理数据
        function handle(data) {
            //获取 result 元素
            const result = document.getElementById('result');
            result.innerHTML = data.name;
        }
    </script>
    <!-- <script src="http://127.0.0.1:5500/%E8%AF%BE%E5%A0%82/%E4%BB%A3%E7%A0%81/7-%E8%B7%A8%E5%9F%9F/2-JSONP/js/app.js"></script> -->
    <script src="http://127.0.0.1:8000/jsonp-server"></script>
```

服务端

```
//jsonp服务
app.all('/jsonp-server', (request, response) => {
    // response.send('console.log("hello jsonp")');
    const data = {
        name: '尚硅谷atguigu'
    };
    //将数据转化为字符串
    let str = JSON.stringify(data);
    //返回结果 end不会加特殊响应头
    response.end(`handle(${str})`);
});
```

总结：利用script的src向服务端发送请求，需要和服务端的 response.end、JSON.stringify打配合

##### 2.1.1  JSONP实践

服务端：

```
//用户名检测是否存在
app.all('/check-username', (request, response) => {
    // response.send('console.log("hello jsonp")');
    const data = {
        exist: 1,
        msg: '用户名已经存在'
    };
    //将数据转化为字符串
    let str = JSON.stringify(data);
    //返回结果
    response.end(`handle(${str})`);
});

```

客户端

```
   //获取 input 元素
        const input = document.querySelector('input');
        const p = document.querySelector('p');
        
        //声明 handle 函数
        function handle(data){
            input.style.border = "solid 1px #f00";
            //修改 p 标签的提示文本
            p.innerHTML = data.msg;
        }

        //绑定事件
        input.onblur = function(){
            //获取用户的输入值
            let username = this.value;
            //向服务器端发送请求 检测用户名是否存在
            //1. 创建 script 标签
            const script = document.createElement('script');
            //2. 设置标签的 src 属性
            script.src = 'http://127.0.0.1:8000/check-username';
            //3. 将 script 插入到文档中
            document.body.appendChild(script);
        }
```

##### 2.1.2 JSONP 的使用

客户端中：

1.动态的创建一个 script 标签

```
var script = document.createElement("script");
```

2.设置 script 的 src，设置回调函数，回调函数作为参数拼接在url中

```
script.src = "http://localhost:3000/testAJAX?callback=abc";
function abc(data) {
alert(data.name);
};
```

3.将 script 添加到 body 中

```
document.body.appendChild(script);
```

4.服务器中路由的处理

函数的调用（js代码，在客户端定义函数），函数中的实参就是服务端想要给客户端返回的结果数据

```
  //将数据转化为字符串
    let str = JSON.stringify(data);
    //返回结果
    response.end(`handle(${str})`);
```

##### 2.1.3 jQuery发送jsonp请求

1）引入jQuery

2）给元素绑定事件

客户端

```
  <button>点击发送 jsonp 请求</button>
    <div id="result">

    </div>
    <script>
        $('button').eq(0).click(function(){
        //URL必须携带query参数（?callback=?看似没有值，但是服务端会收到值，该值就是函数）
            $.getJSON('http://127.0.0.1:8000/jquery-jsonp-server?callback=?', function(data){
                $('#result').html(`
                    名称: ${data.name}<br>
                    校区: ${data.city}
                `)
            });
        });
    </script>
```

 $.getJSON中的url需要添加参数callback=?（固定写法）。在服务端会接收到callback对应的函数（request.query.callback），向客户端返回该函数的调用，实参为返回的数据

服务端

```
app.all('/jquery-jsonp-server', (request, response) => {
    // response.send('console.log("hello jsonp")');
    const data = {
        name: '尚硅谷',
        city: ['北京', '上海', '深圳']
    };
    //将数据转化为字符串
    let str = JSON.stringify(data);
    //接收 callback 参数
    let cb = request.query.callback;
    //返回结果
    response.end(`${cb}(${str})`);
});

```

#### 2.2.  设置cors响应头实现跨域

https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Access_control_CORS

1) CORS 是什么？
CORS（Cross-Origin Resource Sharing），跨域资源共享。CORS 是官方的跨域解决方案，**它的特点是不需要在客户端做任何特殊的操作，完全在服务器中进行处理，支持get 和 post 请求**。跨域资源共享标准新增了一组 HTTP 首部字段，允许服务器声明哪些源站通过浏览器有权限访问哪些资源

2. CORS 怎么工作的？
    CORS 是通过设置一个响应头来告诉浏览器，该请求允许跨域，浏览器收到该响应以后就会对响应放行。
    
3. CORS 的使用

```
 //设置响应头  设置允许跨域
    response.setHeader('Access-Control-Allow-Origin', '*');
```

常用设置的响应头

```
  //设置响应头
    // 设置跨域
    response.setHeader("Access-Control-Allow-Origin", "*");
    // 允许设置任意请求头（包括自定义与预定义）
    response.setHeader("Access-Control-Allow-Headers", '*');
    // 一般默认get、post，如果需要其他请求方式，就添加以下代码
    response.setHeader("Access-Control-Allow-Method", '*');
```

##### 3.1 web容器的反向代理

实际工作解决跨域的方案最常用的既不是jsonp也不是cors，而是设置web容器的反向代理

 总结

（1）使用JSONP实现Ajax跨域请求，需要在客户端和服务端都进行操作

（2）使用cors实现Ajax跨域操作，只需要对服务端进行操作，不影响原本客户端的Ajax请求方式
