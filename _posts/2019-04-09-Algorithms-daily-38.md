---
layout:     post
title:      Algorithms daily 38
subtitle:   Leetcode 739 289 #副标题
date:       2019-04-09
author:     ASeeker
header-img: img/cat_1.jpg
catalog: true
tags:
    - Algorithms
    - Leetcode
    - Array
    - Stack
    
---

## 739. Daily Temperatures
>Given a list of daily temperatures T, return a list such that, for each day in the input, tells you how many days you would have to wait until a warmer temperature. If there is no future day for which this is possible, put 0 instead.  
For example, given the list of temperatures T = [73, 74, 75, 71, 69, 72, 76, 73], your output should be [1, 1, 4, 2, 1, 1, 0, 0].
>See [***leetcode 739***][ref1] for details.   

[ref1]:https://leetcode.com/problems/daily-temperatures/


***My Sol***  
建立一个栈  
与栈顶元素比较 如果小或者等于 入栈； 否则，pop，即那天找到了比之温度高的一天，相减就可以得到天数了


***Code***

```cpp
class Solution {
public:
    vector<int> dailyTemperatures(vector<int>& T) {
        
        vector<int> res(T.size(), 0);
        stack<int> s;
        
        for(int i = 0; i < T.size(); ++i){
            
            if(s.empty()){
                s.push(i);
            }
            
            int j = s.top();
            while(T[i] > T[j]){
                s.pop();
                res[j] = i - j;
                if(!s.empty()){
                    j = s.top();
                }else{
                    break;
                }
            }
            s.push(i);
        }
        
        return res;
    }
};
```


[博主的代码][ref2]  
自己的代码太冗余了

[ref2]:http://www.cnblogs.com/grandyang/p/8097513.html

```cpp
class Solution {
public:
    vector<int> dailyTemperatures(vector<int>& temperatures) {
        int n = temperatures.size();
        vector<int> res(n, 0);
        stack<int> st;
        for (int i = 0; i < temperatures.size(); ++i) {
            while (!st.empty() && temperatures[i] > temperatures[st.top()]) {
                auto t = st.top(); st.pop();
                res[t] = i - t;
            }
            st.push(i);
        }
        return res;
    }
};
```

## 289. Game of Life
>According to the Wikipedia's article: "The Game of Life, also known simply as Life, is a cellular automaton devised by the British mathematician John Horton Conway in 1970."  
Given a board with m by n cells, each cell has an initial state live (1) or dead (0). Each cell interacts with its eight neighbors (horizontal, vertical, diagonal) using the following four rules (taken from the above Wikipedia article):  
Any live cell with fewer than two live neighbors dies, as if caused by under-population.  
Any live cell with two or three live neighbors lives on to the next generation.  
Any live cell with more than three live neighbors dies, as if by over-population..  
Any dead cell with exactly three live neighbors becomes a live cell, as if by reproduction.  
Write a function to compute the next state (after one update) of the board given its current state. The next state is created by applying the above rules simultaneously to every cell in the current state, where births and deaths occur simultaneously.  
>See [***leetcode 289***][ref3] for details.   

[ref3]:https://leetcode.com/problems/game-of-life/


***My Sol***  
这题在算法上并不是特别难    
先计算一下每个点周围的live数量代替之前的0/1  
并且live就正 dead就非正  
最后再loop一下判断

***Code***


```cpp
class Solution {
public:
    void gameOfLife(vector<vector<int>>& board) {
        
        int m = board.size(), n = board[0].size();
        
        for(int i = 0; i < m; ++i){
            for(int j = 0; j < n; ++j){
                int liveNum = 0; 
                //calculate how many live num around it
                for(int k = 0; k < 9; k++){
                    int x = i + k/3 - 1 , y = j + k%3 - 1; //coord
                    if(x >= 0 && x < m && y >= 0 && y < n){
                        if(board[x][y] > 0){
                            liveNum++;
                        }
                    }
                }

                if(board[i][j] == 0){  //if dead 
                    board[i][j] = -liveNum;
                }else{
                    board[i][j] = liveNum;
                } 
            }
        }
    
        for(int i = 0; i < m; ++i){
            for(int j = 0; j < n; ++j){
                if(board[i][j] <= 0){
                    if(board[i][j] == -3){
                        board[i][j] = 1;
                    }else{
                        board[i][j] = 0;
                    }
                }else{
                    if(board[i][j] < 3){
                        board[i][j] = 0;
                    }else if(board[i][j] < 5){
                        board[i][j] = 1;
                    }else{
                        board[i][j] = 0;
                    }
                }
            }
        }
    }
};
```

[博主的解题][ref4]用了一个自动机，以前自己没有碰到过  
感觉很新颖  
代码如下


[ref4]:http://www.cnblogs.com/grandyang/p/4854466.html


```cpp
class Solution {
public:
    void gameOfLife(vector<vector<int> >& board) {
        int m = board.size(), n = m ? board[0].size() : 0;
        int dx[] = {-1, -1, -1, 0, 1, 1, 1, 0};
        int dy[] = {-1, 0, 1, 1, 1, 0, -1, -1};
        for (int i = 0; i < m; ++i) {
            for (int j = 0; j < n; ++j) {
                int cnt = 0;
                for (int k = 0; k < 8; ++k) {
                    int x = i + dx[k], y = j + dy[k];
                    if (x >= 0 && x < m && y >= 0 && y < n && (board[x][y] == 1 || board[x][y] == 2)) {
                        ++cnt;
                    }
                }
                if (board[i][j] && (cnt < 2 || cnt > 3)) board[i][j] = 2;
                else if (!board[i][j] && cnt == 3) board[i][j] = 3;
            }
        }
        for (int i = 0; i < m; ++i) {
            for (int j = 0; j < n; ++j) {
                board[i][j] %= 2;
            }
        }
    }
};
```
