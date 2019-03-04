---
layout:     post
title:      Python daily 1
subtitle:   Accumulation #副标题
date:       2019-03-03
author:     ASeeker
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - python
---

# python
>Go deeper in python  
>Accumulate day by day

## print

>In python3, print is a **function**, while in python2.x, only a **key word**.

Here is the [***reference***][r1].

### def

`print(*objects, sep=' ', end='\n', file=sys.stdout)`

* objects -- one time can output many objects, use`,`to split them.  
* sep -- use what to separate objects, default is ` `.  
* end -- use what to end up with, default is `\n`.  
* file -- print to where.

### ex
```python
print("www","google","com",sep=".")
```

### format
Here is the [**reference**][r2].
>use **location**, use `*` before a list.


```python
'my name is {} ,age {}'.format('aseeker',21)
#out: 'my name is aseeker ,age 21'

'hello{1} hello{0} ok{1} ok{0}'.format('!','?')
#out: 'hello? hello! ok? ok!'

info = ['aseeker', 21]
'my name is {}, age {}'.format(*info)
#out: 'my name is aseeker, age 21'
```
---
>use **key word**, use `**` before a dict.

```python
'my name is {name}, age is {age}'.format(age=21,name='aseeker')
#out: 'my name is aseeker, age is 21'

info = {'name':'aseeker','age':21}
'my name is {name}, age is {age}'.format(**info)
#out: 'my name is aseeker, age is 21'
```
---
>filling

```python
'{1:*>10} , {0::<8}), {2:-^10}'.format('aseeker',21,'center')
#out: '********21 , aseeker:), --center--'
```
---
>precision

```python
'{:.2f}'.format(1.0 / 3)
#out: '0.33'
```
---
>use index

```python
infos = ['aseeker', 21]
'my name is {0[0]}, age is {0[1]}'.format(infos)
#out: 'my name is aseeker, age is 21'

```





[r1]:http://www.runoob.com/python/python-func-print.html
[r2]:https://www.cnblogs.com/benric/p/4965224.html