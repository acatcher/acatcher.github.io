---
layout:     post
title:      Algorithms daily 9
subtitle:   Leetcode 95 96  #副标题
date:       2019-03-11
author:     ASeeker
header-img: img/post-bg-swift2.jpg
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





## 95. Unique Binary Search Trees II

>Given an integer n, generate all structurally unique BST's (binary search trees) that store values 1 ... n.
>See [***leetcode 95***][] for details. 
[***leetcode 95***]: https://leetcode.com/problems/unique-binary-search-trees-ii/


```cpp
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
public:
    vector<TreeNode*> generateTrees(int n) {
        if (n == 0) return {};
        
        return generateHelper(1, n);
        
    }
    
    vector<TreeNode*> generateHelper(int start, int end){
        
        vector<TreeNode*> res;
        
        if(start > end) {
            res.push_back(NULL);
            return res;
        }
        if(start == end){
            TreeNode* t = new TreeNode(start);
            res.push_back(t);
            return res;   
        }
        
        int length = end - start + 1;

        
        for(int i = 0; i < length; ++i){
                
                for(auto lt : generateHelper(start, start+i-1)){
                    for(auto rt : generateHelper(start+i+1, end)){
                        TreeNode *t = new TreeNode(start + i);
                        t->left = lt;
                        t->right = rt;
                        res.push_back(t);
                    }
                }
    }
    
        return res;
    }
    
};
```


## 96. Unique Binary Search Trees

>Given n, how many structurally unique BST's (binary search trees) that store values 1 ... n?  
>See [***leetcode 96***][] for details. 

[***leetcode 96***]: https://leetcode.com/problems/unique-binary-search-trees/

***My Sol***  


***Code***


```cpp
class Solution {
public:
    int numTrees(int n) {
        
        vector<int> res(n+1,0);
        res[0] =  1;
        
        for(int i = 1; i <= n; ++i){
            for(int j = 0; j < i; ++j){
                res[i] += res[j]*res[i-1-j]; 
            }
        }
        return res[n];

    }
};
```