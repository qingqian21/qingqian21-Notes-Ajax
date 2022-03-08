`JSON` 和 `JavaScript` 对象之间的转换

1. 要实现从 `JSON` 字符串转换为 `JavaScript` 对象，使用 `JSON.parse()` 方法

   ```
       <script>
           var obj = JSON.parse('{"name":"Alex","age":20,"sex":"female","hobby":["吃饭","睡觉"]}');
           console.log(obj);
       </script>
   ```

2. 要实现从 `JavaScript` 对象转换为 `JSON` 字符串 ，使用 `JSON.stringify()` 方法

   ```
       <script>
           var string = JSON.stringify({
               name: 'Alex',
               age: 20,
               sex: 'female',
               hobby: ['吃饭', '睡觉']
           });
           console.log(string);
       </script>
   ```

3. 序列化和反序列化

   把 `数据对象` 转换为 `字符串` 的过程，叫做序列化。例如：调用 `JSON.stringify()` 函数的操作，叫做 `JSON` 序列化。

   把 `字符串` 转换为 `数据对象` 的过程，叫做反序列化。例如：调用 `JSON.parse()` 函数的操作，叫做 `JSON` 反序列化。

1. 封装自己的 `Ajax` 函数

   `toUpperCase()` 函数用于转换为大写

   ```
   <!DOCTYPE html>
   <html lang="en">
   
   <head>
       <meta charset="UTF-8">
       <meta http-equiv="X-UA-Compatible" content="IE=edge">
       <meta name="viewport" content="width=device-width, initial-scale=1.0">
       <title>Document</title>
   </head>
   
   <body>
   
       <script>
           // GET 请求测试
           Ajax({
               method: "GET",
               url: "http://www.liulongbin.top:3006/api/getbooks",
               data: {
                   id: 1,
               },
               success: function(res) {
                   console.log(res);
               }
           })
   
           // POST 请求测试
           // Ajax({
           //     method: "POST",
           //     url: "http://www.liulongbin.top:3006/api/addbook",
           //     data: {
           //         bookname: "请求",
           //         author: "张三",
           //         publisher: "图书"
           //     },
           //     success: function(res) {
           //         console.log(res);
           //     }
           // })
   
           // 处理 data 对象，将数据对象转化为查询字符串的格式。
           function resolveData(data) {
               var arr = [];
   
               // for (var key in object) 中，key表示属性名，object[key]表示属性值
               for (var key in data) {
   
                   // array.push()在数组末尾，添加一个或多个数组元素，返回新数组长度，且数组内容发生变化。
                   arr.push(key + "=" + data[key])
               };
   
               // array.join("分隔符")方法，将数组转换为字符串，默认使用","分隔，若有分隔符，则以分隔符分割。返回新字符串
               return arr.join("&");
           }
   
           // 封装 ajax 函数
           function Ajax(object) {
               // 创建 xhr 对象
               var xhr = new XMLHttpRequest();
   
               // 并将 object 中的数据转换为查询字符串的格式
               var string = resolveData(object.data);
   
               // 判断发起 GET 请求 或是 POST 请求
               if (object.method.toUpperCase() === "GET") {
                   // 创建 Ajax 请求
                   xhr.open("GET", object.url + "?" + string);
   
                   // 发送 Ajax 请求
                   xhr.send();
   
               } else if (object.method.toUpperCase() === "POST") {
                   // 创建 Ajax 请求
                   xhr.open("POST", object.url);
   
                   // 设置 Content-Type 属性
                   xhr.setRequestHeader("Content-Type", "application/x-www-form-urlencoded");
   
                   // 发起 Ajax 请求
                   xhr.send(string);
   
               }
   
               // 监听 onreadystatechange 事件
               xhr.addEventListener("readystatechange", function() {
                   if (xhr.readyState === 4 && xhr.status === 200) {
                       var obj = JSON.parse(xhr.responseText);
                       object.success(obj);
                   }
               })
           }
       </script>
   </body>
   
   </html>
   ```

2. `XMLHttpRequest2` 的新功能 

   - 可以设置 `HTTP` 请求的时限( `timeout` 属性 , `timeout` 事件)

     新版本的 `XMLHttpRequest` 对象，增加了 `timeout` 属性，可以设置 `HTTP` 请求时限。若过了这个时限，就会自动停止 `HTTP` 请求。与之配套的还有 `timeout` 事件。

   - 可以使用 `FormData` 对象管理表单数据 或 直接获取网页表单的值( `new FormData(selector)` )

   - 可以上传文件( `sellector.files.length` , `FormData` 对象[ `new FormData()` ,    `append()]` )

     步骤：

     1. 定义 UI 结构
     2. 验证是否选择文件
     3. 向 `FormData` 中追加文件 （使用 `append()` 函数即可）
     4. 使用 `xhr` 发起上传文件的请求
     5. 监听 `onreadystatechange` 事件

   - 可以获得数据传输的进度信息( `xhr.upload.onprogress` [ `event.lengthComputable` ,  `event.loaded`  , `event.total` ] , `xhr.upload.onload` )

     新版本的 `XMLHttpRequest` 对象中，可以通过监听 `xhr.upload.onprogress` 事件，来获取到文件上传进度 

     注意：只有上传文件时，不需要设置 `xhr.setRequestHeader` 中的 `Content-Type` 属性

   ```
   <!DOCTYPE html>
   <html lang="en">
   
   <head>
       <meta charset="UTF-8">
       <meta http-equiv="X-UA-Compatible" content="IE=edge">
       <meta name="viewport" content="width=device-width, initial-scale=1.0">
       <title>Document</title>
       <link rel="stylesheet" href="./css/bootstrap.css">
   </head>
   <!-- 
       XMLHttpRequest2 的新功能 
       1.可以设置 HTTP 请求的时限(timeout属性 , timeout事件)
       2.可以使用 FormData 对象管理表单数据 或 直接获取网页表单的值(new FormData(selector))
       3.可以上传文件(sellector.files.length, FormData 对象[new FormData() , append()])
       4.可以获得数据传输的进度信息(xhr.upload.onprogress[event.lengthComputable , event.loaded , event.total] , xhr.upload.onload)
   
       注意：只有上传文件时，不需要设置 xhr.setRequestHeader 中的 Content-Type 属性
   -->
   
   
   <body>
       <!-- 设置 HTTP 请求的时限  
           新版本的 XMLHttpRequest 对象，增加了 timeout 属性，可以设置 HTTP 请求时限。若过了这个时限，就会自动停止 HTTP 请求。与之配套的还有 timeout 事件。
       -->
   
       <!-- <script>
           var xhr = new XMLHttpRequest();
           // 设置超时时限，超时后，停止 HTTP 请求
           xhr.timeout = 3000;
           // 超时后调用的函数
           xhr.addEventListener("timeout", function() {
               console.log("请求超时！");
           })
           xhr.open("GET", "http://www.liulongbin.top:3006/api/getbooks");
           xhr.send();
           xhr.addEventListener("readystatechange", function() {
               if (xhr.readyState === 4 && xhr.status === 200) {
                   console.log(xhr.responseText);
               }
           })
       </script> -->
   
   
   
       <!-- FormData 对象管理表单数据（HTML5新增） 或 直接获取网页表单的值-->
   
       <!-- FormData 对象管理表单数据 -->
       <!-- <script>
           // 创建 FormData 实例
           var fd = new FormData()
   
           // 通过调用 append() 函数，向 fd 中追加数据
           fd.append("uname", "zs");
           fd.append("upwd", "123456");
   
           var xhr = new XMLHttpRequest();
           xhr.open("POST", "http://www.liulongbin.top:3006/api/formdata");
           xhr.send(fd);
           xhr.onreadystatechange = function() {
               if (xhr.readyState === 4 && xhr.status === 200) {
                   console.log(xhr.responseText);
               }
           }
       </script> -->
   
       <!-- FormData 对象直接获取网页表单的值 -->
       <!-- <form action="" method="post" id="form1">
           <input type="text" name="uname" autocomplete="off" />
           <input type="password" name="upwd" id="">
           <button type="submit">提交</button>
       </form>
   
       <script>
           // 通过 DOM 操作 获取 form 表单元素
           var form = document.querySelector("#form1");
   
           form.addEventListener("submit", function(event) {
               // 阻止表单的默认提交行为
               event.preventDefault();
   
               // 创建 FormDat 对象，快速获取到 form 表单中的数据
               var data = new FormData(form);
   
               // 发起 Ajax 请求
               var xhr = new XMLHttpRequest();
               xhr.open("POST", "http://www.liulongbin.top:3006/api/formdata");
               xhr.send(data);
               xhr.onreadystatechange = function() {
                   if (xhr.readyState === 4 && xhr.status === 200) {
                       console.log(JSON.parse(xhr.responseText));
                   }
               }
           })
       </script> -->
   
   
   
       <!-- 上传文件 -->
       <!-- 步骤：
               1.定义 UI 结构
               2.验证是否选择文件
               3.向 FormData 中追加文件 （使用 append() 函数即可）
               4.使用 xhr 发起上传文件的请求
               5.监听 onreadystatechange 事件
       -->
   
       <!-- <input type="file" name="ufile" id="file1" />
       <button id="btnUpload">上传文件</button>
       <br/>
       <img src="" alt="" width="800" id="avatar">
   
       <script>
           // 获取上传按钮
           var btnUpload = document.querySelector("#btnUpload");
           var img_avatar = document.querySelector("#avatar");
           // 为上传按钮注册点击事件
           btnUpload.addEventListener("click", function() {
               // 获取用户选择的文件列表
               // files 属性是一个数组，files.length 表示文件个数
               var files = document.querySelector("#file1").files;
   
               if (files.length <= 0) {
                   // 此时表示没有上传文件
                   return alert("未上传文件！");
               } else {
                   // 此时表示有文件需要上传
                   var data = new FormData();
                   // 将用户选择的文件添加到 FormData 中
                   data.append("avatar", files[0]);
   
                   // 创建 xhr 对象
                   var xhr = new XMLHttpRequest();
   
                   // 调用 open() ，创建 Ajax 请求
                   xhr.open("POST", "http://www.liulongbin.top:3006/api/upload/avatar");
                   // 调用 send() ，发起 Ajax 请求
                   xhr.send(data);
                   // 监听 onreadystatechange事件
                   xhr.onreadystatechange = function() {
                       if (xhr.readyState === 4 && xhr.status === 200) {
                           // xhr.responseText 是JSON 字符串
                           var avatar = JSON.parse(xhr.responseText);
                           if (avatar.status === 200) {
                               img_avatar.src = "http://www.liulongbin.top:3006" + avatar.url;
                           } else {
                               console.log(avatar.message);
                           }
                       }
                   }
               }
           })
       </script> -->
   
   
   
       <!-- 显示文件上传进度 -->
       <!-- 
           新版本的 XMLHttpRequest 对象中，可以通过监听 xhr.upload.onprogress 事件，来获取到文件上传进度 
       -->
   
       <input type="file" name="ufile" id="file1" />
       <button id="btnUpload">上传文件</button>
       <!-- 进度条 -->
       <div class="progress" style="width: 500px; margin: 15px 10px;">
           <div class="progress-bar progress-bar-striped active" id="percent">
           </div>
       </div>
       <br/>
       <img src="" alt="" width="300" id="avatar">
   
       <script>
           // 获取上传按钮
           var btnUpload = document.querySelector("#btnUpload");
           var img_avatar = document.querySelector("#avatar");
           var percent = document.querySelector("#percent")
               // 为上传按钮注册点击事件
           btnUpload.addEventListener("click", function() {
               // 获取用户选择的文件列表
               // files 属性是一个数组，files.length 表示文件个数
               var files = document.querySelector("#file1").files;
   
               if (files.length <= 0) {
                   // 此时表示没有上传文件
                   return alert("未上传文件！");
               } else {
                   // 此时表示有文件需要上传
                   var data = new FormData();
                   // 将用户选择的文件添加到 FormData 中
                   data.append("avatar", files[0]);
   
                   // 创建 xhr 对象
                   var xhr = new XMLHttpRequest();
   
                   // 监听文件上传的进度
                   xhr.upload.addEventListener("progress", function(event) {
                       // event.lengthComputable 是一个布尔值，表示当前上传的资源是否具有可计算的长度
                       if (event.lengthComputable) {
                           // event.loaded 表示已传输的字节
                           // event.total 表示总需要传输的字节
                           var percentComplete = Math.ceil((event.loaded / event.total) * 100);
                           percent.style.width = percentComplete + "%";
                           percent.innerHTML = percentComplete + "%";
                       }
                   })
   
                   // 监听上传完成的事件
                   xhr.upload.addEventListener("load", function() {
                       percent.className = "progress-bar progress-bar-success";
                   })
   
                   // 调用 open() ，创建 Ajax 请求
                   xhr.open("POST", "http://www.liulongbin.top:3006/api/upload/avatar");
                   // 调用 send() ，发起 Ajax 请求
                   xhr.send(data);
   
                   // 监听 onreadystatechange事件
                   xhr.onreadystatechange = function() {
                       if (xhr.readyState === 4 && xhr.status === 200) {
                           // xhr.responseText 是JSON 字符串
                           var avatar = JSON.parse(xhr.responseText);
                           if (avatar.status === 200) {
                               img_avatar.src = "http://www.liulongbin.top:3006" + avatar.url;
                           } else {
                               console.log(avatar.message);
                           }
                       }
                   }
               }
           })
       </script>
   
   </body>
   
   </html>
   ```

3. 使用 `jQuery` 实现文件上传

   注意：进行文件上传时，只能使用 `$.ajax()` 进行上传

   使用 jQuery 实现 loading 效果

     `ajaxStart(callback)` 

   Ajax 请求开始时，执行 `ajaxStart()` 函数，可以在 `ajaxStart` 的 `callback` 中显示loading 效果

     `ajaxStop(callback)` 

   Ajax 请求结束时，执行 `ajaxStop()` 函数，可以在 `ajaxStop` 的 `callback` 中隐藏 loading 效果

   注意：

   1. 自 `jQuery 1.8` 起，该方法只能被附加到文档

   2.  `$(document).ajaxStart(callback)` 和 `$(document).ajaxStop(callback)` 会监听当前文档中所有的 `Ajax` 请求

   3. ```
      <!DOCTYPE html>
      <html lang="en">
      
      <head>
          <meta charset="UTF-8">
          <meta http-equiv="X-UA-Compatible" content="IE=edge">
          <meta name="viewport" content="width=device-width, initial-scale=1.0">
          <title>Document</title>
          <script src="./js/jQuery.js"></script>
      
      </head>
      
      <!-- 使用 jQuery 实现文件上传 -->
      <!-- 进行文件上传时，只能使用 $.ajax() 进行上传 -->
      
      
      <!-- 使用 jQuery 实现 loading 效果 -->
      <!-- 
          ajaxStart(callback)
          Ajax 请求开始时，执行 ajaxStart() 函数，可以在 ajaxStart 的 callback 中显示 loading 效果
      
          ajaxStop(callback)
          Ajax 请求结束时，执行 ajaxStop() 函数，可以在 ajaxStop 的 callback 中隐藏 loading 效果
      
          ajaxSuccess(callback) 和 ajaxError(callback) Ajax 请求成功 和 失败调用回调函数
      
          注意：
              1.自 jQuery 1.8 起，该方法只能被附加到文档
              2.$(document).ajaxStart(callback) 和 $(document).ajaxStop(callback) 会监听当前文档中所有的 Ajax 请求
       -->
      
      <body>
          <input type="file" name="ufile" id="ufile">
          <button id="btnUpload">上传文件</button>
          <br/>
          <img src="./images/loading.gif" alt="" id="loading" style="margin: 15px auto; display: none;">
          <img src="" alt="" width="300px" id="uavatar">
      
          <script>
              $(function() {
      
                  // 使用 jQuery 实现文件上传
                  $("#btnUpload").on("click", function() {
                      // files属性是 DOM 对象的属性，需要转换为 DOM 对象
                      var files = $("#ufile")[0].files;
                      if (files.length <= 0) {
                          return alert("未上传任何文件！");
                      }
                      var fd = new FormData();
                      fd.append("avatar", files[0]);
      
                      $.ajax({
                          method: "POST",
                          url: "http://www.liulongbin.top:3006/api/upload/avatar",
                          data: fd,
                          // 不修改 Content-Type 属性，使用 FormData 默认的 Content-Type 值
                          contentType: false,
                          // 不对 FormData 中的数据进行 URL 编码，而是将 FormData 数据原样发送到服务器
                          processData: false,
                          success: function(res) {
                              if (res.status !== 200) {
                                  return res.message;
                              }
                              $("#uavatar").prop("src", "http://www.liulongbin.top:3006" + res.url)
                          }
                      })
                  })
      
      
                  // 使用 jQuery 实现 loading 效果
                  $(document).ajaxStart(function() {
                      $("#loading").show();
                  })
      
                  $(document).ajaxStop(function() {
                      $("#loading").hide();
                  })
              })
          </script>
      
      </body>
      
      </html>
      ```

4. `axios` 发起请求

   - `Axios` 是专注于网络数据请求的 `JS` 库。相比于原生的 `XMLHttpRequest` 对象，`Axios` 简单易用。相比于 `jQuery` ，`Axios` 更加轻量化，只专注于网络数据请求。

   - `GET` 请求

       `axios.get("url",{params:{参数}}).then(function(res) {})` 

   - `POST` 请求

       `axios.post("url",{参数}).then(function(res) {})`

   - `axios` 直接发起请求,作用与 `$.ajax()` 类似

   ```
   axios({
   method:"请求类型",
   url:"请求的 URL 地址",
   data:{POST 参数},
   params:{GET 参数}
   }).then(function() {})
   ```

     注意： 

   -  `res.data` 表示服务器返回的数据, `res` 中其他属性都是 `axios` 包装的。

   - `axios()` 中，`POST` 请求用 `data` 属性，`GET` 请求用 `params` 属性

     ```
     <!DOCTYPE html>
     <html lang="en">
     
     <head>
         <meta charset="UTF-8">
         <meta http-equiv="X-UA-Compatible" content="IE=edge">
         <meta name="viewport" content="width=device-width, initial-scale=1.0">
         <title>Document</title>
         <script src="./js/axios.js"></script>
     </head>
     
     <!-- 
         Axios 是专注于网络数据请求的 JS 库。
         相比于原生的 XMLHttpRequest 对象，Axios 简单易用。
         相比于 jQuery，Axios 更加轻量化，只专注于网络数据请求。
      -->
     
     <!-- 
          1.GET 请求
         axios.get("url",{params:{参数}}).then(function(res) {})
     
         2.POST 请求
         axios.post("url",{参数}).then(function(res) {})
     
         3.axios 直接发起请求,作用与 $.ajax() 类似
         axios({
             method:"请求类型",
             url:"请求的 URL 地址",
             data:{POST 参数},
             params:{GET 参数}
         }).then(function() {})
     
         注意： 
             1.res.data 表示服务器返回的数据,res 中其他属性都是 axios 包装的。
             2.axios() 中，POST 请求用 data 属性，GET 请求用 params 属性
     -->
     
     <body>
         <button id="btnGet">发起 GET 请求</button>
         <button id="btnPost">发起 POST 请求</button>
         <button id="btnAxiosGet">直接使用 axios 发起 GET 请求</button>
         <button id="btnAxiosPost">直接使用 axios 发起 POST 请求</button>
     
         <!-- 发起 GET 请求 -->
         <script>
             var btn = document.querySelector("#btnGet");
             btn.addEventListener("click", function() {
                 // 使用 axios.get() 发起 GET 请求
                 axios.get("http://www.liulongbin.top:3006/api/get", {
                     params: {
                         name: "zs",
                         age: 20
                     }
                 }).then(function(res) {
                     // res.data 是服务器返回的真实数据
                     console.log(res.data);
                 })
             })
         </script>
     
         <!-- 发起 POST 请求 -->
         <script>
             var btn = document.querySelector("#btnPost");
             btn.onclick = function() {
                 axios.post("http://www.liulongbin.top:3006/api/post", {
                     location: "北京",
                     address: "顺义"
                 }).then(function(res) {
                     console.log(res.data);
                 })
             }
         </script>
     
     
         <!-- 使用 Axios 直接发起 GET 请求 -->
         <script>
             var btnAxiosGet = document.querySelector("#btnAxiosGet");
             btnAxiosGet.addEventListener("click", function() {
                 axios({
                     method: "GET",
                     url: "http://www.liulongbin.top:3006/api/get",
                     // 注意：GET 请求使用 params 属性
                     params: {
                         name: "钢铁侠",
                         age: 35
                     }
                 }).then(function(res) {
                     console.log(res.data);
                 })
             })
         </script>
     
         <!-- 使用 Axios 直接发起 POST 请求 -->
         <script>
             var btnAxiosPost = document.querySelector("#btnAxiosPost");
             btnAxiosPost.onclick = function() {
                 axios({
                     method: "POST",
                     url: "http://www.liulongbin.top:3006/api/post",
                     // 注意：POST 请求使用 data 属性
                     data: {
                         name: "哈哈",
                         age: 22
                     }
                 }).then(function(res) {
                     console.log(res.data);
                 })
             }
         </script>
     </body>
     
     </html>
     ```

5. 同源策略

   - 同源： 若两个页面的协议，域名和端口都相同，这两个页面具有相同的源。

     域名后边紧跟的":数字"是端口，若没有":数字"，则默认端口为80端口

     例如： `http://www.test.com/index.html` 

     ​		协议：`http`  域名：`www.test.com`  端口：80

   - 同源策略：是浏览器提供的一个安全策略

     官方解释：同源策略限制了从同一个源加载的文档或脚本如何与来自另一个源的资源进行交互，这是一个用于隔离潜在恶意文件的重要安全机制

     通俗理解：浏览器规定，A 网站的 JavaScript，不允许和非同源的网站 C 之间，进行资源交互。

     例如：

     1. 无法读取非同源网页的 `Cookie`，`LocalStorage` ，和 `IndexedDB` 
     2. 无法接触非同源网页的 `DOM`
     3. 无法向非同源地址发送 `Ajax` 请求

6. 跨域

   - 跨域：同源是指两个 URL 的协议，域名和端口一致。反之，则是跨域。

   - 出现跨域的根本原因：浏览器的同源策略不允许非同源的 URL 之间进行资源交互

   - 例如：

     网页：`http://www.test.com/index.html` 

     接口：`http://www.api.com/userlist` 

     若网页向接口发送 `Ajax` 请求，受同源策略影响，会导致 `Ajax` 请求失败

   -   浏览器对跨域请求的拦截

       ![](.\1.png)

       注意：浏览器允许发起跨域请求，但是，跨域请求回来的数据，会被浏览器拦截，无法被页面获取到。
       
   -   实现跨域数据请求：

     实现跨域数据请求，主要两种解决方案：`JSONP` 和 `CORS`。

     `JSONP`：出现的早，兼容性好。是一种临时方案。缺点：只支持 `GET` 请求，不支持 `POS`T 请求。

     `CORS`：出现较晚，属于跨域 `Ajax` 请求的根本解决方案，支持 `GET` 和 `POST` 请求。缺点：不支持某些低版本浏览器。

7. `JSONP` 请求

    - `JSONP` 是 `JSON` 的一种 "使用模式"，用于解决跨域数据访问问题。

    - `JSONP` 实现原理： `<script>` 标签不受浏览器同源策略的影响，可以通过 `src` 属性，请求非同源的 `JS` 脚本。

      因此，`JSONP` 的实现原理，就是通过 `<script>` 标签的 `src` 属性，请求跨域的数据接口，并通过函数调用的形式，接收跨域接口响应回来的数据。

    - `JSONP` 不属于 `Ajax` 请求，其属于 `script` 请求，用于实现一次函数调用。

      由于 `JSONP` 是通过 `<script>` 标签的 `src` 属性，来实现跨域数据获取的，所以，`JSONP` 只支持 `GET` 请求，不支持 `POST` 请求。

    - 注意：`JSONP` 和 `Ajax` 没有任何关系。不能把 `JSONP` 请求数据的方式叫做 `Ajax`，因为 `JSONP` 没有用到 `XMLHttpRequest` 对象。

      ```
      <!DOCTYPE html>
      <html lang="en">
      
      <head>
          <meta charset="UTF-8">
          <meta http-equiv="X-UA-Compatible" content="IE=edge">
          <meta name="viewport" content="width=device-width, initial-scale=1.0">
          <title>Document</title>
      </head>
      
      <!-- 
          JSONP 是 JSON 的一种 "使用模式"，用于解决跨域数据访问问题
       -->
      
      <!-- 
           JSONP实现原理：
           <script> 标签不受浏览器同源策略的影响，可以通过 src 属性，请求非同源的 JS 脚本。
          因此，JSONP 的实现原理，就是通过 <script> 标签的 src 属性，请求跨域的数据接口，并通过函数调用的形式，接收跨域接口响应回来的数据。
        -->
      
      <!-- 
            JSONP 不属于 Ajax 请求，其属于 script 请求，用于实现一次函数调用。
            由于 JSONP 是通过 <script> 标签的 src 属性，来实现跨域数据获取的，所以，JSONP 只支持 GET 请求，不支持 POST 请求。
            注意：JSONP 和 Ajax 没有任何关系。不能把 JSONP 请求数据的方式叫做 Ajax，因为 JSONP 没有用到 XMLHttpRequest 对象。
         -->
      
      <body>
          <script>
              function success(data) {
                  console.log("拿到了data数据：");
                  console.log(data);
              }
          </script>
          <!-- callback 用于告诉服务器返回一个什么样的函数调用 -->
          <script src="http://www.liulongbin.top:3006/api/jsonp?callback=success&name=zs&age=20"></script>
      </body>
      
      </html>
      ```

    - `jQuery` 中的 `JSONP` 

      - `$.ajax()` 不仅能发起真正的 `Ajax` 请求之外，还可以发起 `JSONP` 数据请求.

        注意：

        1. 若要使用 `$.ajax()` 发起 `JSONP` 请求，必须指定 `dataType:"jsonp"`
        2. 默认情况下，使用 `jQuery` 发起 `JSON`P 请求，会自动携带一个 `callback = jQueryXXX` 的参数 , `jQueryXXX` 是随机生成的一个回调函数名称。

      - 自定义参数及回调函数名称

        在使用 `jQuery` 发起 `JSONP` 请求时，若想要自定义 `JSONP` 的参数和回调函数名称，可以通过 `jsonp(参数,默认callback)` 和 `jsonpCallback(回调函数名,称默认jQueryXXX)` 属性来修改。

      - `jQuery` 中 `JSONP` 的实现过程

        `jQuery` 中的 `JSONP` 也是通过 `<script>` 标签的 `src` 属性来实现跨域数据访问的，只不过，`jQuery`采用的是动态移除 `<script>` 标签的方式，来发起 `JSONP` 请求。

      ```
      <!DOCTYPE html>
      <html lang="en">
      
      <head>
          <meta charset="UTF-8">
          <meta http-equiv="X-UA-Compatible" content="IE=edge">
          <meta name="viewport" content="width=device-width, initial-scale=1.0">
          <title>Document</title>
          <script src="./js/jQuery.js"></script>
      </head>
      
      <!-- 
          $.ajax() 不仅能发起真正的 Ajax 请求之外，还可以发起 JSONP 数据请求.
      
          注意：
          1.若要使用 $.ajax() 发起 JSONP 请求，必须指定 dataType:"jsonp"
          2.默认情况下，使用 jQuery 发起 JSONP 请求，会自动携带一个 "callback = jQueryXXX" 的参数 , jQueryXXX 是随机生成的一个回调函数名称。
      -->
      
      <!-- 
          自定义参数及回调函数名称
          在使用 jQuery 发起 JSONP 请求时，若想要自定义 JSONP 的参数和回调函数名称，可以通过 jsonp(参数,默认callback) 和 jsonpCallback(回调函数名,称默认jQueryXXX) 属性来修改。
       -->
      
      <!-- 
           jQuery 中 JSONP 的实现过程
           jQuery 中的 JSONP 也是通过 <script> 标签的 src 属性来实现跨域数据访问的，只不过，jQuery采用的是动态移除 <script> 标签的方式，来发起 JSONP 请求。
        -->
      
      <body>
          <!-- 默认参数和函数名 -->
          <!-- <script>
              $(function() {
                  // 发起 JSONP 的请求
                  $.ajax({
                      url: "http://www.liulongbin.top:3006/api/jsonp?name=zs&age=20",
                      // 指定 dataType:"jsonp" 表示发起 JSONP 请求
                      dataType: "jsonp",
                      success: function(res) {
                          console.log(res);
                      }
                  })
              })
          </script> -->
      
      
          <!-- 自定义参数和函数名 -->
          <script>
              $(function() {
                  $.ajax({
                      url: "http://www.liulongbin.top:3006/api/jsonp?name=zs&age=20",
                      dataType: "jsonp",
                      // 自定义回调函数名称，默认 jQueryXXX
                      jsonpCallback: "jQueryCallback",
                      success: function(res) {
                          console.log(res);
                      }
                  })
      
              })
          </script>
      </body>
      
      </html>
      ```

8. 防抖策略          一段时间内，多次触发，只执行最后一次回调

     - 防抖策略是指事件被触发后，延迟 n 秒后再执行回调，如果在这 n 秒内事件又被触发，则重新计时。

       ![](.\2.png)

     - 应用场景

       在搜索框中连续输入一串字符串，可以通过防抖策略，减少请求次数，节约请求资源

     - 步骤：

       1. 定义延时器id
       2. 定义防抖函数
       3. 清除延时器
       4. 调用防抖函数

       ```
           var timer = null;
       
           function debounceSearch(keywords) {
               timer = setTimeout(function() {
                   getSuggestList(keywords);
               }, 500)
               
           $("#iptSearch").on("keyup", function() {
               // 防抖策略，清除延时器
               clearTimeout(timer);
               if ($(this).val().trim().length <= 0) {
                   // 搜索关键字为空时，隐藏搜索建议列表
                   return $("#suggest-list").empty().hide();
               }
               // 搜索关键字不为空时，调用搜索列表函数
               // getSuggestList($(this).val().trim());
               debounceSearch($(this).val().trim());
           })
       ```

9. 缓存对象

     步骤：

     1. 定义全局的缓存对象
     2. 将搜索结果保存到缓存对象中
     3. 优先从缓存中获取搜索建议

     ```
     	var casheObj = {};
     
     	// 省略代码
     	var k = $(this).val().trim();
     	casheObj[k] = res;
     
         $("#iptSearch").on("keyup", function() {
         	// 省略代码
             if ($(this).val().trim().length <= 0) {
                 // 搜索关键字为空时，隐藏搜索建议列表
                 return $("#suggest-list").empty().hide();
             }
             // 搜索关键字不为空时，调用搜索列表函数
             // getSuggestList($(this).val().trim());
             debounceSearch($(this).val().trim());
         })
     ```

10. 节流策略      一段时间内，多次触发，只执行第一次的回调

     - 可以减少一段时间内事件的触发频率。

       ![](.\3.png)

     - 应用场景

       1. 连续不断地触发某事件，只在单位时间内触发一次
       2. 懒加载时监听计算滚动条的位置，但不必每次滑动都触发，可以降低计算的频率

     - 节流阀

       节流阀为空，表示可以执行下一次操作，反之，表示不能执行下一次操作。

       当前操作执行完，必须将节流阀重置为空，表示可以执行下次操作了。

       每次执行操作之前，必须先判断节流阀是否为空。

     - 延时器的作用可以做节流阀或是做防抖策略

       ```
       $(function() {
         var angel = $('#angel')
         var timer = null // 1.预定义一个 timer 节流阀
         $(document).on('mousemove', function(e) {
           if (timer) { return } // 3.判断节流阀是否为空，如果不为空，则证明距离上次执行间隔不足16毫秒
           timer = setTimeout(function() {
             $(angel).css('left', e.pageX + 'px').css('top', e.pageY + 'px')
             timer = null // 2.当设置了鼠标跟随效果后，清空 timer 节流阀，方便下次开启延时器
           }, 16)
         })
       })
       
       ```

       

11. 节流阀和防抖的区别

       防抖：如果事件被频繁触发，防抖能保证只有最后一次触发生效！前面 N 多次的触发都会被忽略！

       节流：如果事件被频繁触发，节流能够减少事件触发的频率，因此，节流是有选择性地执行一部分事件！
