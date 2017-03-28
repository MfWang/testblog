**import用法：**
><style type="text/css">
&emsp;@import "style.css";@import url("style.css");
</style>

注意:不过最好不要使用@import，因为使用它加载的样式文件会等到页面都加载完成再被加载，这样可能会导致页面浏览出现问题，建议使用标签替代。使用import相当于把css放于底部加载。(这个是百度出来的说法)

我们确实要避免使用css @import， 但原因却不是什么相当于放在了页面底部，而是这样做会导致css无法并行下载，因为使用@import引用的文件只有在引用它的那个css文件被下载、解 析之后，浏览器才会知道还有另外一个css需要下载，这时才去下载，然后下载后开始解析、构建render tree等一系列操作。 星球浏览器在页面所有css下载并解析完成后才会开始渲染页面，因此css @import引起的css解析延迟会加长页面留白期。 所以，要尽量避免使用css @import而尽量采用link标签的方式引入。

**对@import url（）做一下总结：**
1. @import url（）机制是不同于link的，link是在加载页面前把css加载完毕，而@import url（）则是读取完文件后在加载，所以会出现一开始没有css样式，闪烁一下出现样式后的页面(网速慢的情况下)。
1. @import 是css2里面的，所以古老的ie5不支持。
1. 当使用javascript控制dom去改变样式的时候，只能使用link标签，因为@import不是dom可以控制的。
1. link除了能加载css外还能定义RSS，定义rel连接属性，@import只能加载css。
1. @import url(xxx.css); 有最大次数的限制，经测试IE6的最大次数是31次，第32个import及以后的都不能生效


