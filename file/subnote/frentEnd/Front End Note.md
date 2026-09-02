# 前端笔记

## html

### html术语

- 1.标签：一对`<>`  
    - 单标签：`<tagName />`
    - 双标签：`<tagName></tagName>`
        - 开始标签：`<tagName>`
        - 结束标签：`</tagName>`
- 2.属性：对标签特征进行设置的一种方式，一般在开始标签中定义
    - 当设置的属性值和属性名一样时，可以只写属性名
- 3.文本：双标签的开始标签和结束标签中间的文字，单标签是没有文本的
- 4.元素：可理解为一个定义好的标签就是一个元素（dom元素）

### html结构

- 1.文档声明，html5文档类型声明：`<!DOCTYPE html>`

- 2.根标签，`<html></html>`

- 3.头部标签，`<head></head>`
    - 告诉浏览器用指定字符集解码：`<meta charset="utf-8" />`
    - 浏览器显示html文件的页面的标题：`<title></title>`
- 4.主体标签，`<body></body>`

```html
<!DOCTYPE html>
<html lang="zh-CN">
    <head>
        <meta charset="UTF-8" />
        <title></title>
        <style></style>
    </head>
    <body>
        <div></div>
        <script></script>
    </body>
</html>
```

### html标签

#### 元信息/基本信息标签

用来表示该网页的基本信息

```html
<!-- 配置字符编码 -->
<meta charset="UTF-8" />
<!-- 针对IE浏览器的一个兼容性设置，让IE浏览器处于一个最优的渲染模式 -->
<meta http-equiv="X-UA-Compatible" content="IE=edge" />
<!-- 针对一些国产的“双核”浏览器的设置，让浏览器优先使用webkit内核去渲染网页 -->
<meta name="rander" content="webkit">
<!-- 针对移动端的一个配置 -->
<meta name="viewport" content="width=device-width, initial-scale=1.0" />
<!-- 配置网页的关键字，简单理解：搜索引擎会根据用户搜索的关键字，跟网页关键字进行比对，匹配的话该网页可能就会出现在搜索结果中 -->
<meta name="keywords" content="关键字1,关键字2,关键字..." />
<!-- 配置网页描述信息 -->
<meta name="description" content="填写80字以内的一段跟网页内容相关的一段话" />

<!-- 针对搜索引擎爬虫配置，content的值有很多，列举一些说明 -->
<!-- index：允许搜索爬虫索引此页面 -->
<!-- noindex：要求搜索爬虫不索引此页面 -->
<!-- follow：允许搜索爬虫跟随此页面上的链接 -->
<!-- nofollow：要求搜索爬虫不跟随此页面上的链接 -->
<!-- all：相当于index和follow -->
<!-- none：相当于noindex和nofollow -->
<!-- noarchive：要求搜索引擎不缓存页面内容，可以用替代名称nocache -->
<meta name="robots" content="nocache" />

<!-- 配置网页作者 -->
<meta name="author" content="handle" />

<!-- 配置网页生成工具 -->
<meta name="generator" content="如某个ide" />

<!-- 配置网页版权信息 -->
<meta name="copyright" content="2024-2026©版权所有" />

<!-- 配置网页自动刷新，网页打开后3秒跳到百度，如果不设置url就原地刷新 -->
<meta http-equiv="refresh" content="3;url=https://www.baidu.com" />
```

#### 标题

h1-h6共有6级，文本字体大小依次递减

```html
<h1>text</h1>
<h2>text</h2>
<h3>text</h3>
<h4>text</h4>
<h5>text</h5>
<h6>text</h6>
```

#### 段落

p标签里面不能嵌套h1-h6标签

```html
<p>text</p>
```

#### 换行

```html
<!-- 不带分割线 -->
<br />

<!-- 带分割线 -->
<hr />
```

#### 列表

- 有序列表：ol
- 无序列表: ul
- 列表项：li
- 列表可以嵌套列表

```html
<!-- 有序列表 -->
<ol>
    <li>text1</li>
    <li>text2</li>
    <li>text3</li>
</ol>

<!-- 无序列表 -->
<ul>
    <li>text1</li>
    <li>text2</li>
    <li>text3</li>
</ul>
```

#### 超链接

- href，定义要跳转的目标资源的地址，可以是
    - url
    - 相对路径
        - `./`，表示当前资源所在路径，可省略不写
        - `../`，表示当前资源上一层路径
        - 如果当前资源位置变了，路径要跟着变
    - 绝对路径
        - 以`/`开头，从固定位置（如<http://localhost:8080>)作为出发点去找目标资源
        - 如果当前资源位置变了，路径不用跟着变
- target，定义目标资源的打开方式
    - `_self`，在当前窗口打开目标资源
    - `_blank`，在新窗口打开目标资源

```html
<a href="https://www.baidu.com" target="_blank">百度一下</a>


<a href="#anyId">跳转到某个标签</a>
<p id="anyId">今天你很棒棒噢</p>

<!-- 当你点击 <a href="#"> 时 -->
<!-- 浏览器会尝试跳转到当前页面中 id="#" 的元素 -->
<!-- 因为通常页面里不会有这个元素，所以它不会发生实际的页面跳转或刷新 -->
<!-- 但会把滚动条瞬间拉回到页面的最顶部 -->
<a href="#"> 
```

#### 图片

- src，图片路径，可以是
    - url
    - 相对路径
    - 绝对路径
- title，鼠标悬停时提示的文字
- alt，图片加载失败时提示的文字

```html
<img src="" title="" alt="" />
```

#### 表格

- thead，表头，可不写
- tbody，表体，可不写
- tfoot，表尾（如总计），可不写
- tr，表格行
- td，单元格
    - rowspan，指定单元格占多少行，会把跟此单元格同一列的被占位置的单元格往下边挤，因此为了美观可以把被占位置的单元格删掉
    - colpan，指定单元格占多少列，会把跟此单元格同一行的被占位置的单元格往右边挤，因此为了美观可以把被占位置的单元格删掉
- th，自带加粗和居中效果的td
- 如果表头、表体、表尾都不写，浏览器会将tr都放到tbody里面

```html
<table>
    <tr>
        <th>columnName1</th>
        <th>columnName2</th>
        <th>columnName3</th>
    </tr>
    <tr>
        <td>columnValue1</td>
        <td>columnValue2</td>
        <td>columnValue3</td>
    </tr>
</table>
```

#### 表单

- 内部定义可以让用户输入信息的表单项标签

- 实现让用户在界面上输入各种信息并提交的一种标签，是向服务端发送数据的主要方式之一

- action，表单提交的地址，可以是
    - url
    - 相对路径
    - 绝对路径
- method，表单提交的方式
    - get
        - 表单数据会追加到url后面，以`?`作为参数开始的标识，`参数名=参数值`的形式，多个参数用`&`隔开
        - 数据会直接暴露在地址栏上
        - 地址栏长度有限制
        - 地址栏只能是字符，不能提交文件
        - 比post效率高
    - post
        - 表单数据默认不追加到url后面
        - 表单数据不会暴露在地址栏上，单独打包通过请求体发送
        - 提交数据量可以很大
        - 请求体可以是字符或字节数据，可以提交文件
        - 比get效率低

- target，跳转的新地址的打开方式，值：_self、_blank

```html
<form action="" method="" target="">
    <!-- 在此填充表单项 -->
</form>
```

##### 表单项

###### input

- input
    - name，提交时的参数名
    - value，提交时的参数值
    - type，表单项类型
        - text，普通文本框
            - readonly，只读，表单提交时会携带，`readonly=readonly`
            - disabled，不可用，表单提交时不携带，`disabled=disabled`
        - password，密码框
        - file，文件上传框
        - reset，重置按钮
        - submit，提交按钮
        - radio，单选框
            - 多个单选框定义同一个name属性值，来实现互斥效果
            - 还要通过value属性手动指定选中后的参数值是什么
            - 通过checked属性指定是否默认选中，写法：`checked="true"`或`checked="checked"`或`checked`
        - checkbox，复选框
            - 多个复选框定义同一个name属性值
            - 还要通过value属性手动指定选中后的参数值是什么
            - 通过checked属性指定是否默认选中，写法：`checked="true"`或`checked="checked"`或`checked`
        - hidden，隐藏域，不显示在页面上，提交时会携带

```html
<form action="" method="">
    <!-- 表单项 -->
    用户名：<input type="text" name="username" />
    <br />
    密码：<input type="password" name="password" />
    <br />
    头像：<input type="file" name="file" />
    <br />
    性别： 
    <input type="radio" name="gender" value="1" checked/> 男
    <input type="radio" name="gender" value="0"/> 女
    <br />
    爱好：
    <input type="checkbox" name="hobby" value="1" checked/> 唱
    <input type="checkbox" name="hobby" value="2"/> 跳
    <input type="checkbox" name="hobby" value="3"/> rap
    <br />
    <input type="hidden" name="id" value="123"/>
    <br />
    <input type="reset" value="清空" />
    <input type="submit" value="登录" />
</form>
```

###### 普通按钮

在表单中，普通按钮的type为button，如果不写type，默认为submit

以下是表单中的普通按钮的两种写法

```html
<input type="button" value="普通按钮">

<button type="button">普通按钮</button>
```

###### textarea

- textarea，文本域（多行文本框）
    - value就是textarea开始标签和结束标签中间的文本

```html
<form action="" method="">
    <!-- 表单项 -->
    个人简介：<textarea name="briefIntroduction" cols="30" rows="3">page load text</textarea>
</form>
```

###### select

- select，下拉框
    - option，选项
        - value，当不指定option的value属性值时，value就是option开始标签和结束标签中间的文本
        - selected，是否默认选中，写法：`selected="selected"`或`selected`

```html
<form action="" method="">
    <!-- 表单项 -->
    籍贯：
    <select name="birthplace">
        <option value="1">北京</option>
        <option value="2">上海</option>
        <option value="1000" selected>请选择</option>
    </select>
</form>
```

###### label

label可以用for属性，通过id关联其它表单控件

但是一般不与按钮进行关联，因为点label就相当于点了按钮，怪怪的

下面的例子的效果：点击label，username输入框也会获得焦点

```html
<label for="usernameInput">用户名：</label>
<input type="text" id="usernameInput" name="username" />
```

还有一种关联的写法

```html
<label for="usernameInput">
    用户名：
    <input type="text" name="username" />
</label>
```

###### 表单信息分类

```html
<fieldset>
    <legend>主要信息</legend>
    <!-- 以下写放在主要信息里面的表单项 -->
    用户名：<input type="text" name="username" />
    <br />
    密码：<input type="password" name="password" />
    <br />
</fieldset>
```

##### 禁用表单控件

给表单控件加上disabled属性即可禁用该表单控件

input、textarea、button、select、option都可以设置该属性

#### div

- 块元素，自己独占一行的元素
- css样式的宽，高等，往往都生效

#### span

- span，行内元素，不会自己独占一行的元素，img、a也是行内元素
- css样式的宽，高等，很多都不生效

#### iframe（框架标签）

框架标签可以嵌入很多东西，如：普通网页、广告网页、pdf、图片、gif、mp4、压缩包（打不开的文件会直接弹出下载窗口）

在src设置相应的url、相对地址等就行了

- 属性
    - src：资源，可以是网页，文件地址等
    - name：框架名称，可以与其它标签的target属性配合使用
    - frameborder：值可以是0（无边框）或1（有边框）

```html
<!-- 可以嵌入一个普通网页 -->
<iframe src="https://www.baidu.com" width="" height="" frameborder="0"></iframe>

<!-- iframe的name属性与超链接的target属性配合使用 -->
<a href="https://www.baidu.com" target="theTarget">百度一下</a>
<br/>
<iframe name="theTarget" width="" height="" frameborder="0"></iframe>

<!-- iframe的name属性与表单的target属性配合使用 -->
<form action="https://cn.bing.com/search" target="theTarget">
    <input type="text" name="q" />
    <button value="搜索" />
</form>
<br/>
<iframe name="theTarget" width="1280" height="720" frameborder="0"></iframe>
```

### 字符实体

html实体：在html中的一种特殊的形式的内容，表示某个符号

如：空格，当你在html里面写连续空格（按下多个空格键）的时候，浏览器只认第一个空格，余下的空格会当成对代码格式的调整

- 写法有两种：
    - 实体名称写法：`&实体名称;`
    - 实体编号写法：`&#实体编号;`

|符号|写法|
|:-|:-|
|空格|实体名称写法：`&nbsp;`，实体编号写法：`&#160`;|
|`<`|`&lt;`|
|`>`|`&gt;`|
|`&`|`&amp;`|
|`"`|`&quot;`|
|反引号`´`|`&acute;`|
|`¥`|`&yen;`|
|商标`™`|`&trade;`|
|注册商标`®`|`&reg;`|
|版权`©`|`&copy;`|
|乘法符号`×`|`&times;`|
|除法符号`÷`|`&divide;`|

### 全局属性（所有html标签都可以写的属性）

- 属性
    - id：给标签指定唯一标识
        - 同一个html中的id是不能重复的
        - 不能在`<html>`、`<head>`、`<meta>`、`<title>`、`<script>`、`<style>`中使用
    - class：给标签指定类选择器名称，通过在css定义的类选择器给标签设置样式
    - style：给标签设置样式
    - dir：内容在屏幕的位置/方向，值：ltr（从左往右）或rtl（从右往左）
        - 不能在`<html>`、`<head>`、`<meta>`、`<title>`、`<script>`、`<style>`中使用
    - title：给标签设置一个鼠标在其范围悬停时的文字提示，一般超链接和图片用得比较多
    - lang：给标签指定语言
        - 不能在`<head>`、`<meta>`、`<title>`、`<script>`、`<style>`中使用

### html元素间的关系

父元素：元素a直接包裹元素b，元素a就是元素b的父元素

子元素：元素a直接包裹元素b，元素b就是元素a的子元素

祖先元素：父元素的父元素...，一直往外找，都是祖先，父元素也是祖先，但是一般不这么称呼

后代元素：子元素的子元素...，一直往里找，都是后代，子元素也是后端元素，但是一般不这么称呼

兄弟元素：具有相同父元素的元素，互为兄弟元素

## html5

### html5标签

#### 布局标签

##### header

整个页面，或部分区域的头部

```html
<header>
    <h1></h1>
</header>
```

##### footer

整个页面，或部分区域的底部

```html
<footer>
    <nav></nav>
</footer>
```

##### nav

导航

```html
<nav>
    <a href="#">百度一下</a>
</nav>
```

##### article

文章、帖子、杂志、新闻、博客、评论等

article里面可以有多个section

article比section更强调独立性，一块内容如果比较独立，比较完整，应该使用article

```html
<article>
    <h2></h2>
    <section></section>
    <section></section>
</article>
```

##### section

页面中的某段文字，或文章中的某段文字（里面文字通常会包含标题）

section强调的是分段或分块，如果你想将一块内容分成几段的时候，就可以用它

```html
<section>
    <h3></h3>
</section>
```

##### aside

侧边栏

```html
<aside>
    <nav></nav>
</aside>
```

#### 状态标签

##### meter

定义已知范围内的标量测量，也被称为gauge（尺度）

如用来显示电量、磁盘用量等

- 属性
    - optimum，最优值，可选属性
    - 其它几个属性不解释了

```html
<meter max="100" min="0" low="10" high="20" optimum="90" value="60"></meter>
```

##### progress

显示某个任务完成的进度的指示器，一般用于表示进度条

```html
<progress max="100" value="80"></progress>
```

#### 列表标签

##### datalist

用于搜索框的关键字提示

```html
<form action="#">
    <input type="text" list="hotkey" />
    <button>search</button>
</form>
<datalist id="hotkey">
    <option value="java"></option>
    <option value="javascript"></option>
    <option value="python"></option>
</datalist>
```

##### details和summary

details用于展示问题和答案，或对专有名词进行解释

summary写在details的里面，用于指定问题或专有名词

```html
<details>
    <summary>如何在archlinux安装jdk</summary>
    <!-- 下面写补充summary内容的其它标签 -->
    <p>一，……</p>
    <p>二，……</p>
</details>
```

#### 文本标签

##### ruby和rt

用于文本注音

ruby包裹需要注音的文字

rt写注音，rt写在ruby的里面

```html
<div>
    <ruby>
        <span>栗</span>
        <rt>lì</rt>
    </ruby>
    <ruby>
        <span>粟</span>
        <rt>sù</rt>
    </ruby>
</div>
```

##### mark

用于文本标记，默认效果相当于给文字加了黄色背景

可以用来标记搜索结果中的关键字

```html
<p>Lorem ipsum <mark>dolor</mark> sit amet consectetur.</p>
```

#### 表单控件新增的属性

- 属性名
    - placeholder，提示文字，适用于文字输入类的表单控件
    - required，表示该输入项必填，适用于除按钮外的其它表单控件
        - 对于单选框，随便再任意一个选项上设置就行了
        - 对于复选框，设置了该属性的选项表示必须勾选
    - autofocus，自动获取焦点，适用于所有表单控件
    - autocomplete，根据历史输入自动补全，可以设置为on或off，适用于文字输入类的表单控件
        - 密码输入框、多行输入框不可用
        - 还需要在浏览器设置开启这个功能
    - pattern，填写正则表达式，适用于文本输入类表单控件
        - 多行输入不可用，且空的输入框不会验证，往往与required配合

```html
<input type="text"
        name="search"
        placeholder="请输入帐号"
        required
        autofocus
        autocomplete="on"
        pattern="\w{6}"
```

#### 表单控件input新增的type属性值

- 新增的type属性值
    - email，邮箱类型的输入框，表单提交时会验证格式，输入为空则不验证格式
    - url，url类型的输入框，表单提交时会验证格式，输入为空则不验证格式
    - number，数字类型的输入框，表单提交时会验证格式，输入为空则不验证格式
    - search，搜索类型的输入框，表单提交时不会验证格式
    - tel，电话类型的输入框，表单提交时不会验证格式，在移动端使用时，会唤起数字键盘
    - range，范围选择框，默认值为50，表单提交时不会验证格式
    - color，颜色选择框，默认值为黑色，表单提交时不会验证格式
    - date，日期选择框，默认值为空，表单提交时不会验证格式
    - month，月份选择框，默认值为空，表单提交时不会验证格式
    - week，周选择框，默认值为空，表单提交时不会验证格式
    - time，时间选择框，默认值为空，表单提交时不会验证格式
    - datetime-local，日期+时间选择框，默认值为空，表单提交时不会验证格式

```html
<form action="">
    <input type="email" name="email" />
    <input type="url" name="url" />
    <input type="number" name="number" min="20" max="100" step="1" />
    <input type="search" name="keyword" />
    <input type="tel" name="phoneNumber" />
    <input type="range" name="range" min="0" max="100" value="20" />
    <input type="color" name="color" />
    <input type="date" name="date" />
    <input type="month" name="month" />
    <input type="week" name="week" />
    <input type="time" name="time" />
    <input type="datetime-local" name="localDateTime" />
    <br>
    <button>submit</button>
</form>
```

#### form新增的属性

如果给form设置了novalidate，表单提交时不再进行校验

```html
<form action="" novalidate></form>
```

#### 视频标签

用来定义视频

- 属性
    - src，值为url地址，视频地址
    - width，像素值，视频播放器的宽度
    - height，像素值，视频播放器的高度
    - controls，无属性值，显示视频控件，如播放/暂停按钮
    - muted，无属性值，视频静音
    - autoplay，无属性值，视频自动播放
    - loop，无属性值，视频循环播放
    - poster，值为url地址，视频封面图片的地址
    - preload，视频预加载，如果使用autoplay，则忽略该属性，属性值有
        - none，不预加载视频
        - metadata，仅预先获取视频的元数据（如长度），现在也会预加载一段视频
        - auto，可以下载整个视频文件，即使用户不希望使用它（现在应该是浏览器自动协调了）

```html
<video src="/path/to/video" controls muted loop poster="/path/to/视频封面图片" preload="auto"></video>
```

#### 音频标签

用来定义音频

- 属性
    - src，值为url地址，音频地址
    - controls，无属性值，显示音频控件，如播放/暂停按钮
    - muted，无属性值，音频静音
    - autoplay，无属性值，音频自动播放
    - loop，无属性值，音频循环播放
    - preload，音频预加载，如果使用autoplay，则忽略该属性，属性值有
        - none，不预加载音频
        - metadata，仅预先获取音频的元数据（如长度），现在也会预加载一段音频
        - auto，可以下载整个音频文件，即使用户不希望使用它（现在应该是浏览器自动协调了）

```html
<audio src="/path/to/audio" controls muted loop preload="auto"></audio>
```

#### 元素新增的全局属性

- contenteditable，表示元素是否可被用户编辑，值有
    - true
    - false
- draggable，表示元素是否可被用户拖动，值有
    - true
    - false
- hidden，隐藏元素，效果和`display: none`一样
- spellcheck，规定是否对元素进行拼写和语法检查，值有
    - true，检查
    - false，不检查
- contextmenu，规定元素的上下文菜单，在用户鼠标右键点击时显示
- data-x，用于存储页面的私有定制数据

```html
<div data-mydata1="hello" data-mydata2="world"></div>
```

### html5兼容性处理

- 方法一，添加元信息，让浏览器处于最优渲染模式

```html
<!-- 针对IE浏览器的一个兼容性设置，让IE浏览器处于一个最优的渲染模式 -->
<!-- 设置IE总是使用最新的文档模式进行渲染 -->
<meta http-equiv="X-UA-Compatible" content="IE=edge" />
<!-- 针对一些国产的“双核”浏览器的设置，让浏览器优先使用webkit内核（Chromium）去渲染网页，如360等壳浏览器 -->
<meta name="rander" content="webkit">
```

- 方法二，使用html5shiv让低版本浏览器认识H5的语义化标签，这种方式对于H5的个别高级标签如video还是没法兼容的

```html
<!-- 下面三行的意思是判断如果ie浏览器的版本低于ie9，就导入html5shiv.js -->
<!--[if lt ie 9]>
<script src="/path/to/html5shiv.js"></script>
<![endif]-->
```

这种html的判断写法还有如下扩展知识

- lt，小于
- lte，小于等于
- gt，大于
- gte，大于等于
- !，逻辑非

```html
<!-- 仅IE8 -->
<!--[if IE 8]>
<![endif]-->

<!-- IE8以下 -->
<!--[if lt IE 8]>
<![endif]-->

<!-- 仅非IE8 -->
<!--[if !IE 8]>
<![endif]-->
```

## CSS

CSS全称：Cascading Style Sheets（层叠样式表）

CSS也是一种标记语言，用于给html结构设置样式，如文字大小、颜色、元素宽高等

html搭建结构，css添加样式，实现结构和样式的分类

css不区分大小写

### 样式表

#### 1.行内/内联样式表

```html
<div style="..."></div>
```

#### 2.内部样式表

```html
...
<head>
    <style type="text/css">
        ...
    </style>
</head>
...
```

#### 3.外部样式表

```html
<!-- rel是relation，说明引入的文档与当前文档的关系 -->
<link type="text/css" rel="stylesheet" href="xxx/xxx.css">
```

#### 样式表优先级

行内样式 > （内部样式 = 外部样式）

内部样式和外部样式优先级相同，后面的会覆盖前面的

同名的属性，优先级高的顶掉优先级低的，不同名的属性最终都会生效

同一个样式表中优先级也和顺序有关，后面的会覆盖前面的

### CSS 语法

- CSS 规则由两个主要部分构成：
    - 1.`选择器`：通常是需要改变样式的`HTML元素`
    - 2.`声明`（一条或多条）：
        - 声明用`{}`括起来，
        - 其中的每条声明由一个`属性`和一个`值`组成
        - 属性和值用`:`分开，每条声明以`;`结束
    - CSS注释以 `/*` 开始, 以 `*/` 结束

```css
p {
    color: red;
    text-align: center;
    /* css里面要严谨，带单位，不能只写数字 */
    font-size: 30px;
}
```

### 选择器

#### 通配选择器

通配选择器对清除样式很有帮助

```css
/* 选中所有的html元素 */
* {
    background-color: white;
}
```

#### 元素选择器

根据元素的class属性的值，来选中某些元素

为页面中某种元素统一设置样式

- 定义css

```css
/* 语法 */
标签名 {
    属性名: 属性值;
}

h1 {}
div, p {}
```

- 使用css：不用另外写语句，在该css生效的html文件中，对所有属于该类型的html元素都直接生效

```html
<h1></h1>
<div></div>
<p></p>
```

#### 类选择器

- 定义css

```css
/* 语法 */
.类名 {
    属性名: 属性值;
}

/* 选择html中所有class属性值为myClass1的元素 */
.myClass1 {}

/* 选择html中所有class属性值为myClass2的元素 */
.myClass2 {}
```

- 使用css

```html
<!-- class属性的值可以包含多个类名，用空格隔开 -->
<tagName class="myClass1 myClass2"></tagName>
```

#### id选择器

根据元素的id的属性值，来精准选中某个元素

- 定义css

```css
/* 语法 */
#元素id {
    属性名: 属性值;
}

#myId {}
```

- 使用css

```html
<tagName id="myId"></tagName>
```

#### 复合/组合选择器

##### 交集选择器

选中同时符合多个条件的元素

```css
/* 且 */
选择器1选择器2...选择器n {}

/* 虽然也可以这么写，但是很少用，因为id都可以唯一定位元素了，直接用id选择器定义样式就行了 */
标签名.类名#元素id {

}

/* 选中class属性的值包含类名1和类名2的元素，一般不会这么用 */
.类名1.类名2 {

}

/* html中使用css */
<tagName class="类名1 类名2"></tagName>
```

##### 并集选择器

选中多个选择器对应的元素，通常用于集体声明

```css
/* 或 */
选择器1,选择器2,...,选择器n {}

/* 美观的写法 */
选择器1,
选择器2,
...,
选择器n {}

/* 本质其实就相当于下面的写法，但是它们的样式内容是一样的 */
选择器1 {}
选择器2 {}
...     {}
选择器n {}
```

#### 后代选择器

选中指定（祖先）元素中，符合要求的后代元素

注意选中的是后代，不选中祖先

```css
/* 无论祖先还是后代选择器，都可以是上面学的任何一种选择器 */
祖先选择器 后代选择器1 后代选择器2 ... 后代选择器n {}

ul li {}

/* 祖先是类选择器，后代是交集选择器 */
.myClass1 div.myClass2 {}
```

#### 子代选择器

选中指定（父）元素中，符合要求的儿子元素

注意选中的是子代，不选中父元素

```css
/* 无论父还是子选择器，都可以是上面学的任何一种选择器 */
父选择器>子选择器1>子选择器2>...>子选择器n {}

/* 也可以在>的前后加空格 */
父选择器 > 子选择器1 > 子选择器2 > ... > 子选择器n {}

ul>li {}
```

#### 兄弟选择器

相邻兄弟选择器：选中指定元素后面，符合条件的`相邻`的兄弟元素

通用兄弟选择器：选中指定元素后面，符合条件的`所有`的兄弟元素

```html
<p>大哥</p>
<div></div>
<p>二弟</p>
<p>三弟</p>
```

```css
/* 相邻兄弟选择器 */
选择器1+选择器2 {}

/* 通用兄弟选择器 */
选择器1~选择器2 {}

/* 选中div后紧紧相邻的兄弟元素p，也就是二弟，往下找，不往上找 */
div+p {}

/* 选中div后所有的兄弟元素p，也就是二弟、三弟，往下找，不往上找 */
div~p {}
```

#### 属性选择器

选中具有某个属性或属性值符合某种要求的元素

注意一般不会用class属性作为属性选择器

```html
<div title="handle"></div>
```

```css
/* 写法一：选中具有title属性的元素，至于title属性的值是什么，是否相同都无所谓 */
[title] {}

/* 写法二：选中具有title属性，且title属性的值为handle的元素 */
[title="handle"] {}

/* 写法三：选中具有title属性，且title属性的值以字母h开头的元素 */
[title^="h"] {}

/* 写法四：选中具有title属性，且title属性的值以字母e结尾的元素 */
[title$="e"] {}

/* 写法五：选中具有title属性，且title属性的值包含字母a的元素 */
[title*="a"] {}
```

#### 伪类选择器

选中特殊状态的元素

类选择器：`.类名`
伪类选择器：`标签名:状态名`

```html
<a href="https://www.baidu.com">百度一下</a>
```

```css
/* 注意要严格按照link，visited，hover，active这个顺序来定义 */
/* link和visited是a元素独有的状态 */
/* hover和active是所有元素都有的状态 */
/* 选中的是没有访问过的a元素 */
a:link {}

/* 选中的是访问过的a元素 */
a:visited {}
```

##### 动态伪类

- link：超链接未被访问的状态
- visited：超链接访问过的状态
- hover：鼠标悬停在元素上的状态
- active：元素激活（按下鼠标不松开）的状态
- focus：获取焦点（点击，触摸或通过按下键盘的tab键等方式，选择元素时，就是获得焦点）的元素，表单类元素独有

```html
<a href="https://www.baidu.com">百度一下</a>
```

```css
/* 注意要严格按照link，visited，hover，active这个顺序来定义 */
/* link和visited是a元素独有的状态 */
/* hover和active是所有元素都有的状态 */
/* 选中的是没有访问过的a元素 */
a:link {}

/* 选中的是访问过的a元素 */
a:visited {}

/* 选中的是鼠标悬浮状态的a元素 */
a:hover {}

/* 选中的是激活状态的a元素 */
a:active {}

/* 选中的是获取焦点/输入状态的元素，focus是表单元素独有的状态 */
input:focus,select:focus {}
```

##### 结构伪类

- first-child
- last-child
- nth-child(n)：第n个儿子元素，n的值可以是
    - 0或不写，都不选中，一般不这么写
    - n，都选中，一般不这么写
    - 2n或even，表示选中偶数儿子
    - 2n+1或odd，表示选中奇数儿子
    - `-n+5`，表示选中前5个儿子
- nth-last-child(n)：倒数第n个儿子元素，很少用
- first-of-type
- last-of-type
- nth-of-type(n)：第n个某类型儿子元素
- nth-last-of-type(n)：倒数第n个某类型儿子元素，很少用
- only-child：选中独生儿子元素，要求其父元素只有一个儿子元素，很少用
- only-of-type：选中独生某类型儿子元素，要求其父元素只有一个该类型的儿子元素，很少用

###### 结构1

```html
<div>
    <p>选中</p>
    <p></p>
</div>
```

```css
/* 选中的是div的第一个儿子p元素（从div所有的儿子里面去找，即按照所有兄弟元素计算） */
div>p:first-child{}
```

###### 结构2

```html
<div>
    <!-- 都不会选中 -->
    <span></span>
    <p></p>
</div>
```

```css
/* 选中的是div的第一个儿子p元素（从div所有的儿子里面去找，即按照所有兄弟元素计算） */
div>p:first-child{}
```

###### 结构3

```html
<div>
    <p>>选中</p>
    <marquee>
        <p>选中</p>
    </marquee>
    <p></p>
</div>
```

```css
/* 选中的是div的后代p元素，且p的父元素是谁无所谓，但p必须是其父元素的第一个儿子（从div所有的儿子里面去找，即按照所有兄弟元素计算） */
div p:first-child{}
```

###### 结构4

```html
<body>
    <p>>选中</p>
    <div>
        <p>>选中</p>
        <marquee>
            <p>选中</p>
        </marquee>
        <p></p>
    </div>
</body>
```

```css
/* 选中的是p元素，且p的父元素是谁无所谓，但p必须是其父元素的第一个儿子（从所有的儿子里面去找，即按照所有兄弟元素计算） */
p:first-child{}
```

###### 结构5

```html
<div>
    <p></p>
    <p>选中</p>
    <p></p>
</div>
```

```css
/* 第2个儿子 */
div>p:nth-child(2) {}
```

###### 结构6

```html
<div>
    <span></span>
    <p>选中</p>
    <p></p>
</div>
```

```css
/* 选中的是div的第一个儿子p元素（从div所有的儿子p元素里面去找，即按照所有同类型元素计算） */
div>p:first-of-type {}
```

###### 结构7

```html
<html></html>
```

```css
/* 选中的是根元素（html） */
:root {}
```

###### 结构8

```html
<div></div>
```

```css
/* 选中的是没有内容（连一个空格都不要有）的div元素 */
div:empty {}
```

##### 否定伪类

`:not`：排除满足括号中条件的元素

```html
<div>
    <p></p>
    <p class="excludeClass"></p>
</div>
```

```css
/* 选中的是div的儿子p元素，但是排除类名为excludeClass的元素 */
div>p:not(.excludeClass) {}

/* not里面可以有其它写法 */
/* 选中的是div的儿子p元素，但是排除title属性值以hello开头的 */
div>p:not([title^="hello"]) {}

/* 选中的是div的儿子p元素，但是排除第一个儿子p元素 */
div>p:not(:first-child) {}
```

##### UI伪类

```html
<input type="checkbox" />
<input type="text" />
<input type="text" disabled />
```

```css
/* 选中的是勾选的复选框或单选按钮 */
input:checked {}


/* 选中的是可用的input元素（没有disabled属性），很少这么写 */
input:enabled {}
/* 相当于 */
input {}

/* 选中的是被禁用的input元素（有disabled属性） */
input:disabled {}
```

##### 目标伪类

```html
<a href="#top">回到顶部</a>
<div id="top">顶部</div>
```

```css
/* 点击超链接的时候，就会选中超链接的目标，即选中锚点指向的元素 */
div:target {}
```

##### 语言伪类

根据语言属性的值选择元素

```html
<div lang="en_US">handle</div>
```

```css
/* 选中div的lang的值是en_US的元素 */
div:lang(en_US) {}

/* 选中lang的值是zh_CN的元素 */
:lang(zh_CN) {}
```

#### 伪元素选择器

用来选中元素中的一些特殊位置

```html
<div>handle</div>
```

```css
/* 选中的是div开始标签和结束标签之间的文本的第一个字符，这里的例子即字符h */
div::first-letter {}

/* 选中的是div开始标签和结束标签之间的文本的第一行文字 */
div::first-line {}

/* 选中的是div开始标签和结束标签之间的文本中，被鼠标选择的文字 */
div::selection {}

/* 选中的是input元素中的提示文字，例如可以用来改背景色 */
input::placeholder {}

/* 选中的是p元素最开始的位置，例如可以用来添加前缀 */
p::before {
    content: "yourPrefix";
}

/* 选中的是p元素最后的位置，例如可以用来添加后缀 */
p::after {
    content: "yourSuffix";
}
```

#### 选择器优先级

当样式发生冲突时，优先级高的样式生效，优先级简单理解如下：

`!important` > [行内样式 >] id选择器 > 类选择器 > 元素选择器 > 通配选择器

```css
/* !important要慎用，行内样式要少写 */
div {
    color: green !important;
}
```

实际上是比较权重，权重大的生效，格式：(a, b, c)

a：id选择器的总个数

b：类、伪类、属性选择器的总个数

c：元素、伪元素选择器的总个数

- 比较规则：按照从左到右的顺序，依次比较大小，当前位胜出后，后面的位不再对比，如：(1, 0, 0) > (0, 1, 0)
    - 如果最终权重一样大，则后写的样式生效

- 行内样式权重大于所有选择器

- `!important`权重大于行内样式，权重最高

- 并集选择器的每一个部分都是分开算的（回看并集选择器的本质就知道了）

```html
<div class="myClass1">
    <p>
        <span class="myClass2"></span>
        <span></span>
    </p>
</div>
```

```css
/* (0, 2, 1) */
.myClass1 span.myClass2 {}

/* (0, 1, 3) */
div>p>span:nth-child(1) {}
```

### CSS三大特性

- 层叠性：如果发生了样式冲突，就会根据一定的规则，进行样式的层叠（覆盖）
    - 样式冲突：元素的同一个样式名（如color），被设置了不同的值，就发生了样式冲突

- 继承性：元素会自动拥有其父元素或其祖先元素上所设置的某些样式
    - 规则：优先继承离得近的
    - 常见的可继承属性：text-?, font-?, line-?, color ...，参照mdn网站，查询属性是否可被继承

- 优先级：`!important` > [行内样式 >] id选择器 > 类选择器 > 元素选择器 > 通配选择器 > 继承的样式

### 像素和颜色

- 定义长度，单位虽然支持用cm，好像mm也支持，但是一般用像素单位px

- 定义颜色
    - 可以用颜色名如：green，但是一般不这么用，因为支持的颜色名有限
    - 用rgb
        - 全部用数值：rgb(255, 0, 0)
        - 也可以全部用百分比：rgb(%100, 0%, 0%)
        - 三种颜色值相同，呈现的是灰色，值越大，灰色越浅
    - 用rgba，只是跟rgb相比多了透明度这个维度，透明度的值为：0（完全透明）~ 1（不透明），透明度也可以写成百分数，注意rgb必须保持一致（全部是数值或全部是百分比）
        - rgb(255, 0, 0, 0.5)，0.5也可以写成.5
        - rgb(255, 0, 0, 50%)
    - 用hex：就是红绿蓝的颜色值分别用两位的16进制表示，不区分大小写
        - 如：`#ff0000`
        - 如果颜色值全都是两两相同，可以用简写，如：`#f00`
    - 用hexa：只是跟hex相比多了透明度这个维度，透明度的值为：`00`（完全透明）~ `ff`（不透明）
        - 如：`#ff000088`
        - 如果颜色值全都是两两相同，可以用简写，如：`#f008`
            - 如果颜色值用了简写，透明度也必须用简写
        - ie浏览器不支持hexa颜色，但支持hex颜色
    - 用hsl：`hsl(色相, 饱和度, 亮度)`
        - 色相就是颜色，但是是用角度表示的：0deg-360deg，后缀deg可以省略，可以查hsl的色相图了解度数对应的颜色
        - 饱和度：0%-100%，全灰就是0%，彩色（无灰度）就是100%
        - 亮度：0%-100%，0%（黑）和100%（白）是两个极端，一般不用这两个值
        - 如：hsl(0deg, 100%, 50%)，就相当于rgb(255, 0, 0)
    - 用hsla：只是跟hsl相比多了透明度这个维度，透明度的值为：0（完全透明）~ 1（不透明），透明度也可以写成百分数

### 常用字体属性

#### 字体大小

chrome浏览器默认支持的最小字体大小为12px，默认字体大小为16px，设置为0px时字体会自动消失

不同浏览器默认的字体大小可能不一样，最好设置一个明确的字体大小

通常给body设置font-size属性，这样body中的其它元素就都可以继承了

```css
body {
    /* 调整字体大小为16像素 */
    font-size: 16px;
}
```

由于字体设计原因，文字最终呈现的大小，并不一定与font-size的值一直，可能大，也可能小

文字相对字体设计框，并不是垂直居中的，通常都靠下一些

#### 字体族

声明字体族的时候应该统一都用sans-serif或者都用serif字体

为了规范，字体名称应该都用英文+双引号的写法

因为英文字体名兼容性更好，英文字体名按需自行查询

如果字体名包含空格，必须用双引号包裹

可以设置多个字体，浏览器会按照从左到右的顺序逐个查找，找到就用

没有找到就继续找后面的，最后用sans-serif或者都用serif兜底

windows中，默认字体是微软雅黑

```css
body {
    /* 最后的sans-serif是范指，告诉浏览器当前面的字体都不可用的时候，就查用户系统可用的sans-serif字体 */
    font-family: "优先字体1","其次字体2","再而字体3", ..., "字体n", sans-serif;
}
```

#### 字体风格

- font-style有三个值
    - normal：默认值
    - italic：斜体，优先找该字体自带的倾斜字体，没有找到时，强行倾斜，推荐
    - oblique：斜体，强行倾斜

```css
body {
    font-style: italic;
}
```

#### 字体粗细

- font-weight有四个值，依次变粗
    - lighter，细
    - normal，正常
    - bold，粗
    - bolder，更粗，绝大多数字体没有设置这个字体，用的不多
    - 还可以是数值：100-1000，不带单位，但是大多数字体没有设计那么多梯度的粗体，开发的时候用数字的情况还挺多的
        - 100-300等同于lighter
        - 400-500等同于normal
        - 600及以上等同于bold

```css
body {
    font-weight: lighter;
}
```

#### 字体复合属性

```css
body {
    /* 字体族和字体大小是必须的，倒数第一个值必须是字体族，倒数第二个值必须是字体大小，其它值顺序随意 */
    /* 不同的值用空格隔开 */
    /* 开发更推荐这种复合属性写法 */
    font: 20px "微软雅黑", "宋体";
}
```

### 常用文本属性

#### 文本颜色

```css
div {
    color: red;
}
```

#### 文本间距

文本间距的值，0px是默认值，还可以是负值，这样文本就被挤压到一起了

```css
div {
    /* 文本中的字母之间的间距，可以控制汉字 */
    letter-spacing: 20px;
    /* 文本中的英文单词之间的间距，不能控制汉字 */
    /* 如果每个汉字之间添加了空格是可以控制的，但是中文文本一般不会这么写 */
    word-spacing: 20px;
}
```

#### 文本修饰

上划线、下划线、删除线等都是文本修饰

```css
div {
    /* overline：上划线 */
    /* underline：下划线 */
    /* line-through：删除线 */
    /* none：无装饰线，默认，给a标签设置这个值就可以去掉默认的下划线了 */
    text-decoration: line-through;

    /* 还可以设置线的样式：dotted，虚线；wavy，波浪线 */
    text-decoration: line-through wavy;

    /* 还可以设置线的颜色，这几个值的顺序都是随意的 */
    text-decoration: line-through wavy green;
}
```

#### 文本缩进

```css
div {
    font-size: 20px;
    /* 普通写法，设置为font-size的整数倍n来定义缩进为n个字符 */
    text-indent: 40px;
    /* 进阶写法，缩进几个字就写几em */
    text-indent: 2em;
}
```

#### 行高

由于字体设计原因，文字在一行中，并不是绝对垂直居中，字体有可能是超出了字体框的范围的

如果行高设置成跟字体一样大小，可能就会上一行的字体底部跟下一行字体的顶部挤在一起

- line-height的值可以是
    - normal，默认，浏览器根据字体大小自适应行高
    - 像素值
    - 数值，参考自身font-size的倍数，这种写法用的多，1.5~2.0是比较常用的值，如1.5，最终行高为1.5 * font-size的值，
    - 百分比，参考自身font-size的百分比，如数值写法1.5的百分比写法就是150%，最终行高为1.5 * font-size的值

```css
div {
    font-size: 20px;
    /*  */
    line-height: 30px;
}
```

如果行高过小，多行的文字会重叠

行高最小值是0，不能是负数，设置为负数会被浏览器标识无效，并显示为normal的行高

行高是可以继承的，因此用数值写法更方便继承

- line-height和元素的height属性的关系
    - 设置了height，高度就是height的值
    - 没有设置height，高度就是line-height * 行数

- 行高的应用场景
    - 调整多行文字的间距
    - 单行文字的垂直居中（非绝对垂直居中，观感上垂直居中）

```css
div {
    height: 30px;
    font-size: 20px;
    /* line-height设置成和height一样就可以实现单行文本垂直居中了 */
    line-height: 30px;
}
```

#### 文本对齐

- 水平对齐，属性名`text-align`，值有
    - left，默认
    - center
    - right

```css
dev {
    text-align: left;
}
```

- 垂直对齐
    - 顶部：默认
    - 居中
        - 单行文本：line-height设置成和height一样
        - 多行文本用定位解决
    - 底部
        - 单行文本：临时解决办法，设置line-height=height * 2 - font-size - x，x是根据字体族动态决定的一个微调值，更好的底部对齐用定位解决

```css
div {
    height: 30px;
    font-size: 20px;
    /* line-height设置成和height一样就可以实现单行文本垂直居中了 */
    line-height: 30px;
}
```

#### vertical-align

用于指定同一行元素之间，或表格单元格内文字的垂直对齐方式

- 常用值
    - baseline，默认，使元素的基线与父元素的基线对齐
    - top，使元素的顶部与其所在行的顶部对齐
    - middle，使元素的中部与父元素的基线加上父元素字母x的一半对齐（父元素字母x的中心点所在的那条线）
    - bottom，使元素的底部与其所在行的底部对齐

注意vertical-align不能控制块元素

```html
<div>
    fatherx<span>xson</span
</div>
```

```css
div {
    height: 300px;
    font-size: 100px;
    background-color: skyblue;
}

span {
    font-size: 40px;
    vertical-align: middle;
    background-color: orange;
}
```

### 列表相关属性

这些属性都可以用在ul、ol、li元素上

```html
<ul>
    <li></li>
    <li></li>
</ul>
```

```css
ul {
    /* 列表符号，即列表项前面的符号 */
    /* 值有：none，square，lower-roman，upper-roman，decimal，lower-alpha，upper-alpha，disc */
    list-style-type: none;

    /* 列表符号的位置，给li标签设置背景色才能看出效果：inside、outside */
    list-style-position: inside;

    /* 自定义列表符号 */
    list-style-image: url("/path/to/image");

    /* 复合属性，数量、顺序随意 */
    /* 这里自定义列表符号会覆盖decimal */
    list-style: decimal inside url("/path/to/image");
}
li {
    background-color: orange;
}
```

### 边框相关属性

这几个边框属性，除了table，th和td外，其它元素也可以用

- border-style，边框风格，值有
    - none，默认
    - solid，实线
    - dashed，虚线
    - dotted，点线
    - double，双实线

```css
table {
    /* 只要指定了border-style就能显示边框，border-width和border-color都有默认值 */
    border-width: 2px;
    border-color: red;
    border-style: solid;

    /* 复合属性 */
    border: 2px red solid;
}

th,td {
    border: 2px green solid;
}
```

### 表格独有属性

```css
table {
    /* 复合属性 */
    border: 2px red solid;

    width: 500px;

    /* 表格列宽：auto、fixed */
    table-layout: fixed;
    /* 单元格间距，border-collapse为separate时才有效果 */
    border-spacing: 5px;
    /* 合并相邻单元格的边框：collapse、separate */
    border-collapse: collapse;
    /* 隐藏没有内容的单元格：show、hide，设置为hide时， border-collapse为separate才有效果 */
    empty-cells: hide;
    /* 表格标题的位置：top、bottom */
    caption-side: top;
}
```

### 背景相关属性

- background-position的值有
    - 水平方向：left、center、right
    - 垂直方向：top、center、bottom
    - 如果只写一个方向的值，另一个方向的值默认为center
    - 还可以指定坐标位置，分别表示x坐标和y坐标的值，如：100px 200px
    - 只写一个坐标值，会被当作x坐标，y坐标默认为center

```css
div {
    /* 背景颜色，默认transparent，会被背景图片覆盖 */
    background-color: blue;
    /* 背景图片 */
    background-image: url("/path/to/image");
    /* 背景图片的重复方式：repeat、no-repeat、repeat-x、repeat-y */
    background-repeat: repeat;
    /* 背景图片位置 */
    background-position: left top;
    /* 复合属性：属性值随意，顺序随意 */
    background: blue url("/path/to/image") no-repeat left top;
}
```

### 鼠标相关属性

- cursor的值有
    - pointer，小手
    - move，移动图标
    - text，文字选择器
    - crosshair，十字架
    - wait，等待
    - help，帮助
    - 还有其它值，就不一一列举了

```css
div {
    cursor: pointer;

    /* 还可以是图片，如果不显示，用ico格式试试 */
    /* 末尾还要记得加上图片所代表的cursor的值，如pointer */
    cursor: url("/path/to/image"), pointer;
}
```

### 常用长度单位

- px，像素
- em，相对于当前元素或其祖先元素的font-size的倍数
    - 如果当前元素没有设置font-size，就从祖先元素找，如果都没有设置，就用浏览器默认的font-size
    - 如果font-size也设置为em，就从祖先元素找，如果都没有设置，就用浏览器默认的font-size
- rem，r表示root，即html元素，相对于根元素的font-size的倍数
- %，相对当前元素的父元素的百分比
- 不常用单位：cm、mm

注意css中设置长度必须加单位，否则样式无效

```css
div {
    /* 10 x 20px = 200px */
    width: 10em;
    height: 10em;
    font-size: 20px;
}
```

### 元素的显示模式

- 块元素（block），又称块级元素
    - 在页面中独占一行，不会与任何元素共用一行，是从上到下排列的
    - 默认宽度：撑满父元素
    - 默认高度：由内容撑开
    - 可以通过css设置宽高
- 举例：
    - 主体结构元素：html、body
    - 排版元素：h1~h6、hr、p、pre、div
    - 列表元素：ul、ol、li、dl、dt、dd
    - 表格相关元素：table、thead、tbody、tfoot、tr、caption
    - 表单元素：form、option

```html
<div></div>
```

```css
div {
    width: 200px;
    height: 200px;
}
```

- 行内元素（inline），又称内联元素
    - 在页面中不独占一行，一行中不能容纳下的行内元素，会在下一行继续从左到右排列
    - 默认宽度：由内容撑开
    - 默认高度：由内容撑开
    - 无法通过css设置宽高
- 举例：
    - 文本元素：br、em、strong、sup、sub、del、ins
    - a、label

```html
<span></span>
```

- 行内块元素（inline-block），又称内联块元素
    - 在页面中不独占一行，一行中不能容纳下的行内元素，会在下一行继续从左到右排列
    - 默认宽度：由内容撑开
    - 默认高度：由内容撑开
    - 可以通过css设置宽高
- 举例：
    - 图片：img
    - 单元格：th、td
    - 表单控件：input、textarea、select、button
    - 框架元素：iframe

```html
<img src="/path/to/image" alt="" />
```

```css
img {
    width: 200px;
}
```

元素早期只分为：行内元素、块级元素，区分条件也只有一种：是否独占一行

如果按照这种分类方式，行内块元素应该算作行内元素

#### 修改元素的显示模式

```css
div {
    /* 元素作为块级元素显示 */
    display: block;
    display: inline;
    display: inline-block;
    /* 不显示该元素 */
    display: none;
}
```

### 盒子模型

#### 盒子模型的组成部分

![盒子模型](image/boxModel.png)

从外到里：外边距（margin）、边框（border）、内边距（padding）、内容区（content）

css会把所有的html元素看成是这么一个盒子模型

盒子的大小 = content + 左右padding + 左右border

外边距不会影响盒子的大小（当不设置元素的width时），但会影响盒子的位置

```css
div {
    /* 内容区的宽高 */
    width: 200px;
    height: 200px;
    /* 内边距，设置的背景色会填充内边距区域 */
    padding: 8px;
    /* 边框，设置的背景色会填充内边框区域 */
    border: 2px solid red;
    /* 外边距 */
    margin: 5px;
}
```

#### 盒子的内容区

min-width、max-width一般不与width一起使用

min-height、max-height一般不与height一起使用

```css
div {
    width: 800px;

    min-width: 600px;
    max-width: 1000px;
    
    height: 200px;

    min-height: 100px;
    max-height: 400px;
}
```

#### 元素的默认宽度

就是不设置width属性时，元素所呈现出来的宽度

元素总的默认宽度= 父元素的content - 元素自身左右margin

元素内容区的默认宽度 = 父元素的content - 元素自身左右margin - 自身左右border - 自身左右padding

#### 盒子的内边距

内边距不能是负数，负数无效

可以设置行内元素的左右内边距，但是上下内边距不能完美设置

块级元素、行内块元素，四个方向的内边距都可以完美设置

```css
div {
    /* 分开写 */
    padding-top: 8px;
    padding-right: 8px;
    padding-bottom: 8px;
    padding-left: 8px;

    /* 复合属性，写一个值，表示：四个方向的内边距都一样 */
    padding: 8px;

    /* 复合属性，写两个值，分别表示：上下、左右的内边距 */
    padding: 10px 20px;

    /* 复合属性，写三个值，分别表示：上、左右、下的内边距（上中下） */
    padding: 10px 20px 30px;

    /* 复合属性，写四个值，分别表示：上、右、下、左内边距（顺时针） */
    padding: 10px 20px 30px 40px;
}
```

#### 盒子的边框

```css
div {
    border-left-width: 2px;
    border-right-width: 2px;
    border-top-width: 2px;
    border-bottom-width: 2px;

    border-left-color: red;
    border-right-color: green;
    border-top-color: blue;
    border-bottom-color: orange;

    border-left-style: solid;
    border-right-style: dashed;
    border-top-style: double;
    border-bottom-style: dotted;

    /* 只要指定了border-style就能显示边框，border-width和border-color都有默认值 */
    /* 复合属性 */
    border-width: 2px;
    border-color: red;
    border-style: solid;

    /* 复合属性 */
    border-left: 2px red solid;
    border-right: 2px red solid;
    border-top: 2px red solid;
    border-bottom: 2px red solid;

    /* 复合属性 */
    border: 2px red solid;
}
```

#### 盒子的外边距

子元素的margin是参考父元素的content计算的

margin-left、margin-top会影响元素自身的位置；margin-right、margin-bottom会影响兄弟元素的位置

对于行内元素，margin-left、margin-right可以完美设置，margin-top、margin-bottom设置后无效

margin的值可以是负值

```css
div {
    margin-left: 10px;
    margin-right: 10px;
    margin-top: 10px;
    margin-bottom: 10px;

    /* 复合属性，写一个值，表示：四个方向的外边距都一样 */
    margin: 8px;

    /* 复合属性，写两个值，分别表示：上下、左右的外边距 */
    margin: 10px 20px;

    /* 复合属性，写三个值，分别表示：上、左右、下的外边距（上中下） */
    margin: 10px 20px 30px;

    /* 复合属性，写四个值，分别表示：上、右、下、左外边距（顺时针） */
    margin: 10px 20px 30px 40px;
}
```

margin的值可以是auto，给一个块级元素左右margin设置auto可以实现该元素在其父元素内水平居中

```css
div {
    /* 这两个设置可以实现水平居中 */
    /* 跟左边有多远离多远 */
    margin-left: auto;
    /* 跟右边有多远离多远 */
    margin-right: auto;

    /* 也可以这么写，上下随意，左右auto */
    margin: 10px auto;
}
```

#### margin塌陷问题

margin塌陷：第一个子元素的上margin会作用在父元素上（相当于父元素设置了上margin），最后一个子元素的下margin会作用在父元素上，（相当于父元素设置了下margin）

- 解决塌陷
    - 方法一：给父元素设置不为0的padding
    - 方法二：给父元素设置宽度不为0的border
    - 方法三：给父元素设置css样式：`overflow:hidden`

#### margin合并问题

margin合并：上面兄弟元素的下margin和下面兄弟元素的上margin会合并，取二者的最大值，而不是相加

- 如何解决margin合并问题：
    - 无需解决，布局的时候上下兄弟元素，只给一个设置就可以了
    - 如只给上面的兄弟设置下margin，或只给下面的兄弟元素设置上margin

### 处理内容溢出

- overflow、overflow-x、overflow-y属性值
    - visible，显示溢出内容
    - hidden，隐藏溢出内容
    - scroll，无论内容是否溢出都会显示滚动条
    - auto，根据内容是否溢出自动显示滚动条

```css
div {
    /* 主要用这个 */
    overflow: auto;

    /* 不能一个显示一个隐藏 */
    overflow-x: auto;
    overflow-y: auto;
}
```

### 隐藏元素的两种方式

```css
div {
    /* 方式一，彻底隐藏，不但看不见，也不占原来的位置，没有大小了 */
    display: none;

    /* 方式二，属性：show、hidden，元素设置为hidden在页面上看不到了，还会占有原来的位置（元素大小依然保持） */
    visibility: hidden;
}
```

### 样式的继承

有些样式会继承，元素本身如果设置了某个样式，就使用自身设置的样式

如果元素本身没有设置某个样式，会从父元素开始一级一级继承（优先继承离得近的祖先元素）

会继承的属性：字体属性、文本属性（除了vertical-align）、文字颜色等

不会继承的属性：边框、背景、内边距、外边距、宽高、溢出方式等

规律：能继承的属性，都是不影响布局的，也就是跟盒子模型没关系的属性

### 元素的默认样式

- 元素一般都有默认的样式，如
    - a：下划线、字体颜色、鼠标小手
    - h1~h6: 文字加粗、文字大小、上下外边距
    - p：上下外边距
    - ul、ol：左内边距
    - body：8px的外边距
优先级：元素的默认样式 > 继承的样式

如果要重置元素的默认样式，选择器一定要直接选中该元素

### 布局小技巧

行内元素、行内块元素，可以被父元素当作文本处理

即可以像处理文本对齐一样处理行内元素、行内块元素在父元素中的对齐

例如：text-align、line-height、text-indent等

- 如何让子元素在父元素中水平居中
    - 若子元素为块元素，给子元素加上`margin: 0 auto`
    - 若子元素为行内元素或行内块元素，给父元素加上`text-align: center`

- 如何让子元素在父元素中垂直居中
    - 若子元素为块元素，给子元素加上margin-top，值为：（父元素content高 - 子元素盒子高） / 2
    - 若子元素为行内元素或行内块元素
        - 让父元素的line-height和height一样
        - 每个子元素都加上`vertical-align:middle`
        - 若想绝对垂直居中，父元素的字体大小设置为0，然后再给子元素指定想要的字体大小覆盖继承的父元素的字体大小

#### 布局小技巧1

```html
<div class="outer">
    <div class="inner">inner</div>
</div>
```

```css
.outer {
    width: 400px;
    height: 400px;
    background-color: gray;
    /* 解决塌陷问题 */
    overflow: hidden;
}

.inner {
    width: 200px;
    height: 100px;
    background-color: orange;
    /* 元素水平居中 */
    margin: 0 auto;
    /* 注意margin-top会有塌陷问题 */
    /* 元素垂直居中，，计算公式：(父元素content的高度-(自身content+上下padding+上下border))/2 */
    margin-top: 150px;

    /* 文本水平居中 */
    text-align: center;
    /* 文本垂直居中，单行文本设置line-height等于height就行了 */
    line-height: 100px;
}
```

#### 布局小技巧2

```html
<div class="outer">
    <span class="inner">inner</span>
</div>
```

```css
.outer {
    width: 400px;
    height: 400px;
    background-color: gray;

    /* 行内元素、行内块元素可以当成文本来处理，也就是可以使用文本的布局方式 */
    text-align: center;
    line-height: 400px;
}

.inner {
    background-color: orange;
    font-size: 20px;
}
```

#### 布局小技巧3

```html
<div class="outer">
    <span>inner</span>
    <img src="/path/to/image" alt="" />
</div>
```

```css
.outer {
    width: 400px;
    height: 400px;
    background-color: gray;

    text-align: center;
    /* 由于字体设计原因不是绝对居中，将字体大小设置为0就行了 */
    line-height: 400px;
    font-size: 0px;
}

img {
    vertical-align: middle;
}

span {
    font-size: 40px;
    vertical-align: middle;   
    background-color: orange; 
}
```

### 元素之间的空白问题

产生原因：行内元素、行内块元素彼此之间的换行会被浏览器解析为一个空白字符

- 解决办法有两种
    - 去掉换行和空格（不推荐）
    - 给父元素设置`font-size: 0`，再给需要显示文字的子元素单独设置字体大小

```html
<div>
    <span>1</span>
    <span>2</span>
    <span>3</span>
    <img src="" alt="" />
    <img src="" alt="" />
    <img src="" alt="" />
</div>
```

```css
div {
    font-size: 0px;
}

span {
    font-size: 20px;
}
```

### 行内块元素的幽灵空白问题

问题原因：行内块元素与文本的基线（x底部）对齐，而文本的基线与文本最底端之间是有一定间距的

- 解决办法
    - 一，给行内块设置vertical，值不为baseline都可，如top、middle、bottom
    - 二，若父元素中只有一张图片，设置图片为块元素`display:block`
    - 三，给父元素设置字体大小为0，如果该行内块内部还有文本，则另单独设置字体大小

```html
<div>
    <img src="" alt="" />
</div>
```

```css
div {
    width: 600px;
    background-color: gray;
    /* 解决办法三：如果子元素有文本要另外设置子元素的字体大小 */
    font-size: 0px;
}
img {
    height: 200px;
    /* 解决办法一：只要vertical-align不设置为baseline就行 */
    vertical-align: middle;

    /* 解决办法二：如果img前后没有文本才可以这么写 */
    display: block;
}
```

### 浮动

- 属性：float，值有
    - left，向左浮动
    - right，向右浮动

```css
.myClass {
    float: right;
}
```

最初，浮动是用来实现文字环绕图片，或文字环绕文字效果的，现在浮动是主流的页面布局方式之一

- 文字环绕图片

```html
<div>
    <img src="" alt="" />
    ...很长文本...
</div>
```

```css
div {
    width: 600px;
    height: 400px;
    background-color: gray;
}
img {
    width: 200px;
    /* 图片飘浮到左边，让文本在其右边和下方环绕 */
    float: left;
}
```

- 文字环绕文字

```html
<div class="myClass">
    很长文本...
</div>
```

```css
div {
    width: 600px;
    height: 400px;
    background-color: gray;
}
.myClass::first-letter {
    font-size: 80px;
    /* 第一个文字浮动到左边，让文本在其右边和下方环绕 */
    float: left;
}
```

#### 元素浮动后的特点

脱离文档流，元素原来是文档那样平面的上下布局，浮动后可以想像成立体结构了，它是飘在文档上方了

不管浮动前是什么元素，浮动后，默认宽与高都是被内容撑开（尽可能小），并且可以设置宽高

不会独占一行，可以与其它元素共用一行

不会有margin合并和margin塌陷问题，能够完美地设置四个方向的margin和padding

不会像行内块一样被当作文本处理（没有行内块的空白问题）

#### 元素浮动后的影响

- 对兄弟元素的影响
    - 后面的兄弟元素，会占据浮动元素之前的位置，在浮动元素的下方（想像成立体结构，一个天上，一个在地下）
    - 对前面的兄弟元素无影响
- 对父元素的影响
    - 不能撑起父元素的高度，导致父元素高度塌陷，但父元素的宽度依然束缚浮动的元素

#### 解决浮动产生的影响

- 一，给父元素指定高度，只能解决父元素高度塌陷问题
- 二，给父元素也指定浮动，但会带来其它影响（影响父元素的兄弟元素）
- 三，给父元素设置`overflow:hidden`，只能解决父元素高度塌陷问题，但是解决不了其子元素有一个不浮动的问题
- 四，在所有浮动元素的最后面，添加一个块级元素，并给它设置`clear:both`，但解决不了有兄弟元素不浮动的问题
- 五，给浮动元素的父元素，设置伪元素，通过伪元素清除浮动，原理与四相同，推荐使用，，但解决不了有子元素不浮动的问题

```css
.parent::after {
    content: "";
    display: block;
    /* 值有left、right、both，表示清除左、右、全部浮动带来的影响 */
    clear: both;
}
```

布局中的一个原则：设置浮动的时候，兄弟元素要么全都浮动，要么全都不浮动

### 定位

- position
    - static，默认
    - absolute，绝对
    - relative，相对
    - fixed，固定

- left和right只需要设置一个就行，如果都设置，则left生效
- top和bottom只需要设置一个就行，如果都设置，则top生效

```css
.myClass {
    position: absolute;
    top: 100px;
    right:100px;
}
```

#### 相对定位

相对定位参考的是元素原本的位置

不会脱离文档流，元素位置的变化，只是视觉效果上的变化，不会对其它元素产生影响

- 设置了定位的元素的显示层级比普通元素搞，无论什么定位，显示层级都是一样的，如果发生了元素覆盖
    - 设置了定位的元素会盖在普通元素之上
    - 都设置了定位的两个元素，后写的元素会盖在先写的元素之上

设置了相对定位的元素的原本的位置也不会被其它元素占用

相对定位可以和margin、float一起用，但是不推荐

- 相对定位一般用来
    - 对元素进行微调
    - 与绝对定位配合使用

```css
.myClass {
    position: relative;
    left: 100px;
    top: 100px;
}
```

#### 绝对定位

绝对定位参考的是包含块的位置

- 包含块
    - 没有脱离文档流的元素，其父元素就是包含块
    - 脱离文档流的元素
        - 第一个设置了定位属性的祖先元素，就是它的包含块
        - 如果所有祖先元素都没设置定位属性，那整个页面（html）就是它的包含块

设置了绝对定位的元素脱离文档流，会对后面的兄弟元素有影响，也对父元素有影响

绝对定位和浮动不能同时设置，如果同时设置，则绝对定位生效

设置了绝对定位的元素也能通过margin调整位置，但不推荐

无论是什么元素（行内、行内块、块级元素）设置了绝对定位后，都变成了定位元素

定位元素：默认宽高被内容撑开，且能自由设置宽高

子元素设置了绝对定位，一般给其父元素设置相对定位

```html
<div class="outer">
    <div class="inner"></div>
</div>
```

```css
.outer {
    position: relative;
}
.inner {
    position: absolute;
    left: 100px;
    top: 100px;
}
```

#### 固定定位

参考的是它的视口（类似广告的功能，无论滚轮怎么滚动，都会出现在页面的固定位置）

视口：对于PC浏览器来说，视口就是我们看网页的那扇“窗户”

脱离文档流，会对后面的兄弟元素有影响，也会对父元素有影响

固定定位和浮动不能同时设置，如果同时设置，则固定定位生效

设置了绝对定位的元素也能通过margin调整位置，但不推荐

无论是什么元素（行内、行内块、块级元素）设置了固定定位后，都变成了定位元素

```css
.myClass {
    position: fixed;
    left: 100px;
    top: 100px;
}
```

#### 粘性定位

设置粘性定位的元素的参考点是离它最近的一个拥有滚动机制的祖先元素，即使这个祖先不是最近的真实可滚动祖先

不会脱离文档流，是一种专门用于窗口滚动时的新的定位放松（如冻结头部）

粘性定位和浮动不能同时设置，如果同时设置，则粘性定位生效

设置了粘性定位的元素也能通过margin调整位置，但不推荐

粘性定位和相对定位的特点基本一致，不同点是粘性定位可以在元素达到某个位置时将其固定

```css
.myClass {
    position: sticky;
    /* 最常用的是top属性 */
    top: 0px;
}
```

#### 定位的层级

设置了定位的元素的显示层级比普通元素高，无论什么定位，显示层级都是一样的

如果位置发生重叠，默认情况是：后写的元素，会显示在先写的元素之上

- 可以通过css属性`z-index`调整元素的显示层级
    - `z-index`的值时数字，没有单位，值越大显示层级越高
    - 只有设置了定位的元素，设置`z-index`才有效
    - 如果`z-index`值大的元素，依然没有覆盖掉`z-index`值小的元素，那么请检查其包含块的层级

#### 定位的特殊应用

发生绝对定位、固定定位后，元素都变成了定位元素，默认宽高被内容撑开，且依然可以设置宽高

发生相对定位后，元素依然是之前的显示模式（没脱离文档流，原来是块元素还是块元素）

以下所说的特殊应用，只针对绝对定位和固定定位的元素，不包括相对定位的元素

##### 定位的特殊应用1

让定位元素的宽高充满包含块

块宽想与包含块一致，可以给定位元素同时设置left和right为0

高度想与包含块一致，可以给定位元素同时设置top和bottom为0

```html
<div class="outer">
    <div class="inner"></div>
</div>
```

```css
.outer {
    position: relative;
}
.inner {
    padding: 10px;
    border: 2px solid red;
    position: absolute;
    left: 0px;
    right: 0px;
    top: 0px;
    bottom: 0px;
}
```

##### 定位的特殊应用2

让定位元素在包含块中居中

```html
<div class="outer">
    <div class="inner"></div>
</div>
```

```css
.outer {
    width: 800px;
    height: 400px;
    background-color: gray;

    position: relative;
}
.inner {
    /* 必须设置宽和高 */
    width: 400px;
    height: 100px;
    background-color: orange;

    position: absolute;
    /* 方法一，一下的属性缺一不可 */
    left: 0px;
    right: 0px;
    top: 0px;
    bottom: 0px;
    margin: auto;

    /* 方法二，定位+margin，不推荐这种方式 */
    left: 50%;
    top: 50%;
    /* 上面写了left和top,外边距就只能用相应的margin-left和margin-top */
    margin-left: -200px;
    margin-top: -50px;
}
```

### 版心

就是排版中心

在pc端网页中，一般都会有一个固定宽度且水平居中的盒子，来显示网页的主要内容，这是网页的版心

版心的宽度一般是960-1200像素之间

版心可以是一个，也可以是多个

### 常用布局类名

|位置|常用类名|
|:-|:-|
|顶部导航条|topbar|
|页头|header、page-header|
|导航|nav、navigator、navbar|
|搜索框|search、search-box|
|横幅、广告、宣传图|banner|
|主要内容|content、main|
|侧边栏|aside、sidebar|
|页脚|footer、page-footer|

### 重置默认样式

#### 方案一：使用全局选择器

简单案例可以使用，但实际开发不会使用

```css
* {
    margin: 0;
    padding: 0;
    ...
}
```

#### 方案二：reset.css

自己写个reset.css，选择到具有默认样式的元素，清空其默认样式

经过reset后的网页，好似一张白纸，开发人员可根据设计搞，精细地去添加具体的样式

实际开发更多使用这种方案

#### 方案三：Normalize.css

官网：<https://necolas.github.io/normalize.css/>

Normalize.css是一种最新方案，它在清除默认样式的基础上，保留了一些有价值的默认样式

## CSS3

### CSS新特性的浏览器私有前缀写法顺序

某个浏览器对某个css特性的支持情况，可以到<https://caniuse.com/>查询

先写浏览器私有前缀写法，再写css

```css
div {
    /* chromium系列浏览器 */
    -webkit-border-radius: 20px;
    /* 火狐系列浏览器 */
    -moz-border-radius: 20px;
    /* ie浏览器 */
    -ms-border-radius: 20px;
    /* opera浏览器 */
    -o-border-radius: 20px;
    border-radius: 20px;
}
```

其实在编码的时候不用过于关注浏览器私有前缀，即便是为了老浏览器而加前缀

也可以通过借助现代构建工具，自动帮我们添加这些前缀

### 新增长度单位

- vw和vh
    - vw，视口宽度（viewport width）的百分之多少，如50vw就是视口宽度的50%
    - vh，视口高度（viewport height）的百分之多少，如50vh就是视口高度的50%
    - 在PC浏览器用得不多，在移动端比较常用
    - 一般是vw用得比较多

- vmin和vmax
    - vmin，视口宽高中小者的百分之多少
    - vmax，视口宽高中大者的百分之多少

```css
div {
    width: 50vw;
    height: 20vw;
}
```

### 新增盒子模型相关属性

#### box-sizing

使用box-sizing可以设置盒模型的两种类型

- 值
    - content-box，width和height设置的是盒子内容区的大小（默认值）
    - border-box，width和height设置的是盒子总大小（怪异盒模型）

```css
div {
    width: 200px;
    height: 200px;
    padding: 5px;
    border: 5px solid red;
    box-sizing: border-box;
}
```

#### resize

用来控制是否允许用户调节元素大小

- 值
    - none，不允许用户调整元素大小（默认）
    - horizontal，用户可以调整元素的宽度
    - vertical，用户可以调整元素的高度
    - both，用户可以调整元素的宽高

```css
div {
    width: 400px;
    height: 400px;
    background-color: orange;
    resize: vertical;
    overflow: auto;
}
```

#### box-shadow

```css
div {
    width: 400px;
    height: 400px;
    background-color: orange;
    margin: 0 auto;
    margin-top: 100px;
    font-size: 40px;

    /* 没有阴影 */
    box-shadow: none;

    /* 写两个值：水平位置，垂直位置，都可以是负值 */
    box-shadow: 10px 10px;

    /* 写三个值：水平位置，垂直位置 阴影颜色 */
    box-shadow: 10px 10px blue;

    /* 写三个值：水平位置，垂直位置 阴影模糊程度 */
    box-shadow: 10px 10px 10px;

    /* 写四个值：水平位置，垂直位置 阴影模糊程度 阴影颜色，用得比较多 */
    box-shadow: 10px 10px 10px blue;

    /* 写五个值：水平位置，垂直位置 阴影模糊程度 外延值（阴影大小） 阴影颜色 */
    box-shadow: 10px 10px 10px 10px blue;

    /* 写五个值：水平位置，垂直位置 阴影模糊程度 外延值（阴影大小） 阴影颜色 内阴影 */
    /* 不设置inset就是外部阴影，设置了就是内阴影 */
    box-shadow: 10px 10px 10px 10px blue inset;
}
```

#### opacity

设置整个元素（包括元素里面的内容）的不透明度（可见度），值为0-1之间的小数，0是完全透明（完全看不到），1是完全不透明

```css
div {
    width: 400px;
    height: 400px;
    background-color: orange;
    font-size: 40px;
    font-weight: bold;

    opacity: 0.5;
}
```

### 新增背景相关属性

#### background-origin

设置显示背景图的原点（起点）

- 值
    - padding-box，从padding区域开始显示背景图像（默认）
    - border-box，从border区域开始显示背景图像
    - content-box，从content区域开始显示背景图像

```css
div {
    background-image: url("/path/to/image");
    /* 测试background-origin的时候建议设置图片不重复显示 */
    background-repeat: no-repeat;
    background-origin: padding-box;
}
```

#### background-clip

设置背景图的向外裁剪的区域

- 值
    - padding-box，从padding区域开始向外裁剪背景图像（也就是只留下padding内的部分）
    - border-box，从border区域开始向外裁剪背景图像（也就是只留下border内的部分），默认
    - content-box，从content区域开始向外裁剪背景图像（也就是只留下content内的部分）
    - text，背景图只呈现在文字上

```css
div {
    width: 400px;
    height: 400px;
    background-color: skyblue;
    margin: 0 auto;
    font-size: 120px;
    font-weight: bold;
    padding: 50px;
    border: 50px dashed rgba(255,0,0,0.7);
    color: transparent;

    background-image: url("/path/to/image");
    /* 测试background-clip的时候建议设置图片不重复显示 */
    background-repeat: no-repeat;
    background-clip: text;
}
```

#### background-size

设置背景图片的大小

```css
div {
    /* 写法一，用长度值指定，不允许负值 */
    background-size: 1280px 720px;

    /* 写法二，用元素宽高百分比指定，不允许负值 */
    background-size: 100% 100%;

    /* 写法三，auto，背景图片的真实大小，默认值 */
    background-size: auto;

    /* 写法四，contain，将背景图片等比例缩放，使背景图片的宽或高（取图片宽和高大者），与容器的宽或高相等 */
    /* 再将完整背景图片包含在容器内，但要注意，可能会造成容器里部分区域没有背景图片 */
    background-size: contain;

    /* 写法五，cover，将背景图片等比例缩放，直到完全覆盖容器，图片会尽可能全部显示在元素上 */
    /* 但要注意：背景图片可能显示不完整，这是相对比较好的选择 */
    background-size: cover;
}
```

#### background

复合属性，很少用

```css
div {
    /* background: 背景色 背景图 是否重复 位置 / 大小 原点 裁剪方式; */
    /* 原点和裁剪方式如果一样，可以只写一个值，如果写了两个值，前面的是原点，后面的是裁剪方式 */
    /* 位置必须写在大小前面，并且用/分开，并且位置可以用left、top、right、bottom关键字 */
    /* 背景色 背景图 是否重复这三者的顺序可以内部微调 */
    background: skyblue url("/path/to/image") no-repeat 10px 10px / 500px 500px border-box content-box;

    /* 多背景图 */
    background: url("/path/to/image") norepeat left top,
                url("/path/to/image") norepeat right top,
                url("/path/to/image") norepeat left bottom,
                url("/path/to/image") norepeat right bottom;
}
```

### 新增边框属性

#### 边框圆角

将盒子变为圆角

```css
div {
    width: 400px;
    height: 400px;
    border: 2px solid black;
    margin: 0 auto;

    /* 同时设置四个角的圆角，最常用 */
    /* 当值为正方形边长的一半时，变成圆 */
    border-radius: 200px;
    border-radius: 50%;

    /* 也可以单独设置每一个角 */
    border-top-left-radius: 10px;
    border-top-right-radius: 20px;
    border-bottom-left-radius: 30px;
    border-bottom-right-radius: 40px;

    /* 还可以单独设置每一个角为椭圆，分别是x半径，y半径 */
    border-top-left-radius: 10px 20px;
    border-top-right-radius: 20px 10px;
    border-bottom-left-radius: 30px 10px;
    border-bottom-right-radius: 40px 20px;

    /* 还有复合属性写法 */
    /* 先写左上角的x半径值，然后顺时针写其它方向的x半径值 */
    /* 后写左上角的y半径值，然后顺时针写其它方向的y半径值 */
    border-radius: 10px 20px 30px 40px / 20px 10px 10px 20px;
}
```

#### 边框外轮廓

想像成盒子发出的光，不参与计算盒子的大小，也不占位

outline-width，外轮廓宽度

outline-color，外轮廓颜色

- outline-style，外轮廓风格
    - none，无轮廓
    - dotted，点状轮廓
    - dashed，虚线轮廓
    - solid，实线轮廓
    - double，双实线轮廓

outline-offset，设置外轮廓与边框的距离，正负值都可以

注意outline-offset不是outline的子属性，是一个独立属性

```css
div {
    outline-width: 20px;
    outline-color: orange;
    outline-style: solid;

    /* 复合属性写法，顺序随意，注意没有outline-offset */
    outline: 20px orange solid;

    outline-offset: 30px;
}
```

### 新增文本属性

#### text-shadow

用来给文本添加阴影

```css
h1 {
    font-size: 80px;
    text-align: center;

    /* text-shadow: 水平偏移值 垂直偏移值 模糊距离 阴影颜色 */
    /* 水平偏移和垂直偏移为必填，可为负值 */
    text-shadow: none;
    text-shadow: 3px 3px;
    text-shadow: 3px 3px orange;
    text-shadow: 3px 3px 10px orange;
}
```

#### white-space

用来设置文本换行方式

- 值
    - normal，文本超出边界自动换行，文本中的换行被浏览器识别为一个空格（默认值）
    - pre，原样输出，与pre标签的效果相同
    - pre-wrap，在pre效果的基础上，超出元素边界自动换行
    - pre-line，在pre效果的基础上，超出元素边界自动换行，且只识别文本中的换行，（始末位置的）空格会被忽略
    - nowrap，强制不换行

```css
div {
    width: 400px;
    height: 400px;
    border: 1px solid black;
    font-size: 20px;

    white-space: pre;
}
```

#### text-overflow

设置文本内容溢出时的呈现方式

- 值
    - clip，当内容溢出时，将溢出部分裁剪掉（默认）
    - ellipsis，当内容溢出时，将溢出部分替换为英文省略号

要使text-overflow属性生效，必须显式定义overflow为非visible值，white-space为nowrap

```css
div {
    white-space: nowrap;
    overflow: hidden;
    text-overflow: ellipsis;
}
```

#### text-decoration

```css
h1 {
    font-size: 80px;

    /* 子属性 */
    /* solid，实线；double，双实线；dotted，点状线条；dashed，虚线；wavy，波浪线 */
    text-decoration-line: overline;
    text-decoration-style: dashed;
    text-decoration-color: blue;

    /* 复合属性 */
    text-decoration: overline dashed blue;
}
```

### 新增渐变

#### 线性渐变

多个颜色之间的渐变

```css
div {
    /* 从上到下渐变，默认，后面还可以补充其它颜色的，这里不写那么多 */
    background-image: linear-gradient(red, yellow, green);

    /* 用关键词设置渐变的方向 */
    /* 从左往右渐变 */
    background-image: linear-gradient(to right, red, yellow, green);
    /* 从左下到右上渐变 */
    background-image: linear-gradient(to right top, red, yellow, green);

    /* 用角度设置渐变的方向 */
    /* 用角度表示，旋转中心是元素中心点，沿元素水平中心线顺时针旋转的角度 */
    background-image: linear-gradient(20deg, red, yellow, green);

    /* 调整开始渐变的位置 */
    /* 从上到下渐变，0-50px高度时是纯红，50px-100px时又红渐变为黄 */
    /* 在100px-150px时由黄渐变为绿，150px-元素的高度值时是纯绿 */
    background-image: linear-gradient(red 50px, yellow 100px, green 150px);
}
```

#### 径向渐变

径就是半径

```css
div {
    /* 以（元素宽高组成的）矩形的中心向外渐变，根据元素宽高的比例最终效果是圆/椭圆形状的渐变（阳光普照），默认 */
    background-image: radial-gradient(red, yellow, green);

    /* 可以通过关键字调整圆心位置 */
    background-image: radial-gradient(at left top, red, yellow, green);

    /* 可以通过坐标值调整圆心位置 */
    background-image: radial-gradient(at 100px 50px, red, yellow, green);
    
    /* 可以通过circle关键字调整为正圆 */
    background-image: radial-gradient(circle, red, yellow, green);

    /* 可以调整两个半径值，当他们一样时就是正圆 */
    background-image: radial-gradient(100px 100px, red, yellow, green);

    /* 调整开始渐变的位置 */
    /* 从里到外渐变，0-50px半径时是纯红，50px-100px时又红渐变为黄 */
    /* 在100px-150px时由黄渐变为绿，150px-元素的半径值时是纯绿 */
    background-image: radial-gradient(red 50px, yellow 100px, green 150px);

    /* 综合写法 */
    /* at前面分别是长半径和短半径值，at后面是圆心的位置 */
    background-image: radial-gradient(100px 50px at 150px 150px, red 50px, yellow 100px, green 150px);
}
```

#### 重复渐变

```css
div {
    /* 重复线性渐变 */
    background-image: repeat-linear-gradient(red 50px, yellow 100px, green 150px);

    /* 重复径向渐变 */
    background-image: radial-gradient(red 50px, yellow 100px, green 150px);
}
```

- 渐变小案例1

```css
div {
    width: 600px;
    height: 800px;
    padding: 20px;
    border: 1px solid block;
    margin: 0 auto;
    background-image: repeat-linear-gradient(transparent 0px, transparent 29px, gray 30px);
    background-clip: content-box;
}
```

- 渐变小案例2

```css
div {
    width: 200px;
    height: 200px;
    border-radius: 50%;
    background-image: repeat-radial-gradient(at 80px 80px, white, #333);
}
```

### web字体

#### 基本用法

可以通过`@font-face`指定字体的具体地址，浏览器会自动下载该字体，这样就不依赖用户电脑上的字体了

```html
<head>
    <style>
        /* 普通写法 */
        @font-face {
            font-family: "customFontName1";
            src: url("/path/to/font.ttf");
        }
        /* 兼容性最好的写法 */
        @font-face {
            font-family: "customFontName2";
            font-display: swap;
            /* 各种浏览器兼容的字体格式 */
            /* IE9 */
            src: url("/path/to/font.eot");
            /* IE6-IE8 */
            src: url("/path/to/font.eot?#iefix") format("embedded-opentype"),
            /* chromium、firefox */
            url("/path/to/font.woff2") format("woff2"),
            url("/path/to/font.woff") format("woff"),
            /* chromium、firefox、opera、safari、Android、ios 4.2+ */
            url("/path/to/font.ttf") format("truetype"),
            /* ios 4.1- */
            url("/path/to/font.svg#webfont") format("svg");
        }
        h1 {
            /* 使用上面引入的字体 */
            font-family: "customFontName1";
            font-size: 80px;
        }
    </style>
</head>
```

#### 定制字体

由于字体文件很大，使用完整的字体文件不现实，通常针对某几个文字进行单独定制，生成各种浏览器兼容的格式的字体然后引入

可以使用web字体定制工具来定制字体，目前笔者没有去找，先省略

#### 图标字体

也就是矢量图标，把它看成是一种字体，如可以使用font-size来设置图标字体的大小

相比图片更加清晰

灵活性搞，更方便改变大小、颜色、风格等

兼容性好，IE也能支持

图标字体有各种平台的生成和使用方式，如阿里的，根据平台指南使用即可

### 2D变换

二维座标系如下图：

![coordinate](image/coordinate.png)

记住，只要是用transform这个属性，所有变换就都不能应用在行内元素

#### 位移

可以用位移改变元素的位置

- 属性：transform，值有
    - translateX
    - translateY
    - translate，写一个值代表水平方向，两个值代表水平和垂直方向

位移与相对定位很相似，都不脱离文档流，不会影响到其它元素

- 与相对定位的区别：
    - 相对定位的百分比值参考的是其父元素的宽高
    - 位移的百分比值参考的是其自身的宽高

浏览器针对位移有优化，与定位相比，浏览器处理位移的效率更高

位移对行内元素无效

```css
div {
    /* 水平位移 */
    transform: translateX(50px);
    /* 垂直位移 */
    transform: translateY(50px);
    /* 水平+垂直位移的两种写法 */
    transform: translate(50px, 50px);
    transform: translateX(50px) translateY(50px);

    /* 还可以用百分比，参考的是元素自身的宽高 */
    transform: translate(50%, 50%);
}
```

位移配合定位，可实现元素水平垂直居中

```css
.inner {
    position: absolute;
    left: 50%;
    top: 50%;
    transform: translate(-50%, -50%);
}
```

#### 缩放

让元素放大或缩小

```css
div {
    /* 水平缩放，1表示不缩放，小于1缩小，大于1放大，可以为负值，效果是镜像缩放，几乎不用负值 */
    transform: scaleX(1.5);
    /* 垂直缩放，1表示不缩放，小于1缩小，大于1放大，可以为负值，效果是镜像缩放，几乎不用负值 */
    transform: scaleY(0.5);
    /* 同时进行水平和垂直方向缩放，写一个值表示水平和垂直缩放的值，两个值分别表示水平缩放值和垂直缩放值 */
    transform: scale(1.5);
    transform: scaleX(1.5) scaleY(1.5);
}
```

借助缩放，可以实现小于浏览器支持的最小字体大小的文字

#### 旋转

让元素在二维平面内，顺时针或逆时针转（实际上就是绕着Z轴转）

```css
div {
    /* 正值顺时针，负值逆时针 */
    transform: rotateZ(30deg);
    /* rotate只写一个值的时候和rotateZ是一样的效果 */
    transform: rotate(30deg);
}
```

#### 扭曲

让元素在二维平面的水平或垂直方向或同时两个方向被拉扯，进而走形（如变成一个平行四边形）

几乎用不到

```css
div {
    /* 将元素的左上角、右下角分别往相反的水平方向拉扯，由于用得少，负值的拉扯就不记录了 */
    transform: skewX(30deg);
    /* 将元素的左上角、右下角分别往相反的垂直方向拉扯，由于用得少，负值的拉扯就不记录了 */
    transform: skewY(30deg);

    transform: skewX(30deg) skewY(30deg);
    transform: skew(30deg, 30deg);

    /* 只写一个值跟skewX(30deg)是一样的 */
    transform: skew(30deg);
}
```

#### 多重变换

注意多重变换时，建议旋转放最后

```css
div {
    /* 先位移后缩放 */
    transform: translate(100px, 100px) scale(1.5);

    /* 先缩放后位移，注意坐标系在元素缩放之前的那个位置 */
    transform: scale(1.5) translate(100px, 100px);

    /* 先位移后旋转 */
    transform: translate(100px, 100px) rotate(30deg);

    /* 先旋转后位移，注意坐标系在旋转之后变了 */
    transform: rotate(30deg) translate(100px, 100px);
}
```

#### 变换原点

元素进行变换操作时，默认的原点是元素的中心，可以使用`transform-origin`设置变换操作的原点

修改变换原点对位移没有影响，对旋转和缩放有影响

如果给`transform-origin`提供两个值，第一个用于横座标，第二个用于纵坐标

- 如果给`transform-origin`提供一个值
    - 如果这个值是像素值，表示横座标，纵座标取元素高度50%
    - 如果这个值是关键字，则另一个坐标取元素50%

```css
div {
    width: 200px;
    height: 200px;
    background-color: deepskyblue;

    /* 通过关键字调整变换原点 */
    transform-origin: left top;
    
    /* 通过坐标值调整变换原点 */
    transform-origin: 100px 100px;  

    /* 通过百分比调整变换原点 */
    transform-origin: 50% 50%;

    /* 只给一个值，另外一个值就是50% */
    transform-origin: 50%;
    transform-origin: top;

    /* 变换原点的位置对旋转有影响 */
    transform: rotate(30deg);    
    
    /* 变换原点的位置对缩放有影响 */
    transform: scale(0.5);

    /* 变换原点的位置对位移无影响 */
    transform: translate(100px, 100px);
}
```

### 3D变换

#### 3D空间与景深

元素进行3D变换的首要操作：父元素必须开启3D空间

- 使用`transform-style`开启3D空间，值有
    - flat，让子元素位于此元素的二维平面内（2D空间），默认值
    - preserve-3d，让元素位于此元素的三维空间内（3D空间）

景深：指定观察者与Z=0平面的距离，能让发生3D变换的元素，产生透视效果，看起来更加立体

- 使用`perspective`设置景深，也要设置到发生3D变换的元素的父元素，值有
    - none，不指定透视，默认值
    - 长度值，指定观察者与Z=0平面的距离，不允许负值

```html
<div class="outer">
    <div class="inner"></div>
</div>
```

```css
.outer {
    /* 开启3D空间 */
    transform-style: preserve-3d;
    /* 设置景深，如绕X轴旋转，可以先设置为元素的高的一半大一些，然后看效果，最终调大到合适的值 */
    perspective: 500px;
}
.inner {
    transform: rotateX(30deg);
}
```

#### 透视点（观察者）位置

透视点位置，就是观察者位置，默认的透视点在发生3D变换的元素的父元素的中心

通常情况下，不需要调整透视点的位置

一般只在一堆元素堆叠在一起不好观察的时候才会调整透视点的位置

```html
<div class="outer">
    <div class="inner"></div>
</div>
```

```css
.outer {
    /* 开启3D空间 */
    transform-style: preserve-3d;
    /* 设置景深，如绕X轴旋转，可以先设置为元素的高的一半大一些，然后看效果，最终调大到合适的值 */
    perspective: 500px;

    /* 设置透视点的位置，相对坐标轴往右偏移300px，往下偏移300px */
    /* 相当于人向右移动300像素，然后蹲下300像素看元素 */
    perspective-origin: 300px 300px;
}
.inner {
    transform: rotateX(30deg);
}
```

#### 3D位移

3D位移是在2D位移的基础上，可以让元素沿着Z轴位移

```css
.outer {
    /* 开启3D空间 */
    transform-style: preserve-3d;
    /* 设置景深，如绕X轴旋转，可以先设置为元素的高的一半大一些，然后看效果，最终调大到合适的值 */
    perspective: 500px;
}
.inner {
    /* 正值向屏幕外移动，负值像屏幕里移动，且不能写百分比，因为元素没厚度，不知道参考谁 */
    /* 默认透视点位置看起来的效果有点像放大缩小，因此可以设置一个透视点位置来查看效果 */
    transform: translateZ(200px);

    /* 必须三个坐标的位移值都必须写，分别表示x轴、y轴和z轴的位移值 */
    /* 并且z的值只能写像素值，不能写百分比，因为元素没厚度，不知道参考谁 */
    transform: translate3d(100px, 100px, 200px);
}
```

#### 3D旋转

3D旋转就是在2D旋转的基础上，可以让元素沿着x和y轴旋转

```css
div {
    /* 设置x轴旋转角度，面对x轴正方向，正值顺时针，负值逆时针 */
    transform: rotateX(30deg);
    /* 设置y轴旋转角度，面对y轴正方向，正值顺时针，负值逆时针 */
    transform: rotateY(30deg);

    /* 很少用，前三个参数分别代表x轴、y轴和z轴（1表示旋转，0表示不旋转），第四个参数表示旋转的角度，参数不能省略 */
    /* 表示x、y、z分别旋转30度 */
    transform: rotate3d(1, 1, 1, 30deg);
}
```

#### 3D缩放

3D缩放是在2D缩放的基础上，可以让元素沿着z轴缩放

实际上很少用

```css
div {
    /* 设置z轴方向的缩放比例，值为一个数字，小于1缩小，大于1放大，1不缩放 */
    /* 由于元素没有厚度，实际上是缩放景深 */
    /* 通过旋转才能看出效果 */
    transform: scaleZ(1.5) rotateY(30deg);

    /* 分别对应x、y、z轴的缩放值，参数不允许省略 */
    transform: scale3d(1.2, 1.5, 1.8) rotateY(30deg);
}
```

#### 3D多重变换

```css
div {
    /* 设置变换原点 */
    transform-origin: 200px 180px;
    transform: rotateX(30deg);

    /* 设置变换原点 */
    transform-origin: 200px 0px;
    transform: rotateY(30deg);

    /* 多重变换，建议旋转操作放最后 */
    transform: translateZ(100px) scaleZ(1.2) rotateY(30deg);
}
```

#### 背部可见性

```css
div {
    transform: rotateY(30deg);
    /* 设置为hidden，旋转超过90度后的背部就看不到了，然后继续转到270度之后（变成正面了）又能看到 */
    backface-visibility: hidden;
}
```

### 过渡

#### 基础用法

过渡可以在不使用flash动画，不使用js的情况下，让元素从一种样式，平滑过渡为另一种样式

- `transition-property`，定义哪个属性需要过渡，常用值
    - none，不定义任何过渡属性
    - all，所有能过渡的属性都过渡
    - 具体某个属性名，如：width、height，多个属性用英文逗号隔开

不是所有属性都能过渡，值为数字，或者值能转为数字的属性，都支持过渡，否则不支持过渡

常见的支持过渡的属性有：颜色、长度值、百分比、z-index、opacity、2D变换属性、3D变换属性、阴影

- `transition-duration`，设置完成过渡需要的时间，即一个状态过渡到另一个状态要耗时多久，常用值
    - 0，没有任何过渡时间，默认值
    - 数字+s/ms，指定秒数或毫秒数
    - 列表
        - 如果想让所有属性都持续同一个时间，就只写一个值
        - 如果想让每个属性持续不同的时间，就写一个时间的列表，多个时间用英文逗号隔开

```css
.box {
    width: 100px;
    height: 100px;
    background-color: orange;
    opacity: 0.5;

    /* 指定需要过渡效果的属性，多个属性用英文逗号隔开 */
    transition-property: width,height,background-color;

    /* 让所有能过渡的属性，都过渡 */
    transition-property: all;

    /* 分别设置过渡时间，多个时间用英文逗号隔开 */
    transition-duration: 1s,1s,1s;
    /* 只设置一个时间，所有过渡的属性都用它 */
    transition-duration: 1s;
}
.box:hover {
    width: 400px;
    height: 400px;
    background-color: green;
    transform: rotate(30deg);
    box-shadow: 0px 0px 10px black;
    opacity: 1;
}
```

#### 高级用法

`transition-delay`，设置开始过渡的延迟时间，单位是m或ms

- `transition-timing-function`，设置过渡的类型，常用值
    - ease，平滑过渡，默认值
    - linear，线性过渡
    - ease-in，慢->快
    - ease-out，快->慢
    - ease-in-out，慢->快->慢
    - step-start，等同于step(1,start)
    - step-end，等同于step(1,end)
    - step(整数,?)，接受两个参数的步进函数
        - 第一个参数必须为正整数，指定函数的步数
        - 第二个参数取值可以是start或end，指定每一步的值发生变化的时间点，默认值是end
    - cubic-bezie(数值,数值,数值,数值)，特定的贝塞尔曲线
        - 在线制作贝塞尔曲线：<https://cubic-bezier.com>，然后复制就行了

```css
.box {
    width: 100px;
    height: 100px;
    background-color: orange;

    /* 让所有能过渡的属性，都过渡 */
    transition-property: all;
    /* 只设置一个时间，所有过渡的属性都用它 */
    transition-duration: 1s;

    /* 过渡延迟 */
    transition-delay: 2s;

    /* 过渡类型 */
    transition-timing-function: ease;
}
```

#### 过渡复合属性

用得比较多

```css
.outer {
    width: 1000px;
    height: 100px;
    border: 1px solid black;
}
.inner {
    width: 100px;
    height: 100px;
    background-color: orange;
    /* 如果只设置一个时间，表示duration */
    /* 如果设置了两个时间，第一个是duration，第二个是delay */
    /* 所有值顺序随意 */
    transition: 2s 2s linear all;
}

.outer:hover .inner {
    width: 1000px;
}
```

### 动画

帧：一段动画，就是一段时间内连续播放n个画面，每一张画面，就是一帧

一定时间内连续快速播放若干个帧就成了人眼中所看到的动画

同样的时间内，播放的帧数越多，画面看起来越流畅

关键帧：在构成一段动画的若干帧中，起到决定性作用的2-3帧

#### 动画基本用法

- 先定义，后应用动画到元素
- from、to和百分比可以混着写，但不建议这么做

```html
<div class="outer">
    <div class="inner"></div>
</div>
```

```css
.outer {
    width: 1000px;
    height: 100px;
    border: 1px solid black;
}
.inner {
    width: 100px;
    height: 100px;
    background-color: deepskyblue;

    /* 应用动画到元素 */
    animation-name: myAnimation;
    /* 动画持续时间 */
    animation-duration: 3s;
    /* 动画延迟时间 */
    animation-duration: 0.5s;
}

/* 定义一个动画（实际上是定义一组关键帧），写法一 */
@keyframes myAnimation {
    /* 第一帧 */
    from {
        /* 第一帧不做什么的话可以不写 */
    }
    /* 最后一帧 */
    to {
        transform: translate(900px);
        /* 会渐变成最终的颜色 */
        background-color: red;
    }
}

/* 定义一个动画（实际上是定义一组关键帧），写法二 */
@keyframes myAnimation2 {
    /* 第一帧 */
    0% {
        /* 第一帧不做什么的话可以不写 */
    }
    /* 中间按需定义 */
    50% {
        background-color: green;
    }
    /* 最后一帧 */
    100% {
        transform: translate(900px);
        /* 会渐变成最终的颜色 */
        background-color: red;
    }
}
```

##### @keyframes

语法：

```css
/* 定义动画的名称 */
@keyframes animationName {
    /* 动画持续时间的百分比 */
    /* 可以是0-100%的任意值，或from (和0%相同) 或to (和100%相同) */
    from {
        /* 一个或多个合法的CSS样式，即 */
        /* 属性: 值; */
        background: linear-gradient(to right, #2196F3,#fdfdfd,#2196F3) repeat-x 0 0;
    }
    
    to {
        background: linear-gradient(to right, #2196F3,#fdfdfd,#2196F3) repeat-x 500px 0;
    }
}
```

#### 动画其它属性

- `animation-timing-function`，设置动画的类型，值
    - ease，平滑动画，默认值
    - linear，线性动画
    - ease-in，慢->快
    - ease-out，快->慢
    - ease-in-out，慢->快->慢
    - step-start，等同于`step(1,start)`
    - step-end，等同于`step(1,end)`
    - `step(整数,?)`，接受两个参数的步进函数
        - 第一个参数必须为正整数，指定函数的步数
        - 第二个参数取值可以是start或end，指定每一步的值发生变化的时间点，默认值是end
    - cubic-bezie(数值,数值,数值,数值)，特定的贝塞尔曲线
        - 在线制作贝塞尔曲线：<https://cubic-bezier.com>，然后复制就行了

- `animation-iteration-count`，设置动画的播放次数，值
    - 一个正整数，动画循环次数
    - infinite，无限循环

- `animation-direction`，设置动画的方向，值
    - normal，正常方向，默认
    - reverse，反方向运行
    - alternate，动画先正常运行再反方向运行，并持续交替
    - alternate-reverse，动画先反方向运行在正常运行，并持续交替

- `animation-fill-mode`，设置动画之外的状态（不发生动画的时候在哪里），值
    - forwards，设置对象状态为动画结束时的状态
    - backwards，设置对象状态为动画开始时的状态

- `animation-play-state`，设置动画的播放状态，值
    - running，运动，默认
    - paused，暂停

```css
.inner {
    width: 100px;
    height: 100px;
    background-color: deepskyblue;

    /* 应用动画到元素 */
    animation-name: myAnimation;
    /* 动画持续时间 */
    animation-duration: 3s;
    /* 动画延迟时间 */
    animation-duration: 0.5s;

    /* 其它属性 */
    /* 设置动画的类型 */
    animation-timing-function: linear;
    /* 动画播放的次数 */
    animation-iteration-count: 2;
    /* 动画的方向 */
    animation-direction: reverse;
    /* 动画以外的状态（不发生动画的时候在哪里） */
    animation-fill-mode: forwards;
}
.outer:hover .inner {
    /* 动画的播放状态 */
    animation-play-state: paused;
}
```

#### 动画复合属性

- 属性：animation，值
    - 只设置一个时间表示duration
    - 设置两个时间，分别表示duration和delay
    - 其它属性没有数量和顺序要求

```css
.inner {
    width: 100px;
    height: 100px;
    background-color: deepskyblue;

    animation: myAnimation 1s 0.5s linear infinite reverse forwards;
}
.outer:hover .inner {
    /* 暂停一般不用复合属性animation来写 */
    /* 而是单独使用animation-play-state */
    animation-play-state: paused;
}
```

#### 动画与过渡的区别

动画不需要任何的触发条件，过渡必须要有触发条件，如鼠标hover

动画从开始到结束的过程都可以精细地控制，而过渡只关注开始和结束，无法对开始到结束的过程进行控制

### 多列布局

专门用于实现类似于报纸的布局

```html
<div class="outer">
    <h1>新闻标题</h1>
    <p>新闻内容1</p>
    <img src="/path/to/image" alt="" />
    <p>新闻内容2</p>
</div>
```

```css
.outer {
    width: 1000px;
    margin: 0 auto;

    /* 直接指定列数 */
    column-count: 5;

    /* 指定每一列的宽度，浏览器自动计算列数 */
    column-width: 220px;

    /* 复合属性，浏览器根据列宽自动计算出来的列数和指定的列数做比较，谁小谁生效，不推荐使用 */
    column: 3 220px;

    /* 调整列间距 */
    column-gap: 20px;

    /* 每一列的边框宽度 */
    column-rule-width: 2px;
    /* 每一列的边框风格 */
    column-rule-style: dashed;
    /* 每一列的边框颜色 */
    column-rule-color: red;

    /* 边框复合属性 */
    column-rule: 2px dashed red;
}
h1 {
    /* 指定是否跨列，值：none、all，给要跨列的元素加上此属性 */
    /* 标题横跨多列 */
    column-span: all;
    text-align: center;
    font-size: 50px;
}
img {
    /* 图片所在那一列宽度的100% */
    width: 100%;

    transition: 0.2s linear;
}
img:hover {
    box-shadow: 0px 0px 20px black;
    transform: scale(1.02);
}
```

### 伸缩盒模型

Flexible Box：伸缩盒模型，又称弹性盒子

它可以轻松地控制：元素分布方式、元素对齐方式、元素视觉顺序（如写的时候是123顺序，呈现是按321顺序）……

截至目前，除了IE浏览器不支持，其它浏览器均已全部支持

伸缩盒模型的出现，逐渐演变出了一套新的布局方案——flex布局

传统布局是指：基于传统盒模型，主要靠：display属性+position属性+float属性实现的布局

flex布局目前在移动端应用比较广泛，因为传统布局不能很好地呈现在移动设备上

#### 伸缩容器和伸缩项目

伸缩容器：设置了`display: flex;`的元素，就是伸缩容器

- 给容器设置`display: inline-flex;`也能使该元素成为伸缩容器，但是很少用
    - 因为可以给伸缩容器的父元素也设置为伸缩容器
    - 还有就是两个设置了`display: inline-flex;`由于换行会出现缝隙

一个元素可以同时是伸缩容器和伸缩项目

- 伸缩项目：伸缩容器的所有子元素自动成为伸缩项目
    - 仅伸缩容器的子元素成为伸缩项目，孙子元素、重孙子元素等后代元素，不是伸缩项目
    - 无论元素原来是哪种元素（块、行内块、行内），一旦成为伸缩项目，全都会块状化（变成块元素）

```html
<div class="outer">
    <div class="inner">1</div>
    <div class="inner">2</div>
    <div class="inner">3</div>
</div>
```

```css
.outer {
    width: 1000px;
    height: 600px;
    background-color: #888;

    /* 将元素自身变为伸缩容器（或者说开启了flex布局），其直接子元素就自动变成了伸缩项目（item）了 */
    display: flex; 
}
.inner {
    width: 200px;
    height: 200px;
    background-color: skyblue;
    boder: 1px solid black;
    box-sizing: border-box;
}
```

#### 主轴方向

主轴：伸缩项目沿着主轴排列，主轴默认是水平的，默认方向是从左到右（左边是起点，右边是终点）

侧轴：与主轴垂直的就是侧轴，侧轴默认是垂直的，默认方向是从上到下（上边是起点，下边是终点）

- 调整主轴方向用`flex-direction`，值有
    - row，主轴方向水平从左到右，默认值
    - row-reverse，主轴方向水平从右到左
    - column，主轴方向垂直从上到下
    - column-reverse，主轴方向垂直从下到上

改变了主轴的方向，侧轴的方向也随之改变（始终跟主轴垂直）

```css
.outer {
    width: 1000px;
    height: 600px;
    background-color: #888;

    /* 将元素自身变为伸缩容器（或者说开启了flex布局），其直接子元素就自动变成了伸缩项目（item）了 */
    display: flex; 

    /* 调整主轴方向，水平从左到右，默认 */
    flex-direction: row;
}
```

#### 主轴换行方式

- 设置主轴换行方式用`flex-wrap`，值有
    - nowrap，不换行，伸缩项目全都挤在一行，默认值
    - wrap，自动换行，伸缩容器宽度不够了自动换行
    - wrap-reverse，反向换行
        - wrap是从伸缩容器左上角开始从左到右，然后第二行是下一行，继续从左到右……
        - wrap-reverse是从伸缩容器左下角开始从左到右，然后第二行是上一行，继续从左到右……

```css
.outer {
    width: 1000px;
    height: 600px;
    background-color: #888;

    /* 将元素自身变为伸缩容器（或者说开启了flex布局），其直接子元素就自动变成了伸缩项目（item）了 */
    display: flex; 

    /* 默认：nowrap，伸缩项目全都挤在一行 */
    flex-wrap: wrap;
}
```

#### 主轴复合属性

flex属性复合了flex-direction和flex-wrap

不建议使用

```css
.outer {
    width: 1000px;
    height: 600px;
    background-color: #888;

    /* 将元素自身变为伸缩容器（或者说开启了flex布局），其直接子元素就自动变成了伸缩项目（item）了 */
    display: flex; 

    /* 主轴复合属性，没有顺序要求 */
    flex: row wrap;
}
```

#### 主轴的对齐方式

- 使用`justify-content`属性设置主轴的对齐方式，值有
    - flex-start，伸缩项目整体对齐到主轴的起始位置，默认
    - flex-end，伸缩项目整体对齐到主轴的终点位置
    - center，伸缩项目整体居中对齐
    - space-between，伸缩项目均匀分布，两端对齐，最常用
    - space-around，伸缩项目均匀分布，两端距离是中间距离的一半
    - space-evenly，伸缩项目均匀分布，两端距离与中间距离一致

```css
.outer {
    width: 1000px;
    height: 600px;
    background-color: #888;

    /* 将元素自身变为伸缩容器（或者说开启了flex布局），其直接子元素就自动变成了伸缩项目（item）了 */
    display: flex; 

    /* 默认：nowrap，伸缩项目全都挤在一行 */
    flex-wrap: wrap;

    /* 主轴的对齐方式，对齐到主轴的起始位置，默认 */
    justify-content: flex-start;
}
```

#### 侧轴对齐

##### 伸缩项目只有一行

- 使用`align-items`属性设置伸缩项目只有一行的场景下的侧轴对齐，值有
    - flex-start，伸缩项目整体与侧轴的起始位置对齐
    - flex-end，伸缩项目整体与侧轴的终点位置对齐
    - center，伸缩项目整体与侧轴的中点对齐
    - baseline，伸缩项目的第一行文字的基线（x底部）对齐
    - stretch，如果伸缩项目未设置高度，将占满整个容器的高度，默认值

```css
.outer {
    width: 1000px;
    height: 600px;
    background-color: #888;

    /* 将元素自身变为伸缩容器（或者说开启了flex布局），其直接子元素就自动变成了伸缩项目（item）了 */
    display: flex; 

    /* 默认：nowrap，伸缩项目全都挤在一行 */
    flex-wrap: wrap;

    /* 主轴的对齐方式，对齐到主轴的起始位置，默认 */
    justify-content: flex-start;

    /* 侧轴的对齐方式（伸缩项目只有一行），伸缩项目与侧轴的起始位置对齐 */
    align-items: flex-start;
}
```

##### 伸缩项目有多行

- 使用`align-content`属性设置伸缩项目有多行的场景下的侧轴对齐，值有
    - flex-start，伸缩项目整体与侧轴的起始位置对齐
    - flex-end，伸缩项目整体与侧轴的终点位置对齐
    - center，伸缩项目整体与侧轴的中点对齐
    - space-between，伸缩项目与侧轴两端对齐，中间均匀分布
    - space-around，伸缩项目间的距离相等，比伸缩项目到侧轴边缘的距离大一倍
    - space-evenly，伸缩项目均匀分布，伸缩项目间的距离，与伸缩项目到侧轴边缘的距离一致
    - stretch，拉伸，如果伸缩项目未设置高度，占满整个侧轴，默认值

```css
.outer {
    width: 1000px;
    height: 600px;
    background-color: #888;

    /* 将元素自身变为伸缩容器（或者说开启了flex布局），其直接子元素就自动变成了伸缩项目（item）了 */
    display: flex; 

    /* 默认：nowrap，伸缩项目全都挤在一行 */
    flex-wrap: wrap;

    /* 主轴的对齐方式，对齐到主轴的起始位置，默认 */
    justify-content: flex-start;

    /* 侧轴的对齐方式（伸缩项目有多行），伸缩项目与侧轴的起始位置对齐 */
    align-content: flex-start;
}
```

#### 实现元素水平垂直居中

```css
.outer {
    width: 400px;
    height: 400px;
    background-color: #888;

    display: flex; 

    /* 写法一 */
    /* justify-content: center;
    align-items: center; */
}
.inner {
    width: 100px;
    height: 100px;
    background-color: orange;

    /* 写法二 */
    margin: auto;
}
```

#### 伸缩项目在主轴上的基准长度

设置伸缩项目在主轴上的基准长度用`flex-basis`

```html
<div class="outer">
    <div class="inner">1</div>
    <div class="inner inner2">2</div>
    <div class="inner">3</div>
</div>
```

```css
.outer {
    width: 1000px;
    height: 600px;
    background-color: #888;

    /* 将元素自身变为伸缩容器（或者说开启了flex布局），其直接子元素就自动变成了伸缩项目（item）了 */
    display: flex; 

    /* 默认：nowrap，伸缩项目全都挤在一行 */
    flex-wrap: wrap;

    /* 主轴的对齐方式，对齐到主轴的起始位置，默认 */
    justify-content: flex-start;
}
.inner {
    width: 200px;
    height: 200px;
    background-color: skyblue;
    border: 1px solid black;
    box-sizing: border-box;
}
.inner2 {
    /* 设置伸缩项目在主轴上的基准长度 */
    /* 若主轴是横向的，给伸缩项目设置的width的值将失效 */
    /* 若主轴是纵向的，给伸缩项目设置的height的值将失效 */
    /* 默认是auto，原本给伸缩项目设置的宽或高是多少就是多少 */
    /* 浏览器会根据这个值来计算主轴上是否还有富余的空间 */
    /* 一般不会去设置它 */
    flex-basis: 300px;
}
```

#### 伸缩项目-拉伸

`flex-grow`用来定义伸缩项目的放大比例，默认为0，即：纵使主轴存在剩余空间，也不拉伸

- 规则
    - 若所有伸缩项目的`flex-grow`值都是1，则它们将等分剩余空间（如果有剩余空间）
    - 若总共三个伸缩项目，`flex-grow`的值分别是1、2和3，则，分别瓜分到1/6、2/6和3/6的剩余空间

```html
<div class="outer">
    <div class="inner">1</div>
    <div class="inner inner2">2</div>
    <div class="inner">3</div>
</div>
```

```css
.outer {
    width: 1000px;
    height: 600px;
    background-color: #888;

    /* 将元素自身变为伸缩容器（或者说开启了flex布局），其直接子元素就自动变成了伸缩项目（item）了 */
    display: flex; 

    /* 默认：nowrap，伸缩项目全都挤在一行 */
    flex-wrap: wrap;

    /* 主轴的对齐方式，对齐到主轴的起始位置，默认 */
    justify-content: flex-start;
}
.inner {
    width: 200px;
    height: 200px;
    background-color: skyblue;
    border: 1px solid black;
    box-sizing: border-box;

    /* 拉伸比例 */
    flex-grow: 1;
}
.inner2 {
    width: 300px;
}
```

#### 伸缩项目-收缩

`flex-shrink`用来定义伸缩项目的收缩比例，默认为1，即：如果父元素空间不足，伸缩项目将会缩小

收缩也会有个极限，就是保证伸缩项目里面的内容可见

```html
<div class="outer">
    <div class="inner">1</div>
    <div class="inner inner2">2</div>
    <div class="inner">3</div>
</div>
```

```css
.outer {
    width: 400px;
    height: 600px;
    background-color: #888;

    /* 将元素自身变为伸缩容器（或者说开启了flex布局），其直接子元素就自动变成了伸缩项目（item）了 */
    display: flex; 

    /* 默认：nowrap，伸缩项目全都挤在一行 */
    /* flex-wrap: wrap; */

    /* 主轴的对齐方式，对齐到主轴的起始位置，默认 */
    justify-content: flex-start;
}
.inner {
    width: 200px;
    height: 200px;
    background-color: skyblue;
    border: 1px solid black;
    box-sizing: border-box;

    flex-grow: 1; 
    
    /* 收缩比例，默认值就是1 */
    /* flex-shrink: 1;  */
}
.inner1 {
    flex-shrink: 1;
}
.inner2 {
    width: 300px;
    flex-shrink: 2;
}
.inner3 {
    flex-shrink: 3;
}
```

inner1、inner2、inner3宽度分别是200、300、200像素，总占宽度700px，但outer宽度为400像素

因为设置了不换行，因此自动收缩，总收缩值700-400=300

分母：`200*1 + 300*2 + 200*3` = 1400

- 收缩比：
    - inner1收缩比=(200*1)/1400
    - inner2收缩比=(300*2)/1400
    - inner3收缩比=(200*3)/1400

- 收缩值：
    - inner1收缩比*总收缩值
    - inner2收缩比*总收缩值
    - inner3收缩比*总收缩值

- 收缩后宽度：
    - inner1=inner1宽度-inner1收缩值
    - inner2=inner2宽度-inner2收缩值
    - inner3=inner3宽度-inner3收缩值

#### flex复合属性

flex属性复合了：flex-grow flex-shrink flex-basis

```css
.inner {
    width: 200px;
    height: 200px;
    background-color: skyblue;
    border: 1px solid black;
    box-sizing: border-box;

    /* 可以拉伸 */
    flex-grow: 1; 
    
    /* 可以收缩 */
    flex-shrink: 2; 

    /* 基准长度 */
    flex-basis: 100px;

    /* 这三个属性的复合写法，顺序和个数都有要求 */
    flex: 1 2 100px;

    /* 可以拉伸，可以收缩，不设置基准长度，可以简写为：flex: auto; */
    flex: 1 1 auto;

    /* 可以拉伸，可以收缩，基准长度设置为0，这个用得比较多，可以简写为：flex: 1; */
    flex: 1 1 0;

    /* 不可拉伸，不可收缩，不设置基准长度，可以简写为：flex: none; */
    flex: 0 0 auto;

    /* 不可拉伸，可以收缩，不设置基准长度，默认值，可以简写为：flex: 0 auto; */
    flex: 0 1 auto;
}
```

#### 伸缩项目排序

order属性定义伸缩项目的排列顺序，数值越小，排列越靠前，默认是0

```css
.inner {
    width: 200px;
    height: 200px;
    background-color: skyblue;
    border: 1px solid black;
    box-sizing: border-box;

    /* 可以拉伸，可以收缩，基准长度设置为0*/
    flex: 1 1 0;
}
.inner2 {
    /* order默认值是0，值小的排前面显示 */
    order: -1;
}
.inner3 {
    order: -2;
}
```

#### 伸缩项目单独对齐

`align-self`可以单独调整某个伸缩项目的对齐方式，默认是auto，表示继承父元素的align-items属性

用得不多

```css
.inner {
    width: 200px;
    height: 200px;
    background-color: skyblue;
    border: 1px solid black;
    box-sizing: border-box;

    /* 可以拉伸，可以收缩，基准长度设置为0*/
    flex: 1 1 0;
}
.inner2 {
    /* 元素与侧轴单独对齐 */
    align-self: center;
}
```

### 响应式布局

#### 媒体查询

媒体查询不会提升优先级（还是遵循后写覆盖先写），因此要先写普通样式，再写媒体查询样式

##### 媒体类型

- 媒体类型
    - all，检测所有设备
    - screen，检测电子屏幕，包括电脑屏幕、平板屏幕、手机屏幕等
    - print，检测打印机

```css
h1 {
    background-image: linear-gradient(30deg, red, yellow, green);
}
/* 只有在打印机或打印预览才应用的样式 */
@media print {
    h1 {
        background: transparent;
    }
}

/* 只有在屏幕上才应用的样式 */
@media screen {
    h1 {
        font-family: "宋体";
    }
}

/* 一直都应用的样式，这么写没什么意义 */
@media all {
    h1 {
        color: red;
    }
}
```

##### 媒体特性

|值|描述|
|:-|:-|
|width|检测视口宽度|
|min-width|检测视口最小宽度|
|max-width|检测视口最大宽度|
|height|检测视口高度|
|min-height|检测视口最小高度|
|max-height|检测视口最大高度|
|device-width|检测设备屏幕宽度|
|min-device-width|检测设备屏幕最小宽度|
|max-device-width|检测设备屏幕最大宽度|
|orientation|检测视口的旋转方向（是否横屏）<br>1.portrait，视口处于纵向，即高度大于等于宽度<br>2.landscape，视口处于横向，即宽度大于高度|

```css
/* 检测到视口的宽度为800px时，应用的样式 */
@media (width:800px) {}

/* 检测到视口的宽度大于等于800px时，应用的样式 */
@media (min-width:800px) {}

/* 检测到视口的宽度小于等于800px时，应用的样式 */
@media (max-width:800px) {}
```

##### 媒体查询运算符

```css
/* 与运算符，检测到视口的宽度为800px-900px时，应用的样式 */
@media (min-width:800px) and (max-width:900px) {}

@media screen and (min-width:800px) and (max-width:900px) {}

/* 或运算符，检测到视口的宽度大于等于900px，或小于等于800px，新语法用or */
@media screen and (min-width:900px),(max-width:800px) {}
@media screen and (min-width:900px) or (max-width:800px) {}

/* 否定运算符 */
@media not screen {}


/* 肯定运算符，加与不加这个运算符都一样 */
/* 主要用于浏览器兼容，ie不认识only，会把整个样式忽略 */
@media only screen {}
```

#### 常用屏幕宽度阈值

- 超小屏幕：小于768px
- 中等屏幕：768px~992px
- 大屏幕：992px~1200px
- 超大屏幕：大于1200px

```css
/* 超小屏幕 */
@media screen and (max-width:768px) {}

/* 中等屏幕 */
@media screen and (min-width:768px) and (max-width:992px) {}

/* 大屏幕 */
@media screen and (min-width:992px) and (max-width:1200px) {}

/* 超大屏幕 */
@media screen and (min-width:1200px) {}
```

```html
<head>
    <!-- 先引入普通样式，再引入各个屏幕宽度的样式 -->
    <link rel="stylesheet" href="/path/to/index.css">
    <link rel="stylesheet" href="/path/to/small.css">
    <link rel="stylesheet" href="/path/to/medium.css">
    <link rel="stylesheet" href="/path/to/large.css">
    <link rel="stylesheet" href="/path/to/huge.css">
    <!-- 如果不在屏幕宽度样式文件里面写媒体查询，还可以这在这里写 -->
    <link rel="stylesheet" media="screen and (min-width:1200px)" href="/path/to/huge.css">
</head>
```

### BFC

BFC：Block Formatting Context，块级格式上下文，可以理解成元素的一个特异功能

- 当元素满足某些条件后，特异功能被激活（即该元素创建了BFC，又称开启了BFC）
    - 其子元素不会再产生margin塌陷问题
    - 自己不会被其它浮动元素所覆盖
    - 就算其子元素浮动，元素自身高度也不会塌陷

- 想要给元素开启BFC，就让它变成下面的元素就行了
    - 根元素
    - 浮动元素
    - 绝对定位、固定定位的元素
    - 行内块元素（也可以设置`display: inline-block;`）
    - 表格单元格：table、thead、tbody、tfoot、th、td、tr、caption
        - 如果不想写在表格里面，可以用`display: table;`
    - overflow的值不为visible的块元素
    - 伸缩项目
    - 多列容器，即设置`column-count`属性，如`column-count: 1;`
    - column-span为all的元素（即使该元素没有包裹在多列容器中）
    - display属性的值设置为flow-root的元素
        - 这种方式是副作用最小的，缺点是ie浏览器不支持（即约等于没有副作用哈哈）

不要疯狂追求开启BFC，平时怎么写就怎么写，当发生诡异问题的时候再想想开启BFC的事

### calc函数

用于动态计算尺寸

- 语法：calc(expression)
    - 运算符（ "+", "-", "*", "/" ）前后都需要保留一个空格

|值|描述|
|:-|:-|
|expression|必须，一个数学表达式，结果将采用运算后的返回值|

```css
div {
    width: calc(100vw - 20px);
    height: calc(100vh - 20px);
}
```

## JavaScript

JavaScript是一种运行在客户端（浏览器）的编程语言，实现人机交互效果

- JavaScript作用
    - 网页特效（监听用户的一些行为让网页做出对应的反馈）
    - 表单验证（针对表单数据的合法性进行判断）
    - 数据交互（获取后台数据，渲染到前端）
    - 服务端编程（node.js）

- JavaScript组成
    - ECMAScript语言基础
        - 规定了JavaScript基础语法核心知识
    - Web APIs
        - DOM，页面文档对象模型，可以操作页面文档，如对页面元素进行操作
        - BOM，浏览器对象模型，操作浏览器，如页面弹窗、检测窗口宽度、存储数据到浏览器等

### JavaScript书写位置

- 跟CSS类似，JavaScript也有三种书写位置
    - 行内JavaScript
    - 内部JavaScript
    - 外部JavaScript

#### 行内/内联JavaScript

基本不用这种写法

```html
<body>
    <button onclick="alert('你点我了')">点我</button>
</body>
```

#### 内部JavaScript

理论上`<script></script>`可以放在html的任何位置

由于浏览器会按照从上到下的顺序加载html内容

一般将`<script></script>`放在html底部附近

这样避免了：如果先加载的js期望修改html，由于html尚未被加载而失效

```html
<body>
    <!-- 其它html元素 -->
    <script>
        // 在这里写js
    </script>
</body>
```

#### 外部JavaScript

```html
<body>
    <!-- 其它html元素 -->
    <!-- 在这里导入js -->
    <script src="/path/to/file.js">
        // 外部js中间不要写代码，会被忽略
    </script>
</body>
```

### 注释

```js
// 单行注释

/* 多行注释 */
```

### 结束符

英文分号作为js语句的结束符

实际开发中可以不写，浏览器可以自动推断语句的结束位置

为了风格统一，结束符要么每句都写，要么每句都不写

```js
var x = 2;
```

### 输入语法

```js
// 显示一个对话框，对话框中包含一条文字信息，用来提示用户输入文字
prompt("hint");
```

### 输出语法

```js
// 向body输出内容，如果内容是标签，会被解析成网页元素
document.write("content");
document.write("<h1>title</h1>");

// 向页面弹出警告对话框
alert("content");

// 向控制台输出内容，一般调试使用
console.log("content");
```

### JavaScript代码执行顺序

按html文档流顺序执行JavaScript代码

alert和prompt会跳过页面渲染先被执行

### 变量

变量是存储数据的容器

```js
// 想要使用变量，首先需要创建变量（也称为声明/定义变量）
// 声明多个变量用英文逗号隔开，但一般不这么用
// let：允许、让
let name;

// 赋值
name = "handle";

// 使用变量
alert(name);

// 声明并赋值（变量初始化）
let name = "handle";

// 更新变量
name = "Handle";
name = prompt("hint");
```

- let和var
    - var可以先使用，再声明（不合理）
    - var声明过的变量可以重复声明（不合理）
    - var是较旧的js代码使用的，现在不推荐使用了
    - let声明过的变量不允许重复声明
    - let是为了解决var的一些问题而出现的

### 常量

声明变量优先使用const，其次用let，不要用var

数组和对象建议用const声明

```js
// 常量不允许重新赋值，声明的时候必须赋值
const PI = 3.14;
```

### 数据类型

- 基本数据类型，放到栈空间
    - number，数字类型
        - 有一个特殊值`NaN`，即非数字，是不正确或未定义的数学操作所得到的结果
        - 任何对`NaN`的操作都会返回`NaN`
    - string，字符串类型
    - boolean，布尔类型，只有两个字面量
        - true
        - false
    - undefined，未定义类型，只有一个字面量：undefined
        - 只声明变量，不赋值的情况下，变量的默认值就是undefined
    - null，空类型，null表示赋值了，但是内容为空
        - 官方解释：把null作为尚未创建的对象，即如果对象还没创建好，可以先给变量赋值为null
- 引用数据类型，放到堆空间
    - 通过new关键字创建的对象

```js
let name;
// undefined
console.log(name);

// NaN
console.log(undefined + 1);

let name = null;
// null
console.log(name);

// 1
console.log(null + 1);
```

- 引用数据类型：object对象

JavaScript是弱数据类型，变量到底属于哪种数据类型只有赋值后才能确认

#### 数字

整数、小数、正数、负数

```js
123;

3.14;
```

##### 运算符

- 算术运算符：`+ - * / %`
- 赋值运算符：`=、+=、-=、*=、/= %=`
- 自增运算符：`++、--`
- 比较运算符：`>、<、>=、<=、==、===、!=、!==`
    - `==`：左右两边值相等
    - `===`：左右两边类型和值都相等，最推荐使用
    - `!==`：左右两边类型和值不全等
- 逻辑运算符：`&&、||、!`

```js
// 赋值运算符：=
let i = 3;

// JavaScript不区分整数和小数，可以一起使用而无需转换
10+3.14;
```

###### 逻辑中断

也就是短路

```js
// false，第一个是false，输出第一个假值，后面的看都不看了
console.log(false && 2);

// 都是真，返回最后一个真值
console.log(true && 2);

// true，第一个是true，输出第一个真值，后面的看都不看了
console.log(true || 0);

// 都是假，输出最后一个假值
console.log(false || 0);
```

#### 字符串

双引号、单引号、反引号（``）都可以包裹字符串

```js
// 可以用双引号包裹
"hello world";

// 也可以用单引号包裹
'hello world';

// 字符串里面有单引号时，用双引号包裹字符串
"It's javascript";

// 字符串里面有双引号时，用单引号包裹字符串
'say "hello world"';

// 也可以用反斜杠\进行转义
'It\'s javascript';

"say \"hello world\"";

// 字符串长度
'hello'.length

// 字符串转小写
'Hello World!'.toLowerCase();

// 字符串转大写
'hello'.toUpperCase();

// 去掉字符串开始和末尾的空格
" hello world ".trim();

// 字符串拼接：hello
"he" + "llo";
```

##### 模板字符串

```js
let name = "handle";
// 模板字符串：用反引号包裹字符串，变量名用${}包裹
let stringTemplate = `hello ${name}`;
console.log(stringTemplate);
```

#### 布尔

```js
let isGood = false;
isGood = true;

// 其它类型转换为布尔类型
// ""、0、undefined、null、false、NaN转为布尔类型后都是false，其余都是true
let b = Boolean("handle")
```

#### 数组

```js
let array = ["a", "b"];

// 数组的元素的类型可以不是相同类型
let array2 = ["a", 1, array, false];

// 使用构造方法定义一个数组
let array3 = new Array("a", "b");

// 访问数组元素
array[0];
array[array.length -1];

// 修改数组元素的值
array[0]="A";

// 数组长度
array.length;

// 在数组末尾添加一个或多个元素并返回数组新长度
array.push("c", "d");

// 在数组开头添加一个或多个元素并返回数组新长度
array.unshift("c", "d");

// 删除数组最后一个元素并返回删除的元素
array.pop();

// 删除数组第一个元素并返回删除的元素
array.shift();

// 删除数组指定位置的元素并返回删除的元素
array.splice(起始位置索引，删除几个元素);

// 起始位置到数组末尾的元素都删除并返回删除的元素
array.splice(起始位置索引);

// 将数组元素倒序
array.reverse();

// 拼接数组
let array = ["a", "b"];
let array2 = ["c", "d"];
// [ "a", "b", "c", "d" ]
// 返回一个新的数组，array数组不变
array.concat(array2);
```

#### 对象（object）

object可以理解为一种无序的数据集合

数组是有序的数据集合

```js
// 定义对象1
let objectName1 = {
    属性名1: 属性值1,
    属性名2: 属性值2,
    方法名1: 函数1,
    方法名2: 函数2
};

// 定义对象2
let objectName2 = new Object();

let pet = {
    // 定义属性
    name: "dog",
    age: 2,
    // 定义方法
    toString: function() {
        return this.name + " " + this.age;
    }
};

// 访问对象属性
pet.name;
pet["name"];

// 修改对象属性
pet.name = "cat";
pet["name"] = "cat";

// 增加新属性：对象名.新属性名=属性值
pet.weight=0.5

// 删除属性：delete 对象名.属性名
delete pet.weight

// 方法调用：对象名.方法名(实参列表)
pet.toString()
```

##### 内置对象

###### Math

- 提供的方法有
    - random，生成`[0-1)`之间的随机数
    - ceil，向上取整
    - floor，向下取整
    - max
    - min
    - pow，幂运算
    - abs，绝对值
    - round，返回四舍五入后最接近的整数，有点说法，用的时候要注意

```js
console.log(Math.PI);

// 生成[0, n]之间的随机数
Math.floor(Math.random() * (n + 1));

// 生成[n, m]之间的随机数
Math.floor(Math.random() * (m - n + 1)) + n;
```

#### json

```js
// 定义一个json格式的字符串
let json = '{"name": "zhangsan","age": 18,"isMale": true,"pets": ["dog", "cat"],"son": {"name": "xiaoming"}}'

// json->object
let object = JSON.parse(json)

// object->json
let json2 = JSON.stringify(object)
```

#### 检测数据类型

```js
let name = "handle";
// 写法一
console.log(typeof name);
// 写法二
console.log(typeof(name));
```

#### 类型转换

使用表单、prompt获取的数据默认是字符串类型的，如果获取到的是是数字，就不能直接进行算术运算，需要先转换数据类型

##### 隐式转换

有两个操作数时，`+`两边只要有一个是字符串，都会把另一个转成字符串，即任何数据类型和字符串相加结果都是字符串

除了`+`以外的算术运算符，都会把数据转成数字类型

```js
// 特殊情况：作为正数符号解析，最终转成数字类型
console.log(+"1");

// ""和null经过数字转换后为0
console.log(-"");
console.log(-null);

// undefined经过数字转换后为NaN
console.log(-undefined);

// true
console.log(null == undefined);

// false
console.log(null === undefined) 
```

##### 显式转换

```js
let s = '1';
// 转换为数字类型1
console.log(Number(s));

// 转换为数字类型2
let n = +s;

// 只保留整数
// 1
parseInt(1.2)
parseInt("1.2")
parseInt("1.2b")

// 可保留小数
// 1.2
parseInt(1.2)
parseInt("1.2")
parseInt("1.2b")
```

### 条件

#### if语句

- 表达式：表达式是可以被求值的代码，可以写在赋值语句的右侧

语句：语句是一段可以执行的代码，语句不一定有值，如alert()，不能被用于赋值

```js
if(n > 1) {

} else if(n < 1) {

} else {
    
}
```

#### 三元运算符

```js
let max = a > b ? a : b;
```

#### switch语句

switch做的是全等判断

```js
switch(key) {
    case value1:
        code1;
        break;
    case value2:
        code2;
        break;
    default:
        coden;
        break;
}
```

#### while循环

```js
while(condition) {
    // do something
}
```

#### 退出循环

```js
while(condition) {
    if(condition2) {
        // 退出循环体
        break;
    }
    if(condition3) {
        // 结束本次循环，继续下次循环
        continue;
    }
    // another todo
}
```

#### for循环

```js
// 写法1
for(let i = 0; i < 10; i++) {
    // do something
}

// 写法2
// 不推荐遍历数组
let array = ["a", "b", "c"];
for (let i in array) {
    // i是字符串类型的数组的下标
    // array[i]会发生隐式类型转换
    console.log(array[i]);
}

// 推荐遍历对象
let pet = {};
for (let field in pet) {
    // field是对象的属性名
    console.log(pet[field]);
}
```

### 方法

两个相同的方法，后写的会覆盖先写的

- 实参个数和形参个数可以不一致
    - 实参少于形参，没传实参的形参会自动设置undefined
    - 实参多于形参，多余的实参会被忽略

```js
// 定义无参方法
function print() {
    return "hello world";
}

// 定义有参方法
function sum(x, y) {
    return x + y;
}

// 使用无参方法
print();

// 使用有参方法
let n = sum(1, 2);
```

#### 参数默认值

参数默认值是undefined

```js
// 设置参数默认值1
function sum(x=0, y=0) {
    return x + y;
}

// 设置参数默认值2
function sum(x, y) {
    x = x || 0;
    y = y || 0;
    return x + y;
}
```

#### 方法返回值

方法可以没有返回值

当方法没有return语句时，默认返回值是undefined

当方法return没有指定一个值时，默认返回值是undefined

```js
function fun() {
    // 默认返回值是undefined
    return;
}

function fun() {
    // 可以通过返回一个数组来返回多个值
    return [value1, value2];
}
```

#### 作用域

作用域分全局作用域和局部作用域

相应地，变量分为全局变量和局部变量

如果方法内部，变量没有用关键字声明，直接赋值，会变成全局变量

```js
function fun() {
    // 为了规范不要这么声明变量，必须加let或const关键字
    n = 1;
}

// 要先执行方法
fun()

// 然后发现能输出局部变量n
console.log(n);
```

#### 匿名函数

```js
// 具名函数
function fun() {}

// 匿名函数
function() {}
```

- 使用方式
    - 函数表达式：将匿名函数复制给一个变量，并通过变量名称进行调用，作用域遵循变量的作用域规则，必须先声明后调用
    - 立即执行匿名函数，避免全局变量之间的污染

```js
// 函数表达式方式
let fun = function() {}

console.log(fn());

// 立即执行方式1，多个立即执行的匿名函数之间注意加分号
(function(){})();

// 立即执行方式2，多个立即执行的匿名函数之间注意加分号
(function(){}());
```

### Web API

作用：使用js操作html和浏览器

分类：DOM（文档对象模型）、BOM（浏览器对象模型）

#### DOM

DOM是浏览器提供的一套专门用来操作网页内容的API

可以用来开发网页内容特效和实现用户交互功能

DOM树：将html文档以树状结构直观地表现出来，标签与标签的关系一目了然

- DOM对象：浏览器根据html标签生成的js对象
    - 所有的标签属性都可以在这个对象上找到
    - 修改这个对象的属性会自动映射到标签身上

- document对象
    - 是DOM提供的一个对象
    - 它提供的属性和方法都是用来访问和操作网页内容的
    - 网页所有内容都在document里面

##### 获取DOM元素

- 方式
    - 根据css选择器来获取DOM元素
    - 其它方式（了解）

```js
// 获取匹配的第一个元素，如果没有匹配到，返回null
const element = document.querySelector("css选择器");

const element = document.querySelector("div");
// 类选择器要写点号
const element = document.querySelector(".myClass");
// id选择器要写井号
const element = document.querySelector("#elementId");
const element = document.querySelector("ul li:first-child");

// 获取匹配的多个元素
// 得到的是一个伪数组，没有push和pop等数组方法
const elements = document.querySelectorAll("css选择器");

const elements = document.querySelector("ul li");

// 根据元素id获取一个元素，不用写井号
const element = document.getElementById("elementId");

// 根据标签名称获取一类元素
const element = document.getElementsByTagName("div");

// 根据类名获取一类元素
const element = document.getElementsByClassName("box");
```

##### 操作DOM元素内容

- 通过元素innerText属性：只识别文本，不解析标签

```html
<body>
    <div class="box">文本内容</div>
    <script>
        // 类选择器要写点号
        const element = document.querySelector(".box");
        // 获取元素文本内容
        console.log(element.innerText)
        // 修改元素文本内容
        element.innerText = "新文本内容";
        // 不会解析标签
        element.innerText = "<strong>新文本内容</strong>";
    </script>
</body>
```

- 通过元素innerHTML属性：识别文本，解析标签

```html
<body>
    <div class="box">文本内容</div>
    <script>
        // 类选择器要写点号
        const element = document.querySelector(".box");
        // 获取元素文本内容
        console.log(element.innerHTML)
        // 修改元素文本内容
        element.innerHTML = "新文本内容";
        // 会解析标签，多标签建议使用模板字符串
        element.innerHTML = "<strong>新文本内容</strong>";
    </script>
</body>
```

- 如果纠结innerText还是innerHTML，就用innerHTML

##### 操作DOM元素属性

###### DOM元素常用属性

```html
<body>
    <img src="" title="" />
    <script>
        // 获取img元素
        const element = document.querySelector("img");
        // 操作img元素常用属性
        element.src = "/path/to/image";
        element.title = "我的自拍";
    </script>
</body>
```

###### DOM元素样式属性

- 通过style属性修改样式
    - 适用于修改的样式比较少的场景

```html
<body>
    <div class="box">文本内容</div>
    <script>
        // 类选择器要写点号
        const element = document.querySelector(".box");
        element.style.width = "200px";
        element.style.height = "200px";
        // css中带减号的属性名用小驼峰
        element.style.backgroundColor = "white";
        element.style.paddingLeft = "8px";
        element.style.border = "1px solid red";
    </script>
</body>
```

- 通过类名修改样式
    - 适用于修改的样式比较多的场景
    - 会直接顶掉旧的类名

```html
<body>
    <div>文本内容</div>
    <script>
        const element = document.querySelector("div");
        // 设置css类名，不用加点号
        element.className = "box";
    </script>
</body>
```

- 通过classList修改样式
    - 解决了className会直接顶掉旧的类名的问题

```html
<body>
    <div>文本内容</div>
    <script>
        const element = document.querySelector("div");
        // 追加css类名，不用加点号
        element.classList.add("box");
        // 删除css类名，不用加点号
        element.classList.remove("box");
        // 切换css类名，有就删掉，没有就加上，不用加点号
        element.classList.toggle("box");
    </script>
</body>
```

###### DOM表单元素属性

###### DOM元素自定义属性

#### BOM

## JSP

1) 输出<%:在文本中写<\%

2) 使用<%--......--%>注释，在浏览器查看/源文档菜单中看不到

3) 使用<!--......-->注释，在浏览器查看/源文档菜单中看得到

4) request、response:每次请求新页面，就会产生新的request、response对象

5) session:打开浏览器，首次访问服务目录的某个JSP页面时创立，到关闭浏览器或session对象达到最大生存时间时，session对象才被取消

6) application:所有客户共享，服务器启动产生，直到服务器关闭，application对象才被取消

- 居中
  table居中：margin:0 auto;或align:center;

- JSP页面控件 click事件写法
  1）控件属性添加 onclick="javascript:方法名()"
  2）控件`<head></head>`中添加方法体：

```html
<script type="text/javascript">
    function fun() {
        // 方法体
    }
</script>
```

3）在script 方法中显示确定取消弹窗

```javascript
if(window.confirm("是否确定撤销？")) {

}
```

- ajax 只调用方法不传参写法

```javascript
$.ajax({
    url:"<%=basePath%>ClearDownload.action" 
});
```

- 表单提交方式：

```java
// 1
document.czbListform.action="${pageContext.request.contextPath }/revokePregnantRecord.action?index="+index;//这种情况下传参为字符型可能在后台收到乱七八糟的符号，最好只传数值型参数
document.czbListform.submit();

// 2
//这里不传参，但是相应控件全部放在提交的form标签里面，后台action方法形参里面加上需要传参的控件name就可以了
document.czbListform.action="${pageContext.request.contextPath }/revokePregnantRecord.action?;
document.czbListform.submit();
String AddNew(HttpSession session,HttpServletRequest request,int index) {
}

// 3
window.location.href="${pageContext.request.contextPath }/DeleteRecord.action?emp_no="+emp_no+"&pregnantDate="+pregnantDate+"&index="+index;
```

```html
<!-- 4 -->
$.ajax({
    type:"post",
<%--url:"<%=basePath%>addPregnantRecord.action", --%>
    data:{"emp_no":emp_no,"pregnantDate":document.getElementById("pregnantDate").value},
    dataType:"json",
    success:function(data) {
    }
});
```

```jsp
<!-- 条件判断1 -->
<c:if test="${item.getFilePath().length()>0}">
    <c:forTokens items="${item.getFilePath()}" delims=";" var="path" varStatus="s">
        <a href="${pageContext.request.contextPath }/PregnantEmployeeSubmitWeb/FileList.jsp" style="text-decoration: underline;">${fn:substring(path,lfn:lastIndexOf(path,"-")+1,-1)}</a><br>
    </c:forTokens>
</c:if>

<!-- 条件判断1 -->
<c:choose>
    <c:when test="${sign_state== '待签核'}">
    </c:when>
    <c:when test="${emp_no == null}">
    </c:when>
    <c:otherwise>
    </c:otherwise>
</c:choose>
```

10.下拉框 select
1）标签写法

```jsp
<select id="select1" name="select1" >
    <option value="id" <c:if test="${search_mode== 'id'}">selected="selected"</c:if>>
        id
    </option>
    <option value="name" <c:if test="${search_mode== 'name'}">selected="selected" </c:if>>
        name
    </option>
</select>
```

2）javascript获取select的值

```js
 function querySignState() {
    var select=document.getElementById('search_mode').value;
    document.form1.action="${pageContext.request.contextPath }/opaSearch.action?";
    document.form1.submit();
}
```

3）后台获取select的值
jsp中将select标签放在form1中，和form1一起提交，select的name属性作为后台的形参，在函数体中直接使用

```java
@RequestMapping(value = "/hello")
String opaSearch(String select1) {
    // 直接使用 select1
}
```

11.jsp中,文本框没有填写字符串时
1）javascript 函数中的值为
var emp_no=document.getElementById("emp_no").value;//emp_no=""
2）action中的值为
String empno=emp_no;//empno=""

12.jsp中没有写相应的文本框hr_check_signtime时，
1）get方法返回的值为null
public String getHr_check_signtime() {
        if(hr_check_signtime==null)
        {
            hr_check_signtime="";
        }
        return hr_check_signtime;
    }
2）传到mapper的值为
getter为 null时，为null;
getter为 ""时，为'';

- 请求处理方法返回字符串（页面）的写法

```java
@RequestMapping("/getString")
public String getString(Model model) {
    User user = new User();
    user.setName("handle");
    model.addAttribute("user", user);
    // 返回user.jsp
    return "user";
}
```

- 请求处理方法返回类型为void的写法

```java
@RequestMapping("/testVoid")
public void testVoid(HttpServletRequest request, HttpServletResponse response) {
    // 请求转发
    request.getRequestDispatcher("/WEB-INF/pages/success.jsp").forward(request, response);

    // 请求重定向
    response.sendRedirect(request.getContextPath() + "/index.jsp");

    // 设置编码
    response.setCharacterEncoding("UTF-8");
    response.setContentType("text/html;charset=UTF-8");

    // 直接相应
    response.getWriter.print("hello world");
}
```

- ResponseBody 响应json数据（用于ajax请求）

```java
@RequestMapping("/updateUser")
@ResponseBody
public User updateUser(@RequestBody User user) {
    user.setName("handle");
    // 返回json
    return user;
}
```

- 返回ModelAndView，和返回字符串（页面）功能一样

```java
@RequestMapping("/testModelAndView")
public ModelAndView testModelAndView() {
    User user = new User();
    user.setName("handle");

    ModelAndView modelAndView = new ModelAndView();
    // 把user对象存到ModelAndView中，也会把user对象存入到request对象中
    modelAndView.addObject("user", user);
    // 跳转到user页面
    modelAndView.setViewName("user");
    return modelAndView;
}
```

Servlet就是一个能处理HTTP请求，发送HTTP响应的小进程，而发送响应无非就是获取`PrintWriter`，然后输出HTML。

JSP是一种在HTML中嵌入动态输出的文档，它和Servlet正好相反，Servlet是在Java代码中嵌入输出HTML；

```java
PrintWriter pw = resp.getWriter();
pw.write("<html>");
pw.write("<body>");
pw.write("<h1>Welcome, " + name + "!</h1>");
pw.write("</body>");
pw.write("</html>");
pw.flush();
```

只不过，用PrintWriter输出HTML比较痛苦，因为不但要正确编写HTML，还需要插入各种变量。如果想在Servlet中输出一个类似新浪首页的HTML，写对HTML基本上不太可能。就可以用jsp了。

JSP是Java Server Pages的缩写，它的文档必须放到`/src/main/webapp`下，文档名必须以`.jsp`结尾，整个文档与HTML并无太大区别，但需要插入变量，或者动态输出的地方，使用特殊指令`<% ... %>`

整个JSP的内容实际上是一个HTML，但是稍有不同：

- 包含在`<%--`和`--%>`之间的是JSP的注释，它们会被完全忽略；
- 包含在`<%`和`%>`之间的是Java代码，可以编写任意Java代码；
- 如果使用`<%= xxx %>`则可以快捷输出一个变量的值。

JSP页面内置了几个变量，这几个变量可以直接使用：

- out：表示HttpServletResponse的PrintWriter；
- session：表示当前HttpSession对象；
- request：表示HttpServletRequest对象。

JSP和Servlet有什幺区别？其实它们没有任何区别，因为JSP在执行前首先被编译成一个Servlet。在Tomcat的临时目录下，可以找到一个`xxx_jsp.java`的源文档，这个文档就是Tomcat把JSP自动转换成的Servlet源码。

可见JSP本质上就是一个Servlet，只不过无需配置映射路径，Web Server会根据路径查找对应的`.jsp`文档，如果找到了，就自动编译成Servlet再执行。在服务器运行过程中，如果修改了JSP的内容，那幺服务器会自动重新编译。

```jsp
<%@ page language="java" contentType="text/html; charset=UTF-8" pageEncoding="UTF-8"%>
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html lang="zh-CN">
    <head>
        <meta charset="UTF-8">
        <title></title>
    </head>
    <body>
    </body>
</html>
```

jsp页面乱码解决方案：

1） jsp页面头部加上：<%@ page language="java" contentType="text/html; charset=UTF-8" pageEncoding="UTF-8"%>

2）Servlet响应代码中加上：resp.setCharacterEncoding("UTF-8"); //设置HTTP 响应的编码

### JSP高级功能

JSP的指令非常复杂，除了`<% ... %>`外，JSP页面本身可以通过`page`指令引入Java类：

```jsp
<%@ page import="java.io.*" %>
<%@ page import="java.util.*" %>
```

这样后续的Java代码才能引用简单类名而不是完整类名。

使用`include`指令可以引入另一个JSP文档：

```jsp
<html>
<body>
    <%@ include file="header.jsp"%>
    <h1>Index Page</h1>
    <%@ include file="footer.jsp"%>
</body>
```

### JSP Tag

JSP还允许自定义输出的tag，例如：

```jsp
<c:out value = "${sessionScope.user.name}"/>
```

JSP Tag需要正确引入taglib的jar包，并且还需要正确声明，使用起来非常复杂，对于页面开发来说，*不推荐*使用JSP Tag，因为我们后续会介绍更简单的模板引擎，这里我们不再介绍如何使用taglib。

1. 表单提交方式：

```javascript
// 1.这种情况下传参为字符型可能在后台收到乱七八糟的符号，最好只传数值型参数
document.czbListform.action="${pageContext.request.contextPath }/revokePregnantRecord.action?index="+index;
document.czbListform.submit();

// 2.这里不传参，但是相应控件全部放在提交的form标签里面，后台action方法形参里面加上需要传参的控件id就可以了
document.czbListform.action="${pageContext.request.contextPath }/revokePregnantRecord.action?;
document.czbListform.submit();
String AddNew(HttpSession session,HttpServletRequest request,int index) {

}

// 3
window.location.href="${pageContext.request.contextPath }/DeleteRecord.action?emp_no="+emp_no+"&pregnantDate="+pregnantDate+"&index="+index;

$.ajax({
    type:"post",
    url:"<%=basePath%>addPregnantRecord.action",
    data:{"emp_no":emp_no,"pregnantDate":document.getElementById("pregnantDate").value},
    dataType:"json",
    success:function(data) {

    }
});
```

```jsp
<c:if test="${item.getFilePath().length()>0}">
    <c:forTokens items="${item.getFilePath()}" delims=";" var="path" varStatus="s">
        <a href="${pageContext.request.contextPath }/PregnantEmployeeSubmitWeb/FileList.jsp" style="text-decoration: underline;">${fn:substring(path,lfn:lastIndexOf(path,"-")+1,-1)}</a><br>
    </c:forTokens>
</c:if>
```

### 页面预览pdf

```java
    @RequestMapping(value = "OnlineBrowse")
    public void OnlineBrowse(HttpServletResponse response, String fileName) throws UnsupportedEncodingException {
        File file = new File(fileName);
        // Response.setContentType(MIME)的作用是使客户端的浏览器区分不同种类的数据
        // 并根据不同的MIME调用浏览器内不同的进程嵌入模块来处理相应的数据
        // response.setContentType 指定 HTTP 响应的编码,同时指定了浏览器显示的编码
        response.setContentType("application/pdf;charset=UTF-8");
        // 设置下载文档名
        // 在设置Content-Disposition头字段之前，一定要设置Content-Type头字段
        // Content-Disposition属性有两种类型：inline 和 attachment
        // inline ：将文档内容直接显示在页面
        // attachment：弹出对话框让用户下载
        // URLEncoder.encode(file.getName(),"UTF-8") 防止文档名乱码
        // response.setHeader("Content-Type","application/pdf");
        response.setHeader("Content-Disposition", "inline; filename="+URLEncoder.encode(file.getName(),"UTF-8"));
        // 设置从request中取得的值或从数据库中取出的值
        // request.setCharacterEncoding("utf-8");
        // response.setCharacterEncoding 设置HTTP 响应的编码
        // 如果之前使用response.setContentType设置了编码格式
        // 则使用response.setCharacterEncoding指定的编码格式覆盖之前的设置
        // response.setCharacterEncoding("utf-8");

        if (file.exists()) {
            byte[] data = null;
            FileInputStream fileInputStream=null;
            try {
                fileInputStream= new FileInputStream(file);
                data = new byte[fileInputStream.available()];
                fileInputStream.read(data);

                //加载pdf
                PDDocument document = PDDocument.load(data); 
                //获得文档属性对象
                PDDocumentInformation documentInformation = document.getDocumentInformation(); 
                //修改标题属性 这个标题会被展示
                documentInformation.setTitle(file.getName()); 
                document.setDocumentInformation(documentInformation);
                document.setAllSecurityToBeRemoved(true);
                //修改完直接输出到响应体中
                document.save(response.getOutputStream()); 
                document.close();
                //response.getOutputStream().write(data);
            } catch (Exception e) {
                System.out.println("pdf文档处理异常：" + e);
            }finally{
                try {
                    if(fileInputStream!=null){
                        fileInputStream.close();
                    }
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }
    }
```

## Thymeleaf

1. 标签中变量写法：${qrCodeImage}
2. 标签中路径写法：@{/qrCode/generator}
3. 标签中路径有变量的写法：@{/downloads/{qrCodeImage}(qrCodeImage=${qrCodeImage})}，

{qrCodeImage}相当于占位符，qrCodeImage从括号里面取值，${qrCodeImage}表示取后台传过来的值

1. js中变量写法：[[${qrCodeImage}]]

2. js中路径写法：[[@{/qrCode/generator}]]

## Node.js

官网：<https://nodejs.org/zh-cn>

Node.js是一个组合了各种有用的库的JavaScript运行时环境

Node.js使用谷歌的V8引擎来执行浏览器外的代码

### Linux安装Node.js

去官网或用包管理器安装Node.js后

如果在用户目录下安装了另一个node，并且想要覆盖系统级的node

在`~/.bashrc`添加如下配置，然后执行`source ~/.bashrc`

```sh
# 在`~/.bashrc`添加如下配置，然后执行`source ~/.bashrc`
export NODE_HOME=/path/to/node

# 由于系统的包管理器也安装了一个node.js，这里的路径设置必须放在最前面
# 这样查找的时候就先找到用户目录下的node，然后就不会继续查找系统版本的node了
export PATH="${NODE_HOME}/bin:$PATH"
```

- 在`~/.bashrc`添加如下配置，下面的配置用户级node和系统级node都可以配置，然后执行`source ~/.bashrc`

```sh
# 例：npm -g install packageName命令会把包安装到/usr/lib/node_modules目录下，会导致权限问题
# 因此指定npm_config_prefix前缀让它安装到用户级目录下
export npm_config_prefix="$HOME/.local"

# 例：npm install -g @neutralinojs/neu，把包安装到指定目录后
# 附带的可执行文件，neu命令行工具会被链接到"$HOME/.local/bin"下
export PATH="$HOME/.local/bin:$PATH"
```

### Windows安装Node.js

- 1.下载免安装版本，解压

- 2.进入nodejs根目录，新建两个文件夹：`node_global`、`node_cache`

- 3.新建系统变量`NODE_HOME`，设置为nodejs根目录

- 4.编辑环境变量Path，新增：`%NODE_HOME%`、`%NODE_HOME%\node_global`

- 5.用管理员权限打开cmd运行如下命令

```sh
# 配置npm相关路径
npm config set prefix "G:\handle\application\node-v20.17.0-win-x64\node_global"
npm config set cache "G:\handle\application\node-v20.17.0-win-x64\node_cache"
```

- 6.执行完毕后，可在系统盘的当前用户文件夹看到`.npmrc`文件，可以打开查看配置的信息

### 设置镜像源

```sh
# 查看环境变量是否设置成功
node -v

# 设置国内镜像源，官方的是：https://registry.npmjs.org/
# 如阿里巴巴的
npm config set registry https://registry.npmmirror.com/

# 查看镜像源是否设置成功
npm get registry
```

### npm

- npm是Node.js官方的包管理工具，Node.js包含了npm

- 常用命令

```sh
# npm版本
npm -v

# 安装包
# -g表示全局，给所有项目都安装
# 安装完会在node_modules/package.json中dependencies属性看到安装的依赖
npm install [-g] js包名

# 更新包
npm update [-g] packageName

# 更新全部包
npm update [-g]

# 卸载包
npm [-g] uninstall js包名

# 列出已安装的包
# --depth=0，只显示依赖树的顶级包
npm -g list [--depth=0]

# 列出已安装的版本已经老旧的或许需要更新的包
npm -g outdated

# 运行node_modules/package.json中scripts定义的脚本
npm run 脚本名称
```

### npx

npx = 临时运行 npm 包的工具，Node.js 自带，用完即弃

### pnpm

pnpm是高级版的npm，需要自行安装

npm的绝大部分命令都可以改成pnpm执行

- 安装

```sh
# 在任意一个终端执行

# 方法一
npx get-pnpm

# 方法二
npm install -g pnpm

# 测试
pnpm --version
```

## vue

- 响应式特性：（变量）数据的变化可以更新到页面效果上

- 单向绑定：数据变化->页面变化，前提数据是响应式

- 双向绑定：数据变化<->页面变化，前提数据是响应式

- script用ts

- 用let声明变量

- 用const声明常量

### 创建vue3项目

```sh
# 创建vue3项目
npm create vue@latest

# 安装所有的依赖
npm i

# 运行项目
npm run dev
```

### vue项目文件

#### main.ts

```ts
// 1.引入createApp用于创建应用
import { createApp } from "vue";

// 2.引入App根组件(src目录下的App.vue)
import App from "./App.vue";

// 3.调用createApp，传入App，并且挂载到index.html中id为app的标签中
createApp(App).mount('#app')
```

#### vue文件

vue文件里面可以写三种标签

```vue
<!-- 1.template标签写html，以及显示组件 -->
<template>
    <div class="app">
        <!-- 显示组件 -->
        <Person />
    </div>
</template>
<!-- 2.script写各种脚本，如导入组件 -->
<script lang="ts" setup>
    // 导入其它组件
    import Person from './components/Person.vue';
</script>

<!-- 3.写样式，scoped表示局部样式，只对当前vue的template有效 -->
<style scoped>
    .app {
        background-color: #ddd;
    }
</style>
```

### vue语法

#### vue文件中的script写法

##### 写法1

```vue
<script lang="ts">
    export default {
        name: 'SomeVueName',
        setup() {
            let name = 'handle'
            function updateName() {}
            // 缺点：通过return指定返回的数据、方法，template标签中才能使用
            return {name,updateName}
        }
    }
</script>
```

##### 写法2

```vue
<script lang="ts">
    export default {
        name: 'SomeVueName'
    }
</script>

<!-- 相当于写了一个setup() {}，并且返回所有数据、方法 -->
<script lang="ts" setup>
    // 缺点：要写两个script标签
    let name = 'handle'
    function updateName() {}
</script>
```

##### 写法3

```vue
<script lang="ts" setup>
    // 缺点：此文件名是什么组件名就是什么，不能改变此组件的名字
    let name = 'handle'
    function updateName() {}
</script>
```

##### 写法4

通过安装插件支持通过name属性定义组件名

- 1.安装插件

```sh
npm i vite-plugin-vue-setup-extend -D
```

- 2.修改项目根目录下的vite.config.ts文件

```ts
// 2.1导入插件
import VueSetupExtend from 'vite-plugin-vue-setup-extend'

export default defineConfig({
    plugins: [
        // 2.2调用插件
        VueSetupExtend()
    ]
})
```

- 3.通过name属性定义组件名

```vue
<script lang="ts" setup name="SomeVueName">
    let name = 'handle'
    function updateName() {}
</script>
```

#### 变量定义及使用的写法

- 插值写法

```vue
<template>
    <h2>姓名：{{name}}</h2>
</template>
<script lang="ts" setup>
    let name = 'handle'
</script>
```

#### 响应式数据写法

##### 基本类型写法

```vue
<template>
    <h2>姓名：{{ name }}</h2>
    <button @click="updateName">更新姓名</button>
</template>
<script lang="ts" setup>
    import { ref } from 'vue';

    let name = ref('handle')
    function updateName() {
        name.value = 'zhangsan'
    }
</script>
```

##### 对象类型写法

#### ref写法

```vue
<template>
    <h2>姓名：{{ user.name }}</h2>
    <h2>年龄：{{ user.age }}</h2>
    <button @click="updateName">更新姓名</button>
    <button @click="updateAge">更新年龄</button>
</template>
<script lang="ts" setup>
    import { ref } from 'vue';

    //对象写法
    let user = ref({ name: 'handle', age: 18 })
    function updateName() {
        user.value.name = 'zhangsan'
    }
    // 数组写法
    let users = ref([user])
    function updateAge() {
        users.value[0].value.age += 1
    }
    // 重新分配对象写法，对象地址改变
    user.value = { name: 'lisi', age: 20 }
</script>
```

#### reactive写法

```vue
<template>
    <div>
        <h2>姓名：{{ user.name }}</h2>
        <h2>年龄：{{ user.age }}</h2>
        <button @click="updateName">更新姓名</button>
        <button @click="updateAge">更新年龄</button>
    </div>
</template>
<script lang="ts" setup>
    import { reactive } from 'vue';

    //对象写法
    let user = reactive({ name: 'handle', age: 18 })
    function updateName() {
        user.name = 'zhangsan'
    }
    // 数组写法
    let users = reactive([user])
    function updateAge() {
        users[0].age += 1
    }
    // 重新分配对象写法，对象地址不变
    Object.assign(user, { name: 'lisi', age: 20 })
</script>
```

#### toRef和toRefs写法

```vue
<template>
    <div>
        <h2>id：{{ user.id }}</h2>
        <h2>姓名：{{ user.name }}</h2>
        <h2>年龄：{{ user.age }}</h2>
        <button @click="updateUser">更新用户</button>
    </div>
</template>
<script lang="ts" setup>
    import { reactive, toRef, toRefs } from 'vue';

    let user = reactive({ id: 1, name: 'handle', age: 18 })

    // 将user的id赋值给id
    // 并且id也是响应式的，其值改变user的属性跟着改变
    let id = toRef(user, 'id')

    // 将user的name和age（所有属性一次）赋值给name和age
    // 并且name和age也是响应式的，其值改变user的属性跟着改变
    let { name, age } = toRefs(user)

    function updateUser() {
        name.value += 1
        age.value += 1
        id.value += 1
    }
</script>
```

#### 方法定义及调用的写法

- 定义及调用

```vue
<template>
    <button @click="updateName">更新名字</button>
</template>

<script lang="ts" setup name="SomeVueName">
    function updateName() {}
</script>
```

- 显示方法的结果

```vue
<template>
    <div>
        <p>{{ getName() }}</p>
    </div>
</template>
<script lang="ts" setup>
    function getName() {
        return 'handle'
    }
</script>
```

#### computed写法

```vue
<template>
    <div>
        <p>name：{{ name }}</p>
        <p>upperName：{{ upperName }}</p>
        <button type="button" @click="updateUpperName">更新upperName</button>
    </div>
</template>
<script lang="ts" setup>
    import { computed, ref } from 'vue';

    let name = ref('handle')

    let upperName = computed({
        get() {
            return name.value.toUpperCase()
        },
        // upperName改变的时候调用set方法
        set(newUpperName) {
            name.value = newUpperName.toLocaleLowerCase()
        }
    })

    function updateUpperName() {
        upperName.value = 'LISI'
    }
</script>
```

#### watch监视写法

##### 监视ref定义的基本类型数据

```vue
<template>
    <div>
        <p>计数：{{ counter }}</p>
        <button type="button" @click="updateCounter">更新计数</button>
    </div>
</template>
<script lang="ts" setup>
    import { ref, watch } from 'vue';

    let counter = ref(0)

    function updateCounter() {
        counter.value += 1
    }

    // 监视，counter的值变化时触发，按需定义和调用stopWatch来停止监视
    const stopWatch = watch(counter, (newValue, oldValue) => {
        console.log(newValue, oldValue)
        if (newValue > 10) {
            // 停止监视
            stopWatch()
        }
    })
</script>
```

##### 监视ref定义的对象类型数据

- 若修改的是对象的属性，newValue和oldValue的值都是新值，因为它们是同一个对象
- 若修改的是对象，newValue是新值，oldValue旧值，因为它们不是同一个对象

```vue
<template>
    <div>
        <p>年龄：{{ user.age }}</p>
        <button type="button" @click="updateAge">更新年龄</button>
        <button type="button" @click="updateUser">更新user</button>
    </div>

</template>
<script lang="ts" setup>
    import { ref, watch } from 'vue';

    let user = ref({ age: 18 })

    function updateAge() {
        user.value.age += 1
    }

    function updateUser() {
        user.value = { age: 30 }
    }

    // 监视的是对象的地址，若想监视对象内部属性变化，需要手动开启deep监视
    watch(user, (newValue, oldValue) => {
        console.log(newValue, oldValue)
    }, { deep: true })
</script>
```

##### 监视reactive定义的对象类型数据

```vue
<template>
    <div>
        <p>年龄：{{ user.age }}</p>
        <button type="button" @click="updateAge">更新年龄</button>
        <button type="button" @click="updateUser">更新user</button>
    </div>
</template>
<script lang="ts" setup>
    import { reactive, watch } from 'vue';

    let user = reactive({ age: 18 })

    function updateAge() {
        user.age += 1
    }

    function updateUser() {
        Object.assign(user, { age: 30 })
    }

    // 默认开启深度监视，并且无法用deep:false关闭
    watch(user, (newValue, oldValue) => {
        console.log(newValue, oldValue)
    })
</script>
```

##### 监视ref或reactive定义的对象类型数据中的某个属性

- 若该属性不是对象类型，需要写成函数形式
- 若该属性是对象类型，建议写成函数形式

```vue
<template>
    <div>
        <p>年龄：{{ user.age }}</p>
        <p>年龄：{{ user.pet.name }}</p>
        <button type="button" @click="updateAge">更新年龄</button>
        <button type="button" @click="updatePetName">更新宠物名</button>
        <button type="button" @click="updatePet">更新宠物</button>
        <button type="button" @click="updateUser">更新user</button>
    </div>
</template>
<script lang="ts" setup>
    import { reactive, watch } from 'vue';

    let user = reactive({
        age: 18,
        pet: {
            name: 'dog'
        }
    })

    function updateAge() {
        user.age += 1
    }

    function updatePetName() {
        user.pet.name = 'cat'
    }

    function updatePet() {
        user.pet = { name: 'rabbit' }
    }

    function updateUser() {
        Object.assign(user, { age: 30 })
    }

    // 监视属性是基本类型，写成匿名函数形式
    watch(() => user.age, (newValue, oldValue) => {
        console.log(newValue, oldValue)
    })

    // 监视属性是对象类型，直接写:（监视里面的属性变化）
    // 写成匿名函数形式（监视对象地址的变化）
    // 写成匿名函数形式，并且加deep:true（都监视）
    watch(user.pet, (newValue, oldValue) => {
        console.log(newValue, oldValue)
    })
</script>
```

##### 监视上述的多个数据

```vue
<template>
    <div>
        <p>年龄：{{ user.age }}</p>
        <p>年龄：{{ user.pet.name }}</p>
        <button type="button" @click="updateAge">更新年龄</button>
        <button type="button" @click="updatePetName">更新宠物名</button>
    </div>
</template>
<script lang="ts" setup>
    import { reactive, watch } from 'vue';

    let user = reactive({
        age: 18,
        pet: {
            name: 'dog'
        }
    })

    function updateAge() {
        user.age += 1
    }

    function updatePetName() {
        user.pet.name = 'cat'
    }

    watch([() => user.age, () => user.pet.name], (newValue, oldValue) => {
        console.log(newValue, oldValue)
    })
</script>
```

##### watchEffect写法

```vue
<template>
    <div>
        <p>年龄：{{ age }}</p>
        <p>计数：{{ counter }}</p>
        <button type="button" @click="updateAge">更新年龄</button>
        <button type="button" @click="updateCounter">更新counter</button>
    </div>
</template>
<script lang="ts" setup>
    import { ref, watchEffect } from 'vue';

    let age = ref(18)
    let counter = ref(0)

    function updateAge() {
        age.value += 1
    }

    function updateCounter() {
        counter.value += 1
    }

    watchEffect(() => {
        // 不用显示指定age和counter
        if (age.value > 25 || counter.value > 5) {
            console.log(age.value, counter.value)
        }
    })
</script>
```

#### 标签的ref属性

- 子vue

```vue
<template>
    <div>
        <!-- 在html标签上，拿到的是dom元素 -->
        <p ref="ageRef">年龄：{{ age }}</p>
        <button type="button" @click="updateAge">更新年龄</button>
    </div>
</template>
<script lang="ts" setup>
    import { ref } from 'vue';

    let ageRef = ref()

    let age = ref(18)

    function updateAge() {
        age.value += 1
        console.log(ageRef.value)
    }

    // 最后声明可以给父vue看的属性
    defineExpose({ age })
</script>
```

- 父vue

```vue
<template>
    <!-- 在组件上，拿到的是组件实例 -->
    <User ref="user" />
    <button type="button" @click="showUser">显示组件ref</button>
</template>

<script setup lang="ts">
    import User from './components/User.vue';
    import { ref } from 'vue';

    let user = ref()

    function showUser() {
        console.log(user.value)
    }
</script>
```

#### 自定义类型

- 1.定义

```ts
// 定义一个接口，用于限制User对象的具体属性
export interface IUser {
    id: number,
    name: string,
    age: number,
    // 表示创建IUser对象的时候，car是可有可无的
    car?: string
}

// 定义IUser数组，两种方式
export type IUsers = IUser[]
// export type IUsers = Array<IUser>
```

- 2.使用

```vue
<script lang="ts" setup>
    import type { IUser, IUsers } from '@/types/User';

    // 根据接口创建对象
    let user: IUser = { id: 1, name: "handle", age: 18 }

    // 根据接口创建数组两种方式
    let users1: IUsers = [
        { id: 1, name: "handle", age: 18 },
        { id: 2, name: "lisi", age: 30 }
    ]

    let users2: Array<IUser> = [
        { id: 1, name: "handle", age: 18 },
        { id: 2, name: "lisi", age: 30 }
    ]
    
    // reactive泛型写法
    let users3 = reactive<IUser> [
        { id: 1, name: "handle", age: 18 },
        { id: 2, name: "lisi", age: 30 }
    ]
</script>
```

##### defineProps

- 父组件数据传给子组件，使用属性绑定

```vue
<!-- 父组件 -->
<Son :fatherData="" :otherData=""/>

<!-- 子组件 -->
<!-- 指定接收 -->
defineProps<>(['fatherData'])

<!-- 指定接收 + 类型限制 -->
<!-- defineProps<{fatherData:typeName}>() -->

<!-- 指定接收 + 类型限制 + 必要性 + 默认值 -->
<!-- withDefaults(defineProps<{fatherData?:类型名}>(),{fatherData: () => [{id: 1,name: 'handle'}]}) -->

let fatherData = defineProps({
    fatherName,
    // 也可以自定义接受父组件的属性
    fatherAge: {
        type: Number,
        required: true
        default: 25
    }
})
```

##### defineEmits

- 子组件数据传给父组件

```ts
// 子组件定义发生的事件
let emits = defineEmits(['方法名']);
function 方法名() {
    emits("方法名"，参数);
}

// 父组件感知和接收事件值
<Son fatherData @方法名="父组件方法名"/>
```

- 兄弟组件传值：子1传给父，父传给子2

#### vue的html语法

- 格式：v-xxx

- v-html，将变量的值转成html显示

- v-text，显示变量的值

- v-on:click，简写：@click，指定方法名

- v-if，判断指令

- v-bind，简写为英文冒号`:`，标签属性和变量绑定，单向绑定

- v-slot，插槽，简写为井号`#`

##### v-module

- 标签属性值和变量绑定，双向绑定

```vue
<template>
    <div>
        <!-- 将输入框的值和变量name双向绑定，v-model:value简写为v-model -->
        <input type="text" v-model="name" />
    </div>
</template>
<script lang="ts" setup>
    import { ref } from 'vue';

    let name = ref('handle')
</script>
```

##### v-for

- 循环遍历

```vue
<template>
    <div>
        <ul>
            <!-- key，唯一标识，不指定默认index，v-bind:key简写为:key -->
            <li v-for="item in users" :key="item.id">姓名：{{ item.name }}</li>
        </ul>
    </div>
</template>
<script lang="ts" setup>
    let users = [{ id: 1, name: 'handle' }, { id: 2, name: 'zhangsan' }]
</script>
```

#### 生命周期

```ts
// 创建前、创建完毕都整合到setup里面去了

// 挂载前、挂载完毕
onBeforeMount(() => {})
onMounted(() => {})

// 更新前、更新完毕
onBeforeUpdate(() => {})
onUpdated(() => {})

// 卸载前、卸载完毕
onBeforeUnmount(() => {})
onUnmounted(() => {})
```

#### 自定义hooks

- 定义一个useXxx.ts的文件

```ts
// 定义xxx的数据
// 定义xxx的方法
return {xxxDAta, xxxFunction}
```

- 在vue文件中引入useXxx.ts

```ts
const {xxxDAta, xxxFunction} = useXxx()
```

### Vue-Router

- 依赖

```sh
npm install vue-router
```

- 配置

```ts
// 创建路由器
const router = createRouter({
  // 路由器的工作模式
  history: createWebHistory(import.meta.env.BASE_URL),
  // 定义路由
  routes: [
    {
      // 路径
      path: '/home',
      name: 'home',
      // 组件名
      component: HomeView,
      children: [
        {
            // "/home/detail" 子级路由不用写撇斜杠
            path: 'detail',
            component: HomeDetailView
        }
      ]
    },
    {
      path: '/about',
      name: 'aboutRoute',
      // route level code-splitting
      // this generates a separate chunk (About.[hash].js) for this route
      // which is lazy-loaded when the route is visited.
      component: () => import('../views/AboutView.vue')
    }
  ]
})

// 导出路由器
export default router

```

- 使用

```ts
let app = createApp(App)
// 4.使用导出的路由器
app.use(router)
app.mount('#app')
```

- 定义跳转、配置router-link和router-view

```vue
<!-- 定义跳转 -->
<RouterLink to="/home">Home</RouterLink>
<!-- 子级路由路径要写全，不能单单写/detail -->
<RouterLink to="/home/detail">Home</RouterLink>
<!-- 可以通过路由的path或name属性进行映射 -->
<RouterLink :to="{name: 'aboutRoute'}">About</RouterLink>

<!-- 显示跳转的vue内容 -->
<RouterView />
```

#### 路由传参

##### query参数

```vue
<script type="ts">
    // 通过route的query获取传参
    let route = useRoute()
    let {query} = toRefs(route)
</script>
<template>
    <!-- 父组件 -->
    <RouterLink :to="{
    <!-- 传路由名称 -->
    name: '',
    <!-- 或者路由path -->
    path: '',
    <!-- 传参 -->
    query: {
        id: 变量名称1,
        name: 变量名称2,
    }
    <!-- 子组件，获取query里面属性值 -->
    <p>{{query.id}}</p>
    <p>{{query.name}}</p>
}">Home</RouterLink>
</template>
```

##### params参数

```ts
// 路由写法
const router = createRouter({
    history: createWebHistory(import.meta.env.BASE_URL),
    routes: [
        {
            // params: id和name作为占位符
            path: '/home/:id/:name',
            name: 'homeRoute',
            component: HomeView
        }
    ]
})

<!-- 跳转写法，如果加上问号，表示后面的参数不是必须 -->
<RouterLink to="/home?/123/handle">Home</RouterLink>
<!-- 从变量值中填充id和name作为params的写法1 -->
<RouterLink :to="'/home/${id}/${name}'">Home</RouterLink>
<!-- 从变量值中填充id和name作为params的写法2 -->
<RouterLink :to="{
    <!-- 必须传路由名称 -->
    name: 'homeRoute',
    <!-- 传参，和RouterLink定义的参数名要一致 -->
    query: {
        id: 变量名称1,
        name: 变量名称2,
    }
}">Home</RouterLink>

// 通过route的query获取传参
let route = useRoute()
let {params} = toRefs(route)

<!-- 获取params里面属性值 -->
<p>{{params.id}}</p>
<p>{{params.name}}</p>
```

##### props配置

```ts
// 路由写法1
const router = createRouter({
    history: createWebHistory(import.meta.env.BASE_URL),
    routes: [
        {
            path: '/home/:id/:name',
            name: 'homeRoute',
            component: HomeView,
            // 将路由收到的所有params参数作为props传给路由组件HomeView
            // 相当于<HomeView id=?? name=?? />
            props:true
        }
    ]
})

// 路由写法2
const router = createRouter({
    history: createWebHistory(import.meta.env.BASE_URL),
    routes: [
        {
            path: '/home/:id/:name',
            name: 'homeRoute',
            component: HomeView,
            // 将路由收到的所有params参数作为props传给路由组件HomeView
            // 相当于<HomeView id=?? name=?? />
            props(route) {
                // 可以返回query参数或者params参数，但是params用props:true更简洁
                return route.query
                // return route.params
            }
        }
    ]
})
// 路由写法3
const router = createRouter({
    history: createWebHistory(import.meta.env.BASE_URL),
    routes: [
        {
            path: '/home/:id/:name',
            name: 'homeRoute',
            component: HomeView,
            // 将路由收到的所有params参数作为props传给路由组件HomeView
            // 相当于<HomeView id=?? name=?? />
            props: {
                // 自己决定将什么作为parops传给路由组件，但是这种是写死的
                id: 123,
                name: 'handle'
            }
        }
    ]
})

// 获取传参写法
defineProps(['id', 'name'])
```

##### replace属性

```ts
<!-- 跳转写法，默认push：根据浏览器历史记录可前进后退；replace则不行 -->
<RouterLink replace to="/home">Home</RouterLink>
```

##### 编程式路由导航

- 由于在ts中不能用RouterLink

- 编程式路由导航也即脱离RouterLink实现路由跳转

```ts
// 获取路由器
const router = userRouter()
// RouterLink的to有几种写法push/replace的参数就有几种写法
router.push("/home")
// router.replace("/home")

// 通过button的点击实现跳转
// 限制方法的传参
interface typeName {
    id: number
}
function foo(entityName: typeName) {
    router.push({
        name: "homeRoute",
        query: {
            id: entityName.id
        }
    })
}
```

##### 重定向

```ts
// 路由写法
routes: [
    {
        // 访问/就重定向到/home
        path: '/',
        redirect: '/home'
    }
]
```

#### Pinia

- 集中式状态（数据）管理，将共享的组件数据交给Pinia管理

- 依赖

```sh
npm install pinia
```

- main.ts

```ts
import { createPinia } from 'pinia'

const app = createApp(App)
app.use(createPinia())
app.mount('#app')
```

#### Axios

- 依赖

```sh
npm install axios
```

##### axios的get请求

- 不带参数

```ts
let result = await axios.get("url")
    .then(response => {})
    .catch(error => {})
    .finally(() => {})
```

- 带参数

```ts
let result = await axios.get("url", {
        params: {
            id: 123,
            name: 'handle'
        }
    })
    .then(response => {})
    .catch(error => {})
    .finally(() => {})
```

- async/await写法

```ts
async function foo() {
    try {
        let result = await axios.get("url")
        // 连续解构赋值+重命名
        let {data:{content:id}} = await axios.get("url")
        // 一层层获取
        let id = result.data.id
    } catch (error) {

    }
}
```

##### axios的post请求

- 不带参数

```ts
let result = await axios.post("url")
    .then(response => {})
    .catch(error => {})
    .finally(() => {})
```

- 带参数

```ts
let result = await axios.post("url", {
        id: 123,
        name: 'handle'
    })
    .then(response => {})
    .catch(error => {})
    .finally(() => {})
```

##### axios实例

- 写一个ts文件，定义请求的基本信息，然后导出

```ts
const restTemplate = axios.create({
    // 可以填写后端网关地址
    baseURL: 'localhost:8080',
    timeout: 2000,
    headers: {'X-Custom-Header': 'handle'}
})

export default restTemplate
```

- 使用这个实例

```ts
restTemplate.get("/user").then(response => {})
restTemplate.post("/user/add").then(response => {})
```

##### axios拦截器

```ts
// 添加请求拦截器
axios.interceptors.request.use(function (config) {
    // 在发送请求之前做些什么
    return config;
}, function (error) {
    // 对请求错误做些什么
    return Promise.reject(error);
});

// 添加响应拦截器
axios.interceptors.response.use(function (response) {
    // 2xx 范围内的状态码都会触发该函数。
    // 对响应数据做点什么
    // return response;
    return response.data;
}, function (error) {
    // 超出 2xx 范围的状态码都会触发该函数。
    // 对响应错误做点什么
    return Promise.reject(error);
});
```

##### 代理设置

- 修改vite.config.ts文件

```ts
export default defineConfig({
    server: {
        proxy: {
            // 配了代理就不要写axios的baseURL，否则代理不生效
            // 正则表达式写法：http://localhost:5173/api/.. -> http://localhost:8080/..
            '^/api/.*': {
                target: 'http://localhost:8080',
                changeOrigin: true,
                rewrite: (path) => path.replace(/^\/api/, ''),
            },
        },
    }
})
```

- 修改axios默认配置

```ts
// 可以填写后端网关地址，配了代理就不要写完整的地址，会导致代理不生效
// axios.defaults.baseURL = 'http://localhost:8080';
// 可以写相对地址，比如可以加前缀，然后代理配置再把前缀去掉，方便代理配置
axios.defaults.baseURL = '/api',
```

- 修改axios实例配置

```ts
import axios from "axios"

const restTemplate = axios.create({
    // 可以填写后端网关地址，配了代理就不要写完整的地址，会导致代理不生效
    // 可以写相对地址，比如可以加前缀，然后代理配置再把前缀去掉，方便代理配置
    // baseURL: 'http://localhost:8080',
    baseURL: '/api',
    timeout: 3000,
})

export default restTemplate
```

#### 项目部署

- 打包项目

```sh
npm run build
```

- 将打包生成的dist文件夹重命名，然后放到服务器的某个目录下（如：/var/myweb/）

- 修改nginx配置文件

```conf
# 配置nginx根目录为/var/myweb
location / {
    root /var/myweb;
    index index.html index.htm;
    # 当没有匹配资源的时候都匹配到index.html，解决刷新404
    try_files $uri $uri/ /index.html;
}

# 设置代理，转发请求
# location的/api/末尾的/一定要写，proxy_pass地址末尾的/一定要写，才能去掉前缀/api
# http://前端服务器ip:port/api/.. -> http://后端网关ip:port/..
location /api/ {
    # 设置代理目标(后端网关地址)，
    proxy_pass http://后端网关ip:port/;
}
```
