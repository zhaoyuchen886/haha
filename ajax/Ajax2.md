# 1. 响应数据格式

## 1.1 简介

服务器返回的数据不一定是非常简单的字符串，比如在获取留言板中的所有留言的时候，这时获取的数据就比较多，思考，如果是你，你希望返回什么格式的数据呢？希望是一大段拼接到一起的字符串还是一个JS数组或对象呢？

答案肯定是数组或对象，因为数组或对象操作起来更加方便。

```js
// 下面的字符串，表示所有的留言，是不好的
var liuyan = '段老湿：阿萨德发阿萨德发；刘老师：阿萨德饭店; 王老师: 你以为你是小子';

// 下面的表示留言的数据格式非常好
var liuyan = [
    {name:'张三', message:'哈哈哈哈哈,我喝多了'},
    {name:'李四', message:'呵呵呵'},
    {name: 'xx', message: 'xxxxxx'},
    {name: '王老师', message: '你以为你是校长;先'}
];

// 下面是PHP数组的表示方法
array(
	array('wanglaoshi', '你以为'),
    array('wanglaoshi', '你以为'),
    array('wanglaoshi', '你以为'),
);
```

但是不同语言之间的数组和对象语法又不同，所以服务器直接返回该语言的数组或对象是不行的。

语言设计人员早已意识到这个问题，所以专门设计了两种数据表示格式，他们分别是`JSON`和`XML`。在服务器和浏览器之间传输数据的时候，需要先把数据转换成双方都能够识别的格式，即`JSON`或`XML`。这就犹如中国人和其他国家人交流时需要找个翻译一样。

![1560043442970](Ajax2.assets/1560043442970.png)

## 1.2 JSON

JSON（JavaScript Object **Notation**：JS对象**表示法**） 是一种通过普通**==字符串==**描述数据的手段，用于表示有结构的数据。类似于编程语言中字面量的概念，语法上跟 JavaScript 的字面量非常类似。

> 别看JSON长得像JS中的各种数据，但JSON的本质是字符串。

### 数据类型

- null

    ```json
    null
    ```

- number

    ```json
    2048
    ```

- boolean

    ```
    true
    ```

- string

    ```
    "hello"
    ```

- object

    ```
    {
      "name": "zce",
      "age": 18,
      "gender": true,
      "girl_friend": null,
      "arr": []
    }
    ```

- array

    ```
    ["zhangsan", "lisi", "wangwu"]
    ```

### 注意

1. JSON 中属性名称==必须用双引号==包裹
2. JSON 中表述字符串（==值）必须使用双引号==
3. JSON 中不能有单行或多行注释
4. JSON 没有 `undefined` 这个值
5. 一个完整的JSON，不能有其他内容掺杂，必须是一个完整的 “数组” 或完整的 “对象” 

### JSON 数据转换

- JSON 格式转JS数据
    - `JS = JSON.parse(JSON)`
- JS数据转JSON
    - `JSON = JSON.stringify(JS);`

```js
		// 定义一些JS格式的数据
        var a = ['张悦', '假冰冰', '老狗', '苏老湿'];
        var b = true;
        var o = {id: 1, name: '刘老湿', nickname: '北狗最光阴'};

        // 把JS数据转成JSON数据  （JSON.stringify()）
        var jsonA = JSON.stringify(a);
        var jsonB = JSON.stringify(b);
        var jsonO = JSON.stringify(o);
        console.log(jsonA)
        console.log(jsonB)
        console.log(jsonO)

        // 把JSON数据转成JS数据 （JSON.parse()）
        console.log(JSON.parse(jsonA));
        console.log(JSON.parse(jsonB));
        console.log(JSON.parse(jsonO));

        // console.log(JSON.parse('[]{}')); // 报错了，因为JSON格式不是一个完整的JSON
```



### JSON 表述

有了 JSON 这种格式，我们就可以更加容易的表示拥有复杂结构的数据了。

> **注意**：
>
> - 不管是 JSON 也好，还是 XML，只是在 AJAX 请求过程中用到，并不代表它们与 AJAX 之间有必然的联系，它们只是数据协议罢了。
> - 不管服务端是采用 XML 还是采用 JSON 本质上都是将数据返回给客户端。
> - 服务端应该根据响应内容的格式设置一个合理的 Content-Type。

## 1.3 XML

HTML:超文本标记语言

XML: e==X==tension ==M==arkup ==L==anguage   可扩展标记语言

一种数据描述手段

老掉牙的东西，简单演示一下，不在这里浪费时间，基本现在的项目不用了。

淘汰的原因：数据冗余太多

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<students>
	<stu id="1">
    	<name>张三</name>
        <age>18</age>
        <sex>男</sex>
        <other height="175cm" weight="65kg" />
    </stu>
    <stu id="2">
    	<name>李四</name>
        <age>20</age>
        <sex>女</sex>
        <other height="170cm" weight="60kg" />
    </stu>
</students>
```

XML语法规范：

- 和html写法差不多
- 有且只有一个根标签
- 标签区分大小写
- 标签必须闭合
- 属性值必须加引号

如果服务器返回的是XML格式的数据，JS收到数据之后，把收到的数据当做document对象来处理即可。

# 2. 留言板案例

## 2.1 功能介绍及postman工具

- 案例功能介绍

    - 刷新页面，发送Ajax请求( /getMsg )，向服务器请求数据（所有的留言数据）
    - 点击提交按钮，发送Ajax请求(/addMsg)，向服务器发送新添加的数据，完成添加

- 用到的接口

    ![1560045830431](Ajax2.assets/1560045830431.png)

- 测试接口的软件postman介绍

    - 安装软件，一路next
    - 演示发送请求

    ![1560046299110](Ajax2.assets/1560046299110.png)

## 2.2 发送请求，获取所有留言

**开发案例前的准备工作**：

首先将message.html中的JS代码全部删除，修改css路径为本地路径。然后通过浏览器访问127.0.0.1:4000/message.html即可看到初始的页面效果。

初始的页面中，只有一条假的留言（张三，哈哈哈哈哈哈）。

所以下面写Ajax代码，发送Ajax请求，到 /getMsg 接口。获取所有的留言。

```js
// 1、创建XHR对象
var xhr = new XMLHttpRequest();
// 2、设置请求方式、请求url
xhr.open('GET', '/getMsg');
// 3、发送
xhr.send(null);
// 4、当请求响应过程结束的时候，接收服务器返回的数据
xhr.onload = function () {
		// 接收服务器返回的结果，即响应的结果
      var result = this.response;
      // console.log(result);
      // 下面只需要将服务器返回的所有留言展示到页面上
      // 1. 需要将JSON格式的数据转换成JS数组
      var data = JSON.parse(result);
      console.log(data);
      // 2. 循环JS数组，循环的时候，拼接li
      var str = '';
      for (var i = 0; i < data.length; i++) {
        str += `<li class="media">
                  <img class="mr-3" src="./assets/avatar.png">
                  <div class="media-body">
                    <h4>${data[i].name}</h4>
                    <p>${data[i].content}</p>
                  </div>
                </li>`;
      }
      // 3. 循环结束，把拼接好的所有li放到ul中
      document.getElementById('messages').innerHTML = str;
};
```

==整个请求响应过程==：

1. 输入地址 <http://127.0.0.1:3000/message.html> 向服务器发送请求，请求message.html 页面
2. 服务器返回给浏览器 message.html 中的代码
3. 浏览器接受到 message.html 中的代码，然后解析
    1. 遇到加载css的link标签，再次去服务器请求css文件
    2. 遇到图片的img标签，再次去服务器请求图片
    3. 遇到js代码，发送Ajax请求，请求所有的留言数据
        1. 服务器响应的结果是JSON格式的留言数据
        2. 将JSON格式的数据转成JS数组
        3. 通过拼接li的方式，拼接所有的留言
        4. 把拼接好的所有的li，放到ul中

## 2.3 点击按钮，发送请求，添加留言

> 添加完留言，要想实时看到新添加的留言，则需要在添加完成之后，从新从服务器获取所有留言。为了方便，将前面的获取留言的代码封装成 `loadData` 函数。

下面是添加留言的代码：

```js
// 点击提交按钮的时候，取得输入框的内容。然后将内容使用Ajax发送给 /addMsg 接口
    document.getElementById('btn_send').onclick = function () {
      // 获取输入框的DOM对象
      var chenghu = document.getElementById('txt_name');
      var neirong = document.getElementById('txt_content');
      // 发送Ajax请求，将输入的值提交给服务器的 /addMsg 接口
      var xhr = new XMLHttpRequest();
      xhr.open('POST', '/addMsg');
      xhr.setRequestHeader('Content-Type', 'application/x-www-form-urlencoded');
      xhr.send(`name=${chenghu.value}&content=${neirong.value}`);
      xhr.onload = function () {
        // 添加成功返回true，失败返回false
        if (this.response === 'true') {
          // 让新添加的留言显示到页面中
          // location.reload(); // 让页面刷新
          loadData();
          // 清空输入框的内容
          chenghu.value = '';
          neirong.value = '';
        } else {
          alert('添加失败');
        }
      }
    }
```

# 3. 模板引擎

## 3.1 模板引擎介绍

客户端中拿到请求的数据过后最常见的就是把这些数据呈现到界面上。

如果数据结构简单，可以直接通过字符串操作（拼接）的方式处理，但是如果数据过于复杂，字符串拼接维护成本太大，就不推荐了。

> 模板引擎：
>
> - artTemplate：https://aui.github.io/art-template/

模板引擎实际上就是一个 API，模板引擎有很多种，使用方式大同小异，目的为了可以更容易更高效的将数据渲染到HTML字符串中。==通俗的说，模板引擎的目的就是将服务器返回的数据显示到HTML页面中==。

## 3.2 使用模板引擎步骤

1. 准备一个存放数据的盒子（不是必须的，使用body也可以）
2. 引入template-web.js文件
3. 定义模板（具体语法可以去官网查看），一定要指定script的id和type属性
4. 调用template函数，为模板分配数据，template函数有两个参数一个返回值
    1. 参数1：模板的id
    2. 参数2：分配的数据，必须是一个JS对象的形式
    3. 一个返回值：是数据和模板标签组合好的结果
5. 将 “拼接” 好的结果放到准备好的盒子中（不是必须的，console出来也可以看结果）

```html
<!-- 使用模板引擎-1.加载js文件 -->
<script src="./assets/template-web.js"></script>

<!-- 使用模板引擎-2.设置模板 -->
<script id="ss" type="text/html">
        <h1>{{title}}</h1>
        <p>我是老汤，我今年{{age}}岁了</p>
</script>

<script>
    // 使用模板引擎-3.调用template函数
    // var 模板和数据组合好的结果 = template(模板id, 模板中使用的数据必须是js对象类型);
    var data = {
        title: '这是模板引擎的例子',
        age: 20
    };
    // “拼接”
    // 调用插件提供的方法template。将数据和模板组合起来。
    var str = template('ss', data);
    console.log(str);
    /*
        <h1>这是模板引擎的例子</h1>
        <p>我是老汤，我今年20岁了</p>
        */
    document.body.innerHTML = str;

</script>
```

> 定义模板时的script标签一定好指定id和type
>
> tempalte函数语法：var html = template(模板id,  Object);

## 3.3 模板语法

- 输出普通数据（字符串、数值等）

    ```
    // 模板写法
    {{var}}
    
    // template函数写法
    var html = template('id', {
        var: 'hello world'
    });
    ```

- 条件

    ```
    // 模板写法
    {{if age > 18}}
    	大于18
    {{else}}
    	小于18
    {{/if}}
    
    // template函数写法
    var html = template('id', {
        age: 20
    });
    ```

- 循环

    ```
    // 模板写法
    {{each arr}}
    	{{$index}} -- 数组的下标
    	{{$value}} -- 数组的值
    {{/each}}
    
    // template函数写法
    var html = template('id', {
        arr: ['apple', 'banana', 'orange']
    });
    ```

完整的代码：

```html
	<script src="./assets/template-web.js"></script>

    <!-- 定义模板 -->
    <script id="xxx" type="text/html">
        <h1>{{title}}</h1>
        <p>{{author}}</p>
        <p>{{content}}</p>
        
        {{if age > 18}}
        <p>欢迎来玩~</p>
        {{else}}
        <p>禁止未成年进入~</p>
        {{/if}}

        <ul>
            {{each hero}}
            <li> {{$index}} - {{$value}} </li>
            {{/each}}
        </ul>
    </script>

    <script>
        // 调用template函数
        var html = template('xxx', {
            title: '咏鹅',
            author: '骆宾王',
            content: '鹅鹅鹅，曲项向天歌',
            age: 20,
            hero: ['亚索', '德玛', '德邦', '皇子']
        });

        /*
        <h1>咏鹅</h1>
        <p>骆宾王</p>
        <p>鹅鹅鹅，曲项向天歌</p>
        */
       console.log(html);
       document.body.innerHTML = html;
    </script>
```



## 3.4 案例中使用模板引擎处理响应数据

```html
<!-- 引入template-web.js -->
<script src="/template-web.js"></script>

<!-- 定义模板 -->
<script id="xxx" type="text/html">
    {{each abcd}}
    <li class="media">
      <img class="mr-3" src="avatar.png" alt="">
      <div class="media-body">
        <h4>{{$value.name}}</h4>
        <p>{{$value.content}}</p>
    </div>
    </li>
    {{/each}}
</script>
```

```js
xhr.onload = function () {
		// 完全接收到服务器返回的数据
    	var result = this.responseText;
    	// 将JSON格式的数据，转换成JS数据
    	data = JSON.parse(result);
    	// 使用模板引擎，不用拼接字符串了
    	var str = template('xxx', {
      		abcd: data
    	});
    	// 把处理好的html放到ul中
    	document.getElementById('messages').innerHTML = str;
};
```

# 总结

- 请求响应
    - 请求：指的是浏览器向服务器发送的请求
        - GET
            - 浏览器地址栏输入地址，按回车
            - 超链接跳转
            - form表单提交（不要设置method属性）
            - Ajax的GET方式请求 -- 需要通过JS代码来发请求
        - POST
            - form表单提交（必须设置method属性，属性必须为POST）
            - Ajax的POST请求 -- 需要通过JS代码来发请求
    - 响应
        - 浏览器请求什么，服务器就响应什么
        - 服务器响应的不是文件下载，而是文件中的源代码
- 能够发送ajax的GET和POST请求
    - GET
        - 4步
        - 传参的话，参数要写到url的后面
    - POST
        - 5步
        - 传参的话，参数要当做send的参数
- GET和POST的区别（Ajax请求和非Ajax）
    - 字母意思不同，如果请求希望从服务器获取数据，则使用GET方式；如果请求是把数据提交给服务器，则使用POST方式
    - GET请求传递大小有限制，一般限制为2k；POST请求传参大小没有限制
    - GET请求传递的参数在url上，不是很安全；POST请求传递的数据不在url上，稍微安全一些
    - GET请求不能上传文件；POST请求可以上传文件
- JSON
    - 两种编程语言交互的数据的时候，互相不认识对方的数据，所以要把数据转成大家都认识的格式，就是JSON
    - var js = JSON.parse(json数据);
    - var json = JSON.stringify(JS数据);
- 模板引擎
    - 步骤
        - 加载template-web.js
        - 定义模板
            - 要使用script标签
            - 要设置id属性
            - 要设置type属性
        - 调用template函数
            - 参数1：模板的id
            - 参数2：必须是js对象，表示模板中使用的数据
            - 返回值：模板和数据组合好的结果
    - 模板语法
        - {{title}}
        - {{if 条件}} ..... {{/if}}
        - {{each 变量}}   {{$index}} {{$value}}   {{/each}}