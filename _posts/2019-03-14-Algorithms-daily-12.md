---
layout:     post
title:      Algorithms daily 12
subtitle:   Leetcode 279 338  #副标题
date:       2019-03-14
author:     ASeeker
header-img: img/post-bg-debug.png
catalog: true
tags:
    - Algorithms
    - Leetcode
    - Dynamic Programming
    
---

#  Algorithms

>Playing with leetcode ...  
>Just a green hand just for study.   
I would appreciate your comments.   
And some codes were learned from blogs. I will delete it if I infringed your rights.  



## 279. Perfect Squares

>Given a positive integer n, find the least number of perfect square numbers (for example, 1, 4, 9, 16, ...) which sum to n.  
>See [***leetcode 279***][] for details. 

[***leetcode 279***]:https://leetcode.com/problems/perfect-squares/

***My Sol***  
清晰的dp思路  
向前减去平方数

***Code***


```cpp
class Solution {
public:
    int numSquares(int n) {
        
        vector<int> dp(n+1);
        
        dp[0] = 0;
        
        for(int i = 1; i <= n; ++i){
            
            dp[i] = dp[i-1] + 1;
            for(int j = 2; j * j <= i; j++){
                dp[i] = min(dp[i-j*j] + 1, dp[i]);
            }          
        }
        
        return dp[n];
    }
};
```



## 338. Counting Bits

>Given a non negative integer number num. For every numbers i in the range 0 ≤ i ≤ num calculate the number of 1's in their binary representation and return them as an array.  
>See [***leetcode 338***][] for details. 

[***leetcode 338***]:https://leetcode.com/problems/counting-bits/

***My Sol***  
这题自己没能想出什么好的方法，又瞄了下大神的[博客][]  
就是酱紫～～  很巧妙 利用二进制的特性 自己居然没想到。。。
00  （000）   
01  （001）  
10  （010）  
11	 （011）

1 00  
1 01  
1 10  
1 11  

copy `0-7` to `8-15`  
1 000  
1 001  
1 010  
1 011  
1 100  
1 101   
1 110  
1 111  

[博客]: http://www.cnblogs.com/grandyang/p/5294255.html

***Code***

```cpp
class Solution {
public:
    vector<int> countBits(int num) {
        
        vector<int> dp(num+1,0);
        
        dp[0] = 0;
        if(num == 0)
            return dp;
        
        dp[1] = 1;
        
        for(int i = 1; pow(2,i) <= num; ++i){
            int base = pow(2,i);
            for(int j = base; j < base * 2 && j <= num; ++j){
                dp[j] = dp[j-base] + 1;
            }
        }
        
        return dp;
    }
};
```

