# 一、Javascript的组成

JavaScript的实现包括以下3个部分：

| ECMAScript(核心)      | 描述了JS的语法和基本对象。 |
| --------------------- | -------------------------- |
| 文档对象模型 （DOM）  | 处理网页内容的方法和接口   |
| 浏览器对象模型（BOM） | 与浏览器交互的方法和接口   |

javascript 有三部分构成，ECMAScript，DOM和BOM，根据宿主（浏览器）的不同，具体的表现形式也不尽相同，ie和其他的浏览器风格迥异,IE 扩展了 BOM，加入了 ActiveXObject 类，可以通过 JavaScript 实例化 ActiveX 对象。

1. DOM 是 W3C 的标准； [所有浏览器公共遵守的标准]
2. BOM 是 各个浏览器厂商根据 DOM在各自浏览器上的实现;[表现为不同浏览器定义有差别,实现方式不同]
3. window 是 BOM 对象，而非 js 对象；javacsript是通过访问BOM（Browser Object Model）对象来访问、控制、修改客户端(浏览器)

**ECMAScript扩展知识：**

① ECMAScript是一个标准，JS只是它的一个实现，其他实现包括ActionScript。

② “ECMAScript可以为不同种类的宿主环境提供核心的脚本编程能力……”，即ECMAScript不与具体的宿主环境相绑定，如JS的宿主环境是浏览器，AS的宿主环境是Flash。

③ECMAScript描述了以下内容：语法、类型、语句、关键字、保留字、运算符、对象。

# 二、DOM, DOCUMENT, BOM, WINDOW 区别

DOM 是为了操作文档出现的 API，document 是其的一个对象；
BOM 是为了操作浏览器出现的 API，window 是其的一个对象。

BOM是浏览器对象模型，DOM是[文档对象模型](https://www.baidu.com/s?wd=文档对象模型&tn=44039180_cpr&fenlei=mv6quAkxTZn0IZRqIHcvrjTdrH00T1Y4mvn3mWKWmWT4nW99myRv0ZwV5Hcvrjm3rH6sPfKWUMw85HfYnjn4nH6sgvPsT6KdThsqpZwYTjCEQLGCpyw9Uz4Bmy-bIi4WUvYETgN-TLwGUv3EnHTvPH6Ln1DzPH0snWR1P1fvn0)，前者是对浏览器本身进行操作，而后者是对浏览器（可看成容器）内的内容进行操作

![img](https://img-blog.csdn.net/20160826135326655?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

**归DOM管的：**

E区（就是你说的document啦。由web开发人员呕心沥血写出来的一个文件夹，里面有index.html，CSS和JS什么鬼的，部署在服务器上，我们可以通过浏览器的地址栏输入URL然后回车将这个document加载到本地，浏览，右键查看源代码等。

**归BOM管的：**

A区（浏览器的标签页，地址栏，搜索栏，书签栏，窗口放大还原关闭按钮，菜单栏等等）

B区（浏览器的右键菜单）

C区（document加载时的状态栏，显示http状态码等）

D区（滚动条scroll bar）

## 2.1 DOM

DOM 全称是 Document Object Model，也就是文档对象模型。是针对XML的基于树的API。描述了处理网页内容的方法和接口，是HTML和XML的API，DOM把整个页面规划成由节点层级构成的文档。

这个DOM定义了一个HTMLDocument和HTMLElement做为这种实现的基础,就是说为了能以编程的方法操作这个 HTML 的内容（比如添加某些元素、修改元素的内容、删除某些元素），我们把这个 HTML 看做一个对象树（DOM树），它本身和里面的所有东西比如 <div></div> 这些标签都看做一个对象，每个对象都叫做一个节点（node），节点可以理解为 DOM 中所有 Object 的父类。

![img](https://img-blog.csdn.net/20160826135305647?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

DOM 有什么用？就是为了操作 HTML 中的元素，比如说我们要通过 JS 把这个网页的标题改了，直接这样就可以了：

```javascript
 document.title = 'how to make love';
```

这个 API 使得在网页被下载到浏览器之后改变网页的内容成为可能。

## 2.2 document

当浏览器下载到一个网页，通常是 HTML，这个 HTML 就叫 document（当然，这也是 DOM 树中的一个 node），从上图可以看到，document 通常是整个 DOM 树的根节点。这个 document 包含了标题（document.title）、URL（document.URL）等属性，可以直接在 JS 中访问到。

在一个浏览器窗口中可能有多个 document，例如，通过 iframe 加载的页面，每一个都是一个 document。

在 JS 中，可以通过 document 访问其子节点（其实任何节点都可以），如

document.body;document.getElementById('xxx');

## 2.3 BOM

BOM 是 Browser Object Model，浏览器对象模型。

刚才说过 DOM 是为了操作文档出现的接口，那 BOM 顾名思义其实就是为了控制浏览器的行为而出现的接口。
浏览器可以做什么呢？比如跳转到另一个页面、前进、后退等等，程序还可能需要获取屏幕的大小之类的参数。
所以 BOM 就是为了解决这些事情出现的接口。比如我们要让浏览器跳转到另一个页面，只需要

location.href = "http://www.xxxx.com";这个 location 就是 BOM 里的一个对象。

由于BOM的window包含了document，因此可以直接使用window对象的document属性，通过document属性就可以访问、检索、修改XHTML文档内容与结构。因为document对象又是DOM（Document Object Model）模型的根节点。

可以说，BOM包含了DOM(对象)，浏览器提供出来给予访问的是BOM对象，从BOM对象再访问到DOM对象，从而js可以操作浏览器以及浏览器读取到的文档。

## 2.4 window

window 也是 BOM 的一个对象，除去编程意义上的“兜底对象”之外，通过这个对象可以获取窗口位置、确定窗口大小、弹出对话框等等。例如我要关闭当前窗口：

window.close();

# 三、浏览器对象模型(BOM)

![img](https://img-blog.csdn.net/20160826135345273?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)



## 3.1 Window对象

Window 对象是 JavaScript 层级中的顶层对象。

Window 对象代表一个浏览器窗口或一个框架。

Window 对象会在 <body> 或 <frameset> 每次出现时被自动创建。	

**1）对象属性**

成员对象属性

window //窗户自身, window=window.self 可使用全局属性 window访问　Window对象

document 对 Document 对象的只读引用。请参阅 Document 对象。

history 对 History 对象的只读引用。请参数 History 对象。

location 用于窗口或框架的 Location 对象。请参阅 Location 对象。

screen 对 Screen 对象的只读引用。请参数 Screen 对象。

navigator 对 Navigator 对象的只读引用。请参数 Navigator 对象。

external.AddFavorite("地址","标题" ) //把网站新增到保藏夹

基本属性

| 属性                                    | 描述                                                         |
| --------------------------------------- | ------------------------------------------------------------ |
| closed                                  | 返回窗口是否已被关闭。                                       |
| defaultStatus                           | 设置或返回窗口状态栏中的默认文本。                           |
| innerHeight                             | 返回窗口的文档显示区的高度。                                 |
| innerWidth                              | 返回窗口的文档显示区的宽度。                                 |
| length                                  | 设置或返回窗口中的框架数量。                                 |
| name                                    | 设置或返回窗口的名称。                                       |
| opener                                  | 返回对创建此窗口的窗口的引用。                               |
| outerheight                             | 返回窗口的外部高度。                                         |
| outerwidth                              | 返回窗口的外部宽度。                                         |
| pageXOffset                             | 设置或返回当前页面相对于窗口显示区左上角的 X 位置。          |
| pageYOffset                             | 设置或返回当前页面相对于窗口显示区左上角的 Y 位置。          |
| parent                                  | 返回父窗口。                                                 |
| self                                    | 返回对当前窗口的引用。等价于 Window 属性。                   |
| window                                  | window 属性等价于 self 属性，它包含了对窗口自身的引用。      |
| status                                  | 设置窗口状态栏的文本。                                       |
| top                                     | 返回最顶层的先辈窗口。                                       |
| screenLeft、screenTop、screenX、screenY | 只读整数。声明了窗口的左上角在屏幕上的的 x 坐标和 y 坐标。IE、Safari 和 Opera 支持 screenLeft 和 screenTop，而 Firefox 和 Safari 支持 screenX 和 screenY。 |

**2）对象方法**

> window.close(); //关闭窗口
>
> window.alert("message"); //弹出一个具有OK按钮的系统消息框，显示指定的文本
>
> window.confirm("Are you sure?"); //弹出一个具有OK和Cancel按钮的询问对话框，返回一个布尔值
>
> window.prompt("What's your name?", "Default"); //提示用户输入信息，接受两个参数，即要显示给用户的文本和文本框中的默认值，将文本框中的值作为函数值返回
>
> window.status //可以使状态栏的文本暂时改变
>
> window.defaultStatus //默认的状态栏信息，可在用户离开当前页面前一直改变文本
>
> window.setTimeout("alert('xxx')", 1000); //设置在指定的毫秒数后执行指定的代码，接受2个参数，要执行的代码和等待的毫秒数
>
> window.clearTimeout("ID"); //取消还未执行的暂停，将暂停ID传递给它
>
> window.setInterval(function, 1000); //无限次地每隔指定的时间段重复一次指定的代码，参数同setTimeout()一样
>
> window.clearInterval("ID"); //取消时间间隔，将间隔ID传递给它
>
> window.history.go(-1); //访问浏览器窗口的历史，负数为后退，正数为前进
>
> window.history.back(); //同上
>
> window.history.forward(); //同上
>
> window.history.length //可以查看历史中的页面数  
>
> clearInterval() 取消由 setInterval() 设置的 timeout。
>
> clearTimeout() 取消由 setTimeout() 方法设置的 timeout。
>
> createPopup() 创建一个 pop-up 窗口。
>
> moveBy() 可相对窗口的当前坐标把它移动指定的像素。
>
> moveTo() 把窗口的左上角移动到一个指定的坐标。
>
> open() 打开一个新的浏览器窗口或查找一个已命名的窗口。
>
> print() 打印当前窗口的内容。
>
> resizeBy() 按照指定的像素调整窗口的大小。
>
> resizeTo() 把窗口的大小调整到指定的宽度和高度。
>
> scrollBy() 按照指定的像素值来滚动内容。
>
> scrollTo() 把内容滚动到指定的坐标。
>
> setInterval() 按照指定的周期（以毫秒计）来调用函数或计算表达式。
>
> setTimeout(方法,秒数) 在指定的毫秒数后调用函数或计算表达式。　
>
> timeOutEvent = setTimeout("longPress('" + obj + "')",1500);定时器传参数

**扩展**

① 如果文档包含框架（frame 或 iframe 标签），浏览器会为 HTML 文档创建一个 window 对象，并为每个框架创建一个额外的 window 对象。

② window.frames 返回窗口中所有命名的框架

③parent是父窗口（如果窗口是顶级窗口，那么parent==self==top）

top是最顶级父窗口（有的窗口中套了好几层frameset或者iframe）

self是当前窗口（等价window）

opener是用open方法打开当前窗口的那个窗口

④与消息框有关的方法：alert(String)、confirm(String)、prompt(String)

⑤两种定时器：setTimeout(code,latency) 和 setInterval(code,period)

注：setTimeout只执行一次code，如果要多次调用，可以让code自身再次调用setTimeout()；setInteval()会不停地调用code，直到clearInterval()被调用。

## 3.2 History

```javascript
window.history.length //浏览过的页面数

history.back() //在浏览历史里后退一步

history.forward() //在浏览历史里前进一步

history.go(i) //到汗青详细登记单的第i位

//i>0进步,i<0撤退退却
```

## 3.3 Screen对象

```javascript
screen对象：用于获取某些关于用户屏幕的信息，也可用window.screen引用它

window.screen.width //屏幕宽度

window.screen.height //屏幕高度

window.screen.colorDepth //屏幕颜色深度

window.screen.availWidth //可用宽度(除去任务栏的高度)

window.screen.availHeight //可用高度(除去任务栏的高度)
```

## 3.4 Navigator对象

navigator`对象：包含大量有关Web浏览器的信息，在检测浏览器及操作系统上非常有用

```javascript
　　window.navigator.appCodeName //浏览器代码名

　　window.navigator.appName //浏览器步伐名

　　window.navigator.appMinorVersion //浏览器补钉版本

　　window.navigator.cpuClass //cpu类型 x86

　　window.navigator.platform //操作体系类型 win32

　　window.navigator.plugins

　　window.navigator.opsProfile

　　window.navigator.userProfile

　　window.navigator.systemLanguage //客户体系语言 zh-cn简体中文

　　window.navigator.userLanguage //用户语言,同上

　　window.navigator.appVersion //浏览器版本(包括 体系版本)

　　window.navigator.userAgent//用户代理头的字符串表示

　　window.navigator.onLine //用户否在线

　　window.navigator.cookieEnabled //浏览器是否撑持cookie

　　window.navigator.mimeTypes
```

## 3.5 Location对象

```javascript
location对象：表示载入窗口的URL，也可用window.location引用它

location.href //当前载入页面的完整URL，如http://www.somewhere.com/pictures/index.htm

location.portocol //URL中使用的协议，即双斜杠之前的部分，如http

location.host //服务器的名字，如www.wrox.com

location.hostname //通常等于host，有时会省略前面的www

location.port //URL声明的请求的端口，默认情况下，大多数URL没有端口信息，如8080

location.pathname //URL中主机名后的部分，如/pictures/index.htm

location.search //执行GET请求的URL中的问号后的部分，又称查询字符串，如?param=xxxx

location.hash //如果URL包含#，返回该符号之后的内容，如#anchor1

location.assign("http:www.baidu.com"); //同location.href，新地址都会被加到浏览器的历史栈中

location.replace("http:www.baidu.com"); //同assign()，但新地址不会被加到浏览器的历史栈中，不能通过back和forward访问

location.reload(true | false); //重新载入当前页面，为false时从浏览器缓存中重载，为true时从服务器端重载，默认为false

document.location.reload(URL) //打开新的网页
```

## 3.6 Document对象

    HtmlDocument 为 Internet Explorer 的文档对象提供托管包装，该文档对象也称为 HTML 文档对象模型 (DOM)。您可以通过 WebBrowser 控件的 Document 属性获取 HtmlDocument 的实例。

HTMLDocument 接口对 DOM Document 接口进行了扩展，定义 HTML 专用的属性和方法。

HTML 文档中的 HTML 标记可以相互嵌套。因此，HtmlDocument 表示一个文档树，其子级是 HtmlElement 类的实例。下面的代码示例演示一个简单的 HTML 文件。

HtmlDocument 表示 HTML 标记内的整个文档。BODY、DIV、FORM 和 SPAN 标记各由一个单独的 HtmlElement 对象表示。

**1）对象属性**

```javascript
document.body//提供对 <body> 元素的直接访问。对于定义了框架集的文档，该属性引用最外层的 <frameset>。

document.cookie 返回与当前文档有关的所有 cookie。

document.title //返回文档标题等价于HTML的title标签

document.domain 返回当前文档的域名。

document.URL //设置URL属性从而在同一窗口打开另一网页

document.referrer 返回载入当前文档的文档的 URL。

document.lastModified 返回文档被最后修改的日期和时间。
```

**2）常用对象方法**

```javascript
document.write() //动态向页面写入内容

document.writeln() 等同于 write() 方法，不同的是在每个表达式之后写一个换行符。

document.createElement(<Tag>) //用指定标签类型创建一个新的element对象)

document.getElementById(ID) //获得指定ID值的对象

document.getElementsByName(Name) //获得指定Name值的对象

document.getElementsByTagName() 返回带有指定标签名的对象集合。
```

**3）body主体子对象**

```javascript
document.body //指定文档主体的开始和结束等价于body>/body>

document.body.bgColor //设置或获取对象后面的背景颜色

document.body.link //未点击过的链接颜色

document.body.alink //激活链接(焦点在此链接上)的颜色

document.body.vlink //已点击过的链接颜色

document.body.text //文本色

document.body.innerText //设置body>…/body>之间的文本

document.body.innerHTML //设置body>…/body>之间的HTML代码

document.body.topMargin //页面上边距

document.body.leftMargin //页面左边距

document.body.rightMargin //页面右边距

document.body.bottomMargin //页面下边距

document.body.background //背景图片

document.body.appendChild(oTag) //动态生成一个HTML对象
```



## Document子对象HTMLElement对象详解

**HTML DOM 节点**

在 HTML DOM （文档对象模型）中，每个部分都是节点：

1.文档本身是文档节点

2.所有 HTML 元素是元素节点

3.所有 HTML 属性是属性节点

4.HTML 元素内的文本是文本节点

5.注释是注释节点

**Element 对象**

在 HTML DOM 中，Element 对象表示 HTML 元素。

Element 对象可以拥有类型为元素节点、文本节点、注释节点的子节点。

NodeList 对象表示节点列表，比如 HTML 元素的子节点集合。

元素也可以拥有属性。属性是属性节点

**获取**

document.getElementById(ID) //获得指定ID值的对象

document.getElementsByName(Name) //获得指定Name值的对象

getElementsByTagName() 返回带有指定标签名的对象集合。

**属性和方法**

方法里有a,b的参数仅仅是为了加深说明,其他元素没有a,b不代表是无参方法

Element.add(<class>)给元素添加指定的类

element.accessKey 设置或返回元素的快捷键。

element.appendChild() 向元素添加新的子节点，作为最后一个子节点。

element.attributes 返回元素属性的集合。

element.childNodes 返回元素子节点的 NodeList。

element.className 设置或返回元素的 class 属性。

element.clientHeight 返回元素的可见高度。

element.clientWidth 返回元素的可见宽度。

element.cloneNode() 克隆元素。

element.compareDocumentPosition() 比较两个元素的文档位置。

element.contentEditable 设置或返回元素的文本方向。

element.dir 设置或返回元素的文本方向。

element.firstChild 返回元素的首个子。

element.getAttribute() 返回元素节点的指定属性值。

element.getAttributeNode() 返回指定的属性节点。

element.getElementsByTagName() 返回拥有指定标签名的所有子元素的集合。

element.getFeature() 返回实现了指定特性的 API 的某个对象。

element.getUserData() 返回关联元素上键的对象。

Element.hidden获取或设置hidden属性的存在状态

element.hasAttribute() 如果元素拥有指定属性，则返回true，否则返回 false。

element.hasAttributes() 如果元素拥有属性，则返回 true，否则返回 false。

element.hasChildNodes() 如果元素拥有子节点，则返回 true，否则 false。

element.id 设置或返回元素的 id。

element.innerHTML 设置或返回元素的内容。

element.insertBefore(<a>,<b>) 在指定的已有的子节点之前插入新节点。A插到b前

element.isContentEditable 设置或返回元素的内容。

element.isDefaultNamespace() 如果指定的 namespaceURI 是默认的，则返回 true，否则返回 false。

element.isEqualNode(<a>) 检查a元素是否与当前元素相等。

element.isSameNode(a) 检查指定元素是否就是当前元素.

element.isSupported() 如果元素支持指定特性，则返回 true。

element.lang 设置或返回元素的语言代码。

element.lastChild 返回元素的最后一个子元素。

element.namespaceURI 返回元素的 namespace URI。

element.nextSibling 返回当前元素之后的兄弟元素

element.nodeName 返回元素的名称。

element.nodeType 返回元素的节点类型。

element.nodeValue 设置或返回元素值。

element.normalize() 合并元素中相邻的文本节点，并移除空的文本节点。

element.offsetHeight 返回元素的高度。

element.offsetWidth 返回元素的宽度。

element.offsetLeft 返回元素的水平偏移位置。

element.offsetParent 返回元素的偏移容器。

element.offsetTop 返回元素的垂直偏移位置。

element.ownerDocument 返回元素的根元素（文档对象）。

element.parentNode 返回元素的父节点。

element.previousSibling 返回当前元素之前的兄弟元素

Element.remove(<class>) 从元素移除指定的类

element.removeAttribute() 从元素中移除指定属性。

element.removeAttributeNode() 移除指定的属性节点，并返回被移除的节点。

element.removeChild(a) 从元素中移除子节点。

element.replaceChild(a,b) 替换元素中的子节点。

element.scrollHeight 返回元素的整体高度。

element.scrollLeft 返回元素左边缘与视图之间的距离。

element.scrollTop 返回元素上边缘与视图之间的距离。

element.scrollWidth 返回元素的整体宽度。

element.setAttribute() 把指定属性设置或更改为指定值。

element.setAttributeNode() 设置或更改指定属性节点。

element.setIdAttribute()

element.setIdAttributeNode()

element.setUserData() 把对象关联到元素上的键。

element.style 设置或返回元素的 style 属性。

Element.toggle(<class>)如果类不存在就添加它存在就移除它

element.tabIndex 设置或返回元素的 tab 键控制次序。

element.tagName 返回元素的标签名。

element.textContent 设置或返回节点及其后代的文本内容。

element.title 设置或返回元素的 title 属性。

element.toString() 把元素转换为字符串。

nodelist.item() 返回 NodeList 中位于指定下标的节点。

nodelist.length 返回 NodeList 中的节点数。

contentWindow 属性    如果文档里有iframe属性利用此属性可以返回iframe的内容

**事件监听方法单讲**

**element.addEventListener(event, function, useCapture)**

| 参数       | 描述                                                         |
| ---------- | ------------------------------------------------------------ |
| event      | 必须。字符串，指定事件名。注意: 不要使用 "on" 前缀。 例如，使用 "click" ,而不是使用 "onclick"。提示： 所有 HTML DOM 事件，可以查看我们完整的 HTML DOM Event 对象参考手册。 |
| function   | 必须。指定要事件触发时执行的函数。当事件对象会作为第一个参数传入函数。 事件对象的类型取决于特定的事件。例如， "click" 事件属于 MouseEvent(鼠标事件) 对象。 |
| useCapture | 可选。布尔值，指定事件是否在捕获或冒泡阶段执行。可能值:true - 事件句柄在捕获阶段执行，false- false- 默认。事件句柄在冒泡阶段执行 |

**Table 对象详解**
**Table 对象集合**

| 集合      | **描述**                              |
| --------- | ------------------------------------- |
| cells[]   | 返回包含表格中所有单元格的一个数组。  |
| rows[]    | 返回包含表格中所有行的一个数组。      |
| tBodies[] | 返回包含表格中所有 tbody 的一个数组。 |

rows 集合返回表格中所有行（TableRow 对象）的一个数组，该集合包括 <thead>、<tfoot> 和 <tbody> 中定义的所有行。



TableRow 对象

TableRow 对象代表一个 HTML 表格行。

在 HTML 文档中 <tr> 标签每出现一次，一个 TableRow 对象就会被创建。

 

cells 集合返回表格中所有 <td> 或 <th> 元素。

TableCell 对象

TableCell 对象代表一个 HTML 表格单元格。

在一个 HTML 文档中 <td> 标签每出现一次，一个 TableCell 对象就会被创建

 

**Table 对象方法**

| 方法            | 描述                                |
| --------------- | ----------------------------------- |
| createCaption() | 为表格创建一个 caption 元素。       |
| createTFoot()   | 在表格中创建一个空的 tFoot 元素。   |
| createTHead()   | 在表格中创建一个空的 tHead 元素。   |
| deleteCaption() | 从表格删除 caption 元素以及其内容。 |
| deleteRow()     | 从表格删除一行。                    |
| deleteTFoot()   | 从表格删除 tFoot 元素及其内容。     |
| deleteTHead()   | 从表格删除 tHead 元素及其内容。     |
| insertRow()     | 在表格中插入一个新行。              |

# 四、文档对象模型(DOM)



![img](https://img-blog.csdn.net/20160826135409617?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

   DOM模型将整个文档（XML文档和HTML文档）看成一个树形结构，

  在DOM中，HTML文档的层次结构被表示为一个树形结构。并用document对象表示该文档，树的每个子节点表示HTML文档中的不同内容。

     每个载入浏览器的 HTML 文档都会成为 Document 对象,Document是探索DOM的入口,利用全局变量document可以访问Document对象
## 4.1 认识DOM

![img](https://img-blog.csdn.net/20160826135427874?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

将HTML代码分解为DOM节点层次图:

![img](https://img-blog.csdn.net/20160826135444664?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

DOM通过创建树来表示文档，描述了处理网页内容的方法和接口，从而使开发者对文档的内容和结构具有空前的控制力，用DOM API可以轻松地删除、添加和替换节点。

**1）节点类型**

DOM规定文档中的每个成分都是一个节点（Node）,HTML文档可以说由节点构成的集合，DOM节点有:

| 类型                | 描述                                                         |
| ------------------- | ------------------------------------------------------------ |
| 元素节点（Element） | 上图中<html>、<body>、<p>等都是元素节点，即标签。            |
| 文本节点（Text）    | 向用户展示的内容，如<li>...</li>中的JavaScript、DOM、CSS等文本。 |
| 属性节点（Attr）    | 元素属性，元素才有属性,如<a>标签的链接属性href="http://www.baidu.com"。 |

**2） DOM节点三大属性（XML DOM）**

| 属性      | 描述                                                         |
| --------- | ------------------------------------------------------------ |
| nodeName  | 元素节点、属性节点、文本节点分别返回元素的名称、属性的名称和#text的字符串。 |
| nodeType  | 元素节点、属性节点、文本节点的nodeType值分别为1、2、3。      |
| nodeValue | 元素节点、属性节点、文本节点的返回值分别为null、属性值和文本节点内容。 |

## 4.2 DOM常见操作

Node为所有节点的父接口，其定义了一组公共的属性和方法，如下：

![img](https://img-blog.csdn.net/20160826135508681?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

1）**获取节点**

① 获取元素节点：通过document对象的三个方法获取

document.getElementById("ID")

document.getElementByName("name")

document.getElementsByTagName("p");

② 获取属性节点：属性节点附属于元素节点，可通过元素节点的getAttributeNode(attrName)方法获取属性节点，也可通过getAttribute(attrName)直接获取属性值。（与之相对的是Element接口的setAttribute(attrName , attrValue)方法，如果属性不存在，则直接添加到元素节点上）

③ 获取文本节点：文本节点为元素节点的子节点，可通过元素节点（Element接口）提供的childnodes()[index] 方法获得。

childNodes //NodeList，所有子节点的列表

firstChild //Node，指向在childNodes列表中的第一个节点  

lastChild //Node，指向在childNodes列表中的最后一个节点  

parentNode //Node，指向父节点  

previousSibling /Node，/指向前一个兄弟节点：如果这个节点就是第一个节点，那么该值为 null

nextSibling //Node，指向后一个兄弟节点：如果这个节点就是最后一个节点，那么该值为null

hasChildNodes()` //Boolean，当childNodes包含一个或多个节点时，返回真值

**2）改变节点**

① 改变属性节点的值：可以通过属性节点的nodeValue直接修改属性值，也可通过元素节点的setAttribute()方法改变。

② 改变文本节点的值：通过文本节点的nodeValue直接修改。

在HTML DOM中，获取和改变元素内容最简单方法是使用元素的innerHTML属性（innerText属性返回去掉标签的innerHTML）

**拓展：**

innerText、innerHTML、outerHTML、outerText

innerText: 表示起始标签和结束标签之间的文本

innerHTML: 表示元素的所有元素和文本的HTML代码

如：<div><b>Hello</b> world</div>的innerText为Hello world，innerHTML为Hello world

outerText: 与前者的区别是替换的是整个目标节点，问题返回和innerText一样的内容

outerHTML: 与前者的区别是替换的是整个目标节点，返回元素完整的HTML代码，包括元素本身

 

一张图了解OUTHTML和innerText、innerHTML：

![img](https://img-blog.csdn.net/20160826135542094?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

改变HTML样式（style属性）：element.style.color =“red”;

 

**3）删除节点**

 

① 删除元素节点：要想删除元素节点A，需获得A的父节点B，父节点可通过17.1.1中的方法获得，也可以直接通过A的parentNode属性获得（推荐）。调用B的removeChild(A) 即可删除A节点。

② 删除属性节点：可通过属性节点所属的元素节点的removeAttribute(attrName)或removeAttributeNode(node)删除。

③ 清空文本节点：最简单也是最常用的方法就是直接设置文本节点的nameNode属性为空串：textNode.nodeValue = ””。

 

**混淆点：**

> <p id="example" title="texts">
>
>   这是一段文本
>
>   <span></span>
>
> </p>
>
> var p = document.getElementById('example');
>
> p.nodeValue //null,p是元素节点，所以nodeValue为null
>
>  
>
> p.getAttributeNode('id').nodeValue  //example，这里获取到p的id属性的属性节点，nodeValue就是它的属性值
>
>  
>
> p.childNodes[0].nodeValue   //这是一段文本,
>
> p是含有两个子节点的，插入的文本虽然没有标签，但它依然是一个节点。
>
> 其类型是是文本节点，其nodeValue是就是写入在其中的字符串，包含换行和缩进
>
>  
>
> p.innerHTML//这是一段文本 <span></span>"
>
> 这里innerHTML返回了p所包含的全部的节点的所包含的各种值了，以字符串的形式。



**4）创建和添加节点**

 

① 创建节点：通过document对象的createElement(eleName)、createTextNode(nodeValue)方法可分别创建元素节点和文本节点。属性节点也有自己的create方法，但是用的少，直接用元素节点的setAttribute()方法即可添加属性。

② 添加节点：两个重要的方法：appendChild()和insertBefore()，具体见Node接口中的方法。

扩展：上面的两个方法都是在已知父节点时用到的，也可以直接在兄弟节点后添加新节点：x.insertBefore(newNode) 和 x.appendChild(newNode) 都可以向 x 后追加一个新的子节点。

 

**5）替换节点**

主要掌握replaceChild(newNode , oldNode) 替换元素节点。（属性节点和文本节点有更简单的方法

## 4.3 DOM事件

DOM同时两种事件模型：冒泡型事件和捕获型事件

冒泡型事件：事件按照从最特定的事件目标到最不特定的事件目标的顺序触发

<body οnclick="handleClick()">

  <div οnclick="handleClick()">Click Me</div>

</body>

触发的顺序是：div、body、html(IE 6.0和Mozilla 1.0)、document、window(Mozilla 1.0)

 

捕获型事件：与冒泡事件相反的过程，事件从最不精确的对象开始触发，然后到最精确

上面例子触发的顺序是：document、div

DOM事件模型最独特的性质是，文本节点也触发事件(在IE中不会)。

**1)事件处理/监听函数**

```javascript
var oDiv = document.getElementById("div1");

oDiv.onclick = function(){ //onclick只能用小写

      alert("Clicked!");

}
```

或者

```javascript
var elem =document.getElementById(“id”)

 

elem.οnmοuseοver=handleMouseOver  //handleMouseOver  是函数名

 

function handleMouseOve(e){...}

 

在HTML中：

<div οnclick="javascript: alert("Clicked!")"></div> //onclick大小写任意
```

**扩展:**

> IE事件处理程序attachEvent()和detachEvent()
>
> 在IE中，每个元素和window对象都有两个方法：attachEvent()和detachEvent()，这两个方法接受两个相同的参数，事件处理程序名称和事件处理程序函数，如：
>
> [object].attachEvent("name_of_event_handler","function_to_attach")
>
> [object].detachEvent("name_of_event_handler","function_to_remove")
>
> var fnClick = function(){
>
>   alert("Clicked!");
>
> }
>
> oDiv.attachEvent("onclick", fnClick); //添加事件处理函数
>
> oDiv.attachEvent("onclick", fnClickAnother); // 可以添加多个事件处理函数
>
> oDiv.detachEvent("onclick", fnClick); //移除事件处理函数
>
> 在使用attachEvent()方法的情况下，事件处理程序会在全局作用域中运行，因此this等于window。

**2) 跨浏览器的事件处理程序**

addHandler()和removeHandler()

addHandler()方法属于一个叫EventUntil()的对象，这两个方法均接受三个相同的参数，要操作的元素，事件名称和事件处理程序函数。

**3) 事件处理器**

执行JavaScript 代码的程序在事件发生时会对事件做出响应。为了响应一个特定事件

而被执行的代码称为事件处理器。

在HTML标签中使用事件处理器的语法是：

<HTML标签 事件处理器="JavaScript代码''>

**4)事件处理程序**

事件就是用户或浏览器自身执行的某种动作。比如click,mouseup,keydown,mouseover等都是事件的名字。而响应某个事件的函数就叫事件处理程序（事件监听器），事件处理程序以on开头，因此click的事件处理程序就是onclick

**5）DOM 0级事件处理程序**

DOM 0级事件处理程序：把一个函数赋值给一个事件的处理程序属性

> HTML
>
> <input type="button" value="按钮2" id="ben2"/>
>
> JS
>
>  var btn2=document.getElementById('btn2');获得btn2按钮对象
>
>   btn2.onclick      //给btn2添加onclick属性，属性又触发了一个事件处理程序
>
>  
>
> btn2.οnclick=function(){
>
> }                    //添加匿名函数
>
>  
>
> btn2.οnclick=null      //删除onclick属性
>

**6) DOM2级事件处理程序**

DOM 2级事件定义了两个方法，用于指定和删除事件处理程序的操作。addEventListener()和removeEventListener()

**addEventListener()和removeEventListener()**

在DOM中，addEventListener()和removeEventListener()用来分配和移除事件处理函数，与IE不同的是，这些方法需要三个参数：事件名称，要分配的函数和处理函数是用于冒泡阶段(false)还是捕获阶段(true)，默认为冒泡阶段false

[object].addEventListener("name_of_event",fnhander,bcapture)

[object].removeEventListener("name_of_event",fnhander,bcapture)

var fnClick = function(){

  alert("Clicked!");

}

oDiv.addEventListener("onclick", fnClick, false); //添加事件处理函数

oDiv.addEventListener("onclick", fnClickAnother, false); // 与IE一样，可以添加多个事件处理函数

 

oDiv.removeEventListener("onclick", fnClick, false); //移除事件处理函数

如果使用addEventListener()将事件处理函数加入到捕获阶段，则必须在removeEventListener()中指明是捕获阶段，才能正确地将这个事件处理函数删除

oDiv.onclick = fnClick;

oDiv.onclick = fnClickAnother; //使用直接赋值，后续的事件处理函数会覆盖前面的处理函数

oDiv.onclick = fnClick;

oDiv.addEventListener("onclick", fnClickAnother, false); //会按顺序进行调用，不会覆盖

**7) 事件类型**

鼠标事件：click、dbclick、mousedown、mouseup、mouseover、mouseout、mousemove

键盘事件：keydown、keypress、keyup

HTML事件：load、unload、abort、error、select、change、submit、reset、resize、scroll、focus、blur

Window 事件属性(没加蓝的都是h 5新事件)

针对 window 对象触发的事件（应用到 <body> 标签）：

属性

onafterprint    文档打印之后运行的脚本。

onbeforeprint 文档打印之前运行的脚本。

onbeforeunload 文档卸载之前运行的脚本。

onerror 在错误发生时运行的脚本。

onhaschange 当文档已改变时运行的脚本。

onload 页面结束加载之后触发。

onmessage 在消息被触发时运行的脚本。

可以使用调用 postMessage ()向主线程发送消息,在某些场景下，业务调用方可能需要主动跟定位组件通信,可以通过html5 postMessage的方式主动与定位组件发起通信

onoffline 当文档离线时运行的脚本。

ononline 当文档上线时运行的脚本。

onpagehide 当窗口隐藏时运行的脚本。

onpageshow 当窗口成为可见时运行的脚本。

onpopstate 当窗口历史记录改变时运行的脚本。

onredo 当文档执行撤销（redo）时运行的脚本。

onresize 当浏览器窗口被调整大小时触发。

onstorage 在 Web Storage 区域更新后运行的脚本。

onundo 在文档执行 undo 时运行的脚本。

onunload 一旦页面已下载时触发（或者浏览器窗口已被关闭）。

Form 事件

由 HTML 表单内的动作触发的事件（应用到几乎所有 HTML 元素，但最常用在 form 元素中）：

属性

onblur 元素失去焦点时运行的脚本。

onchange在元素值被改变时运行的脚本。

oncontextmenu 当上下文菜单被触发时运行的脚本。

onfocus 当元素失去焦点时运行的脚本。

onformchange 在表单改变时运行的脚本。

onforminput 当表单获得用户输入时运行的脚本。

oninput 当元素获得用户输入时运行的脚本。

oninvalid 当元素无效时运行的脚本。

onreset 当表单中的重置按钮被点击时触发。HTML5 中不支持。

onselect 在元素中文本被选中后触发。

onsubmit 在提交表单时触发。



Event对象

Event对象的常用属性

名称

说明

返回

type

事件的名称如mouseover

字符串

target

事件指向的元素

HTMLElement

获得event对象兼容性写法

 document.οnclick=function(event){
       event = event || window.event;             

  };

 

个别事件讲解

oninput,onpropertychange,onchange的用法

oninput:

oninput 事件在用户输入时触发。

该事件在 <input> 或 <textarea> 元素的值发生改变时触发。

提示： 该事件类似于 onchange 事件。不同之处在于 oninput 事件在元素值发生变化是立即触发， onchange 在元素失去焦点时触发。另外一点不同是 onchange 事件也可以作用于 <keygen> 和 <select> 元素。

onchange

onchange 事件会在域的内容改变时发生。

onchange 事件也可用于单选框与复选框改变后触发的事件。

 

onchange触发事件必须满足两个条件：

a）当前对象属性改变，并且是由键盘或鼠标事件激发的（脚本触发无效）

b）当前对象失去焦点(onblur)；

onpropertychange的话，只要当前对象属性发生改变，都会触发事件，但是它是IE专属的；

oninput是onpropertychange的非IE浏览器版本，支持firefox和opera等浏览器，但有一点不同，它绑定于对象时，并非该对象所有属性改变都能触发事件，它只在对象value值发生改变时奏效。

在textarea中，如果想捕获用户的键盘输入，用onkeyup检查事件就可以了，但是onkeyup并不支持复制和粘贴，因此需要动态监测textarea中值的变化，这就需要onpropertychange（用在IE浏览器）和oninput（非IE浏览器）结合在一起使用了。

 

移动端 触摸事件

 ontouchstart、ontouchmove、ontouchend、ontouchcancel

1、Touch事件简介

pc上的web页面鼠 标会产生onmousedown、onmouseup、onmouseout、onmouseover、onmousemove的事件，但是在移动终端如 iphone、ipod  Touch、ipad上的web页面触屏时会产生ontouchstart、ontouchmove、ontouchend、ontouchcancel 事件，分别对应了触屏开始、拖拽及完成触屏事件和取消。

当按下手指时，触发ontouchstart；

当移动手指时，触发ontouchmove；

当移走手指时，触发ontouchend。

当一些更高级别的事件发生的时候（如电话接入或者弹出信息）会取消当前的touch操作，即触发ontouchcancel。一般会在ontouchcancel时暂停游戏、存档等操作。

 

2、Touch事件与Mouse事件的出发关系

在触屏操作后，手指提起的一刹那（即发生ontouchend后），系统会判断接收到事件的element的内容是否被改变，如果内容被改变，接下来的事 件都不会触发，如果没有改变，会按照mousedown，mouseup，click的顺序触发事件。特别需要