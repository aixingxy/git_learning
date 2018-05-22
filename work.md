# 2018-05-12
## 发现的问题
1. get_label_result.py中的group_label_data()将每个句子的所有意图都存放在句子为key，意图列表的list为value的字典中。key没有进行标点的去除，使得有些句子只是标点不同，实际文字是相同的。


|用户列表|
|:-:|
|admin_import|
|张莹文|
|admin_rule|
|蒋成林|
|郑啸龙|


```sql
# 查找label_sen视图中所有用户
SELECT distinct author FROM intent.label_sen;
```
```sql
# 查找label_sen视图中单用户的打标句子总数
SELECT count(DISTINCT sen_id)  FROM intent.label_sen where author='用户名';
```
```python
def fetch_set_for_one_user_one_sentence(user, sentence):
    sql_operation = SqlOperation(host=ip)
    data = sql_operation.fetch_labeled()
    # data (('句子1'， ‘意图1’， ’用户1’)， ('句子1'， ‘意图2’， ’用户1’), ...)
    user_labels = set()
    for j in range(len(data)):
      for s, i, a in data[j]:
        if s == sentence and a == author:
          user_labels.add(i)
    return user_labels
```
# 2018-05-14
1. 添加 用户评分时显示用户打标数量

mysql导入数据库
``` bash
cd path_to_xx.sql
mysql -u root -p -D intent < db_intent_180508.sql
```
网页与数据库连接
借鉴 condition_query.html 与 start.py 中的condition_query

``` bash
# 强制覆盖本地
git fetch --all
git reset --hard origin/master
git pull
```

物理删除数据库
在root权限下，切换到该目录中
使用命令：cd /var/lib/mysql/
找到data数据库目录删除
使用命令：rm -r -R data

# 2018-05-15
``` python
def test_not(s):
    if '-' in s:
        wl = s.split('-')
        if '' in wl:
            return 'sentence not like "%{}%"'.format(wl[1])
        else:
            return 'sentence like "%{}%" and sentence not like "%{}%"'.format(wl[0], wl[1])
    else:
        return 'sentence like "%{}%"'.format(s)


def test_and(str):
    if '&' in str:
        wl = str.split('&')
        n = len(wl) # and 数目
        res = ''
        for i in range(n):
            res += test_not(wl[i])
            if i < n-1:
                res = res + ' and '
        return res
    else:
        return test_not(str)

def string_to_sql(str):
    """字符串解析成sql
    ;表示或关系
    -表示非
    &表示与
    """
    wl = str.split(';')
    sql = ''
    for i in range(len(wl)):
        sql += '(' + test_and(wl[i]) + ')'
        if i < len(wl) - 1:
            sql += ' or '
    return sql
```
# 2018-05-16
1 审核
  宽度问题
  都对 功能
2 关键词批量
  更新->批量打标
  更新确认
  查询结果保存到 session
  关调试语句
  分页
3 意图选择
  编辑 改名 为 意图选择
4 条件查询
  提示：默认为所有日期
  加上意图选择
  查询结果为空：表格不更新
  删除标签 confirm 去掉
  删除句子没刷新页面
  最后一列宽度会变
5 所有的分页：
  跳转到指定页
  首页/末页：无效按钮不显示
6 用户打分脚本改进:
  显示打标数 加选项，
  选择是否运行打分（太慢）

# 2018-05-17
## python读写json文件
https://www.cnblogs.com/bigberg/p/6430095.html

## github下载指定分支
方法一：
``` bash
git clone -b 分支名字 URL
```
方法二：
``` bash
git clone URL
git checkout -b 分支名字
git pull origin 分支名字
```

## ubuntu修改文件权限
``` bash
sudo chown -R xxy:xxy dir
```

## ubuntu配置php环境
``` bash
sudo apt-get update && apt-get upgrade
sudo apt-get install php
# 验证安装
php -v
# PHP 7.0.30-0ubuntu0.16.04.1 (cli) ( NTS )
# Copyright (c) 1997-2017 The PHP Group
# Zend Engine v3.0.0, Copyright (c) 1998-2017 Zend Technologies
#     with Zend OPcache v7.0.30-0ubuntu0.16.04.1, Copyright (c) 1999-2017, by Zend Technologies
# 安装PHP7.0其他模块
sudo apt-get install -y php-pear php7.0-dev php7.0-zip php7.0-curl php7.0-gd php7.0-mysql php7.0-mcrypt php7.0-xml libapache2-mod-php7.0
```

### 安装apache2
``` bash
sudo apt-get install -y apache2
```

## 1.运行服务
`python3 n-app.py`
2. 复制
`cp -r /home/xxy/PycharmProjects/dolphin/unittest /home/sellbot/dist/app/cfgs`
3. 运行client
`python3 assistant/client.py -a 127.0.0.1 -p 10000 -c unittest`

# 2018-05-18
## 读写JSON
http://python3-cookbook.readthedocs.io/zh_CN/latest/c06/p02_read-write_json_data.html

## 将python字典转换为JSON字符串（None->null, True->true, False->false）
``` python3
import json
data = {
    'name': 'ACME',
    'shares': None,
    'price': 542.23,
    'bool': True
}
json_str = json.dumps(data)
print(json_str)
print(type(json_str))
"""
会将字典中的None自动转换成json中null，True转换成true
{"name": "ACME", "shares": null, "price": 542.23, "bool": true}
<class 'str'>
"""
```

## JSON（字符串）转换成pyton字典
``` python3
data = json.loads(json_str)
print(data)
print(type(data))
"""
{'name': 'ACME', 'shares': None, 'price': 542.23, 'bool': False}
<class 'dict'>
"""
```

# 2018-05-21
python 中的分号：如果在一行中书写多条语句，就必须用分号分隔每个句子

dc-tts https://github.com/atommutou/dc_tts

论文：https://arxiv.org/pdf/1710.08969.pdf

Tensorflow学习笔记4：分布式Tensorflow： https://www.cnblogs.com/lienhua34/p/6005351.html

TensorFlow 中三种启动图 用法： https://blog.csdn.net/lyc_yongcai/article/details/73467480

如何使用Supervisor： https://blog.csdn.net/u012436149/article/details/53341372

运行
``` python
~/anaconda2/bin/python prepo.py
```


# 2018-05-22
python join()方法用于将序列中的元素以指定的字符连接成一个新的字符串
``` python
str = '-'
seq = ("a", "b", "c")
print(str.join(seq))
# 输出
# a-b-c
```

## 文本标准化
问题
你正在处理Unicode字符串，需要确保所有字符串在底层有相同的表示。

解决方案
在Unicode中，某些字符能够用多个合法的编码表示。为了说明，考虑下面的这个例子：

>>> s1 = 'Spicy Jalape\u00f1o'
>>> s2 = 'Spicy Jalapen\u0303o'
>>> s1
'Spicy Jalapeño'
>>> s2
'Spicy Jalapeño'
>>> s1 == s2
False
>>> len(s1)
14
>>> len(s2)
15
>>>
这里的文本”Spicy Jalapeño”使用了两种形式来表示。 第一种使用整体字符”ñ”(U+00F1)，第二种使用拉丁字母”n”后面跟一个”~”的组合字符(U+0303)。

在需要比较字符串的程序中使用字符的多种表示会产生问题。 为了修正这个问题，你可以使用unicodedata模块先将文本标准化：

>>> import unicodedata
>>> t1 = unicodedata.normalize('NFC', s1)
>>> t2 = unicodedata.normalize('NFC', s2)
>>> t1 == t2
True
>>> print(ascii(t1))
'Spicy Jalape\xf1o'
>>> t3 = unicodedata.normalize('NFD', s1)
>>> t4 = unicodedata.normalize('NFD', s2)
>>> t3 == t4
True
>>> print(ascii(t3))
'Spicy Jalapen\u0303o'
>>>
normalize() 第一个参数指定字符串标准化的方式。 NFC表示字符应该是整体组成(比如可能的话就使用单一编码)，而NFD表示字符应该分解为多个组合字符表示。
## python cookbook https://python3-cookbook.readthedocs.io/zh_CN/latest/index.html
## 检索和替换
Python 的 re 模块提供了re.sub用于替换字符串中的匹配项。

re.sub(pattern, repl, string, count=0, flags=0)

参数：
pattern : 正则中的模式字符串。
repl : 替换的字符串，也可为一个函数。
string : 要被查找替换的原始字符串。
count : 模式匹配后替换的最大次数，默认 0 表示替换所有的匹配。

os.path.basename(),返回path最后的文件名

## 打包
https://blog.csdn.net/b876144622/article/details/79962642

``` sudo pip install pyinstall ```

编写test.py
``` python
# -*-coding:utf-8 -*-
import numpy as np
if __name__ == "__main__":
    a = np.array([1, 2, 3])
    b = np.array([4, 5, 6])
    print(a + b)
```
``` pyinstaller test.py  ```
报错
Unable to find "/usr/include/python2.7/pyconfig.h" when adding binary and data files.
https://github.com/pyinstaller/pyinstaller/issues/1539

``` sudo apt-get install python-dev ```
还是不行
https://www.crifan.com/use_pyinstaller_to_package_python_to_single_executable_exe/

### python 分片”与“步长”
>>>  a='0123456'
>>>  a[::-1]
Out[10]: '6543210'

## 梯度剪裁

![](.png)
https://www.cnblogs.com/lindaxin/p/7998196.html

https://blog.csdn.net/u012436149/article/details/53006953

刚开始使用github的时候不是很了解，新手一般的都会遇到这个问题Permanently added the RSA host key for IP address ‘192.30.252.128’ to the list of known hosts。其实这只是一个警告无伤大雅，继续用就是了，但是看着就是不爽，然后就想办法把他KO，一招致命。

出现的问题如下图：

![](.png)

上述那条警告的大概意思就是：警告：为IP地址192.30.252.128的主机（RSA连接的）持久添加到hosts文件中，那就来添加吧！

解决办法：

sudp vim /etc/hosts

添加一行：192.30.252.128　　github.com

效果如图：

![](.png)

tensorflow 转pd的官方文档

https://github.com/tensorflow/tensorflow/blob/master/tensorflow/python/tools/freeze_graph_test.py
https://github.com/tensorflow/models/blob/master/research/object_detection/export_inference_graph.py

tensorflow 转pd的blog
https://www.jianshu.com/p/243d4f0b656c
https://blog.csdn.net/michael_yt/article/details/74737489
https://blog.csdn.net/yjl9122/article/details/78341689

查看tensorflow ckpt文件中的变量名和对应值
https://blog.csdn.net/u010698086/article/details/77916532
