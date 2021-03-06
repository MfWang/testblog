
#****一、处理跨域的方式：****
****
###**1.代理**

###**2.XHR2**
HTML5中提供的XMLHTTPREQUEST Level2（及XHR2）已经实现了跨域访问。但ie10以下不支持。只需要在服务端填上响应头：
>`
header("Access-Control-Allow-Origin:*");
/*星号表示所有的域都可以接受，*/
header("Access-Control-Allow-Methods:GET,POST");`

###**3.jsonp**
原理：
 ajax本身是不可以跨域的，通过产生一个script标签来实现跨域。因为script标签的src属性是没有跨域的限制的。
其实设置了dataType: 'jsonp'后，$.ajax方法就和ajax XmlHttpRequest没什么关系了，取而代之的则是JSONP协议。JSONP是一个非官方的协议，它允许在服务器端集成Script tags返回至客户端，通过javascript callback的形式实现跨域访问。
 ajax的跨域写法：
（其余写法和不跨域的一样）：
/*当前网址是localhost:3000*/
js代码
>`
$.ajax({ 
    type:"get", 
    url:"http://localhost:3000/showAll",/*url写异域的请求地址*/ 
    dataType:"jsonp",/*加上datatype*/ 
    jsonpCallback:"cb",/*设置一个回调函数，名字随便取，和下面的函数里的名字相同就行*/
     success:function(){ 
        //... 
    }
});`
 
/*而在异域服务器上，*/
app.js
>`
app.get('/showAll',students.showAll);/*这和不跨域的写法相同*/
/*在异域服务器的showAll函数里，*/
var db = require("./database");
exports.showAll =function(req,res){
    /**设置响应头允许ajax跨域访问**/
   res.setHeader("Access-Control-Allow-Origin","*");
   /*星号表示所有的异域请求都可以接受，*/
   res.setHeader("Access-Control-Allow-Methods","GET,POST");
   var con = db.getCon();
   con.query("select * from t_students",function(error,rows){ 
      if(error){  
         console.log("数据库出错："+error); 
      }else{  
         /*注意这里，返回的就是jsonP的回调函数名+数据了*/  
         res.send("cb("+JSON.stringify(r)+")"
      );
   }
}`

#**二、解决ajax跨域访问、 JQuery 的跨域方法**
JS的跨域问题，我想很多程序员的脑海里面还认为JS是不能跨域的，其实这是一个错误的观点；有很多人在网上找其解决方法，教其用IFRAME去解决的文章很多，真有那么复杂吗？其实很简单的，如果你用JQUERY，一个GETJSON方法就搞定了，而且是一行代码搞定。

下面开始贴出方法。
>`
//跨域（可跨所有域名）
$.getJSON("http://user.hnce.com.cn/getregion.aspx?id=0&jsoncallback=?",function(json){ 
    //要求远程请求页面的数据格式为： ?(json_data) 
    //例如： 
    //?([{"_name":"湖南省","_regionId":134},{"_name":"北市","_regionId":143}]) alert(json[0]._name);
});
$.getJSON("http://user.hnce.com.cn/getregion.aspx?id=0&jsoncallback=?",function(json){ 
    //要求远程请求页面的数据格式为： ?(json_data) 
    //例如： 
    //?([{"_name":"湖南省","_regionId":134},{"_name":"北京市","_regionId":143}]) alert(json[0]._name);
});`

注意，getregion.aspx中，在输出JSON数据时，一定要用
Request.QueryString["jsoncallback"]，将获取的内容放到返回JSON数据的前面。假设实际获取的值为42342348，那么返回的值就是 42342348([{"_name":"湖南省","_regionId":134},{"_name":"北京市","_regionId":143}])

因为getJSON跨域的原理是把？随机变一个方法名，然后返回执行的，实现跨域响应的目的。

下面一个是跨域执行的真实例子：
>`<script src="http://common.cnblogs.com/script/jquery.js" type="text/javascript"></script>
<script type="text/javascript"> 
    //跨域（可跨所有域名） 
    $.getJSON("http://e.hnce.com.cn/tools/ajax.aspx?jsoncallback=?", { id: 0, action: 'jobcategoryjson' }, function(json) { 
    alert(json[0].pid); 
    alert(json[0].items[0]._name); 
});
</script>
****
<script src="http://common.cnblogs.com/script/jquery.js" type="text/javascript"></script>
<script type="text/javascript"> 
    //跨域（可跨所有域名） 
    $.getJSON("http://e.hnce.com.cn/tools/ajax.aspx?jsoncallback=?", { id: 0, action: 'jobcategoryjson' }, function(json) { 
    alert(json[0].pid); 
    alert(json[0].items[0]._name); 
});
</script>`

**jQuery跨域原理:**
浏览器会进行同源检查,这导致了跨域问题,然而这个跨域检查还有一个例外那就是HTML的<Script>标记;我们经常使用<Script>的src属性,脚本静态资源放在独立域名下或者来自其它站点的时候这里是一个url;这个url响应的结果可以有很多种,比如JSON,返回的Json值成为<Script>标签的src属性值.这种属性值变化并不会引起页面的影响.按照惯例，浏览器在URL的查询字符串中提供一个参数，这个参数将作为结果的前缀一起返回到浏览器;
看下面的例子：
><script type="text/javascript" src="http://domain2.com/getjson?jsonp=parseResponse"> </script>
响应值：parseResponse({"Name": "Cheeso", "Rank": 7})
<script type="text/javascript" src="http://domain2.com/getjson?jsonp=parseResponse"> </script>
响应值：parseResponse({"Name": "Cheeso", "Rank": 7})

这种方式被称作JsonP;(如果链接已经失效请点击这里:JSONP);即：JSON with padding 上面提到的前缀就是所谓的“padding”。那么jQuery里面是怎么实现的呢？
貌似并没有<Script>标记的出现！？OKay，翻看源码来看：
页面调用的是getJSON：
 
>getJSON: function( url, data, callback ) { 
return jQuery.get(url, data, callback, "json");
},

继续跟进
 
>`get: function( url, data, callback, type ) { 
    // shift arguments if data argument was omited 
    if ( jQuery.isFunction( data ) ) { 
        type = type || callback; 
        callback = data; 
        data = null; 
    } 
    return jQuery.ajax({ 
        type: "GET", 
        url: url, 
        data: data, 
        success: callback, 
        dataType: type 
    });`

跟进jQuery.ajax，下面是ajax方法的代码片段：
>`
// Build temporary JSONP function
if ( s.dataType === "json" && (s.data && jsre.test(s.data) || jsre.test(s.url)) ) { 
     jsonp = s.jsonpCallback || ("jsonp" + jsc++); 
    // Replace the =? sequence both in the query string and the data 
    if ( s.data ) { 
        s.data = (s.data + "").replace(jsre, "=" + jsonp + "$1"); 
    } 
    s.url = s.url.replace(jsre, "=" + jsonp + "$1"); 
    // We need to make sure 
    // that a JSONP style response is executed properly 
    s.dataType = "script"; 
    // Handle JSONP-style loading 
    window[ jsonp ] = window[ jsonp ] || function( tmp ) { 
        data = tmp; 
        success(); 
        complete(); 
        // Garbage collect 
        window[ jsonp ] = undefined; 
        try { 
            delete window[ jsonp ]; 
        } catch(e) {} 
        if ( head ) { 
            head.removeChild( script ); 
        } 
    };
}
if ( s.dataType === "script" && s.cache === null ) { 
    s.cache = false;
}
if ( s.cache === false && type === "GET" ) { 
    var ts = now(); 
    // try replacing _= if it is there 
    var ret = s.url.replace(rts, "$1_=" + ts + "$2"); 
    // if nothing was replaced, add timestamp to the end 
    s.url = ret + ((ret === s.url) ? (rquery.test(s.url) ? "&" : "?") + "_=" + ts : "");
}
// If data is available, append data to url for get requests
if ( s.data && type === "GET" ) { 
    s.url += (rquery.test(s.url) ? "&" : "?") + s.data;
}
// Watch for a new set of requests
if ( s.global && ! jQuery.active++ ) { 
    jQuery.event.trigger( "ajaxStart" );
}
// Matches an absolute URL, and saves the domain
var parts = rurl.exec( s.url ), 
    remote = parts && (parts[1] && parts[1] !== location.protocol || parts[2] !== location.host);
// If we're requesting a remote document
// and trying to load JSON or Script with a GET
if ( s.dataType === "script" && type === "GET" && remote ) { 
    var head = document.getElementsByTagName("head")[0] || document.documentElement; 
    var script = document.createElement("script"); 
    script.src = s.url; 
    if ( s.scriptCharset ) { 
        script.charset = s.scriptCharset; 
    } 
    // Handle Script loading 
    if ( !jsonp ) { 
        var done = false; 
        // Attach handlers for all browsers 
        script.onload = script.onreadystatechange = function() { 
            if ( !done && (!this.readyState || this.readyState === "loaded" || this.readyState === "complete") ) { 
                done = true; 
                success(); 
                complete(); 
                // Handle memory leak in IE 
                script.onload = script.onreadystatechange = null; 
                if ( head && script.parentNode ) { 
                    head.removeChild( script ); 
                } 
            } 
        }; 
    } 
    // Use insertBefore instead of appendChild to circumvent an IE6 bug. 
    // This arises when a base node is used (#2709 and #4378). 
    head.insertBefore( script, head.firstChild ); 
    // We handle everything using the script element injection 
    return undefined;
}`

上面的代码第1行到第10行：判断是JSON类型调用，为本次调用创建临时的JsonP方法，并且添加了一个随机数字，这个数字源于用日期值；
 关注第14行，这一行相当关键，注定了我们的结果最终是<Script> ;然后是构造Script片段，第95行在Head中添加该片段，修成正果；
 不仅仅是jQuery,很多js框架都是用了同样的跨域方案，这就是getJSON跨域的原理。

**追加一种解决方式**
追求永无止境，在google的过程中，无意中发现了一个专门用来解决跨域问题的jQuery插件-[jquery-jsonp](https://github.com/congmo/jquery-jsonp)。
有第一种方式的基础，使用jsonp插件也就比较简单了，server端代码无需任何改动。
来看一下如何使用jquery-jsonp插件解决跨域问题吧。
>`
var url="http://localhost:8080/WorkGroupManagment/open/getGroupById" +"?id=1&callback=?";
$.jsonp({ 
    "url": url, 
    "success": function(data) { 
        $("#current-group").text("当前工作组:"+data.result.name); 
    }, 
    "error": function(d,msg) { 
                alert("Could not find user "+msg); 
    }
});`
