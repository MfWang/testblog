1、 安装TortoiseGit
2、 添加账户凭证    [TortoiseGit之配置密钥](http://blog.csdn.net/bendanbaichi1989/article/details/17916795) 
3、 任意文件夹下，右键选择git clone，复制仓库地址
4、 新建本地分支，右键选择TortoiseGit->Create Branch，按图并切换至新分支

![1.png](http://upload-images.jianshu.io/upload_images/1874069-422763f4318e5ead.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

5、 右键选择Git Commit -> "xl-20160321-demo"，提交文件改动
6、 所有文件改动都提交后，右键选择TortoiseGit->Push，提交代码到主干
7、 切换到gitlab.365eche.com，点击所在项目365eche->Commits->Branches->Compare，审查代码


![2.png](http://upload-images.jianshu.io/upload_images/1874069-c311f0c6750b9d3c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

8、 点击create merge request，创建合并请求，在Assignee选择审批人，等待审核
9、 更新代码，项目文件夹下右键选择Git Sync，填写本地分支以及远程分支后，点击Pull即可
