---
layout:     post
title:      Algorithms daily 5
subtitle:   Leetcode 46 47 #副标题
date:       2019-03-07
author:     ASeeker
header-img: img/post-bg-debug.png
catalog: true
tags:
    - Algorithms
    - Leetcode
    - Backtracking
    
---

#  Algorithms

>Playing with leetcode ...  
>Just a green hand just for study.   
I would appreciate your comments.   
And some codes were learned from blogs. I will delete it if I infringed your rights.



## 46. Permutations

>Given a collection of distinct integers, return all possible permutations.
See [**leetcode 46**][ref1] for details. 
[ref1]:https://leetcode.com/problems/permutations/

***Sol***  
Classic problem.  
Use `Backtracking(DFS)`

***Code***  

```cpp
class Solution {
public:
    vector<vector<int>> permute(vector<int>& nums) {
        
        
        vector<vector<int>> res;
        
        int n = nums.size();
        
        vector<int> flag(n,0);
        
        vector<int> temp;
        
        permuteHelper(res, n, flag,temp,nums);
     
        return res;
    }
    
    
    
    void permuteHelper(vector<vector<int>> &res, int left, vector<int> &flag, vector<int> temp,vector<int>& nums){
        
        if(left == 0){
            res.push_back(temp);
            return;
        }
        
        for(int i = 0; i < nums.size(); ++i){
            
            if(flag[i] == 0){
                
                flag[i] = 1;
                temp.push_back(nums[i]);
                permuteHelper(res, left - 1, flag,temp,nums);
                temp.pop_back();
                flag[i] = 0;
                
            }     
         
        }

    }
    
    
};
```

***Another Sol***  
exchange

***Code*** 

```cpp
class Solution {
public:
    vector<vector<int>> permute(vector<int>& num) {
        vector<vector<int>> res;
        permuteDFS(num, 0, res);
        return res;
    }
    void permuteDFS(vector<int>& num, int start, vector<vector<int>>& res) {
        if (start >= num.size()) res.push_back(num);
        for (int i = start; i < num.size(); ++i) {
            swap(num[start], num[i]);
            permuteDFS(num, start + 1, res);
            swap(num[start], num[i]);
        }
    }
};
```


## 47. Permutations II

>Given a collection of numbers that might contain duplicates, return all possible unique permutations.  
See [**leetcode 47**][] for details. 
[**leetcode 47**]:https://leetcode.com/problems/permutations-ii/
[**blog by grandyang**]:http://www.cnblogs.com/grandyang/p/4359825.html

***Sol***  
What I think is just use a `set`, and this is so silly.  
Then I look up the [**blog by grandyang**][], the idea is:    

* Sort  
* Check the previous same digit has been used to make sure the permutation is unique  

***Code*** 

```cpp
class Solution {
public:
    vector<vector<int>> permuteUnique(vector<int>& nums) {
        
        
        vector<vector<int>> res;
        
        vector<int> temp;
        int n = nums.size();
        
        vector<int> flag(n,0);
        
        sort(nums.begin(), nums.end());
        
        permuteUniqueHelper(res, n, temp, flag, nums);
        
        return res;
        
    }
    
    
    void permuteUniqueHelper(vector<vector<int>> &res, int left, vector<int> &temp, vector<int> &flag, vector<int>& nums){
        if(left == 0){
            res.push_back(temp);
            return;
        }
        
        for(int i = 0; i < nums.size(); ++i){
            
           if(flag[i] == 1) continue;
           if(i > 0 && nums[i] == nums[i-1] && flag[i-1] == 0) continue;
            
                
           temp.push_back(nums[i]);
           flag[i] = 1;
           permuteUniqueHelper(res, left - 1, temp, flag, nums);
           temp.pop_back();
           flag[i] = 0;
            
            
        }
        
        
        
    }
    
};


```