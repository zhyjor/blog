###在客户端存储数据
h5提供了两种在客户端存储数据的新方法

* localStorage-没有时间限制的数据存储
* sessionStorage - 针对一个 session 的数据存储（关闭网页后数据不再保存）
* cookie机制采用的是在客户端保持状态的方案，而session机制采用的是在服务器端保持状态的方案。

不同的网站，数据存储于不同的区域，并且一个网站只能访问自身的数据，BTW，数据访问不能跨域,不同的网站不能使用相同的数据库

###1. 本地存储
####1.1 localStorage方式
localStorage存储的数据没有时间限制，不会失效；在当前域下都可以直接获取                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                

<pre>
<code>
//添加或修改
window.localStorage.setItem("name","zhyjor")
//获取
alert(window.localStorage.getItem("name"));
//删除
window.localStorage.removeItem(name)
//清空
window.localStorage.clear();

//也可与通过数组和“.”操作符来添加和获取
window.localStorage.name;
window.localStorage["name"]

//使用key()获取键的集合
 for(var i=0;i < window.localStorage.length;i++){
   var key=window.localStorage.key(i);
   console.log(key);
 }


</code>
</pre>

注意：

* localStorage只支持string类型的存储。无论存储的是什么数据结构，取出的都是string，Json数据需要经过stringfy（）转换。读取的时候需要解析parse()
* 官方推荐的是getItem\setItem这两种方法对其进行存取
* localStorage拓展了cookie的4K限制,相当于一个5M大小的针对于前端页面的数据库。
* localStorage不能被爬虫抓取到
* IE8以上的IE版本才支持localStorage这个属性
* 不同浏览器无法共享localStorage或sessionStorage中的信息。相同浏览器的不同页面间可以共享相同的localStorage（页面属于相同域名和端口）

####1.2  sessionStorage 方法
localStorage与sessionStorage的唯一一点区别就是localStorage属于永久性存储，而sessionStorage属于当会话结束的时候，sessionStorage中的键值对会被清空。

sessionStorage 方法针对一个 session 进行数据存储。当用户关闭浏览器窗口后，数据会被删除。

两者的方法相同！

####1.3 storage事件
当储存的数据发生变化时，会触发storage事件。我们可以指定这个事件的回调函数。

<pre><code>
window.addEventListener("storage",function onStorageChange(event) {  
     console.log(event.key);      
});  
</code></pre>

event一个有四个属性

* key:发生变化的键名
* oldValue:更新前的值。如果该键为新增加，则这个属性为null
* newValue:更新后的值。如果该键被删除，则这个属性为null。 
* url：原始触发storage事件的那个网页的网址。 



监听storage变更事件会发现，当数据发生变化时本页是监听不到storage事件变更消息的。而同域的其他打开的页面反而监听到了该消息。**该事件不在导致数据变化的当前页面触发**。一个页面改变sessionStorage或localStorage的数据时，同域的其他打开的页面的storage事件会被触发，而原始页面并不触发storage事件。可以通过这种机制，**实现多个窗口之间的通信**。IE浏览器除外，它会在所有页面触发storage事件。

####1.4 storage事件当前界面捕获

如何在当前页响应这些修改数据库的事件呢？

当前页面获取storage这个是有必要的，尤其是在**单页面（SPA应用）开发时**，这种限制几乎是致命的。单页面应用只在一个页面中渲染不同的内容，因此我们需要重写localStorage的方法来达到可以监听自己本身页面修改的效果。

此时我们需要重写localStorage的方法，并设置自定义的事件

<pre>
<code>
var orignalSetItem = localStorage.setItem;
localStorage.setItem = function(key,newValue){
    var setItemEvent = new Event("setItemEvent");
    setItemEvent.newValue = newValue;
    window.dispatchEvent(setItemEvent);
    orignalSetItem.apply(this,arguments);
}
window.addEventListener("setItemEvent", function (e) {
    alert(e.newValue);
});
localStorage.setItem("nm","1234");
</code>
</pre>



###2. Cookies方式
cookie数据始终在同源的http请求中携带，cookie在浏览器和服务器之间进行传递，cookie的数据还有path的概念，可以限制cookies只属于某个路径下，cookie数据不能超过4k，同时因为每次http请求都会携cookie，所以cookie只适合保存很小的数据，如会话标识。


---
---
cookie，localStorage，sessionStorage都可以实现客户端存储，三者的区别有哪些了？

cookie作为最早期的被设计web浏览器存储少量数据，从底层看，它是作为http协议的一种扩展实现。cookie数据会自动在web浏览器和web服务器之间传输数据。

cookie有效期：cookie默认有效期非常短暂，存在于web浏览器会话期间，当浏览器关闭，cookie也就消失了。如果要延长cookie的有效期，可以设置max-age属性（单位秒）

cookie作用域：cookie作用域是通过domain文档源和path文档路径来确定的。默认情况下，cookie和创建它的web页面有关，并对web页面和该web页面同目录或者子目录的其他web页面可见。当设置path="/"，它的作用域就变成文档源级别的了。

localStorage, sessionStorage是HTML5中新增的web存储的功能，它解决了客户端存储的一些缺点，并提供更强大的功能和操作API。

localStorage有效期：永不失效，除非web应用主动删除。

localStorage作用域：localStorage的作用域是限定在文档源级别的。文档源通过协议、主机名以及端口三者来确定。

sessionStorage有效期：sessionStorage的有效期是和存储数据脚本所在的最顶层的窗口或者是浏览器标签是一样的，一旦窗口或者标签页被永久关闭了，存储的数据也就失效了。

sessionStorage作用域：sessionStorage的作用域也是限定在文档源级别。但需要注意的是，如果相同文档源的页面渲染在不同的标签中，sessionStorage的数据是无法共享的。

除了上述列举的，cookie和localStorage、sessionStorage在存储大小的限制也不一样， 由于每个浏览器的实现标准都不一样，只说一下RFC 2965推荐标准（浏览器保存cookie不超过300个，为每个服务器保存的cookie不超过20个，每个cookie大小不超过4KB）。localStorage、sessionStorage设置值时可以达到8M.
 

###3.Session与Cookie的区别与联系
详情请看另一片博客xxxxx