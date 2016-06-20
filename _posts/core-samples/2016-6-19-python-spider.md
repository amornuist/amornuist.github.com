---
layout: default
category : lessons
tagline: "Supporting tagline"
tags : [intro, beginner, jekyll, tutorial]
---
{% include JB/setup %}


第一次用python，简单写了个登陆知乎抓取登陆后个人页面的爬虫，记录下过程。

## 1.安装

---

在python官网下载python3.4：_https://www.python.org/_，下载之后再path里设置下环境变量即可。但python自带的编辑器用起来实在谈不上方便，所以这里还是用_sublime_来写代码，当然在sublime里装上pip扩展，这样就可以在sublime里随意安装各种功能丰富的插件。

---

## 2.爬虫思路

---

主要用到urllib和http.cookiejar函数库，函数都比较简单。需要注意的有以下几点

> -登陆时要有header信息和post信息，这和http协议有关，很多网站会反爬虫，为骗过服务器需要将header信息写到程序里，伪装成浏览器传给服务器；传输方式一般是get和post两种方式，一般网页登陆用post传输，这时候就需要把post信息也写进去。可以用firebug插件来抓取这些信息。
> 
> -有时服务器为验证身份，会传一个会话cookie，类似于你的身份证，这时候就需要有cookie信息，这里用http.cookiejar来解决这一问题。当然会有些网站这种方法还获取不到sessionid，可以考虑别的函数库来解决，也可以手动把sessionid写到post里，当然这种方法时效很短。

---

## 3.代码

---

<pre><code>

	import urllib  
	import sys  
	import http.cookiejar  
	import webbrowser
	import re
	import urllib.request
	from urllib.error import HTTPError
	import gzip

	def ungzip(data):
		try:        # 尝试解压
			print('正在解压.....')
			data = gzip.decompress(data)
			print('解压完毕!')
		except:
			print('未经压缩, 无需解压')
		return data
	cj = http.cookiejar.CookieJar()   
	def getOpener(head):
		# deal with the Cookies
		global cj
		pro = urllib.request.HTTPCookieProcessor(cj)
		opener = urllib.request.build_opener(pro)
		header = []
		for key, value in head.items():
			elem = (key, value)
			header.append(elem)
		opener.addheaders = header
		return opener 

	#先在登陆页面获取_xsrf参数  
	def getXSRF(data):
		cer = re.compile('name=\"_xsrf\" value=\"(.*)\"', flags = 0)
		strlist = cer.findall(data)
		return strlist[0]

	#设置头信息，用firebug插件抓取即可
	header = {
		'Connection': 'Keep-Alive',
		'Accept': 'text/html, application/xhtml+xml, */*',
		'Accept-Language': 'en-US,en;q=0.8,zh-Hans-CN;q=0.5,zh-Hans;q=0.3',
		'User-Agent': 'Mozilla/5.0 (Windows NT 6.1; WOW64; rv:46.0) Gecko/20100101 Firefox/46.0',
		'Accept-Encoding': 'gzip, deflate',
		'Host': 'www.zhihu.com',
		'DNT': '1'
	}

	url = 'http://www.zhihu.com/'
	opener = getOpener(header)
	op = opener.open(url)
	for ck in cj: 
		print(ck.name,':',ck.value)
	data = op.read()
	data = ungzip(data)     # 解压
	_xsrf = getXSRF(data.decode())

	url += 'login/email'

	#设置post信息，同样利用firebug插件抓取,因为知乎登陆不需要验证码，所以可以直接设置
	postDict = {
			'_xsrf':_xsrf,
			'email':'',
			'captcha':'',
			'password':'',
			'remember_me':'true'
	}
	postData = urllib.parse.urlencode(postDict).encode()
	print(postData)
	op = opener.open(url, postData)
	testurl="https://www.zhihu.com/people/xi-e-79"
	op=opener.open(testurl)
	data = op.read()
	data = ungzip(data)
	f=open("results.html","w",encoding='utf-8')
	f.write(data.decode('utf-8'))
	webbrowser.open("results.html")  

</code></pre>

---

## 4.后续

---

当然实际情况并不都是这么简答，比如很多网站sessionid并不能通过上述函数得到，比如登陆需要输入验证码，比如网页中有很多js代码，通过抓取源代码并不能抓到想要的数据，这些问题都有待后续继续学习解决。

---