## linux(Ubuntu20.04)下配置mysql8.0



### 前期准备

输入命令 : `dpkg --list|grep mysql` 查看是否有旧版本存在

如果存在旧版本,卸载旧版本和配置 : `sudo apt-get purge mysql-*`

删除不使用的依赖项 : `apt-autoremove`

​								 	`apt-autoclean`

### Step1

`sudo apt update`

### Step2

`sudo apt-get install mysql-server`

### Step3

初始化mysql配置

`sudo mysql_secure_installation`

```
#1
VALIDATE PASSWORD PLUGIN can be used to test passwords...
Press y|Y for Yes, any other key for No: N (选择N ,不会进行密码的强校验)

#2
Please set the password for root here...
New password: (输入密码)
Re-enter new password: (重复输入)

#3
By default, a MySQL installation has an anonymous user,
allowing anyone to log into MySQL without having to have
a user account created for them...
Remove anonymous users? (Press y|Y for Yes, any other key for No) : N (选择N，不删除匿名用户)

#4
Normally, root should only be allowed to connect from
'localhost'. This ensures that someone cannot guess at
the root password from the network...
Disallow root login remotely? (Press y|Y for Yes, any other key for No) : N (选择N，允许root远程连接)

#5
By default, MySQL comes with a database named 'test' that
anyone can access...
Remove test database and access to it? (Press y|Y for Yes, any other key for No) : N (选择N，不删除test数据库)

#6
Reloading the privilege tables will ensure that all changes
made so far will take effect immediately.
Reload privilege tables now? (Press y|Y for Yes, any other key for No) : Y (选择Y，修改权限立即生效)
```

### Step4

检查mysql服务是否运行

`systemctl status mysql.service`



### Step5





### 配置远程主机访问

找到 bind-address 修改值为 0.0.0.0(如果需要远程访问) 

`sudo vi /etc/mysql/mysql.conf.d/mysqld.cnf ` 

重启mysql

`sudo /etc/init.d/mysql restart ` 



+ 登录mysql 
+ 切换数据库 `use mysql;`
+ 查看状态 `select Host,user,plugin from user;`
+ 使用mysql_native_password修改root用户加密规则 `ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY '密码'; `

+ 设置root密码永不过期 `ALTER USER 'root'@'localhost' IDENTIFIED BY '密码' PASSWORD EXPIRE NEVER;`
+ 允许远程使用root账户访问 `UPDATE user SET host = '%' WHERE user = 'root'; `
+ 刷新配置 `flush privileges;` `quit;`



### 新建用户赋权并设置远程访问

必须先创建用户（密码规则：mysql8.0以上密码策略限制必须要大小写加数字特殊符号）
`CREATE USER 'new_user'@'%' IDENTIFIED WITH mysql_native_password BY 'password';`
赋权
`GRANT ALL PRIVILEGES ON *.* TO 'new_user'@'%' WITH GRANT OPTION;`



### 新建数据库和用户

创建数据库studentService
`CREATE DATABASE studentService;`
创建用户teacher(密码admin) 并赋予其studentService数据库的远程连接权限
`GRANT ALL PRIVILEGES ON teacher.* TO studentService@% IDENTIFIED BY "admin";`



### mysql服务命令

检查服务状态

`systemctl status mysql.service`



mysql服务启动停止

`sudo service mysql start`

`sudo service mysql stop`



### mysql服务操作

+ `mysql -u root -p   ` 

  进入mysql数据库

+ `mysql-> status;  ` 

  查看数据库版本

+ `mysql-> quit;`  

  退出mysql操作

+  `service mysql restart`   

  重启mysql服务

+ `mysql-> mysqladmin -uroot -proot password 123456  `  

  更改密码 ：mysqladmin -u用户名 -p旧密码 password 新密码

+ `mysql-> grant select,insert,update,delete on mydb.* to test2@localhost identified by "abc";`

  增加一个用户test2密码为abc,让他只可以在localhost上登录，并可以对数据库mydb进行查询、插入、修改、删除的操作

+ `mysql-> grant select,insert,update,delete on mydb.* to test2@localhost identified by "";`

  如果你不想test2有密码，可以再打一个命令将密码消掉。

+ `mysql-> show variables like 'character%'; `

  查看字符集











