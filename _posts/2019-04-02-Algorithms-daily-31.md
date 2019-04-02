---
layout:     post
title:      Algorithms daily 31
subtitle:   Leetcode 611 646 #副标题
date:       2019-04-02
author:     ASeeker
header-img: img/hp_1.jpeg
catalog: true
tags:
    - Algorithms
    - Leetcode
    - Array
    - Greedy
    
---

## 611. Valid Triangle Number

>Given an array consists of non-negative integers, your task is to count the number of triplets chosen from the array that can make triangles if we take them as side lengths of a triangle.  
>See [***leetcode 611***][ref1] for details.   

[ref1]:https://leetcode.com/problems/valid-triangle-number/


***My Sol***  
这道题自己没有考虑特别多 直接一个三重暴力 先排序一下  
在判断不成立后可以直接剪掉后一部分 因为那个数都已经比最小的两个数大了 后面的更不可能了  
侥幸过了oj  不过这个解法确实也是不怎么样  看了一下[博客][ref2]  

 

[ref2]:http://www.cnblogs.com/grandyang/p/7053730.html

***My Code***


```cpp
class Solution {
public:
    int triangleNumber(vector<int>& nums) {
        sort(nums.begin(),nums.end());
        int res = 0,n = nums.size();
        for(int i = 0; i < n-2; ++i){
            for(int j = i+1; j < n-1; ++j){
                for(int k = j+1; k < n; ++k){
                    if(nums[i] + nums[j] > nums[k]){
                        res++;
                    }else{
                        break;
                    }           
                    
                }
            }
        }
        return res;
    }
};
```

博客给的第一种解法  
首先一个n^2的复杂度列举所有的前两个数  
然后用一个二分在剩下的数中找到小于这两个数和的位置  


```cpp
class Solution {
public:
    int triangleNumber(vector<int>& nums) {
        int res = 0, n = nums.size();
        sort(nums.begin(), nums.end());
        for (int i = 0; i < n; ++i) {
            for (int j = i + 1; j < n; ++j) {
                int sum = nums[i] + nums[j], left = j + 1, right = n;
                while (left < right) {
                    int mid = left + (right - left) / 2;
                    if (nums[mid] < sum) left = mid + 1;
                    else right = mid;
                }
                res += right - 1 - j;
            }
        }
        return res;
    }
};
```

博客的第二种解法  这种解法比较巧妙 n^2的复杂度  
设置三个位置left right = i-1  
对i遍历    
left从0开始  
left和right逐渐逼近  
如果成立 left与right之间的都ok  

```cpp
class Solution {
public:
    int triangleNumber(vector<int>& nums) {
        int res = 0, n = nums.size();
        sort(nums.begin(), nums.end());
        for (int i = n - 1; i >= 2; --i) {
            int left = 0, right = i - 1;
            while (left < right) {
                if (nums[left] + nums[right] > nums[i]) {
                    res += right - left;
                    --right;
                } else {
                    ++left;
                }
            }
        }
        return res;
    }
};
```



## 646. Maximum Length of Pair Chain

>You are given n pairs of numbers. In every pair, the first number is always smaller than the second number.  
Now, we define a pair (c, d) can follow another pair (a, b) if and only if b < c. Chain of pairs can be formed in this fashion.  
Given a set of pairs, find the length longest chain which can be formed. You needn't use up all the given pairs. You can select pairs in any order.  
>See [***leetcode 646***][ref3] for details.   

[ref3]:https://leetcode.com/problems/maximum-length-of-pair-chain/

***My Sol***  
一道DP  
首先排序  按照第一个值  因为可以证明肯定是第一个较小的会在前面  
然后用dp[i] 表示以pairs[i] 结尾的最长串  
之后往前找 直到找到一个比之小的pairs[j][1]
 
博客直接用了一个greedy  
hhh自己看了一个related topic是dp 就盲目的用dp做了。。。 

[ref4]:http://www.cnblogs.com/grandyang/p/7381633.html

***My Code***



```cpp
class Solution {
public:
    int findLongestChain(vector<vector<int>>& pairs) {
        
        sort(pairs.begin(),pairs.end(),[](vector<int> &a, vector<int> &b){
            return a[0] < b[0];
        });
        
        int res = 0, n = pairs.size();
        int dp[n];
        if(pairs.empty() || pairs[0].empty()){
            return 0;
        }
        dp[0] = 1;
        res = 1;
        
        for(int i = 1; i < n; ++i){
            
            int j = i-1;
            dp[i] = 1;
            while(j >= 0){
                if(pairs[j][1] < pairs[i][0]){
                    dp[i] += dp[j];
                    if(dp[i] > res){
                        res = dp[i];
                    }
                    break;
                }
                j--;
            }
        }
        return res;
        
    }
};
```

直接用贪心做   
判断结束  每次使end最小 给后面的留下更大空间

```cpp
class Solution {
public:
    int findLongestChain(vector<vector<int>>& pairs) {
        int res = 0, end = INT_MIN;
        sort(pairs.begin(), pairs.end(), [](vector<int>& a, vector<int>& b) {
            return a[1] < b[1];
        });
        for (auto pair : pairs) {
            if (pair[0] > end) {
                ++res;
                end = pair[1];
            }
        }
        return res;
    }
};
```