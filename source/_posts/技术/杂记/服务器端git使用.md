---
title: 初始化服务器git仓库
categories:
  - code
tags: 
  - git
  - server
---

这个hexo博客，根据官方文档指示，部署步骤稍显繁琐，并且一旦换了台电脑，就无法获得以前的_posts，因此，我将source目录初始化为git仓库，并设置hooks在提交后自动执行generate

这样不管在哪里，只要克隆下source仓库就可以进行写作了

<!-- more -->

##### 初始化source为git仓库

```shell

cd /path/to/source

git init

```

现在我们创建好了source仓库，还需要一个裸的仓库进行版本的管理
我们在自己电脑上真正与之交互的就是下面要生成的裸仓库

##### 生成一个裸仓库用于版本管理 

```shell

#接上一步
cd ../

git clone --bare source

```

于是我们有了一个可以用于push的仓库，那么现在我们与要让这个仓库真正生效

```shell

cd source

git remote add origin ../source.git

git add .

git commit -m "initial"

git push origin master

```

至此，服务器上简单的git仓库搭建完成
我们想要每次提交后自动到source，也就是真正需要修改的仓库进行拉取，并自动执行 `hexo g`

##### 创建post-update脚本，自动拉取并生成文件

```shell

cd ../source.git/hooks

mv post-update.sample post-update

vim post-update

```

post-update脚本内容如下：


```bash

#!/bin/sh
#exec git update-server-info

unset GIT_DIR
NowPath=`pwd`
DeployPath='/home/hexob/source'
GeneratePath='/home/hexob'

cd $DeployPath
git pull origin master

cd $GeneratePath
hexo g

cd $NowPath
exit 0

```

这篇博客就是通过git上传并自动生成的