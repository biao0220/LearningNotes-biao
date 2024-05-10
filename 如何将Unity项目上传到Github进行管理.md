```
git init
git remote add origin git@github.com:biao0220/LearningNotes-biao.git
git pull origin main
git add .
git commit -m "make in biao"
git push origin master
```



git push origin --delete master

git clone git@github.com:biao0220/DaimaoWang-Unity2DGame-biao.git

# 在Github上面新建一个仓库

**这里.gitignore文件十分建议选择Unity模板****(一定要改)**

复制仓库地址
把这里的地址复制一下，后面需要用

# 在项目文件夹打开Git Bash

进入项目文件夹右击选择Git Bash Here

# 初始化仓库

输入

git init


# 建立远程连接

输入

git remote add origin git@github.com:biao0220/unity2Dgame-biao.git


# 配置信息

输入

git config user.name "名字"
git config user.email "邮箱"


或

git config --global user.name "名字"
git config --global user.email "邮箱"


后者只需要一次，以后上传其他仓库仍按保留，不再需要进行配置

# 下载仓库到本地

输入

git pull origin main



完成后，本地文件夹会下载空仓库中的.gitignore和LICENSE文件，务必确保执行这一步之前本地文件夹没有这两个文件

# 上传项目到仓库

输入

git add .
git commit -m "make in biao"
git push origin main

至此完成本地项目的上传
————————————————

                            版权声明：本文为博主原创文章，遵循 CC 4.0 BY-SA 版权协议，转载请附上原文出处链接和本声明。

原文链接：https://blog.csdn.net/qstxwlq/article/details/124969791