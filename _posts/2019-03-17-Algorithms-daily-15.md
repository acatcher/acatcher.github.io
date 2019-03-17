---
layout:     post
title:      Algorithms daily 15
subtitle:   Leetcode 171 258 #副标题
date:       2019-03-17
author:     ASeeker
header-img: img/post-bg-swift2.jpg
catalog: true
tags:
    - Algorithms
    - Leetcode
    
---

#  Algorithms

>Playing with leetcode ...  
>Just a green hand just for study.   
I would appreciate your comments.   
And some codes were learned from blogs. I will delete it if I infringed your rights.  
今天下午考了ccf csp ，实在是太累太累了，两道超简单的题放松一下  
贵在坚持hhh

## 171. Excel Sheet Column Number
>Given a column title as appear in an Excel sheet, return its corresponding column number.  
For example:  
 A -> 1  
    B -> 2  
    C -> 3  
    ...  
    Z -> 26  
    AA -> 27  
    AB -> 28   
    ...  
>See [***leetcode 171***][] for details. 

[***leetcode 171***]:https://leetcode.com/problems/excel-sheet-column-number/

***My Sol***  
26进制

***Code***

```cpp
class Solution {
public:
    int titleToNumber(string s) {
        
        int size = s.size();
        
        long base = 1;
        long res = 0;
    
        
        for(int i = 0; i< size; ++i){
            
            res += base*(s[size-1-i] - 'A' + 1);
            
            base*= 26;

        }
           
        return res;
    }
};
```

## 258. Add Digits
>Given a non-negative integer num, repeatedly add all its digits until the result has only one digit.  
>See [***leetcode 258***][] for details. 

[***leetcode 258***]:https://leetcode.com/problems/add-digits/

***My Sol***  
26进制

***Code***
模10 除10

```cpp
class Solution {
public:
    int addDigits(int num) {
        
        while(num/10){
            
            int res = 0;
            do{
                res += (num%10);
                num/=10;
            }while(num/10);
            res += num;
            num = res;
        }
           
        return num;
    }
};
```

***Others Sol***  
虽然是简单的题，大神们还是有惊人的O（1）解法，不得不让人佩服。   
还是要多思考多发现  
来自 [blog][]  
[blog]:http://www.cnblogs.com/grandyang/p/4741028.html

1    1  
2    2  
3    3  
4    4  
5    5  
6    6  
7    7  
8    8     
9    9     
10    1   
11    2  
12    3      
13    4  
14    5  
15    6  
16    7  
17    8  
18    9  
19    1  
20    2  

实在是太巧妙啦，9 为一个loop  很nice啊

 

***O(1) Code***

```cpp
class Solution {
public:
    int addDigits(int num) {
        return (num == 0) ? 0 : (num - 1) % 9 + 1;
    }
};


```

