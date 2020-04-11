---
title: 配置php.ini以及文件夹权限使nginx支持图片上传
date: 2017-08-014
categories:
  - code
  - server
  - php
tags: 
  - php
  - nginx
---


最近在做一个图片展示类的个人站点，需要将图片上传到服务器,选择了php进行开发
<!-- more -->
框架选型为yaf，少就是多，毕竟个人站点，选择yaf会有更多的DIY的空间

#### 关于php.ini的几个配置项

```bash
;;;;;;;;;;;;;;;;;;;
; Resource Limits ;
;;;;;;;;;;;;;;;;;;;

; Maximum execution time of each script, in seconds
; http://php.net/max-execution-time
; Note: This directive is hardcoded to 0 for the CLI SAPI
max_execution_time = 80
; 意思就是每个脚本最大执行时间，单位为秒，这个根据你服务器的配置来吧，配置低就设大点
; 不然脚本还没执行完就被关闭你都不知道错哪儿

; Maximum amount of time each script may spend parsing request data. Its a good
; idea to limit this time on productions servers in order to eliminate unexpectedly
; long running scripts.
; Note: This directive is hardcoded to -1 for the CLI SAPI
; Default Value: -1 (Unlimited)
; Development Value: 60 (60 seconds)
; Production Value: 60 (60 seconds)
; http://php.net/max-input-time
max_input_time = 90
; 每个脚本在处理请求数据时所能运行的最大时间，官方建议限制这个时间以防止始料未及的长时间
; 的脚本运行，但是 who care ，我想要我的大文件传上去，但我网速不好，所以我只能多给她点时间了

; Maximum input variable nesting level
; http://php.net/max-input-nesting-level
;max_input_nesting_level = 64
; 请求最大嵌套深度，防止哈希碰撞攻击用的，暂时不用管他


; How many GET/POST/COOKIE input variables may be accepted
; max_input_vars = 1000
; 最多有多少请求参数，暂时可不管

; Maximum amount of memory a script may consume (128MB)
; http://php.net/memory-limit
memory_limit = 128M
; 默认配置也足够用了，一个PHP脚本能占多少内存

; 下面是关于文件上传的配置
;;;;;;;;;;;;;;;;
; File Uploads ;
;;;;;;;;;;;;;;;;

; Whether to allow HTTP file uploads.
; http://php.net/file-uploads
file_uploads = On

; Temporary directory for HTTP uploaded files (will use system default if not
; specified).
; http://php.net/upload-tmp-dir
upload_tmp_dir =

; Maximum allowed size for uploaded files.
; http://php.net/upload-max-filesize
upload_max_filesize = 200M

; Maximum number of files that can be uploaded via a single request
max_file_uploads = 20

; 其实最主要就是把file_uploads打开

```

其实配置个php还是很容易，毕竟配置文件里面的注释都已经非常详细了，但我在配置完成以后仍然无法上传文件
最坑的地方是。。。我nginx错误日志竟然是空白
##### 至今我还是不明白为什么error.log会是空白的，在前端报了500的情况下。。。

直到偶然间打开error.log发现不再是空白一片时，我知道自己得救了，果然

```bash
open() "/var/lib/nginx/tmp/client_body/0000000007" failed (13: Permission denied)
```
好吧，看来果然是权限的问题

```shell
chown -R nginx /var/lib/nginx
```

重新上传图片果然就可以了

