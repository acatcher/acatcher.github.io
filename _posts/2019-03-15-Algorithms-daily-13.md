---
layout:     post
title:      Algorithms daily 13
subtitle:   Leetcode 53 746  #副标题
date:       2019-03-15
author:     ASeeker
header-img: img/post-bg-swift.jpg
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


## 53. Maximum Subarray
>Given an integer array nums, find the contiguous subarray (containing at least one number) which has the largest sum and return its sum.  
>See [***leetcode 53***][] for details. 

[***leetcode 53***]:https://leetcode.com/problems/maximum-subarray/

***My Sol***  
这道题比较简单，直接用一个dp[i]表示到i为止并且包含i的最大值即可。  
与之前做过的某题思路一致。
不过当时题目说还有一种分治可以做，自己傻乎乎想了一会怎么优化到O(logn), 后面才意识到不可能是logn的，因为光跑一遍都n了。分治可以用nlogn来做，虽然复杂度高了些，但是也算熟悉熟悉分治的思路吧。  
关于代码的质量，比较了自己写的和[博客][]的，还是有很多可以学习的地方。

[博客]:http://www.cnblogs.com/grandyang/p/4377150.html

***Code***

```cpp
class Solution {
public:
    int maxSubArray(vector<int>& nums) {
        
        if(nums.empty())
            return  0;
        vector<int> dp(nums.size());
        
        int maxRes = nums[0];
        
        dp[0] = nums[0];
        
        for(int i = 1; i< nums.size(); ++i){
            
            dp[i] = max(dp[i-1] + nums[i], nums[i]);
            
            if(dp[i] > maxRes)
                maxRes = dp[i];
        }
        return maxRes;
    }
    
};
```

***others code***

```cpp
class Solution {
public:
    int maxSubArray(vector<int>& nums) {
        int res = INT_MIN, curSum = 0;
        for (int num : nums) {
            curSum = max(curSum + num, num);
            res = max(res, curSum);
        }
        return res;
    }
};
```

分治解法

```cpp
class Solution {
public:
    int maxSubArray(vector<int>& nums) {
        if (nums.empty()) return 0;
        return helper(nums, 0, (int)nums.size() - 1);
    }
    int helper(vector<int>& nums, int left, int right) {
        if (left >= right) return nums[left];
        int mid = left + (right - left) / 2;
        int lmax = helper(nums, left, mid - 1);
        int rmax = helper(nums, mid + 1, right);
        int mmax = nums[mid], t = mmax;
        for (int i = mid - 1; i >= left; --i) {
            t += nums[i];
            mmax = max(mmax, t);
        }
        t = mmax;
        for (int i = mid + 1; i <= right; ++i) {
            t += nums[i];
            mmax = max(mmax, t);
        }
        return max(mmax, max(lmax, rmax));
    }
};
```




## 746. Min Cost Climbing Stairs
>On a staircase, the i-th step has some non-negative cost cost[i] assigned (0 indexed).  
Once you pay the cost, you can either climb one or two steps. You need to find minimum cost to reach the top of the floor, and you can either start from the step with index 0, or the step with index 1.  
See [***leetcode 746***][] for details. 

[***leetcode 746***]:https://leetcode.com/problems/maximum-subarray/


***My Sol***  
今天比较晚了，就选了两道都是easy的题目  
一道比较简单的dp  
注意一下题目的意思走到顶要再走一个，比较一下自己的代码与[博客][]的代码。。。。



[博客]:http://www.cnblogs.com/grandyang/p/4377150.html

***Code***

```cpp
class Solution {
public:
    int minCostClimbingStairs(vector<int>& cost) {
        
        vector<int> dp(cost.size(), 0);
        
        if(cost.size() <= 1)
            return 0;
        int i = 2;
        for(i = 2; i < cost.size()-1; ++i){
            dp[i] = min(dp[i-2] + cost[i-2], dp[i-1] + cost[i-1]);
            
        }
         
        dp[i] = min(dp[i-2] + cost[i-2] + cost[i], dp[i-1] + cost[i-1]);
        
        
        return dp[cost.size()-1];
         
    }
};
```

***others code***

```cpp
class Solution {
public:
    int minCostClimbingStairs(vector<int>& cost) {
        int n = cost.size();
        vector<int> dp(n + 1, 0);
        for (int i = 2; i < n + 1; ++i) {
            dp[i] = min(dp[i- 2] + cost[i - 2], dp[i - 1] + cost[i - 1]);
        }
        return dp.back();
    }
};
```
