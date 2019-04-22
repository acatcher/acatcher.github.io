---
layout:     post
title:      Algorithms daily 51
subtitle:   Leetcode 49 73  #副标题
date:       2019-04-22
author:     ASeeker
header-img: img/xiaomai_1.jpeg
catalog: true
tags:
    - Algorithms
    - Leetcode
    - Hash Table
    - Array
    
---


## 49. Group Anagrams
>Given an array of strings, group anagrams together.  
See [***leetcode 49***][ref1] for details.   

[ref1]:https://leetcode-cn.com/problems/group-anagrams/

***My Sol***   
这道题自己的解法是建一个hash，对字符串排序  
用排序后的字符串作为键  
map里存储索引



***Code***

```cpp
class Solution {
public:
    vector<vector<string>> groupAnagrams(vector<string>& strs) {
        
        map<string, vector<int>> m;
        //unordered_set<string> s;
        
        vector<vector<string>> res;
        
        int n = strs.size();
        
        for(int i = 0; i < n; i++){
            
            string temp = strs[i];
            
            sort(temp.begin(),temp.end());
            m[temp].push_back(i);

        }
        
        for(auto p : m){
            vector<string> temp;
            
            for(auto i : p.second){
                
                temp.push_back(strs[i]);
 
            }
            res.push_back(temp);
            
        }
        
        return res;
        
    }
};
```

又到了学习[博主的代码][ref3]的环节了～  

[ref3]:http://www.cnblogs.com/grandyang/p/4385822.html

第一种写法与自己的思路一致  

```cpp
class Solution {
public:
    vector<vector<string>> groupAnagrams(vector<string>& strs) {
        vector<vector<string>> res;
        unordered_map<string, vector<string>> m;
        for (string str : strs) {
            string t = str;
            sort(t.begin(), t.end());
            m[t].push_back(str);
        }
        for (auto a : m) {
            res.push_back(a.second);
        }
        return res;
    }
};
```

不用排序 也就是统计各个字母出现的次数

```cpp
class Solution {
public:
    vector<vector<string>> groupAnagrams(vector<string>& strs) {
        vector<vector<string>> res;
        unordered_map<string, vector<string>> m;
        for (string str : strs) {
            vector<int> cnt(26, 0);
            string t = "";
            for (char c : str) ++cnt[c - 'a'];
            for (int d : cnt) t += to_string(d) + "/";
            m[t].push_back(str);
        }
        for (auto a : m) {
            res.push_back(a.second);
        }
        return res;
    }
};
```


## 73. Set Matrix Zeroes
>Given a m x n matrix, if an element is 0, set its entire row and column to 0. Do it in-place.  
>See [***leetcode 73***][ref2] for details.   

[ref2]:https://leetcode-cn.com/problems/set-matrix-zeroes/

***My Sol***   
这道题交了三发才过，一些细节忽视了  
大致思路是先扫一遍矩阵，如果有0，使得边界为0


***Code***

```cpp
class Solution {
public:
    void setZeroes(vector<vector<int>>& matrix) {
        
        if(matrix.empty() || matrix[0].empty()){
            return;
        }
        
        int m = matrix.size(), n = matrix[0].size();
        bool col0 = false;        


        for(int j = 0; j < m; j++){
            if(matrix[j][0] == 0){
                col0 = true;
                break;
            }

        }

        //label where is 0
        for(int i = 0; i < m; ++i){
            for(int j = 1; j < n; j++){
                if(matrix[i][j] == 0){
                    matrix[i][0] = 0;
                    matrix[0][j] = 0;    
                }
            }
        }
        
        //set col 0
        //special case col 0
        for(int col = 1; col < n; col++){

            if(matrix[0][col] == 0){
                for(int row = 1; row < m; row++){
                    matrix[row][col] = 0;

                }
            }
        }

        //set row 0
        for(int row = 0; row < m; row++){
            if(matrix[row][0] == 0){
                for(int col = 1; col < n; col++){
                    matrix[row][col] = 0;
                }
            }
        }
        
        //set col 0
        if(col0){
            for(int row = 0; row < m; row++){
                    matrix[row][0] = 0;
            }
        }
        
    }
};
```

