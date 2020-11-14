---
title: bugku_writeup_giveup
date: 2020-04-25 13:16:01
tags: [view-source协议,php弱类型,eregi截断漏洞]
categories:  bugku_writeup
---

### never give up

#### 分析前端代码

> 发现了注释为`1p.html`，访问这个页面，被重定向到了bugku主页

`view-source:http://123.206.87.240:8006/test/1p.html`

> 为什么要用这个协议，是因为1p.html重定向了网页，查看网页源代码只能这样

> 发现很多百分号首先推测是url编码，接着是base64，解码后显示网页源代码

```javascript
<script>window.location.href='http://www.bugku.com';</script> 
<!--";if(!$_GET['id']) //如果没有id值为falseh或者0则跳转
{
	header('Location: hello.php?id=1');
	exit();
}
$id=$_GET['id'];
$a=$_GET['a'];
$b=$_GET['b'];
if(stripos($a,'.')) //判断a中是否有.
{
	echo 'no no no no no no no';
	return ;
}
$data = @file_get_contents($a,'r');//类似file函数，把文件数据读入$data

if($data=="bugku is a nice plateform!" and $id==0 and strlen($b)>5 and eregi("111".substr($b,0,1),"1114") and 
//id要等于0(注意==只要求数据相等，并不是数据类型)，字符串'111'连接b的第一个字符去匹配‘1114’ 且b的第一个字符不能是4
substr($b,0,1)!=4)
{
	require("f4l2a3g.txt");
}
else
{
	print "never never never give up !!!";
}
?>-->
```

#### 思路

> 利用eregi的截断漏洞 ：当第一个参数出现%00默认结束
>
> 其实这题可以利用其它payload：b的第一个参数可以是`.`,来形成任意参数
>
> 

==PHP伪协议：PHP://input==

> `payload:http://123.206.87.240:8006/test/hello.php?id='0'&a=php://input&b=%00123456`