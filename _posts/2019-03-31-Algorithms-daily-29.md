---
layout:     post
title:      Algorithms daily 29
subtitle:   Leetcode 24 718 #副标题
date:       2019-03-31
author:     ASeeker
header-img: img/post-bg-map.jpg
catalog: true
tags:
    - Algorithms
    - Leetcode
    - Linked List
    - Bit Manipulation
    
---


## 24. Swap Nodes in Pairs

>Given a linked list, swap every two adjacent nodes and return its head.  
You may not modify the values in the list's nodes, only nodes itself may be changed.  
>See [***leetcode 24***][ref1] for details.   

[ref1]:https://leetcode.com/problems/swap-nodes-in-pairs/

***My Sol***  
这种题主要要理顺节点改变的关系  

***Code***


```cpp
class Solution {
public:
    ListNode* swapPairs(ListNode* head) {
        
        ListNode *cur = head, *res = NULL;
        
        if(!head){
            return head;
        }else if(head->next){
            res = head->next;
        }else{
            return head;
        }
        
        while(cur){
            
                ListNode *next = cur->next->next;
                cur->next->next = cur;
                if(next && next->next){
                    cur->next = next->next;
                }else{
                    cur->next = next;
                    return res;
                }
                cur = next;   
            
        }
        
        return res;
        
        
    }
};
```


***Sol***  
比较一下[博客的解法][ref2]  
思路更为清晰 首先加入一个头节点  
然后改动三处的next指针  
这题也可以用递归来做  

[ref2]:http://www.cnblogs.com/grandyang/p/4441680.html

***Code***

```cpp
class Solution {
public:
    ListNode* swapPairs(ListNode* head) {
        ListNode *dummy = new ListNode(-1), *pre = dummy;
        dummy->next = head;
        while (pre->next && pre->next->next) {
            ListNode *t = pre->next->next;
            pre->next->next = t->next;
            t->next = pre->next;
            pre->next = t;
            pre = t->next;
        }
        return dummy->next;
    }
};

//递归解法
class Solution {
public:
    ListNode* swapPairs(ListNode* head) {
        if (!head || !head->next) return head;
        ListNode *t = head->next;
        head->next = swapPairs(head->next->next);
        t->next = head;
        return t;
    }
};
```

## 477. Total Hamming Distance
>The Hamming distance between two integers is the number of positions at which the corresponding bits are different.  
Now your job is to find the total Hamming distance between all pairs of the given numbers.  
>See [***leetcode 477***][ref3] for details.   


***My Sol***  
这种关于位操作的自己碰到的还不是很多  
这道题就是统计每一位上的`0`、`1`的数目 使用左移、与运算  
然后将0的数目乘以1的数目就是结果  因为只有0与1之间才有距离嘛    

***Code***

[ref3]:https://leetcode.com/problems/total-hamming-distance/

```cpp
class Solution {
public:
    int totalHammingDistance(vector<int>& nums) {
        
        int res = 0;
        if(nums.empty())
            return 0;
        
        for(int i = 0; i < 32; ++i){
            int ones = 0, zeros = 0;
            int temp = 1<<i;
            for(auto num: nums){
                if(!(num & temp))
                    zeros++;
                else{
                    ones++;
                }
            }
            res += ones* zeros;
            
        }  
        return res;
    }
};
```


***Sol***  
这道题自己的runtime有点多 看了一下官方贴的一些解答 还是有一些改进的空间  

***Code***

```cpp
class Solution {
public:
    int totalHammingDistance(vector<int>& nums) {
        vector<int> count(32, 0);
        int n = nums.size();
        int res = 0;
        for(auto num:nums){
            int i = 0;
            while(num > 0){
                count[i++] += num & 1;
                num >>= 1;
            }
        }
        
        for(auto k:count){
            res += k * (n - k);
        }
        
        return res;
    }
};
```
