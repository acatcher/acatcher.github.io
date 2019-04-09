---
layout:     post
title:      Algorithms daily 37
subtitle:   Leetcode 179 200 #副标题
date:       2019-04-08
author:     ASeeker
header-img: img/view_1.jpeg
catalog: true
tags:
    - Algorithms
    - Leetcode
    - Sort
    - Depth-first Search
    
---

## 179. Largest Number

>Given a list of non negative integers, arrange them such that they form the largest number.  
>See [***leetcode 179***][ref1] for details.   

[ref1]:https://leetcode.com/problems/largest-number/


***My Sol***  
转化为string的大小比较即可

***Code***

```cpp
class Solution {
public:
    string largestNumber(vector<int>& nums) {
        
        sort(nums.begin(), nums.end(),[](int a, int b){
            return to_string(a) + to_string(b) > to_string(b) + to_string(a);
        });
        
        string res;
        
        for(int i = 0; i < nums.size(); ++i){
            res += to_string(nums[i]);
        }
        
        return res[0] == '0' ? "0" : res;
        
    }
};
```

先全转成string快那么一丢丢

```cpp
class Solution {
public:
    string largestNumber(vector<int>& nums) {
        
        vector<string> numsStr;
        
        for(int i = 0; i < nums.size(); ++i){
            numsStr.push_back(to_string(nums[i]));
        }
        
        sort(numsStr.begin(), numsStr.end(),[](string a, string b){
            return a + b > b + a;
        });
        
        string res;
        
        for(int i = 0; i < numsStr.size(); ++i){
            res += numsStr[i];
        }
        
        return res[0] == '0' ? "0" : res;
        
    }
};
```


## 200. Number of Islands
>Given a 2d grid map of '1's (land) and '0's (water), count the number of islands. An island is surrounded by water and is formed by connecting adjacent lands horizontally or vertically. You may assume all four edges of the grid are all surrounded by water.  
>See [***leetcode 200***][ref2] for details.   

[ref2]:https://leetcode.com/problems/number-of-islands/


***My Sol***  
对于每个碰到的1将其扩张 使其周围的1都做上标记  


***Code***

```cpp
class Solution {
public:
    int numIslands(vector<vector<char>>& grid) {
        
        if(grid.empty() || grid[0].empty()){
            return 0;
        }
        
        int m = grid.size(), n = grid[0].size();
        int res = 0;
        
        for(int i = 0; i < m; ++i){
            for(int j = 0; j < n; j++){
                if(grid[i][j] == '1'){
                    res++;
                    numIslandsHelper(grid, i, j);
                    
                }
            }
        }
        
        return res;
        
    }
    
    void numIslandsHelper(vector<vector<char>>& grid, int i, int j){
        if(i < 0 || j < 0 || i == grid.size() || j == grid[0].size()){
            return;
        }
        
        if(grid[i][j] == '#' || grid[i][j] == '0'){
            return;
        }
        
        grid[i][j] = '#';
        
        numIslandsHelper(grid, i-1, j);
        numIslandsHelper(grid, i+1, j);
        numIslandsHelper(grid, i, j-1);
        numIslandsHelper(grid, i, j+1);
        
    }
    
};
```