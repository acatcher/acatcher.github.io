---
layout:     post
title:      Algorithms daliy 1
subtitle:   Leetcode 70 91 621 #副标题
date:       2019-03-03
author:     ASeeker
header-img: img/post-bg-debug.png
catalog: true
tags:
    - Algorithms
    - Leetcode
    - Dynamic Programming
    - Greedy
---

#  Algorithms

>Playing with leetcode ...  
>Just a green hand just for study.   
I would appreciate your comments.   
And some codes were learned from blogs. I will delete it if I infringed your rights.

## 70. Climbing Stairs
>You are climbing a stair case. It takes n steps to reach to the top.
Each time you can either climb 1 or 2 steps. In how many distinct ways can you climb to the top?  
Note: Given n will be a positive integer.  
See [**leetcode 70**][ref1] for details

 
***My Sol***  
a simple dp  
One more stair at a time, there are two conditions:  

*  treat it as a single stair
*  combine it with the previous stair

***Code***  

```c++
class Solution {
public:
    int climbStairs(int n) {
        
        int steps[n];
        if(n == 1)
            return 1;
        steps[0] = 1;
        steps[1] = 2;
        
        for(int i = 2; i < n; ++i)
            steps[i] = steps[i-1] + steps[i-2];
        
        return steps[n-1];
        
        
    }
};
```

[ref1]:https://leetcode.com/problems/climbing-stairs/
[ref2]:https://leetcode.com/problems/decode-ways/
[ref3]:https://leetcode.com/problems/task-scheduler/
[ref4]:http://www.cnblogs.com/grandyang/p/7098764.html


## 91. Decode Ways
>A message containing letters from A-Z is being encoded to numbers using the following mapping:  
>'A' -> 1
'B' -> 2  
...  
'Z' -> 26  
Given a non-empty string containing only digits, determine the total number of ways to decode it.  
See [**leetcode 91**][ref2] for details
 
 ***My Sol***  
Because the length is no more than 2, just like #70,only need to consider if it is illegal.  
e.g.
 
 * single `0`  
 * nums starts with `0`, like `01`
 * nums bigger than `26`, like `55`

***Code***

```cpp
class Solution {
public:
    int numDecodings(string s) {
        
        int length = s.size();
        
        if(s[0] == '0')
            return 0;
        
        vector<int> ways(length + 1, 0);
        ways[0] = 1;
        ways[1] = 1;
        
        for(int i = 2; i < length + 1; ++i){
            if(s[i-1] != '0')
                ways[i] = ways[i-1];
            
            if(s[i-2] == '1' || s[i-2] == '2' && s[i-1] <= '6')
                ways[i] += ways[i-2];
            
            if(ways[i] == 0)
                return 0;
 
        }

        return ways[length];
        
    }
};
```
***A Better Sol***  
O(1) space

```cpp
class Solution {
public:
    int numDecodings(string s) {
        
        if(s[0] == '0')
             return 0;
        
         int length = s.size(), pre1 = 1, pre2 = 1;
        
        for(int i = 1; i < length; ++i){
            if(s[i] == '0')
                pre1 = 0;
            if(s[i - 1] == '1' || s[i - 1] == '2' &&s[i] <= '6'){
                pre1 += pre2;
                pre2 = pre1 - pre2;
            }else
                pre2 = pre1;

        }
        return pre1;
        
    }
};
```

***Review***

* start from 1D dp
* cpp, vector
* recycle to reduce space


## 621. Task Scheduler
>Given a char array representing tasks CPU need to do. It contains capital letters A to Z where different letters represent different tasks. Tasks could be done without original order. Each task could be done in one interval. For each interval, CPU could finish one task or just be idle.  
However, there is a non-negative cooling interval n that means between two same tasks, there must be at least n intervals that CPU are doing different tasks or just be idle.  
You need to return the least number of intervals the CPU will take to finish all the given tasks.  
See [**leetcode 621**][ref3] for details

***My Sol*** 

Greedy  
Each time choose one task having the most times, use a priority queue.  
After seeing [blog][ref4] from grandyang, I find my way is complicated. What I think is like implementation in `code 1`. Since we only need to know the minimun time to run out the task, we can find the rules that we have an interval `n+1` from each task. So we can treat each `n+1` as a block excpet the last block, because in the last time, there are no more tasks. See `code 2` for implementation.

***Code1***

```cpp
class Solution {
public:
    int leastInterval(vector<char>& tasks, int n) {
        int res = 0, cycle = n + 1;
        unordered_map<char, int> m;
        priority_queue<int> q;
        for (char c : tasks) ++m[c];
        for (auto a : m) q.push(a.second);
        while (!q.empty()) {
            int cnt = 0;
            vector<int> t;
            for (int i = 0; i < cycle; ++i) {
                if (!q.empty()) {
                    t.push_back(q.top()); q.pop();
                    ++cnt;
                }
            }
            for (int d : t) {
                if (--d > 0) q.push(d);
            }
            res += q.empty() ? cnt : cycle;
        }
        return res;
    }
};
```

***Code2***

```cpp
class Solution {
public:
    int leastInterval(vector<char>& tasks, int n) {
        vector<int> times(26,0);
        for(auto c : tasks)
            times[c - 'A']++;
        
        sort(times.begin(), times.end());
        
        int mx = times[25];
        
        int i = 24;
        while(i >=0 && times[i] == mx) --i;
        
        return tasks.size() > (mx - 1) * (n + 1) + (25 - i) ? tasks.size() : (mx - 1) * (n + 1) + (25 - i) ;
        
    }
};
```