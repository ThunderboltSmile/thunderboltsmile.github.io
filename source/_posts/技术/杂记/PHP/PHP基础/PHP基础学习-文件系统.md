---
title: PHP基础学习--文件系统
date: 2017-07-16
categories:
  - PHP
tags:
  - 基础知识
---
PHP拥有丰富的文件操作函数如file_get_contents,file_exists,file_put_contents等。
<!-- more -->
#### 读取文件内容

最简单的读取方法是`file_get_contents`

    syntax:file_get_contents(path,include_path,context,start,max_length)

```php
<?php
  $content=file_get_contents('./test.txt');
?>
```

也可以使用`fopen,fgets,fread`等方法。
fgets可以从文件指针中读取一行，freads可以读取指定长的的内容

```php
$fp = fopen('./text.txt', 'rb');
while(!feof($fp)) {
    echo fgets($fp); //读取一行
}
fclose($fp);
//or use freads
$fp = fopen('./text.txt', 'rb');
$contents = '';
while(!feof($fp)) {
    $contents .= fread($fp, 4096); //一次读取4096个字符
}
fclose($fp);
```
使用fopen打开的文件，最好使用fclose关闭文件指针，以避免文件句柄被占用。

#### 写入一个文件
对某个文件进行写操作可以使用`file_put_contents`或者fwrite的方式

```php
<?php
$filename = './test.txt';
$data = 'test';
file_put_contents($filename, $data);
//or
$fp = fopen('./test.txt', 'w');
fwrite($fp, 'hello');
fwrite($fp, 'world');
fclose($fp);
?>
```

#### 文件系统函数
* basename — Returns trailing name component of path
* chgrp — Changes file group
* chmod — Changes file mode
* chown — Changes file owner
* clearstatcache — Clears file status cache
* copy — Copies file
* delete — See unlink or unset
* dirname — Returns a parent directory's path
* disk_free_space — Returns available space on filesystem or disk partition
* disk_total_space — Returns the total size of a filesystem or disk partition
* diskfreespace — Alias of disk_free_space
* fclose — Closes an open file pointer
* feof — Tests for end-of-file on a file pointer
* fflush — Flushes the output to a file
* fgetc — Gets character from file pointer
* fgetcsv — Gets line from file pointer and parse for CSV fields
* fgets — Gets line from file pointer
* fgetss — Gets line from file pointer and strip HTML tags
* file_exists — Checks whether a file or directory exists
* file_get_contents — Reads entire file into a string
* file_put_contents — Write a string to a file
* file — Reads entire file into an array
* fileatime — Gets last access time of file
* filectime — Gets inode change time of file
* filegroup — Gets file group
* filemtime — Gets file modification time
* fileowner — Gets file owner
* fileperms — Gets file permissions
* filesize — Gets file size
* filetype — Gets file type
* fnmatch — Match filename against a pattern
* fopen — Opens file or URL
* nfpassthru — Output all remaining data on a file pointer
* fputcsv — Format line as CSV and write to file pointer
* fputs — Alias of fwrite
* fread — Binary-safe file read
* fscanf — Parses input from a file according to a format
* fseek — Seeks on a file pointer
* fstat — Gets information about a file using an open file pointer
* ftell — Returns the current position of the file read/write pointer
* ftruncate — Truncates a file to a given length
* fwrite — Binary-safe file write
* glob — Find pathnames matching a pattern
* is_dir — Tells whether the filename is a directory
* is_executable — Tells whether the filename is executable
* is_file — Tells whether the filename is a regular file
* is_link — Tells whether the filename is a symbolic link
* is_readable — Tells whether a file exists and is readable
* is_uploaded_file — Tells whether the file was uploaded via HTTP POST
* is_writable — Tells whether the filename is writable
* is_writeable — Alias of is_writable
* lchgrp — Changes group ownership of symlink
* lchown — Changes user ownership of symlink
* link — Create a hard link
* linkinfo — Gets information about a link
* lstat — Gives information about a file or symbolic link
* mkdir — Makes directory
* move_uploaded_file — Moves an uploaded file to a new location
* parse_ini_file — Parse a configuration file
* parse_ini_string — Parse a configuration string
* pathinfo — Returns information about a file path
* pclose — Closes process file pointer
* popen — Opens process file pointer
* readfile — Outputs a file
* readlink — Returns the target of a symbolic link
* realpath_cache_get — Get realpath cache entries
* realpath_cache_size — Get realpath cache size
* realpath — Returns canonicalized absolute pathname
* rename — Renames a file or directory
* rewind — Rewind the position of a file pointer
* rmdir — Removes directory
* set_file_buffer — Alias of stream_set_write_buffer
* stat — Gives information about a file
* symlink — Creates a symbolic link
* tempnam — Create file with unique file name
* tmpfile — Creates a temporary file
* touch — Sets access and modification time of file
* umask — Changes the current umask
* unlink — Deletes a file

虽然与这么多函数，不得不承认看起来很崩溃，不过我们只要熟悉这些函数有哪些功能以及特性，能够在合适的情境下通过手册进行使用就足够啦。


