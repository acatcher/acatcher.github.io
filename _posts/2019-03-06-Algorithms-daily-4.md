---
layout:     post
title:      Algorithms daily 4
subtitle:   Leetcode 22 17 #副标题
date:       2019-03-06
author:     ASeeker
header-img: img/post-bg-debug.png
catalog: true
tags:
    - Algorithms
    - Leetcode
    - Backtracking
    - String
    
---

#  Algorithms

>Playing with leetcode ...  
>Just a green hand just for study.   
I would appreciate your comments.   
And some codes were learned from blogs. I will delete it if I infringed your rights.



## 22. Generate Parentheses
>Given n pairs of parentheses, write a function to generate all combinations of well-formed parentheses.  
For example, given n = 3, a solution set is:  
[
  "((()))",
  "(()())",
  "(())()",
  "()(())",
  "()()()"
]  
See [**leetcode 22**][ref1] for details. 
[ref1]:https://leetcode.com/problems/generate-parentheses/

***My Sol***  
Use the <mark>Backtracking</mark> thoughts.  
Check if the number of left brackets is more than that of right brackets. The end condition of <mark>Recursion</mark> is an invalid intermediate sol or find a valid sol. 


***code***



```cpp
class Solution {
public:
    vector<string> generateParenthesis(int n) {
        
        vector<string> res;
        
        generateHelper(res, "", n, n);
        
        return res;
    }
    
    void generateHelper(vector<string> &res, string temp ,int left, int right){
        
        if(left > right || left < 0 )
            return;
        
        if(left == 0 && right == 0){
            res.push_back(temp);
            return;
        }

        generateHelper(res, temp + '(', left - 1, right);
        generateHelper(res, temp + ')', left, right - 1);
 
    }
    
    
};
```

***Another Clever Sol***  
Refer to the [***blog***][ref2] by grandyang.
[ref2]:https://www.cnblogs.com/grandyang/p/4444160.html
Each time find a left bracket, then add `()`. In the end, add `()` in the beginning.  

***code***

```cpp
class Solution {
public:
    vector<string> generateParenthesis(int n) {
        set<string> t;
        if (n == 0) t.insert("");
        else {
            vector<string> pre = generateParenthesis(n - 1);
            for (auto a : pre) {
                for (int i = 0; i < a.size(); ++i) {
                    if (a[i] == '(') {
                        a.insert(a.begin() + i + 1, '(');
                        a.insert(a.begin() + i + 2, ')');
                        t.insert(a);
                        a.erase(a.begin() + i + 1, a.begin() + i + 3);
                    }
                }
                t.insert("()" + a);
            }
        }
        return vector<string>(t.begin(), t.end());
    }
};
```


## 17. Letter Combinations of a Phone Number
>Given a string containing digits from 2-9 inclusive, return all possible letter combinations that the number could represent.  
A mapping of digit to letters (just like on the telephone buttons) is given below. Note that 1 does not map to any letters.  
See [**leetcode 17**][ref3] for details. 

[ref3]:https://leetcode.com/problems/letter-combinations-of-a-phone-number/

***My Sol***  
just use loop


***code***

```cpp
class Solution {
public:
    vector<string> letterCombinations(string digits) {
        
        vector<string> res;
        if(digits.empty()) return res;
        res.push_back("");
        
        string dict[] = {"", "", "abc", "def", "ghi", "jkl", "mno", "pqrs", "tuv", "wxyz"};
        
        for(int i = 0; i < digits.size(); ++i){
            vector<string> temp;
            string s = dict[digits[i] - '0'];
            for(int j = 0; j < s.size(); ++j)
                for(auto a : res)
                    temp.push_back(a + s[j]);
  
            res = temp;
        }

        return res;
    }
};
```