---
layout:     post
title:      Algorithms daily 16
subtitle:   Leetcode 199 207 #副标题
date:       2019-03-18
author:     ASeeker
header-img: img/post-bg-swift2.jpg
catalog: true
tags:
    - Algorithms
    - Leetcode
    - Tree
    - Graph
    
---

#  Algorithms

>Playing with leetcode ...  
>Just a green hand just for study.   
I would appreciate your comments.   
And some codes were learned from blogs. I will delete it if I infringed your rights.  
前一段时间做了一些dp，今天换换口味  





## 199. Binary Tree Right Side View
>Given a binary tree, imagine yourself standing on the right side of it, return the values of the nodes you can see ordered from top to bottom.
>See [***leetcode 199***][] for details. 

[***leetcode 199***]:https://leetcode.com/problems/binary-tree-right-side-view/

***My Sol***  
层次遍历  
取每层的最后一个  


***Code***



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
    vector<int> rightSideView(TreeNode* root) {
        
       
        
        queue<TreeNode*> q;
        
        vector<int> res;
         if(!root)
             return res;
        
        int cur = 1;
        int next = 0;
        
        q.push(root);
        
        TreeNode* temp;
        
        while(!q.empty()){
            
            res.push_back(q.back()->val);
            
            while(cur > 0){
                
                temp = q.front();
                q.pop();
                if(temp->left){
                    q.push(temp->left);
                    next++;
                }
                if(temp->right){
                    q.push(temp->right);
                    next++;
                }
                cur--;
            }
            cur = next;
            next = 0;
            
            
            
        }
        
       return res; 
    }
};
```



## 207. Course Schedule

>There are a total of n courses you have to take, labeled from 0 to n-1.  
Some courses may have prerequisites, for example to take course 0 you have to first take course 1, which is expressed as a pair: [0,1]  
Given the total number of courses and a list of prerequisite pairs, is it possible for you to finish all courses?

>See [***leetcode 207***][] for details. 

[***leetcode 207***]:https://leetcode.com/problems/course-schedule/

***My Sol***  
可能最近dp做多了，自己想了一种利用floyd算法检测回路，即矩阵对角线是否可以走通，oj给了一1000的数据，直接爆了。。。复杂度想想确实很高

看了下[grandyang's blog][]  
这些入度出度的概念还是挺有用的  
实际上就是拓扑排序 真的是好久没复习数据结构了  
也很久没有碰图了（打脸其实就没咋做过hhh），一些算法都没能想到  
这题考虑使用BFS DFS 看看最后有没有某个节点最后入度不为0  


[grandyang's blog]:http://www.cnblogs.com/grandyang/p/4484571.html

***BFS Code***


```cpp
class Solution {
public:
    bool canFinish(int numCourses, vector<pair<int, int>>& prerequisites) {
        
       
        vector<vector<int>> graph(numCourses);
        
        vector<int> in(numCourses,0);
        
        queue<int> q;
        
        for(auto a : prerequisites){
            
            graph[a.second].push_back(a.first);
            in[a.first]++;
        }
        
        for(int i = 0; i < numCourses; ++i){
            if (in[i]==0)
                q.push(i);
        }
        
        while(!q.empty()){
            int t = q.front();
            q.pop();
            for(auto a : graph[t]){
                in[a]--;
                if(in[a]==0)
                    q.push(a);
                
            }

        }
        
        for(auto a : in){
            if(a!= 0)
                return false;
            
        }
        
        return true;
        
        
        
    }
};
```