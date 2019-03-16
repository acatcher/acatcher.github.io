---
layout:     post
title:      Algorithms daily 8
subtitle:   Leetcode 84 85 315  #副标题
date:       2019-03-10
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


#  Algorithms

>Playing with leetcode ...  
>Just a green hand just for study.   
I would appreciate your comments.   
And some codes were learned from blogs. I will delete it if I infringed your rights.  

## 84. Largest Rectangle in Histogram
>Given n non-negative integers representing the histogram's bar height where the width of each bar is 1, find the area of largest rectangle in the histogram.
>See [***leetcode 84***][] for details. 

[***leetcode 84***]:https://leetcode.com/problems/largest-rectangle-in-histogram/

***My Sol***  
这题是真的把自己难住了。。。  
参考[博客][]  
搞了好久 安静一会等会再写分析

[博客]:http://www.cnblogs.com/grandyang/p/4322653.html

***Code***

```cpp
class Solution {
public:
    int largestRectangleArea(vector<int>& heights) {
        
        int maxArea = 0;
        
        for(int i = 0; i < heights.size(); ++i){
            
            if( i+1 < heights.size() && heights[i+1] > heights[i])
                continue;
            
            int curHeight = heights[i];
            
            for(int j = i; j >= 0 ; --j){
                
                curHeight = min(curHeight, heights[j]);
                maxArea = max(maxArea, curHeight * (i-j+1));
                
            }
        }
        return maxArea;
        
        
    }
};
```

## 85. Maximal Rectangle
>Given a 2D binary matrix filled with 0's and 1's, find the largest rectangle containing only 1's and return its area.
>See [***leetcode 85***][] for details. 

[***leetcode 85***]: https://leetcode.com/problems/maximal-rectangle/

***My Sol***  
可以说就是上面一题。。

***Code***

```cpp
class Solution {
public:
    int maximalRectangle(vector<vector<char>>& matrix) {
        if (matrix.empty() || matrix[0].empty()) return 0;
        int m = matrix.size();
        int n = matrix[0].size();
        int maxAera = 0;
        
        vector<int> hist(n,0);
        
        for(int i = 0; i < m; ++i){
            for(int j = 0; j < n; ++j){
                
                if(matrix[i][j] == '0'){ hist[j] = 0;}
                else{ hist[j]++; }
            }
            
            maxAera = max(maxAera, maxHist(hist));
            
        }
            
       return maxAera; 
        
    }
    
    int maxHist(vector<int> &hist){
        
        stack<int> st;
        int maxAera = 0;
        hist.push_back(0);
        
        for(int i = 0; i < hist.size(); ++i){
            if(st.empty() || hist[i] >= hist[st.top()]){
                st.push(i);
                continue;
            }
            int index = st.top();
            st.pop();
            maxAera = max(maxAera, hist[index] * (st.empty() ? i : i-st.top()-1 ));
            i--; 
        }
         
        return maxAera;
    }
    
    
};
```

## 315. Count of Smaller Numbers After Self
>You are given an integer array nums and you have to return a new counts array. The counts array has the property where counts[i] is the number of smaller elements to the right of nums[i].
>See [***leetcode 315***][] for details. 

[***leetcode 315***]: https://leetcode.com/problems/count-of-smaller-numbers-after-self/

***My Sol***  
倒这来

***Code***


```cpp
// Binary Search
class Solution {
public:
    vector<int> countSmaller(vector<int>& nums) {
        vector<int> t, res(nums.size());
        for (int i = nums.size() - 1; i >= 0; --i) {
            int left = 0, right = t.size();
            while (left < right) {
                int mid = left + (right - left) / 2;
                if (t[mid] >= nums[i]) right = mid;
                else left = mid + 1;
            }
            res[i] = right;
            t.insert(t.begin() + right, nums[i]);
        }
        return res;
    }
};
```