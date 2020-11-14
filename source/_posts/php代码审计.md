---
title: php代码审计
date: 2020-01-03 20:33:34
tags: [php,魔术引号,PHP函数]
categories: PHP代码审计
---

## PHP代码审计

[toc]

### php.ini设置

> `magic_quotes_gpc`=on
>
> > 过滤四种字符 防御SQL注入 ,但是能被16进制绕过和宽字节绕过
>
> `safe_mode`=on
>
> > 安全模式禁用php只的一些敏感函数 提权和后门会被影响
>
> `Disable_function`:
>
> > 自定义的禁用一些敏感函数
>
> `open_basedir`
>
> > 限制后门的访问
>
> `register_globals =off`
>
> > 直接将post get等传递的数据直接自动形成变量
>
> `short_

