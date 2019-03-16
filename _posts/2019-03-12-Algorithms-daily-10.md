---
layout:     post
title:      Algorithms daily 10
subtitle:   Leetcode 139 152  #副标题
date:       2019-03-12
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


## 139. Word Break
>Given a non-empty string s and a dictionary wordDict containing a list of non-empty words, determine if s can be segmented into a space-separated sequence of one or more dictionary words.  
Note:  
The same word in the dictionary may be reused multiple times in the segmentation.
You may assume the dictionary does not contain duplicate words.  
See [***leetcode 139***][] for details. 

[***leetcode 139***]:https://leetcode.com/problems/word-break/

***Sol***  
纯dp解法

***Code***

```cpp
class Solution {
public:
    bool wordBreak(string s, vector<string>& wordDict) {
        
        unordered_set<string> wordSet(wordDict.begin(), wordDict.end());
        
        vector<bool> res(s.size()+1);    
        
        res[0] = true;
        
        for(int i = 1; i <= wordDict.size(); ++i){
            
            for(int j = 0; j < i; j++){
                if(res[j] && wordSet.count(s.substr(j, i-j))){
                    res[i] = true;
                    break;
                }

            } 
        }
            
        return res[s.size()];
        
    }
};
```
***Sol***  
带备忘的递归解法  
深度优先策略

***Code***

```cpp
class Solution {
public:
    bool wordBreak(string s, vector<string>& wordDict) {
        
        unordered_set<string> wordSet(wordDict.begin(), wordDict.end());
        
        vector<int> memo(s.size(), -1);
        
        
        
        return wordBreakHelper(s,0,wordSet,memo);
        
    }
    
    bool wordBreakHelper(string &s ,int start, unordered_set<string> &wordSet, vector<int> &memo){
        
        if(start == s.size()) return true;
        if(memo[start] != -1) return memo[start];
        
        for(int i = start; i < s.size(); ++i){
            
            if(wordSet.count(s.substr(start, i-start+1))&& wordBreakHelper(s,i+1,wordSet,memo) ){
                
                return memo[start] = 1;
            }
            
        }
        return memo[start] = 0;
        
        
    }
    
};          
```

***Sol***   
广度优先策略

***Code***

```cpp
class Solution {
public:
    bool wordBreak(string s, vector<string>& wordDict) {
        
        unordered_set<string> wordSet(wordDict.begin(), wordDict.end());
        
        queue<int> q;
        q.push(0);
        int start;
        vector<bool> visited(s.size(), false);
        
        while(!q.empty()){
            start = q.front();
            q.pop();
            if(!visited[start]){
                for(int i = start; i < s.size(); ++i){
                    if(wordSet.count(s.substr(start, i-start+1))){
                        q.push(i+1);
                        if(i+1 == s.size())
                            return true;
                    }
                }
                visited[start] = true;
            }
        }
        return false; 
    }
    
};
```


## 152. Maximum Product Subarray
>Given an integer array nums, find the contiguous subarray within an array (containing at least one number) which has the largest product.  
See [***leetcode 152***][] for details. 

[***leetcode 152***]:https://leetcode.com/problems/maximum-product-subarray/

***My Sol***  
实际上只是brute-force  
剪掉了0而已

***Code***

```cpp
class Solution {
public:
    int maxProduct(vector<int>& nums) {
        
        vector<int> res(nums.size());
        
        res[0] = nums[0];
        
        
        for(int i = 1; i < nums.size(); ++i){
            
            int temp = 1;
            res[i] = res[i-1];
            for(int j = i; j >= 0; --j){
                
                temp *= nums[j];
                if(temp > res[i])
                    res[i] = temp;
                if(temp == 0)
                    break;
            }
        }
        
        return res[nums.size() - 1];
        
    }
};
```


***Sol***  
两个dp数组

***Code***

```cpp
class Solution {
public:
    int maxProduct(vector<int>& nums) {
        
        vector<int> minRes(nums.size());
        vector<int> maxRes(nums.size());
        int res;
        
        res = minRes[0] = maxRes[0] = nums[0];
        
        for(int i = 1; i < nums.size(); ++i){
                        
            maxRes[i] = max(max(nums[i], maxRes[i-1] * nums[i]), minRes[i-1] * nums[i]);
            minRes[i] = min(min(nums[i], maxRes[i-1] * nums[i]), minRes[i-1] * nums[i]);
            if(res < maxRes[i])
                res = maxRes[i];            
        }
        
        return res;
    }
};
```

***Sol***  
空间优化

```cpp
public int maxProduct(int[] A) {
   assert A.length > 0;
   int max = A[0], min = A[0], maxAns = A[0];
   for (int i = 1; i < A.length; i++) {
      int mx = max, mn = min;
      max = Math.max(Math.max(A[i], mx * A[i]), mn * A[i]);
      min = Math.min(Math.min(A[i], mx * A[i]), mn * A[i]);
      maxAns = Math.max(max, maxAns);
   }
   return maxAns;
}
```