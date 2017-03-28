1.下载乌龟  
2.乌龟生成和保存的是私钥，而现在这边需要用到公钥，可以在命令行下面拉取代码


生成SSH密钥：https://git-scm.com/book/zh/v1/%E6%9C%8D%E5%8A%A1%E5%99%A8%E4%B8%8A%E7%9A%84-Git-%E7%94%9F%E6%88%90-SSH-%E5%85%AC%E9%92%A5
git.bongmi.cn "Profile Setting"--> "SSH keys" 添加密匙（格式为：ssh-rsa XXXXXXXXXXXX）

![](https://app.yinxiang.com/shard/s64/res/b5dc4712-0971-4b39-9e6c-b2ab7db11c4b/L0IWP9_%24L8%5B%40KNS%5B%24BU5E%29G.png)
![](https://app.yinxiang.com/shard/s64/res/6f9a0409-b9f3-4541-8ebb-13c9b045e8c6/KYV%5DDY%40Q%5DD%7DL%40ZEX%40LJ8G.png)

最后把 cat命令行下的公钥复制到gitlab中的ssh-key中，会自动生成title,然后保存，就可以在命令行中拉取项目代码了
