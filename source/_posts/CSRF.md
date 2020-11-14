---
title: CSRF&SSRF
date: 2019-05-1 18:49:19
tags: [ssrf,csrf]
categories: CSRF&SSRF

---

## CSRF

> 受害者浏览器中有登录并且验证通过的web服务，之后访问了恶意的url或者网站，执行用户非本意的操作，叫做 利用HTML标签或者url链接

### sessionID保存位置

> `cookie url 表单的隐藏域中`

### 漏洞原因:

> 1. url的所有参数可确定
> 2. 只验证了`cookie`

### 防御：

> 1. 验证`referer`字段，敏感操作只运行所在的web服务网站
>
>    > 只能抵御 站外请求
>    >
>    > https->http 不携带`Referer`
>
> 2. 验证`token`
>
>    > AJAX由于同源策略不可能拿到`token`
>    >
>    > token一般设置在隐藏域中
>
> 3. 敏感操作==二次验证==
>
> 4. 设置`samesite cookies= strict`

## SSRF

![image-20200607230053099](CSRF/image-20200607230053099.png)

> 调用其他web服务器 ，没有验证输入从而被利用
>
> 触发点：
>
> 1. webmail
>
> 2. 远程下载和加载
>
>    

>  利用：
>
> > 1. 端口扫描 内网扫描  内网拓扑结构
> >
> > 3. 读取系统本地文件
> >
> >    > `?url=file://c:\windows\syestem32\drivers\etc\hosts`
> >
> > 4. 内网web识别
> >
> > 5. 攻击内网应用

### bypass



### 防御

> 1. 限制协议：dict file
> 2. 显示IP 显示跳转url并检查
> 3. 限制端口
> 4. 过滤返回信息
> 5. 统一错误信息（避免错误信息泄露端口信息）

### 案例

> weblogic 从ssrf到redis未授权访问
>
> v