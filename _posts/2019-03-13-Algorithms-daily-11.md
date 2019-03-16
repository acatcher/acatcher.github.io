---
layout:     post
title:      Algorithms daily 11
subtitle:   Leetcode 121 300 #副标题
date:       2019-03-13
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


## 121. Best Time to Buy and Sell Stock
>Say you have an array for which the ith element is the price of a given stock on day i.  
If you were only permitted to complete at most one transaction (i.e., buy one and sell one share of the stock), design an algorithm to find the maximum profit.  
Note that you cannot sell a stock before you buy one.  
See [***leetcode 121***][] for details. 

[***leetcode 121***]:https://leetcode.com/problems/best-time-to-buy-and-sell-stock/

***My Sol***  


***Code***

```cpp
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        
        if(prices.size() == 0)
            return 0;
        
        int res = 0;
        int in = prices[0];
        
        for(int i = 1; i < prices.size(); ++i){
            res = max(res, prices[i] - in);
            in = min(in, prices[i]);
        }
        return res;
    }
};
```

***Sol***  


***Code***

```cpp
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        int res = 0, buy = INT_MAX;
        for (int price : prices) {
            buy = min(buy, price);
            res = max(res, price - buy);
        }
        return res;
    }
};
```




## 300. Longest Increasing Subsequence
>Given an unsorted array of integers, find the length of longest increasing subsequence.  
>See [***leetcode 300***][] for details. 

[***leetcode 300***]:https://leetcode.com/problems/longest-increasing-subsequence/

***My Sol***  
Fail to find a sol better than brute-force.  
So I refer to the blog.


***Sol 1***  
just like bf
  

***Code***

```cpp
class Solution {
public:
    int lengthOfLIS(vector<int>& nums) {
        vector<int> dp(nums.size(), 1);
        int res = 0;
        for (int i = 0; i < nums.size(); ++i) {
            for (int j = 0; j < i; ++j) {
                if (nums[i] > nums[j]) {
                    dp[i] = max(dp[i], dp[j] + 1);
                }
            }
            res = max(res, dp[i]);
        }
        return res;
    }
};
```

***Sol 2***  
use dp's length to represent the result
  

***Code***


```cpp
class Solution {
public:
    int lengthOfLIS(vector<int>& nums) {
        
        if(nums.size() == 0)
            return 0;
        
        vector<int> dp({nums[0]});
        
        for(int i = 1; i < nums.size(); ++i){
            
            if(nums[i] > dp[dp.size()-1])
                dp.push_back(nums[i]);
            else if(nums[i] < dp[0])
                dp[0] = nums[i];
            else{
                int left = 0, right = dp.size()-1;
                while(left < right){
                    int mid = left + (right - left)/2;
                    if(nums[i] <= dp[mid])
                        right = mid;
                    else
                        left = mid + 1;
                }
                dp[left] = nums[i]; 
            }
        }
        
        return dp.size();
    }
};
```