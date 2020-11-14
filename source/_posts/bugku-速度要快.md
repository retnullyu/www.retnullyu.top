---
title: bugku_速度要快
date: 2020-04-23 22:49:28
tags: [BUGKU,requests模块]
categories: bugku_writeup
---

### setp1 burp抓包

![image-20200423225157486](bugku-速度要快/image-20200423225157486.png)

> 发现了注释信息以及head里面有flag字段，明显是一个base64，尝试解密提交失败。注释说要发送一个key为margin的post数据，刷新多次发现flag都不同，所以只能脚本了

### setp2 脚本

```python
import requests
import base64
import re #正则匹配模块
s = requests.session() #建立会话
url = "http://123.206.87.240:8002/web6/"
head = s.get(url).headers #获取头部信息
result = head['flag'] #得到flag的value
result = base64.b64decode(result).decode('utf-8') #第一次base64解码
result = re.search('\w+$', result).group(0) #正则匹配base64编码的flag
result = base64.b64decode(result).decode('utf-8') #第二次解码
payload = {'margin': result}
print(s.post(url, data=payload).text) #post传输数据且输出返回信息


```

### 总结

> 这题没有什么难度，作用是熟悉一下requests模块。坑是注意head里的flag第一次是全部base64编码，解码后输出的那串字符串也是base64，需要再解码一次