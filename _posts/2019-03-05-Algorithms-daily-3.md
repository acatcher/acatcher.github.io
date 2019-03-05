---
layout:     post
title:      Algorithms daily 3
subtitle:   Leetcode 63 64 #副标题
date:       2019-03-05
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
Whole classes in daytime, so still choose two simple games to play.

## 63. Unique Paths II

>A robot is located at the top-left corner of a m x n grid (marked 'Start' in the diagram below).  
The robot can only move either down or right at any point in time. The robot is trying to reach the bottom-right corner of the grid (marked 'Finish' in the diagram below).  
Now consider if some obstacles are added to the grids. How many unique paths would there be?   
An obstacle and empty space is marked as 1 and 0 respectively in the grid.  
See [**leetcode 63**][ref1] for details.  
Refer to the [***blog***][ref3] by grandyang.

[ref1]:https://leetcode.com/problems/unique-paths-ii/
[ref2]:https://leetcode.com/problems/triangle/
[ref3]:https://www.cnblogs.com/grandyang/p/4353680.html
[ref4]:https://leetcode.com/problems/minimum-path-sum/
[ref5]:http://www.cnblogs.com/grandyang/p/4353255.html

***My Sol***  
dp
A similar Q to Q62. Just add some limits.
This code is better than me, its condition judge is better.


***Code***

```cpp
class Solution {
public:
    int uniquePathsWithObstacles(vector<vector<int>>& obstacleGrid) {
        if (obstacleGrid.empty() || obstacleGrid[0].empty() || obstacleGrid[0][0] == 1) return 0;
        int m = obstacleGrid.size(), n = obstacleGrid[0].size();
        vector<long> dp(n, 0);
        dp[0] = 1;
        for (int i = 0; i < m; ++i) {
            for (int j = 0; j < n; ++j) {
                if (obstacleGrid[i][j] == 1) dp[j] = 0;
                else if (j > 0) dp[j] += dp[j - 1];
            }
        }
        return dp[n - 1];
    }
};
```
  

## 64. Minimum Path Sum  

>Given a m x n grid filled with non-negative numbers, find a path from top left to bottom right which minimizes the sum of all numbers along its path.  
Note: You can only move either down or right at any point in time.  
See [**leetcode 64**][ref4] for details.

***My Sol***  
dp  
The idea is nearly same with the previous ,add one line at each time. The dp equation can be established by `move either down or right`.  
Comparing my code the code by others, I can find something to improve.
Their codes are more concise and understandable.  
The second code is from  [***blog***][ref5] here.


***My Code***

```cpp
class Solution {
public:
    int minPathSum(vector<vector<int>>& grid) {
        
        int m = grid.size();
        int n = grid[0].size();
        
        vector<int> minsum(n,0);
        
        minsum[0] = grid[0][0];
        
        for(int i = 1; i < n; ++i)
            minsum[i] = minsum[i-1] + grid[0][i];
        
        for(int i = 1; i < m; ++i)
            for(int j = 0; j < n; ++j){
                
                if(j == 0)
                    minsum[0] += grid[i][0];
                else
                    minsum[j] = grid[i][j] + (minsum[j] < minsum[j -1]?minsum[j] : minsum[j -1]);
            }
     
        return minsum[n-1];
        
    }
};
```

***Learning Code written by others***

```cpp
class Solution {
public:
    int minPathSum(vector<vector<int> > &grid) {
        int m = grid.size(), n = grid[0].size();
        int dp[m][n];
        dp[0][0] = grid[0][0];
        for (int i = 1; i < m; ++i) dp[i][0] = grid[i][0] + dp[i - 1][0];
        for (int i = 1; i < n; ++i) dp[0][i] = grid[0][i] + dp[0][i - 1];
        for (int i = 1; i < m; ++i) {
            for (int j = 1; j < n; ++j) {
                dp[i][j] = grid[i][j] + min(dp[i - 1][j], dp[i][j - 1]);
            }
        }
        return dp[m - 1][n - 1];
    }
};
```