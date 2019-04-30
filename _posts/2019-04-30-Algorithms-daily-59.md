---
layout:     post
title:      Algorithms daily 59
subtitle:   Algorithm 初级小卡片～ Part II  #副标题
date:       2019-04-30
author:     ASeeker
header-img: img/gandaofu_1.jpg
catalog: true
tags:
    - Algorithms
    - Leetcode
    - Array
    
---

> 参考博客[^1] [^2] [^3]

[^1]: 48, http://www.cnblogs.com/grandyang/p/4389572.html
[^2]: 206, http://www.cnblogs.com/grandyang/p/4478820.html
[^3]: 2, http://www.cnblogs.com/grandyang/p/4129891.html


## 48. Rotate Image
>You are given an n x n 2D matrix representing an image.  
Rotate the image by 90 degrees (clockwise).  
>See [***leetcode 48***][ref1] for details.   

[ref1]:https://leetcode-cn.com/problems/rotate-image/

***My Sol***   
四个对应的位置转

***Code***

```cpp
class Solution {
public:
    void rotate(vector<vector<int>>& matrix) {

    	int n = matrix.size();
    
    	for(int i = 0; i < n/2; ++i){
    		for(int j = i; j < n-1-i; ++j){
    			int temp = matrix[i][j];

    			matrix[i][j] = matrix[n-1-j][i];
    			matrix[n-1-j][i] = matrix[n-1-i][n-1-j];
    			matrix[n-1-i][n-1-j] = matrix[j][n-1-i];
    			matrix[j][n-1-i] = temp;
    		}
    	}
    }
};
```
先转置矩阵，然后每行反转一下  

```cpp
class Solution {
public:
    void rotate(vector<vector<int>>& matrix) {
        int n = matrix.size();

    	for (int i = 0; i < n; ++i)
    	{
    		for (int j = i+1; j < n; ++j)
    		{
    			swap(matrix[i][j], matrix[j][i]);
    		}
    		reverse(matrix[i].begin(), matrix[i].end());
    	}
    }
};
```

## 387. First Unique Character in a String
>Given a string, find the first non-repeating character in it and return it's index. If it doesn't exist, return -1.
>See [***leetcode 387***][ref2] for details.   

[ref2]:https://leetcode-cn.com/problems/first-unique-character-in-a-string/

***My Sol***   
hash

***Code***

```cpp
class Solution {
public:
    int firstUniqChar(string s) {
  		map<int,int> m;

  		for(auto& a:s){
  			m[a-'a']++;
  		}

  		int i = 0;
  		for (; i < s.size(); ++i)
  		{
  			/* code */
  			if(m[s[i]-'a'] == 1){
  				return i;
  			}
  		}
  		return i == s.size() ? -1 : i;
    }
};
```

## 242. Valid Anagram
>Given two strings s and t , write a function to determine if t is an anagram of s.  
>See [***leetcode 242***][ref3] for details.   

[ref3]:https://leetcode-cn.com/problems/valid-anagram/

***My Sol***   
也是hash 不过可以用长度为26的数组

***Code***

```cpp
class Solution {
public:
    bool isAnagram(string s, string t) {
        vector<int> cnt(26,0);

        for(auto& a:s)
        {
        	cnt[a-'a']++;
        }

        for(auto& a:t)
        {
        	cnt[a-'a']--;
        }

        for(auto& a:cnt)
        {
        	if(a == 0)
        	{
        		continue;
        	}
        	return false;
        }
        return true;
    }
};
```

## 206. Reverse Linked List
>Reverse a singly linked list.  
>See [***leetcode 206***][ref4] for details.   

[ref4]:https://leetcode-cn.com/problems/reverse-linked-list/

***My Sol***   
....

***Code***

```cpp
class Solution {
public:
    ListNode* reverseList(ListNode* head) {
        ListNode* ln = new ListNode(1);

        while(head){
        	ListNode* temp = ln->next;
        	ln->next = head;
        	head = head->next;
        	ln->next->next = temp;
        }
        return ln->next;
    }
};
```

学习经典的代码

```cpp
class Solution {
public:
    ListNode* reverseList(ListNode* head) {
        ListNode *newHead = NULL;
        while (head) {
            ListNode *t = head->next;
            head->next = newHead;
            newHead = head;
            head = t;
        }
        return newHead;
    }
};
```

递归解法  

```cpp
class Solution {
public:
    ListNode* reverseList(ListNode* head) {
        if (!head || !head->next) return head;
        ListNode *newHead = reverseList(head->next);
        head->next->next = head;
        head->next = NULL;
        return newHead;
    }
};
```

## 2. Add Two Numbers
>You are given two non-empty linked lists representing two non-negative integers. The digits are stored in reverse order and each of their nodes contain a single digit. Add the two numbers and return it as a linked list.  
You may assume the two numbers do not contain any leading zero, except the number 0 itself.  
>See [***leetcode 2***][ref5] for details.   

[ref5]:https://leetcode-cn.com/problems/add-two-numbers/

***My Sol***   
....

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
    ListNode* addTwoNumbers(ListNode* l1, ListNode* l2) {
        int carry = 0;
        ListNode* ln1 = l1;
        ListNode* ln2 = l2;
        ListNode* last;

        while(l1 && l2)
        {
        	carry = l1->val + l2->val + carry;
        	l1->val = l2->val = carry%10;
        	carry /= 10;
        	if(!l1->next)
        	{
        		last = l2;
        	}
        	l1 = l1->next;
        	l2 = l2->next;
        }

        if(!l1)
        {
        	l1 = l2;
        	ln1 = ln2;
        }

        while(l1 && carry)
        {
        	carry = l1->val + carry;
        	l1->val = carry%10;
        	carry /= 10;
        	if(!l1->next)
        	{
        		last = l1;
        	}
        	l1 = l1->next;
        }

        if(carry)
        {
        	last->next = new ListNode(carry);
        }

        return ln1;

    }
};
```

博客的代码漂亮得多  

```cpp
class Solution {
public:
    ListNode* addTwoNumbers(ListNode* l1, ListNode* l2) {
        ListNode *dummy = new ListNode(-1), *cur = dummy;
        int carry = 0;
        while (l1 || l2) {
            int val1 = l1 ? l1->val : 0;
            int val2 = l2 ? l2->val : 0;
            int sum = val1 + val2 + carry;
            carry = sum / 10;
            cur->next = new ListNode(sum % 10);
            cur = cur->next;
            if (l1) l1 = l1->next;
            if (l2) l2 = l2->next;
        }
        if (carry) cur->next = new ListNode(1);
        return dummy->next;
    }
};
```



## 430. Flatten a Multilevel Doubly Linked List
>You are given a doubly linked list which in addition to the next and previous pointers, it could have a child pointer, which may or may not point to a separate doubly linked list. These child lists may have one or more children of their own, and so on, to produce a multilevel data structure, as shown in the example below.  
Flatten the list so that all the nodes appear in a single-level, doubly linked list. You are given the head of the first level of the list.  
>See [***leetcode 430***][ref6] for details.   

[ref6]:https://leetcode-cn.com/problems/flatten-a-multilevel-doubly-linked-list/

***My Sol***   
用了一个栈， 先将next放入栈中，再将child放入

***Code***

```cpp
class Solution {
public:
    Node* flatten(Node* head) {
        
        if(!head)
        {
        	return head;
        }

    	Node* pre = new Node();

    	stack<Node*> st;
    	st.push(head);

    	while(!st.empty())
    	{
    		Node* n = st.top();
    		st.pop();
    		pre->next = n;
            n->prev = pre;
    		if(n->next)
    		{
    			st.push(n->next);
    		}
    		if(n->child)
    		{
    			st.push(n->child);
                n->child = NULL;
    		}
    		pre = n;
    	}
        
        head->prev = NULL;
    	return head;
    }
};
```

## 191. Number of 1 Bits
>Write a function that takes an unsigned integer and return the number of '1' bits it has (also known as the Hamming weight).
>See [***leetcode 191***][ref7] for details.   

[ref7]:https://leetcode-cn.com/problems/number-of-1-bits/

***My Sol***   
....

***Code***

```cpp
class Solution {
public:
    int hammingWeight(uint32_t n) {
        int res = 0, bit = 1;

        for(int i = 0; i < 31; ++i)
        {
        	if((bit & n) == bit)
        	{
        		res++;
        	}
        	bit <<= 1;
        }
        
        if((bit & n) == bit)
        {
        	res++;
        }

       
        return res;
    }
};
```

也可以移动n

```cpp
class Solution {
public:
    int hammingWeight(uint32_t n) {
        int res = 0;
        for (int i = 0; i < 32; ++i) {
            res += (n & 1);
            n = n >> 1;
        }
        return res;
    }
};
```



----