---
title: msf使用教程
date: 2020-06-03 23:17:54
tags: msf
categories: msf
---

## metasploit

[toc]

### 常用命令

> `back`:返回
>
> 
>
> #### 信息收集
>
> > ==nmap==
> >
> > `db_namp`
> >
> > 查看结果 : 
> >
> > `hosts`
> >
> > `db_services`
>
> #### 漏洞利用模块
>
> > `session -l`
> >
> > `info`:显示详细信息
> >
> > `job`:显示后台运行的任务
> >
> > `search`
> >
> > > Metasploit模块的命名约定使用下划线和连字符。
> > >
> > > `search cve:2009 type:exploit app:client`
> > >
> > > `search platform:linux`
> >
> > `show encoders`:编码
> >
> > `set  unset`
>
> `grep mysql search linux`
>
> 

### meterpreter

> * 获取用户名
>
>   `getuid`
>
> * 获取目标机器基本信息
>
>   `sysinfo`
>
> * 获取的channel
>
>   `channel -l `
>
> * 返回msfconsol，把当前session置于后天
>
>   `background`
>
> * sessions 
>
> ...



