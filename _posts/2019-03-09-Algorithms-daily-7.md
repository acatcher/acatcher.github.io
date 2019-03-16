---
layout:     post
title:      Algorithms daily 7
subtitle:   Leetcode 39 40 #副标题
date:       2019-03-09
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



## 39. Combination Sum

>Given a set of candidate numbers (candidates) (without duplicates) and a target number (target), find all unique combinations in candidates where the candidate numbers sums to target.  
The same repeated number may be chosen from candidates unlimited number of times.  
Note:  
All numbers (including target) will be positive integers.
The solution set must not contain duplicate combinations.   
[***leetcode 39***][]

[***leetcode 39***]:https://leetcode.com/problems/combination-sum/

***My Sol***

***Code***

```cpp
class Solution {
public:
    vector<vector<int>> combinationSum(vector<int>& candidates, int target) {
        
        
        vector<vector<int>> res;
        vector<int> temp;
        
        sort(candidates.begin(), candidates.end());
        
        combinationsHelper(res, 0, temp,  candidates, target);
     
        return res;
        
    }
    
    
    void combinationsHelper(vector<vector<int>> &res, int start, vector<int> &temp, vector<int>& candidates, int remain){
        
        if(remain == 0)
        {
           res.push_back(temp);
            return;   
        }
        
        for(int i = start; i < candidates.size(); ++i){
            
            if(remain >= candidates[i]){
                temp.push_back(candidates[i]);
                combinationsHelper(res, i, temp,  candidates, remain - candidates[i]);
                temp.pop_back();  
            }
            
        }
               
    }
    
    
};
```

## 40. Combination Sum II

>Given a collection of candidate numbers (candidates) and a target number (target), find all unique combinations in candidates where the candidate numbers sums to target.  
Each number in candidates may only be used once in the combination.  
Note:  
All numbers (including target) will be positive integers.  
The solution set must not contain duplicate combinations.  
[***leetcode 40***][]

[***leetcode 40***]:https://leetcode.com/problems/combination-sum-ii/

***My Sol***

***Code***

```
class Solution {
public:
    vector<vector<int>> combinationSum2(vector<int>& candidates, int target) {
        
        vector<vector<int>> res;
        vector<int> temp;
        
        vector<int> flag(candidates.size(),0);
        
        sort(candidates.begin(), candidates.end());
        
        combination2Helper(res, temp, candidates, target, 0,flag);
        
        return res;
        
    }
    
    
    void combination2Helper(vector<vector<int>> &res, vector<int> &temp, vector<int>& candidates, int remain, int start, vector<int> &flag){
        
        
        if(remain == 0){
            res.push_back(temp);
            return;
        }
        
        for(int i = start; i < candidates.size(); ++i){
            
            if(remain < candidates[i]){ 
                return;
            }
            if(i != 0 && candidates[i] == candidates[i-1] && !flag[i-1]){
                continue;
            }
            
            temp.push_back(candidates[i]);
            flag[i] = 1;
            combination2Helper(res, temp, candidates, remain - candidates[i], i + 1, flag);
            temp.pop_back();
            flag[i] = 0;
            
            
        }
    }
        
        
    };
```


## Debug

<mark>0</mark>