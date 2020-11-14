---
title: python_requests模块
date: 2020-04-23 21:24:08
tags: [python,qequests]
categories: python常用模块
---

### requests模块简单用法

```python
import requests
r = requests.get("https://retnull.top/")
# 创建对象
#get传递参数
payload = {"key1":"value1","key2":"value2"}
r = requests.get("https://retnull.top/",params=payload)

#编码
r.encoding='utf-8'

#请求头
headers={"flag": "dfkfdk"}
r=requests.get(url,headers=headers)
#post传递
payload = {"key1": "value1", "key2": "value2"}
r = requests.post(url,data=payload)
#转态码
r.status_code

#访问headers
r.headers['key']

#访问cookie
r.cookies['key']
#会话对象
s = requests.session()
r=s.get()
#访问服务器发送的header
r.headers
#访问自己发送的header
r.request.headers


#发送cookie
cookie={"key","value"}

```

