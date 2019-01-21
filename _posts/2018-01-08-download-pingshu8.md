---
layout: post
title: '从“评书吧”批量下载'
date: 2018-01-08
author: Me

categories: other
tags: python,download
---

> 批量下载“评书吧”的mp3文件.

# 新版本,加入浏览器混淆
```python
from bs4 import BeautifulSoup
import http.cookiejar
import requests
import urllib
import re
import time
import urllib.parse
import io  
import sys 
import os
from random import choice

def get_id_url(base_url):

	UAS =[
			'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/63.0.3239.84 Safari/537.36',
			'Mozilla/5.0 (Windows NT 6.1; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/63.0.3239.84 Safari/537.36',
			'Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:57.0) Gecko/20100101 Firefox/57.0',
			'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/62.0.3202.94 Safari/537.36',
			'Mozilla/5.0 (Windows NT 6.1; Win64; x64; rv:57.0) Gecko/20100101 Firefox/57.0',
			'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/63.0.3239.108 Safari/537.36',
			'Mozilla/5.0 (Windows NT 6.1; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/62.0.3202.94 Safari/537.36'
		]

	
	# 创建一个 cookie对象来保存 cookie


	print("正在分析...")
	html = requests.get(base_url).content  
	soup = BeautifulSoup(html, 'html.parser', from_encoding='gb18030')

	#找出专辑名称
	result = soup.find("div",class_="t1")
	ablum_name = (result.find("h1").contents[0])

	#找出所有页面
	result = soup.find("select",{"name":"turnPage"})

	all_links = result.find_all("option")
	print("共 %d 页"%(len(all_links)))

	
	id_list = []
	name_list = []
	i = 0
	for link in all_links:
		i=i+1
		print("第 %d 页" %(i))
		#time.sleep(15.0)
		headers = {'Referer':base_url,#如某些网站（如p站）要检查referer，就给他加上
           'User-Agent':choice(UAS)#每个爬虫必备的伪装
          }
		html = requests.get("http://www.pingshu8.com"+link['value'],headers=headers).content  
		print("http://www.pingshu8.com"+link['value'])
		soup = BeautifulSoup(html, 'html.parser', from_encoding='gb18030')
		result = soup.find_all("input",type="checkbox")
		for item in result:
			#print(item['value'])
			id_list.append(item['value'])

		a1 = soup.find_all("li",class_="a1")
		#print(len(a1))
		#print(a1)
		for item in a1:
			name_list.append(str(item.find("a").contents[0]))
	print("分析完成，共%d个文件."%(len(id_list)))
	return [ablum_name, dict(zip(id_list, name_list))]

def down_load(save_path,id_url_list, start_from = 1):
	UAS =[
			'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/63.0.3239.84 Safari/537.36',
			'Mozilla/5.0 (Windows NT 6.1; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/63.0.3239.84 Safari/537.36',
			'Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:57.0) Gecko/20100101 Firefox/57.0',
			'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/62.0.3202.94 Safari/537.36',
			'Mozilla/5.0 (Windows NT 6.1; Win64; x64; rv:57.0) Gecko/20100101 Firefox/57.0',
			'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/63.0.3239.108 Safari/537.36',
			'Mozilla/5.0 (Windows NT 6.1; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/62.0.3202.94 Safari/537.36'
		]

	print('开始下载...')
	# 创建一个 cookie对象来保存 cookie
	cookie = http.cookiejar.CookieJar()
	# 创建一个 cookie对象
	handler = urllib.request.HTTPCookieProcessor(cookie)
	# 构建一个 handler对象
	opener = urllib.request.build_opener(handler, urllib.request.HTTPHandler)
	urllib.request.install_opener(opener)
	#sys.stdout = io.TextIOWrapper(sys.stdout.buffer,encoding='gb18030') 
	i = 0
	for id, name in id_url_list.items():
		i+=1
		if(i < start_from):
			continue
		filename = os.path.join(save_path,name+".mp3")
		print("正在下载第%d个文件：%s"%(i, filename))

		url = "http://www.pingshu8.com/bzmtv_Inc/download.asp?fid="+id
		ref_url="http://www.pingshu8.com/down_"+id+".html"
		req = urllib.request.Request(url)
		req.add_header('Referer', ref_url)

		for attempt in range(0,3):
			try:
				req.add_header('User-Agent', choice(UAS))
				data = opener.open(req).read()
			except Exception:
				import traceback
				print('generic exception: ' + traceback.format_exc())
				print("下载失败，正在重试... %d"%(attempt))
				time.sleep(60.0)
				continue
			break
		
		

		if not os.path.exists(os.path.dirname(filename)):
			try:
				os.makedirs(os.path.dirname(filename))
			except OSError as exc: # Guard against race condition
				if exc.errno != os.errno.EEXIST:
					raise
	
		open(filename, 'wb').write(data)
		time.sleep(15.0)
	print("下载完成")
	
if __name__ == '__main__':
	if len(sys.argv) < 2:
		 print('Usage: pingshu8 base_url')
		 exit(1)
	base_url = sys.argv[1]


	ablum_name,id_url_list= get_id_url(base_url)
	if(len(sys.argv)==2):
		down_load(ablum_name,id_url_list)
	elif(len(sys.argv)==3):
		down_load(ablum_name,id_url_list,int(sys.argv[2]))
```
# 脚本 

```python
#pingshu8.py

from bs4 import BeautifulSoup
import http.cookiejar
import requests
import urllib
import re
import time
import urllib.parse
import io  
import sys 
import os

def get_id_url(base_url):
	print("正在分析...")
	html = requests.get(base_url).content  
	soup = BeautifulSoup(html, 'html.parser', from_encoding='gb18030')

	#找出专辑名称
	result = soup.find("div",class_="t1")
	ablum_name = (result.find("h1").contents[0])

	#找出所有页面
	result = soup.find("select",{"name":"turnPage"})

	all_links = result.find_all("option")
	print("共 %d 页"%(len(all_links)))

	id_list = []
	name_list = []
	i = 0
	for link in all_links:
		i=i+1
		print("第 %d 页" %(i))
		time.sleep(5.0)
		html = requests.get("http://www.pingshu8.com"+link['value']).content  

		soup = BeautifulSoup(html, 'html.parser', from_encoding='gb18030')
		result = soup.find_all("input",type="checkbox")
		for item in result:
			#print(item['value'])
			id_list.append(item['value'])

		a1 = soup.find_all("li",class_="a1")
		for item in a1:
			name_list.append(str(item.find("a").contents[0]))
	print("分析完成，共%d个文件."%(len(id_list)))
	return [ablum_name, dict(zip(id_list, name_list))]

def down_load(save_path,id_url_list):
	print('开始下载...')
	# 创建一个 cookie对象来保存 cookie
	cookie = http.cookiejar.CookieJar()
	# 创建一个 cookie对象
	handler = urllib.request.HTTPCookieProcessor(cookie)
	# 构建一个 handler对象
	opener = urllib.request.build_opener(handler, urllib.request.HTTPHandler)
	urllib.request.install_opener(opener)
	#sys.stdout = io.TextIOWrapper(sys.stdout.buffer,encoding='gb18030') 
	i = 0;
	for id, name in id_url_list.items():
		i+=1
		filename = os.path.join(save_path,name+".mp3")
		print("正在下载第%d个文件：%s"%(i, filename))

		url = "http://www.pingshu8.com/bzmtv_Inc/download.asp?fid="+id
		ref_url="http://www.pingshu8.com/down_"+id+".html"
		req = urllib.request.Request(url)
		req.add_header('Referer', ref_url)
		req.add_header('User-Agent', 'Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/50.0.2661.102 Safari/537.36')

		data = opener.open(req).read()

		if not os.path.exists(os.path.dirname(filename)):
			try:
				os.makedirs(os.path.dirname(filename))
			except OSError as exc: # Guard against race condition
				if exc.errno != errno.EEXIST:
					raise
	
		open(filename, 'wb').write(data)
		time.sleep(15.0)
	print("下载完成")
	
if __name__ == '__main__':
	if len(sys.argv) != 2:
		 print('Usage: pingshu8 base_url')
		 exit(1)
	base_url = sys.argv[1]


	ablum_name,id_url_list= get_id_url(base_url)
	down_load(ablum_name,id_url_list)
```


# 使用
```bash
pingshu8.py http://www.pingshu8.com/MusicList/mmc_63_5829_1.Htm
```