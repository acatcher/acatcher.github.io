---
layout:     post
title:      Algorithms daliy 2
subtitle:   Leetcode 62 120 #副标题
date:       2019-03-04
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
Just choose two simple questions for today.

## 62. Unique Paths

>A robot is located at the top-left corner of a m x n grid (marked 'Start' in the diagram below).  
The robot can only move either down or right at any point in time. The robot is trying to reach the bottom-right corner of the grid (marked 'Finish' in the diagram below).  
How many possible unique paths are there?  
See [**leetcode 62**][ref1] for details.

[ref1]:https://leetcode.com/problems/unique-paths/
[ref2]:https://leetcode.com/problems/triangle/

***My Sol***  
dp
For each grid, add its up & left number.

***Code***

```cpp
class Solution {
public:
    int uniquePaths(int m, int n) {
        
        vector<int> paths(m,1);
        
        for(int row = 1; row < n; ++row)
            for(int col = 1; col < m; ++col)
                paths[col] = paths[col] + paths[col - 1];
                      
        return paths[m - 1];
         
    }
};
```
  

## 120. Triangle  

>Given a triangle, find the minimum path sum from top to bottom. Each step you may move to adjacent numbers on the row below.  
For example, given the following triangle  
[  
     [2],  
    [3,4],  
   [6,5,7],  
  [4,1,8,3]  
]  
The minimum path sum from top to bottom is 11 (i.e., 2 + 3 + 5 + 1 = 11).  
See [**leetcode 120**][ref2] for details.

***My Sol***  
dp  
add one line at each time  
compute each item's min length in a row

***Code***

```cpp
class Solution {
public:
    int minimumTotal(vector<vector<int>>& triangle) {
        
        int n = triangle.size();
        
        if(n == 0)
            return 0;
        
        vector<int> length(n);
        
        length[0] = triangle[0][0];
        
        for(int i = 1; i < n; ++i){
            for(int j = i; j >= 0; j--)
            
                if(j == 0)
                    length[0] = length[0] + triangle[i][0];
                else if(j == i)
                    length[i] = length[i -1 ]+ triangle[i][j];
                else
                    length[j] = triangle[i][j] + ( length[j - 1] < length[j] ? length[j - 1] : length[j] );
            
        }
        
        sort(length.begin(), length.end());
        
        return length[0];
        
        
       
    }
};
```
