---
layout:     post
title:      Algorithms daily 42
subtitle:   Leetcode 378 160  #副标题
date:       2019-04-13
author:     ASeeker
header-img: img/dog_2.jpg
catalog: true
tags:
    - Algorithms
    - Leetcode
    - Binary Search
    - Linked List
    
---

## 378. Kth Smallest Element in a Sorted Matrix

>Given a n x n matrix where each of the rows and columns are sorted in ascending order, find the kth smallest element in the matrix.  
Note that it is the kth smallest element in the sorted order, not the kth distinct element.  
See [***leetcode 378***][ref1] for details.   

[ref1]:https://leetcode.com/problems/kth-smallest-element-in-a-sorted-matrix/


***Sol***   
这道题没想出来怎么做。。。看了一下[博客][ref2]  
二分矩阵的最大值最小值 找mid在矩阵中的位置 这样就可以用二分来啦 
关于找mid最简单的就是对矩阵的每一行使用二分  
当然还有一种巧妙的方法，从左下角开始  
这道题没能想出来有点不应该。。。。  
明天就要去打校赛了2333，第一次尝试acm比赛   

[ref2]:http://www.cnblogs.com/grandyang/p/5727892.html


***Code***


```cpp
class Solution {
public:
    int kthSmallest(vector<vector<int>>& matrix, int k) {
        
        int left = matrix[0][0], right = matrix.back().back();
        
        while(left < right){
            
            int mid = left + (right-left)/2;
            
            int cnt = countNum(matrix,mid);
            
            if(cnt < k){
                left = mid + 1;
            }else{
                right = mid;
            }  
        }
        return left;
    }
    
    int countNum(vector<vector<int>>& matrix, int num){
        
        int n = matrix.size();
        int i = n-1, j = 0;
        int res = 0;
        
        //from the left bottom
        while(i >= 0 && j < n){
            
            if(matrix[i][j] <= num){
                
                res += (i+1);
                ++j;
            }else{
                --i;
            }
        }
        return res;
    }
    
};
```

## 160. Intersection of Two Linked Lists
>Write a program to find the node at which the intersection of two singly linked lists begins.  
See [***leetcode 160***][ref3] for details.   

[ref3]:https://leetcode.com/problems/intersection-of-two-linked-lists/

***My Sol***   
找一个easy题找回自信hhh  
先使俩长度到达相等，然后就可以开始找啦  

***Code***

```cpp
class Solution {
public:
    ListNode *getIntersectionNode(ListNode *headA, ListNode *headB) {
    
        int m = 0, n = 0;
        ListNode *pa = headA, *pb = headB;
        
        while(pa){
            m++;
            pa = pa->next;
        }
        
        while(pb){
            n++;
            pb = pb->next;
        }
        
        pa = headA, pb = headB;

        while(m > n){
            pa = pa->next;
            m--;
        }
        while(n > m){
              pb = pb->next;
              n--;
         } 

        while(pa!=pb && pa){
            pa = pa->next;
            pb = pb->next;
        }
        
        return pa;

    }
};
```

虽说是简单题，[博客][ref4]还是提供了一种特别巧妙的思路   
利用走过的路程相等，将两个链表连成一个环   

[ref4]:http://www.cnblogs.com/grandyang/p/4128461.html

```cpp
class Solution {
public:
    ListNode *getIntersectionNode(ListNode *headA, ListNode *headB) {
        if (!headA || !headB) return NULL;
        ListNode *a = headA, *b = headB;
        while (a != b) {
            a = a ? a->next : headB;
            b = b ? b->next : headA;
        }
        return a;
    }
};
```
