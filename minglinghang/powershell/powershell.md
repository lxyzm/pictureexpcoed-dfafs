###### 快捷键
end 将光标移至命令行末尾
esc 清空当前命令行
F7对话框显示命令历史记录
F9根据命令历史记录编号选择命令
Ctrl+左右方向键

####### 管道
*  ls  | Format-Table Name,Mode
####### 重定向
* ‘>‘ 为覆盖，’>>‘追加
####### 进行数学运算
```
PS C:\Users\admin> 1+1
2
```
####### Powershell 执行外部命令
默认键入一个字符串，powershell会将它原样输出，如果该字符串是一个命令或者启动程序，在字符串前加‘&’可以执行命令，或者启动程序。
####### 别名
* get-Alias  查看所有别名
* get-alias  ls 查看别名ls的powershell脚本
* set-alias edit notepad
* export-alias a.ps1 导出别名
* Import-Alias -Force .\a.ps1  导入别名
* 设置永久别名
	* 查看是否存在配置文件
		* test-path $profile
	* 创建文件
		* New-Item -path $profile -itemtype file -Force 
	* 添加记录
		* Set-Alias note notepad
	* powershell默认在启动的时候，会以Restricted模式运行，此时不允许执行任何脚本。需执行：(有权限限制)
		* Set-Executionpolicy Remotesigned
####### 变量
########正在使用的变量
* Powershell将变量的相关信息的记录存放在名为variable:的驱动中
* ls variable: 查看正在使用的所有变量
* $variable:PWD 查看pwd变量的值
* test-path variable:a 验证变量a是否存在
* del variable:a 删除变量a
* powershell提供了五个专门管理变量的命令Clear-Variable，Get-Variable，New-Variable，Remove-Variable，Set-Variable,new-variable可以在定义变量时，指定变量的一些其它属性，比如访问权限。同样Get-Variable也可以获取这些附加信息。
########环境变量
* Powershell把所有环境变量的记录保存在env: 虚拟驱动中
* ls env: 查看所有环境变量
* $env:path 显示环境变量path的值

########powershell 命令返回数组
* 当我们把一个命令的执行结果保存到一个变量中，Powershell会把文本按每一行作为元素存为数组
* 判断一个变量是否是数组
* $ip -is [array]
* ipconfig | select-string "IP"
* 任何一个对象都可以使用Format-List * 查看它所有的属性和方法。
######### * 数组(可以同时存任何类型的数据)
* $a=1,2,3,3 声明数组
* $a=@() 空数组
* $a=,"moss" 一个元素的数组
* $result[0,3,5,12] 访问数组 
* $books+="元素4" 添加到数组
* $chsNew=$chs.Clone() 使用默认的的赋值运算符在两个变量之间赋值只是复制了一个引用，两个变量共享同一份数据。这样的模式有一个弊病如果其中一个改变也会株连到另外一个。所以复制数组最好使用Clone()方法	
* $stu=@{ Name = "小明";Age="12";sex="男" }	
* $stu["Name"]			   