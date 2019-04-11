---
layout:     post
title:      Algorithms daily 40
subtitle:   Leetcode 334 743  #副标题
date:       2019-04-11
author:     ASeeker
header-img: img/view_3.jpeg
catalog: true
tags:
    - Algorithms
    - Leetcode
    - Graph
    
---

## 334. Increasing Triplet Subsequence
>Given an unsorted array return whether an increasing subsequence of length 3 exists or not in the array.  
Formally the function should:    
Return true if there exists i, j, k   
such that arr[i] < arr[j] < arr[k] given 0 ≤ i < j < k ≤ n-1 else return false.  
Note: Your algorithm should run in O(n) time complexity and O(1) space complexity.    
See [***leetcode 334***][ref1] for details.   

[ref1]:https://leetcode.com/problems/increasing-triplet-subsequence/


***Sol***   
这道题自己只想出了dp的解法，但是不满足题目的要求  
[博客][ref2]给出的方法真的是挺巧妙的，用两个数记录前两个较小的数  
这道题自己以后还要多思考思考  

[ref2]:http://www.cnblogs.com/grandyang/p/5194599.html

***Code***

```cpp
class Solution {
public:
    bool increasingTriplet(vector<int>& nums) {
        int num1 = INT_MAX, num2 = INT_MAX;
        
        for(int i = 0; i < nums.size(); ++i){
            if(num1 >= nums[i]){
                num1 = nums[i];
            }else if(num2 >= nums[i]){
                num2 = nums[i];
            }else{
                return true;
            }
        }
        return false;
    }
};
```
另一种解法  
用一个数组记录前面最小的数，另一个数组记录剩余部分最大的数  
如果夹在中间，那么true

```cpp
class Solution {
public:
    bool increasingTriplet(vector<int>& nums) {
        if (nums.size() < 3) return false;
        int n = nums.size();
        vector<int> f(n, nums[0]), b(n, nums.back());
        for (int i = 1; i < n; ++i) {
            f[i] = min(f[i - 1], nums[i]);
        }
        for (int i = n - 2; i >= 0; --i) {
            b[i] = max(b[i + 1], nums[i]);
        }
        for (int i = 1; i < n-1; ++i) {
            if (nums[i] > f[i] && nums[i] < b[i]) return true;
        }
        return false;
    }
};
```

## 743. Network Delay Time
>There are N network nodes, labelled 1 to N.  
Given times, a list of travel times as directed edges times[i] = (u, v, w), where u is the source node, v is the target node, and w is the time it takes for a signal to travel from source to target.  
Now, we send a signal from a certain node K. How long will it take for all nodes to receive the signal? If it is impossible, return -1.  
See [***leetcode 743***][ref3] for details.   

[ref3]:https://leetcode.com/problems/network-delay-time/


***Sol***   
这道题其实就是一个dj算法  
代码里没有标记visited数组，感觉时候加上快，有时可能不加快，因为重复问题也不是特别大  
最后返回的值就是最长的时间  


***Code***

```cpp
class Solution {
public:
    int networkDelayTime(vector<vector<int>>& times, int N, int K) {
        
        vector<vector<int>> g(N,vector<int>(N,-1));
        vector<int>  dist(N,INT_MAX);
        
        //first point
        dist[K-1] = 0;
        queue<int> q;
        q.push(K-1);
        
        //load graph weight
        for(auto a:times){
            g[a[0]-1][a[1]-1] = a[2];
        }
        
        //dj
        while(!q.empty()){  
            int u;
            //last time
            for(int i = q.size(); i > 0; --i){
                u = q.front();
                q.pop();
            
                //find if there are smaller
                for(int v = 1; v <= N; v++){
                    if( g[u][v-1] != -1 && dist[u]+g[u][v-1] < dist[v-1]){
                        q.push(v-1);
                        dist[v-1] =  dist[u]+g[u][v-1];
                    }
                }
            }
        }
        
        int res = 0;
        for(auto a:dist){
            res = max(res,a);
        }
        
        return res == INT_MAX ? -1:res;
    }
};
```
