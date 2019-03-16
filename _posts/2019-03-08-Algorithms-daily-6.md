---
layout:     post
title:      Algorithms daily 6
subtitle:   Leetcode 401 784 #副标题
date:       2019-03-08
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


## 401. Binary Watch

>A binary watch has 4 LEDs on the top which represent the hours (0-11), and the 6 LEDs on the bottom represent the minutes (0-59).  
Each LED represents a zero or one, with the least significant bit on the right.  
Given a non-negative integer n which represents the number of LEDs that are currently on, return all possible times the watch could represent.  
See [**leetcode 401**][] for details. 
[**leetcode 401**]: https://leetcode.com/problems/binary-watch/

***Sol***  
  
Use `Backtracking(DFS)`

***Code***  

```cpp
class Solution {
public:
    vector<string> readBinaryWatch(int num) {
        
        vector<string> res;
        
        
        
        int hours = 0;
        int minutes = 0;
        
        vector<int> flag(10,0);
        
        readWatchHelper(res, flag, num, hours, minutes,0);
        
        return res;
        
    }
    
    void readWatchHelper(vector<string> &res, vector<int> &flag, int remains, int &hours, int &minutes, int start){
        
        if(hours >= 12 || minutes >= 60)
            return;
        if(remains == 0){
            if(minutes < 10){
                string temp(to_string(hours) + ":" + "0" + to_string(minutes));
                res.push_back(temp);
            }
            else{
                string temp(to_string(hours) + ":" + to_string(minutes));
                res.push_back(temp);
            }
            
            
            return;
        }
        
        for(int i = start; i < 10; ++i){
            
            if( i < 4){
                if(flag[i] == 0){
                    hours += pow(2, i);
                    flag[i] = 1;
                    readWatchHelper(res, flag, remains - 1, hours, minutes, i + 1);
                    hours -= pow(2, i);
                    flag[i] = 0;
                }
            }
            else{
                    if(remains >= 6)
                        break;
                    else
                        if(flag[i] == 0){
                            minutes += pow(2, i - 4);
                            flag[i] = 1;
                            readWatchHelper(res, flag, remains - 1, hours, minutes, i + 1);
                            minutes -= pow(2, i - 4);
                            flag[i] = 0;
                        }
                }
            
        }
        
    }
    
};
```

***Other Sol***  


from [**blog by grandyang**][]


[**blog by grandyang**]: http://www.cnblogs.com/grandyang/p/5896454.html

***Code***

```cpp
class Solution {
public:
    vector<string> readBinaryWatch(int num) {
        vector<string> res;
        for (int h = 0; h < 12; ++h) {
            for (int m = 0; m < 60; ++m) {
                if (bitset<10>((h << 6) + m).count() == num) {
                    res.push_back(to_string(h) + (m < 10 ? ":0" : ":") + to_string(m));
                }
            }
        }
        return res;
    }
};
```

## 784. Letter Case Permutation

>Given a string S, we can transform every letter individually to be lowercase or uppercase to create another string.  Return a list of all possible strings we could create.  
>See [**leetcode 784**][] for details. 
[**leetcode 784**]: https://leetcode.com/problems/letter-case-permutation/


```cpp
class Solution {
public:
    vector<string> letterCasePermutation(string S) {
        
        
        vector<string> res;
        
        
        letterHelper(res, S, 0);
        
        return res;
        
        
    }
    
    
    void letterHelper(vector<string> &res, string temp, int start){
        
        res.push_back(temp);
        
        
        for(int i = start; i < temp.size(); ++i){
            
            if(temp[i] >= 'A' && temp[i] <= 'Z'){
                temp[i] += 32;
                letterHelper(res, temp, i + 1);
                temp[i] -= 32;
            }
            else if(temp[i] >= 'a' && temp[i] <= 'z'){
                temp[i] -= 32;
                letterHelper(res, temp, i + 1);
                temp[i] += 32;
            }
            
            
        }
        
        
        
        
    }
    
};
```


***Other Sol***  


from [**blog by grandyang**][]


[**blog by grandyang**]: https://www.cnblogs.com/grandyang/p/9065702.html

***Code***

```cpp
class Solution {
public:
    vector<string> letterCasePermutation(string S) {
        vector<string> res{""};
        for (char c : S) {
            int len = res.size();
            if (c >= '0' && c <= '9') {
                for (string &str : res) str.push_back(c);
            } else {
                for (int i = 0; i < len; ++i) {
                    res.push_back(res[i]);
                    res[i].push_back(c);
                    res[len + i].push_back(c ^ 32);
                }
            }
        }
        return res;
    }
};
```
another  

```cpp
class Solution {
public:
    vector<string> letterCasePermutation(string S) {
        vector<string> res;
        helper(S, 0, res);
        return res;
    }
    void helper(string& s, int pos, vector<string>& res) {
        if (pos == s.size()) {
            res.push_back(s);
            return;
        }
        helper(s, pos + 1, res);
        if (s[pos] > '9') {
            s[pos] ^= 32;
            helper(s, pos + 1, res);
        }
    }
};
```
another  

```cpp
class Solution {
public:
    vector<string> letterCasePermutation(string S) {
        vector<string> res;
        int cnt = 0;
        for (char c : S) {
            if (c > '9') ++cnt;
        }
        for (int i = 0; i < (1 << cnt); ++i) {
            int j = 0;
            string word = "";
            for (char c : S) {
                if (c > '9') {
                    if (((i >> j++) & 1) == 1) {
                        word.push_back(tolower(c));
                    } else {
                        word.push_back(toupper(c));
                                            }
                } else {
                    word.push_back(c);
                }
            }
            res.push_back(word);
        }
        return res;
    }
};
```
