这两天在做中英文双版的文件，页面根据语言读取不同的内容。js模板用的是ejs
>json文件： 
`
"components":{
  "pages":{
    "home":{
        "meta":{
          "title":"首页",
          "keywords":"关键字",
          "desc":"描述"
        },
       "title":"登录失败",
       "button":"重新登录"
    }
  }
}`
页面js代码
`let metaTitle = "<%= components.pages.home.meta.title %>"
console.log(metaTitle)
//控制台输出：首页 `
但是当json文件中数据很多，而又都需要的情况下，显然不能一条一条赋值，最好是可以将整个对象进行赋值，比如：
`let home ="<%= components.pages.home %>"
console.log(home)
//控制台输出：[object Object]`
这种情况下，控制台输出的是[object Object]，看不到具体的内容，于是需要将它转换成与json文件中格式一致，方便在js中使用
* JSONObject.fromObject()
`
let home = "<%= components.pages.home %>"
let homeObj = JSONObject.fromObject(home);
console.log(homeObj)
//ReferenceError: JSONObject is not defined[详细了解]
`
* JSON.stringify()
`
let home = "<%= components.pages.home %>"
let homeObj = JSON.stringify(home);
console.log(homeObj)
//"[object Object]"
`
* JSON.parse()
`
let home = "<%= components.pages.home %>"
let homeObj = JSON.parse(home);
console.log(homeObj)
//SyntaxError: JSON.parse: unexpected character at line 1 column 2 of the JSON data[详细了解]
`
* ps:let home = "<%= components.pages.home %>" 这里的双引号在解析的时候，会和内部的双引号行程匹配，所以下一种办法中，将""改成了''
* 最后提供一种目前自己代码使用的办法
 `
let home = JSON.parse(decodeURI('<%- encodeURI(JSON.stringify(components.pages.home)) %>'))
`
后来发现并不需要decode和encode
`
let home = JSON.parse('<%- JSON.stringify(components.pages.home) %>')
`
