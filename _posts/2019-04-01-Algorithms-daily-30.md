---
layout:     post
title:      Algorithms daily 30
subtitle:   Leetcode 79 491 #副标题
date:       2019-04-01
author:     ASeeker
header-img: img/Camaro_1.png
catalog: true
tags:
    - Algorithms
    - Leetcode
    - Depth-first Search
    - Backtracking
    
---


## 79. Word Search

>Given a 2D board and a word, find if the word exists in the grid.  
The word can be constructed from letters of sequentially adjacent cell, where "adjacent" cells are those horizontally or vertically neighboring. The same letter cell may not be used more than once.  
>See [***leetcode 79***][ref3] for details.   

[ref3]:https://leetcode.com/problems/word-search/

***My Sol***  
图的深度优先搜索思路 即矩阵中向四周扩散寻找  
第一个loop暴力找到和string[0]相等的位置  
这道题还是挺简单的，和[博客的解题方法][ref4]几乎一致  
不过博客还是有两个小trick值得学习  
递归调用结束后 可以直接将flag矩阵恢复为false 这样就不用像我这样重新init了  
还有一种更省空间的办法 直接将相应位置改为'#'号   

[ref4]:http://www.cnblogs.com/grandyang/p/4332313.html

***Code***


```cpp
class Solution {
public:
    bool exist(vector<vector<char>>& board, string word) {
        
        if(board.empty() || board[0].empty()){
            return false;
        }
        
        for(int i = 0; i < board.size(); ++i){
            for(int j = 0; j < board[0].size(); ++j){
                
                if(board[i][j] == word[0]){
                    vector<vector<bool>> flag(board.size(), vector<bool>(board[0].size(), false));
                    if(existHelper(board,flag,word,0,i,j))
                        return true;
                } 
            }
        }
        return false;
    }
    
    bool existHelper(vector<vector<char>>& board, vector<vector<bool>>& flag, string &word,int n, int x, int y){
        
         if(n == word.size())
            return true;
        
        if(x < 0 || y < 0 || x >= board.size() || y >= board[0].size() || flag[x][y]){
            return false;
        }
         
        if(board[x][y] == word[n]){
            flag[x][y] = true;
            bool curFlag =  existHelper(board,flag,word,n+1,x-1,y) || existHelper(board,flag,word,n+1,x+1,y) || existHelper(board,flag,word,n+1,x,y-1) || existHelper(board,flag,word,n+1,x,y+1);
            
            if(!curFlag){
                flag[x][y] = false;
                return false;
            }
            return true;
            
        }
        return false;
    }
};
```

***Code for Learning***  

重新标记为false

```cpp
class Solution {
public:
    bool exist(vector<vector<char>>& board, string word) {
        if (board.empty() || board[0].empty()) return false;
        int m = board.size(), n = board[0].size();
        vector<vector<bool>> visited(m, vector<bool>(n, false));
        for (int i = 0; i < m; ++i) {
            for (int j = 0; j < n; ++j) {
                if (search(board, word, 0, i, j, visited)) return true;
            }
        }
        return false;
    }
    bool search(vector<vector<char>>& board, string word, int idx, int i, int j, vector<vector<bool>>& visited) {
        if (idx == word.size()) return true;
        int m = board.size(), n = board[0].size();
        if (i < 0 || j < 0 || i >= m || j >= n || visited[i][j] || board[i][j] != word[idx]) return false;
        visited[i][j] = true;
        bool res = search(board, word, idx + 1, i - 1, j, visited) 
                 || search(board, word, idx + 1, i + 1, j, visited)
                 || search(board, word, idx + 1, i, j - 1, visited)
                 || search(board, word, idx + 1, i, j + 1, visited);
        visited[i][j] = false;
        return res;
    }
};
```

也可以修改为'#‘，进一步节省空间

```cpp
class Solution {
public:
    bool exist(vector<vector<char>>& board, string word) {
        if (board.empty() || board[0].empty()) return false;
        int m = board.size(), n = board[0].size();
        for (int i = 0; i < m; ++i) {
            for (int j = 0; j < n; ++j) {
                if (search(board, word, 0, i, j)) return true;
            }
        }
        return false;
    }
    bool search(vector<vector<char>>& board, string word, int idx, int i, int j) {
        if (idx == word.size()) return true;
        int m = board.size(), n = board[i].size();
        if (i < 0 || j < 0 || i >= m || j >= n || board[i][j] != word[idx]) return false;
        char c = board[i][j];
        board[i][j] = '#';
        bool res = search(board, word, idx + 1, i - 1, j) 
                 || search(board, word, idx + 1, i + 1, j)
                 || search(board, word, idx + 1, i, j - 1)
                 || search(board, word, idx + 1, i, j + 1);
        board[i][j] = c;
        return res;
    }
};
```



## 491. Increasing Subsequences

>Given an integer array, your task is to find all the different possible increasing subsequences of the given array, and the length of an increasing subsequence should be at least 2 .  
>See [***leetcode 491***][ref1] for details.   

[ref1]:https://leetcode.com/problems/increasing-subsequences/

***My Sol***  
set保存结果 最后再转化为vector  
深度优先 递归  

***Code***

```cpp
class Solution {
public:
    vector<vector<int>> findSubsequences(vector<int>& nums) {
        
        set<vector<int>> res;
        
        vector<int> temp;
       
        findHelper(res,temp,nums,0);
        return vector<vector<int>> (res.begin(),res.end());
    }
    
    void findHelper(set<vector<int>> &res, vector<int> &temp,vector<int>& nums,int n){

        if(n >= nums.size()){
            return;
        }
        
        findHelper(res,temp,nums,n+1);
        
        if(temp.size() == 0 || temp[temp.size()-1] <= nums[n]){
            temp.push_back(nums[n]);
            if(temp.size()>1)
                res.insert(temp);
            findHelper(res,temp,nums,n+1);
            temp.pop_back();
        }
        
    }
    
};
```


***Sol***  
学习一下[博客的解法][ref2]  
最简单的解法与自己的思路较为一致


[ref2]:http://www.cnblogs.com/grandyang/p/6388103.html

```cpp
class Solution {
public:
    vector<vector<int>> findSubsequences(vector<int>& nums) {
        set<vector<int>> res;
        vector<int> out;
        helper(nums, 0, out, res);
        return vector<vector<int>>(res.begin(), res.end());
    }
    void helper(vector<int>& nums, int start, vector<int>& out, set<vector<int>>& res) {
        if (out.size() >= 2) res.insert(out);
        for (int i = start; i < nums.size(); ++i) {
            if (!out.empty() && out.back() > nums[i]) continue;
            out.push_back(nums[i]);
            helper(nums, i + 1, out, res);
            out.pop_back();
        }
    }
};
```

第二种解法  
当某个值用过之后，在该轮递归里就不允许再次使用

```cpp
class Solution {
public:
    vector<vector<int>> findSubsequences(vector<int>& nums) {
        vector<vector<int>> res;
        vector<int> out;
        helper(nums, 0, out, res);
        return res;
    }
    void helper(vector<int>& nums, int start, vector<int>& out, vector<vector<int>>& res) {
        if (out.size() >= 2) res.push_back(out);
        unordered_set<int> st;
        for (int i = start; i < nums.size(); ++i) {
            if (!out.empty() && out.back() > nums[i] || st.count(nums[i])) continue;
            out.push_back(nums[i]);
            st.insert(nums[i]);
            helper(nums, i + 1, out, res);
            out.pop_back();
        }
    }
};
```

非迭代的解法  

```cpp
class Solution {
public:
    vector<vector<int>> findSubsequences(vector<int>& nums) {
        set<vector<int>> res;
        vector<vector<int>> cur(1);
        for (int i = 0; i < nums.size(); ++i) {
            int n = cur.size();
            for (int j = 0; j < n; ++j) {
                if (!cur[j].empty() && cur[j].back() > nums[i]) continue;
                cur.push_back(cur[j]);
                cur.back().push_back(nums[i]);
                if (cur.back().size() >= 2) res.insert(cur.back());
            }
        }
        return vector<vector<int>>(res.begin(), res.end());
    }
};
```

非迭代  
不用set  
map记录其最近的位置 我的理解是即要有一串相同的数只能一直连续是 否则便进入下一个数

```cpp
class Solution {
public:
    vector<vector<int>> findSubsequences(vector<int>& nums) {
        vector<vector<int>> res, cur(1);
        unordered_map<int, int> m;
        for (int i = 0; i < nums.size(); ++i) {
            int n = cur.size();
            int start = m[nums[i]];
            m[nums[i]] = n;
            for (int j = start; j < n; ++j) {
                if (!cur[j].empty() && cur[j].back() > nums[i]) continue;
                cur.push_back(cur[j]);
                cur.back().push_back(nums[i]);
                if (cur.back().size() >= 2) res.push_back(cur.back());
            }
        }
        return res;
    }
};

```
