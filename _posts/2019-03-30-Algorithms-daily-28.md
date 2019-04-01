
---
layout:     post
title:      Algorithms daily 28
subtitle:   Leetcode 92 718 #副标题
date:       2019-03-30
author:     ASeeker
header-img: img/post-bg-map.jpg
catalog: true
tags:
    - Algorithms
    - Leetcode
    - Dynamic Programming
    - Linked List
    
---


## 92. Reverse Linked List II

>Reverse a linked list from position m to n. Do it in one-pass.
Note: 1 ≤ m ≤ n ≤ length of list.  
>See [***leetcode 92***][ref1] for details. 

[ref1]:https://leetcode.com/problems/reverse-linked-list-ii/

***My Sol***  
原地反转  
用两个变量保存 m m-1位置  
pre保存前一个用于改变next 

***Code***

```cpp
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
public:
    ListNode* reverseBetween(ListNode* head, int m, int n) {
        
        ListNode *first = NULL, *cur = head, *back = NULL, *pre = NULL;
        
        for(int i = 1; i <= n; ++i){
            if(i == m-1){ //save
                first = cur;
            }
            if(i == m){ //save
                back = cur;
            }
            if(i==n){ //when it is n
                if(first){
                    first->next = cur;
                   
                }else{
                    head = cur;
                }
                 back->next = cur->next;
            }
            
            if(i > m && i <= n){
                ListNode *temp = cur->next;
                cur->next = pre;
                pre = cur;
                cur = temp;
            }else{
                pre = cur;
                cur = cur->next;
            }
        
        }
        
        return head;
    }
};
```

***Sol***  
看了一下[博客][ref2]的解法，发现自己的解法实在是太菜了。。
有两个技巧
[ref2]:http://www.cnblogs.com/grandyang/p/4306611.html
  
* 一是在最前面加一个dummy节点 以解决头的问题
* 二是反转 也是该解法的核心 每次插入到m-1节点的后面

***Code***

```cpp
class Solution {
public:
    ListNode *reverseBetween(ListNode *head, int m, int n) {
        ListNode *dummy = new ListNode(-1), *pre = dummy;
        dummy->next = head;
        for (int i = 0; i < m - 1; ++i) pre = pre->next;
        ListNode *cur = pre->next;
        for (int i = m; i < n; ++i) {
            ListNode *t = cur->next;
            cur->next = t->next;
            t->next = pre->next;
            pre->next = t;
        }
        return dummy->next;
    }
};
```


## 718. Maximum Length of Repeated Subarray

>Given two integer arrays A and B, return the maximum length of an subarray that appears in both arrays.  
>See [***leetcode 718***][ref3] for details. 

[ref3]:https://leetcode.com/problems/maximum-length-of-repeated-subarray/

***My Sol***  
用一个二位数组`matrix[i][j]` 保存以位置i、j结尾的最长串的长度  
如果A[i] != B[j], 那么肯定为0  
如果等于，那么其值为 `1 + matrix[i-1][j-1]`   
这题自己的思路与博客提供的一致^_^

***Code***


```cpp
class Solution {
public:
    int findLength(vector<int>& A, vector<int>& B) {
        
        int m = A.size(), n = B.size();
        
        int res = 0;
        int matrix[m+1][n+1];
        
        for(int i = 0; i <= m; ++i){
            matrix[i][0] = 0;
        }
        
        for(int i = 0; i <= n; ++i){
            matrix[0][i] = 0;
        }
        
        for(int i = 1; i <=m; ++i){
            for(int j = 1; j <= n; ++j){
                if(A[i-1] == B[j-1]){
                    matrix[i][j] = 1 + matrix[i-1][j-1];
                    res = max(matrix[i][j] , res);
                }else{
                    matrix[i][j] = 0;
                }    
            }
        }
        
        return res;
        
    }
};
```
