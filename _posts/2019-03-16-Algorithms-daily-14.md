---
layout:     post
title:      Algorithms daily 14
subtitle:   Leetcode 357 413 983  #副标题
date:       2019-03-16
author:     ASeeker
header-img: img/post-bg-swift2.jpg
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
好吧我实在不能用英文强行写(zhuang)了(b),怕是以后连自己都会看不懂的,还是老老实实的用中文流畅的blog吧 ^_^ 


## 357. Count Numbers with Unique Digits
>Given a non-negative integer n, count all numbers with unique digits, x, where 0 ≤ x < 10^n.  
>See [***leetcode 357***][] for details. 

[***leetcode 357***]:https://leetcode.com/problems/count-numbers-with-unique-digits/

***My Sol***  
这道题就不说什么了。。。

***Code***

```cpp
class Solution {
public:
    int countNumbersWithUniqueDigits(int n) {
        
        vector<int> dp(n,0);
        
        if(n == 0)
            return 1;
        
        int last = 9;
        dp[0] = 10;
        
        for(int i = 2; i <= n; ++i){
            
            last *= (11-i);
            dp[i-1] = dp[i-2] + last;
            
        }
        
        return dp[n-1];
        
    }
};
```


## 413. Arithmetic Slices

>A sequence of number is called arithmetic if it consists of at least three elements and if the difference between any two consecutive elements is the same.  
For example, these are arithmetic sequence:  
1, 3, 5, 7, 9  
7, 7, 7, 7  
3, -1, -5, -9  
The following sequence is not arithmetic.  
1, 1, 2, 5, 7  
A zero-indexed array A consisting of N numbers is given. A slice of that array is any pair of integers (P, Q) such that 0 <= P < Q < N.  
A slice (P, Q) of array A is called arithmetic if the sequence:
A[P], A[p + 1], ..., A[Q - 1], A[Q] is arithmetic. In particular, this means that P + 1 < Q.  
The function should return the number of arithmetic slices in the array A.  
>See [***leetcode 413***][] for details. 

[***leetcode 413***]:https://leetcode.com/problems/arithmetic-slices/

***My Sol***  
这道题的思路也比较简单，因为题目要求切片是一个连续的区间，所以用一个dp[i]表示以i为结尾的数字可组成的数列的个数。然后对于一个新的i，判断能否与前面两个数连成新的数列。  
这道题的代码自己写的还挺简洁的。

***Code***



```cpp
class Solution {
public:
    int numberOfArithmeticSlices(vector<int>& A) {
        
        vector<int> dp(A.size(),0);
        int res = 0;
        
        for(int i = 2; i < A.size(); ++i){
            
            if((A[i] - A[i-1]) == (A[i-1] - A[i-2])){
                dp[i] = dp[i-1] + 1;
                res += dp[i];
            }else{
                dp[i] = 0;
            }
            
        }
        
        return res;
        
    }
};
```

很简单的优化一下空间

***code***

```cpp
class Solution {
public:
    int numberOfArithmeticSlices(vector<int>& A) {
        
        int pre = 0;
        int res = 0;
        
        for(int i = 2; i < A.size(); ++i){
            
            if((A[i] - A[i-1]) == (A[i-1] - A[i-2])){
                pre += 1;
                res += pre;
            }else{
                pre = 0;
            }
            
        }
        
        return res;
        
    }
};
```

## 983. Minimum Cost For Tickets

>In a country popular for train travel, you have planned some train travelling one year in advance.  The days of the year that you will travel is given as an array days.  Each day is an integer from 1 to 365.  
Train tickets are sold in 3 different ways:  
a 1-day pass is sold for costs[0] dollars;  
a 7-day pass is sold for costs[1] dollars;  
a 30-day pass is sold for costs[2] dollars.  
The passes allow that many days of consecutive travel.  For example, if we get a 7-day pass on day 2, then we can travel for 7 days: day 2, 3, 4, 5, 6, 7, and 8.  
Return the minimum number of dollars you need to travel every day in the given list of days.  
>See [***leetcode 983***][] for details. 

[***leetcode 983***]:https://leetcode.com/problems/minimum-cost-for-tickets/

***My Sol***  
今天的前面两道题有点简单，今天又是周末，于是又挑了一题玩玩  
这道题找bug花了一点时间，基本思路是dp[i]代表第i天结束时所花费的所少的money  
但是要注意向前的 七天前 和 一个月前的 dp[j] 不一定是最小的花费  
需要在向前时寻找到最小的花费
这题是leetcode上比较新的题，没看到有什么博客有详解，暂且就这样吧 :)


***Code***

```cpp
class Solution {
public:
    int mincostTickets(vector<int>& days, vector<int>& costs) {

        vector<int> dp(days.size(),0);
        dp[0] = costs[0];
        
        for(int i = 1; i < days.size(); ++i){
           
            int choose1 = dp[i-1] + costs[0];
            int choose2,choose3;
            int precost;
            
            int j = i-1;
            precost = dp[j];
            while((j >= 0) && (days[i] - days[j] <= 6)){
                precost = min(precost,dp[j]);
                j--;
            }  
            if(j == -1)
                choose2 = costs[1];
            else{
                precost = min(precost,dp[j]);
                choose2 = precost + costs[1];
            }
            
             j = i-1;
            precost = dp[j];
            while((j >= 0) && (days[i] - days[j] <= 29)){
                precost = min(precost,dp[j]);
                j--;
            }
             if(j == -1)
                choose3 = costs[2];
            else{
                precost = min(precost,dp[j]);
                choose3 = precost + costs[2];
            }
            
           
            dp[i] = min(choose1, min(choose2,choose3));
            
            
        }
        
        return dp[days.size()-1];
        
    }
};
```