# HEXO的Linux安装总结

说明：需要大家有一定的Linux，git知识。这是我第一次安装总结，很多不足，希望大家一起学习一起进步。

谢谢！！！

## 第一步：安装nodejs

1. 登陆官网https://nodejs.org/en/

   根据自己需要下载对应的Linux版本，这边我下载的是10.16.0LTS

2. 下载完成后找找到下载目录

   在下载目录下，可以找到我们下载的内容;是以.tar.xz结尾

   ```
   #ls -l
   //下面的不是指令
   -rw-rw-r--  1 panyexiong panyexiong  13039792 6月  29 21:11 node-v10.16.0-linux-x64.tar.xz
   ```

3. 解压文档到指定的位置

   因为是以.tar.xz结尾所以需要使用以下打包指令

   我选择将文件打包（解压）到/home/software，这个目录是我的软件安装目录，可根据个人需求更换成其他路径。

   ```
   #tar -xvJf node-v10.16.0-linux-x64.tar.xz -C /home/software
   ```

4. 解压完成

   - 查看版本

     ```
     #node -v
     //如果成功看到版本号，则跳到第5步
     ```

   - 出现找不到的指令，则我们需要为解压文件夹/nodejs/bin/下的npm和node建立软连接

     ```
     #ln -s /home/software/nodejs/bin/npm /usr/local/bin/
     #ln -s /home/software/nodejs/bin/node /usr/local/bin/
     #node -v
     ```

5. 安装完成！！！

## 第二步：安装cnpm

1. 安装

   ```
   #npm install -g cnpm --registry=https://registry.npm.taobao.org
   ```

2. 测试

   ```
   #cnpm -v
   ```

3. 如果也找不到，则也需要为cnpm建立软连接

   ```
   #ln -s /home/software/nodejs/bin/cnpm /usr/local/bin/cnpm
   ```

## 第三步：安装HEXO

1. 通过cnpm安装HEXO

   ```
   #cnpm install -g hexo-cli
   //测试安装是否成功
   #hexo -v
   //建立软连接
   #ln -s /home/software/nodejs/lib/node_modules/hexo-cli/bin/hexo /usr/local/bin/hexo
   ```

## 第四步：写博客

1. 初始化：我在/home路径下新建了一个blog文件夹用于存放我的第一个blog

   ```
   #cd /home
   #mkdir blog
   //进入blog目录
   #cd blog/
   
   //初始化一个blog
   #hexo init
   
   //查看当前目录路径
   #pwd
   /home/panyexiong/blog
   //查看目录文件
   # ls -l
   总用量 160
   -rw-r--r--   1 panyexiong panyexiong   1859 6月  29 23:44 _config.yml
   -rw-rw-r--   1 panyexiong panyexiong    174 6月  29 23:47 db.json
   drwxr-xr-x 447 panyexiong panyexiong  20480 6月  29 22:30 node_modules
   -rw-r--r--   1 panyexiong panyexiong    483 6月  29 22:30 package.json
   -rw-r--r--   1 panyexiong panyexiong 111209 6月  29 22:12 package-lock.json
   drwxrwxr-x   6 panyexiong panyexiong   4096 6月  29 23:46 public
   drwxr-xr-x   2 panyexiong panyexiong   4096 6月  29 22:10 scaffolds
   drwxr-xr-x   3 panyexiong panyexiong   4096 6月  29 23:45 source
   drwxr-xr-x   4 panyexiong panyexiong   4096 6月  29 22:56 themes
   ```

2. 启动博客

   ```
   #hexo server
   或者
   #hexo s
   ```

   启动成功后可以，在浏览器中输入localhost:4000来访问我们本地的博客

3. 新建一篇博客

   ```
   #hexo n "我的第一篇文章"
   ```

   我们可以进入以下路径查看文章

   ```
   #cd /home/panyexiong/blog/source/_posts/
   
   #ls -l
   ```

   编辑《我的第一篇文章》

   ```
   #vim 我的第一篇博客文章.md 
   //注意md格式
   ```

   编辑完成，退回到blog目录

   ```
   #cd /home/panyexiong/blog/
   
   #hexo clean
   //生成
   #hexo g
   #hexo s
   ```

   再次刷新localhost:4000，就会出现

## 第五步：推送到github

1. 先在自己的github新建一个仓库。仓库名称：github名称.github.io

   例如panyexiong.github.io

2. 在本地blog目录下安装git插件

   ```
   cnpm install --save hexo-deployer-git
   ```

3. 设置_config.yml(blog目录下)

   ```
   #vim _config.yml
   //输入i进入编辑模式
   //找到最下面
   deploy:
     type: git
     repo: （刚才新建的panyexiong.github.io仓库的地址，ssh形式）
     branch: master
   
   ```

4. 推送

   ```
   #hexo d
   ```

5. 完成

   在浏览器输入网址panyexiong.github.io即可访问。

## 补充

- 如果大家对git，Linux指令不太熟悉可以自己先熟悉一下，我也在学习的过程中，

  在以后的日子里，我也会把我学习git和Linux的总结，放到我的博客上面，谢谢大家。

- 更换主题的部分，大家可以去引用中的视频进行学习。

## 引用

- 本篇文章的安装使用过程，主要通过观看b站up主CodeSheep，这个一个很棒的up主，希望大家可以从他那里学到很多东西。他的bilibili地址：https://space.bilibili.com/384068749

