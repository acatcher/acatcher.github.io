---
layout:     post
title:      Algorithms daily 60
subtitle:   Linked List小卡片～  #副标题
date:       2019-05-01
author:     ASeeker
header-img: img/yiren_1.jpg
catalog: true
tags:
    - Algorithms
    - Leetcode
    - Linked List
    
---

五一小长假第一天hhhh  
继续收割小卡片  
昨晚和今天看完了`蚁人2`🐜  
斯科特笑死我了23333🤣 很质朴很有爱的一位父亲

> 参考博客[^1] [^2] [^3] [^4] [^5] [^6] [^7]

[^1]: 141, http://www.cnblogs.com/grandyang/p/4137187.html
[^2]: 19, http://www.cnblogs.com/grandyang/p/4606920.html
[^3]: 小结 - 链表中的双指针, https://leetcode-cn.com/explore/learn/card/linked-list/194/two-pointer-technique/748/
[^4]: 203, http://www.cnblogs.com/grandyang/p/4452252.html
[^5]: 61, http://www.cnblogs.com/grandyang/p/4355505.html
[^6]: 21, http://www.cnblogs.com/grandyang/p/4086297.html
[^7]: 138, https://leetcode-cn.com/problems/copy-list-with-random-pointer/submissions/

## 707. Design Linked List
>Design your implementation of the linked list. You can choose to use the singly linked list or the doubly linked list. A node in a singly linked list should have two attributes: val and next. val is the value of the current node, and next is a pointer/reference to the next node. If you want to use the doubly linked list, you will need one more attribute prev to indicate the previous node in the linked list. Assume all nodes in the linked list are 0-indexed.  
>See [***leetcode 707***][ref1] for details.   

[ref1]:https://leetcode-cn.com/problems/design-linked-list/

***My Sol***   
双链表  
说实话不得不吐糟一下这题，没有说清楚负数的情况  
 
***Code***

```cpp
struct DoublyListNode {
    int val;
    DoublyListNode* next;
    DoublyListNode* prev;
    DoublyListNode(int x) : val(x), prev(nullptr), next(nullptr) {}
};

class MyLinkedList {
public:
    /** Initialize your data structure here. */
    MyLinkedList() {
        head = nullptr;
    }
    
    /** Get the value of the index-th node in the linked list. If the index is invalid, return -1. */
    int get(int index) {
        DoublyListNode* temp = head;
        if(index < 0)
        {
            return -1;
        }
        while(temp && index>0)
        {
            temp = temp->next;
            index--;
        }

        if(temp)
        {
            return temp->val;
        }else{
            return -1;
        }

    }
    
    /** Add a node of value val before the first element of the linked list. After the insertion, the new node will be the first node of the linked list. */
    void addAtHead(int val) {

        DoublyListNode* dLN = new DoublyListNode(val);

        if(!head)
        {
            head = dLN;
        }else{
            head->prev = dLN;
            dLN->next = head;
            head = dLN;
        }

    }
    
    /** Append a node of value val to the last element of the linked list. */
    void addAtTail(int val) {
        DoublyListNode* dLN = new DoublyListNode(val);
        DoublyListNode* temp = head;
        while(temp->next)
        {
            temp = temp->next;
        }

        temp->next = dLN;
        dLN->prev = temp;

    }
    
    /** Add a node of value val before the index-th node in the linked list. If index equals to the length of linked list, the node will be appended to the end of linked list. If index is greater than the length, the node will not be inserted. */
    void addAtIndex(int index, int val) {
        if(index <= 0){
            addAtHead(val);
        }else{
            DoublyListNode* temp = head;
            while(index > 1 && temp)
            {
                index--;
                temp = temp->next;
            }

            if(temp)
            {
                DoublyListNode* dLN = new DoublyListNode(val);
                dLN->next = temp->next;
                dLN->prev = temp;
                if(temp->next)
                {
                    temp->next->prev = dLN;
                }
                temp->next = dLN;
            }
        }
    }
    
    /** Delete the index-th node in the linked list, if the index is valid. */
    void deleteAtIndex(int index) {
        DoublyListNode* temp = head;
        if(index < 0)
        {
            return;
        }
        if(index == 0)
        {
            head = head->next;
            delete temp;
        }else{

            while(index > 1 && temp)
            {
                temp = temp->next;
                index--;
            }

            //invalid
            if(!temp || !temp->next)
            {
                return;
            }


            if(temp->next->next)
            {
                temp->next->prev = temp;
            }

            DoublyListNode* temp1 = temp->next;
            temp->next = temp1->next;
            delete temp1;

        }

    }

private:
    DoublyListNode *head;

};
```
 
##  141. Linked List Cycle
>Given a linked list, determine if it has a cycle in it.  
>See [***leetcode 141***][ref2] for details.   

[ref2]:https://leetcode-cn.com/problems/linked-list-cycle/

***My Sol***   
经典的快慢指针登场了   
相遇问题，速度相差一，所以肯定会追到
 
***Code***
 
```cpp
class Solution {
public:
    bool hasCycle(ListNode *head) {
        
        if(!head || !head->next)
        {
            return false;
        }

        ListNode* slow = head;
        ListNode* fast = head->next;

        while(fast->next && fast->next->next)
        {
            if(fast == slow)
            {
                return true;
            }

            slow = slow->next;
            fast = fast->next->next;
        }

        return false;
    }
};
```

看到博主的解答哽咽了一下  
鲜明对比

```cpp
class Solution {
public:
    bool hasCycle(ListNode *head) {
        ListNode *slow = head, *fast = head;
        while (fast && fast->next) {
            slow = slow->next;
            fast = fast->next->next;
            if (slow == fast) return true;
        }
        return false;
    }
};
```

## 142. Linked List Cycle II
>Given a linked list, return the node where the cycle begins. If there is no cycle, return null.  
>See [***leetcode 142***][ref3] for details.   

[ref3]:https://leetcode-cn.com/problems/linked-list-cycle-ii/

***My Sol***   
很赞的一道题   
上一题的延伸   
注意路程两倍这一关键信息 画张图。。

![](https://ws3.sinaimg.cn/large/006tNc79ly1g2loz7ehkej30kg09ujre.jpg)

慢指针的运动距离很容易计算 `a+k`  
在慢指针进入环时，快指针可能已经跑了很多圈了,最终跑的路程就是`a+nb+k`  
而快指针的路程是慢的俩倍 又有`a+nb+k = 2a+2k`  
即`a = nb-k`  
这样就可以放两个指针开始跑咯，静静的等待他们再次相遇～ 


 
***Code***

```cpp
class Solution {
public:
    ListNode *detectCycle(ListNode *head) {
        
        ListNode* fast = head;
        ListNode* slow = head;
        bool flag = false;

        while(fast && fast->next)
        {
            fast = fast->next->next;
            slow = slow->next;

            if(fast == slow)
            {
                flag = true;
                break;
            }
        }

        if(!flag)
        {
            return nullptr;
        }

        fast = head;

        while(fast != slow)
        {
            fast = fast->next;
            slow = slow->next;
        }

        return fast;
    }
};
```

## 160. Intersection of Two Linked Lists
>Write a program to find the node at which the intersection of two singly linked lists begins.  
>See [***leetcode 160***][ref4] for details.   

[ref4]:https://leetcode-cn.com/problems/intersection-of-two-linked-lists/

***My Sol***   
上次用了两个长度  
这次用一下双指针，将一个连到另一个上面，代码非常的简洁   

 
***Code***

```cpp
class Solution {
public:
    ListNode *getIntersectionNode(ListNode *headA, ListNode *headB) {
        
        ListNode* l1 = headA;
        ListNode* l2 = headB;

        while(l1 != l2)
        {
            l1 = !l1 ? headB : l1->next;
            l2 = !l2 ? headA : l2->next;
        }

        return l1;
 
    }
};
```


## 19. Remove Nth Node From End of List
>Given a linked list, remove the n-th node from the end of list and return its head.  
>See [***leetcode 19***][ref5] for details.   

[ref5]:https://leetcode-cn.com/problems/remove-nth-node-from-end-of-list/

***My Sol***   
双指针，一个在另一个的后面n个  
这里为了得到prev，我移到了后面n+1个  

 
***Code***


```cpp
class Solution {
public:
    ListNode* removeNthFromEnd(ListNode* head, int n) {
        
        ListNode *h = new ListNode(1);
        h->next = head;

        ListNode* l1 = h;
        ListNode* l2 = h;

        while(n >= 0)
        {
            l2 = l2->next;
            n--;
        }

        while(l2)
        {
            l1 = l1->next;
            l2 = l2->next;
        }

        ListNode* temp = l1->next;
        l1->next = l1->next->next;
        delete temp;
        return h->next;

    }
};
```

博客是判断`->next` 以及 先判断一下是否是删除头节点  

```cpp
class Solution {
public:
    ListNode* removeNthFromEnd(ListNode* head, int n) {
        if (!head->next) return NULL;
        ListNode *pre = head, *cur = head;
        for (int i = 0; i < n; ++i) cur = cur->next;
        if (!cur) return head->next;
        while (cur->next) {
            cur = cur->next;
            pre = pre->next;
        }
        pre->next = pre->next->next;
        return head;
    }
};
```

## 小结- 链表双指针

```cpp
// Initialize slow & fast pointers
ListNode* slow = head;
ListNode* fast = head;
/**
 * Change this condition to fit specific problem.
 * Attention: remember to avoid null-pointer error
 **/
while (slow && fast && fast->next) {
    slow = slow->next;          // move slow pointer one step each time
    fast = fast->next->next;    // move fast pointer two steps each time
    if (slow == fast) {         // change this condition to fit specific problem
        return true;
    }
}
return false;   // change return value to fit specific problem
```

## 203. Remove Linked List Elements
>Remove all elements from a linked list of integers that have value val.  
>See [***leetcode 203***][ref6] for details.   

[ref6]:https://leetcode-cn.com/problems/remove-linked-list-elements/

***My Sol***   
先移动head  
再用一个尾指针和当前已经遍历到的指针  

 
***Code***

```cpp
class Solution {
public:
    ListNode* removeElements(ListNode* head, int val) {
        
        while(head && head->val == val)
        {
            head = head->next;
        }

        if(!head)
        {
            return nullptr;
        }

        ListNode* tail = head;
        ListNode* cur = head->next;

        while(cur)
        {
            if(cur->val == val)
            {
                cur = cur->next;
                continue;
            }

            tail->next = cur;
            tail = cur;
            cur = cur->next;
        }

        tail->next = nullptr
        return head;

    }
};
```

博客先加了一个`dummy`，然后用`pre->next`  这样也挺方便的  

```cpp
class Solution {
public:
    ListNode* removeElements(ListNode* head, int val) {
        ListNode *dummy = new ListNode(-1), *pre = dummy;
        dummy->next = head;
        while (pre->next) {
            if (pre->next->val == val) {
                ListNode *t = pre->next;
                pre->next = t->next;
                t->next = NULL;
                delete t;
            } else {
                pre = pre->next;
            }
        }
        return dummy->next;
    }
};
```

递归的写法  

```cpp
class Solution {
public:
    ListNode* removeElements(ListNode* head, int val) {
        if (!head) return NULL;
        head->next = removeElements(head->next, val);
        return head->val == val ? head->next : head;
    }
};
```

## 234. Palindrome Linked List
>Given a singly linked list, determine if it is a palindrome.  
>See [***leetcode 234***][ref7] for details.     

[ref7]:https://leetcode-cn.com/problems/palindrome-linked-list/

***My Sol***   
three steps:

- 找到中间位置
- reverse后半部分
- 检测是否相等

这找到中间位置就要用到快慢指针    

 
***Code***

```cpp
class Solution {
public:
    bool isPalindrome(ListNode* head) {

        if(!head)
        {
            return true;
        }

        ListNode* slow = head;
        ListNode* fast = head;

        while(fast && fast->next && fast->next->next)
        {
            slow = slow->next;
            fast = fast->next->next;
        }

        ListNode* cur = slow->next;
        slow->next = nullptr;
        //reverse
        while(cur)
        {
            ListNode* temp = cur->next;
            cur->next = slow->next;
            slow->next = cur;
            cur = temp;
        }

        //the back half part
        cur = slow->next;
        //the front half part
        slow = head;
        while(cur)
        {
            if(slow->val != cur->val)
            {
                return false;
            }
            slow = slow->next;
            cur = cur->next;
        }

        return true;


    }
};
```

## 328. Odd Even Linked List
>Given a singly linked list, group all odd nodes together followed by the even nodes. Please note here we are talking about the node number and not the value in the nodes.  
You should try to do it in place. The program should run in O(1) space complexity and O(nodes) time complexity.   
>See [***leetcode 328***][ref8] for details.     

[ref8]: https://leetcode-cn.com/problems/odd-even-linked-list/

***My Sol***   
这题自己之前已经做过一回了，思路是设一个odd指针，一个even指针，然后让even后面的连到odd或者even的后面  

 
***Code***


```cpp
class Solution {
public:
    ListNode* oddEvenList(ListNode* head) {
         if(!head || !head->next)
         {
            return head;
         }

         ListNode* oddNode = head;
         ListNode* evenNode = head->next;

         while(evenNode && evenNode->next)
         {
            ListNode* temp = oddNode->next;
            oddNode = oddNode->next = evenNode->next;
            evenNode = evenNode->next = evenNode->next->next;

            oddNode->next = temp;
         }

         return head;
    }
};
```


## 61. Rotate List
>Given a linked list, rotate the list to the right by k places, where k is non-negative.  
>See [***leetcode 61***][ref9] for details.     

[ref9]:https://leetcode-cn.com/problems/rotate-list/

***My Sol***   
这题感觉也搞的比较没意思。。 又是k超过了链表长，又是k特别大的。。。 坑了我好几发提交。。 希望题目可以说清楚一点

其实思路很简单，就是两根指针找到k的位置，然后next换一换

 
***Code***


```cpp
class Solution {
public:
    ListNode* rotateRight(ListNode* head, int k) {
        
        if(!head)
        {
            return head;
        }
        
        ListNode* l1 = head;
        ListNode* l2 = head;

        int length = 0;

        while(l2)
        {
            length++;
            l2 = l2->next;
        }

        l2 = head;

        k %= length;

        while(k > 0)
        {
            l2 = l2->next;
            k--;
        }

        if(l1 == l2)
        {
            return head;
        }

        while(l2->next)
        {
            l2 = l2->next;
            l1 = l1->next;
        }

        l2->next = head;
        head = l1->next;
        l1->next = nullptr;

        return head;
    }
};
```

哈哈哈哈，思维僵化了，这种解法很棒  题目n给的很大，不可避免就要先遍历一下链表了，既然这样，就把链表环起来，然后在该断的地方剪断，妙啊～

```cpp
class Solution {
public:
    ListNode *rotateRight(ListNode *head, int k) {
        if (!head) return NULL;
        int n = 1;
        ListNode *cur = head;
        while (cur->next) {
            ++n;
            cur = cur->next;
        }
        cur->next = head;
        int m = n - k % n;
        for (int i = 0; i < m; ++i) {
            cur = cur->next;
        }
        ListNode *newhead = cur->next;
        cur->next = NULL;
        return newhead;
    }
};
```

## 21. Merge Two Sorted Lists
>Merge two sorted linked lists and return it as a new list. The new list should be made by splicing together the nodes of the first two lists.  
>See [***leetcode 21***][ref10] for details.     

[ref10]:https://leetcode-cn.com/problems/merge-two-sorted-lists/

***My Sol***   
....

 
***Code***

```cpp
class Solution {
public:
    ListNode* mergeTwoLists(ListNode* l1, ListNode* l2) {
        ListNode* dummy = new ListNode(1);

        ListNode* tail = dummy;

        while(l1 && l2)
        {
            if(l1->val < l2->val)
            {
                tail->next = l1;
                tail = l1;
                l1 = l1->next;

            }else{
                tail->next = l2;
                tail = l2;
                l2 = l2->next;
            }

        }

        if(!l1)
        {
            l1 = l2;
        }

        tail->next = l1;

        return dummy->next;

    }
};
```

递归写法

```cpp
class Solution {
public:
    ListNode* mergeTwoLists(ListNode* l1, ListNode* l2) {
        if (!l1) return l2;
        if (!l2) return l1;
        if (l1->val < l2->val) {
            l1->next = mergeTwoLists(l1->next, l2);
            return l1;
        } else {
            l2->next = mergeTwoLists(l1, l2->next);
            return l2;
        }
    }
};
```

## 138. Copy List with Random Pointer
>A linked list is given such that each node contains an additional random pointer which could point to any node in the list or null.  
Return a deep copy of the list.  
>See [***leetcode 138***][ref11] for details.     

[ref11]:https://leetcode-cn.com/problems/copy-list-with-random-pointer/

***My Sol***   
就是hash  
很奇怪 构造方法必须要加上nullptr，不然oj会报错，这个问题以后研究一下  

 
***Code***

```cpp
class Solution {
public:
    Node* copyRandomList(Node* head) {
        map<Node*, Node*> m;
        Node* dummy = new Node(1, nullptr, nullptr);
        Node* tail = dummy;
        

        while(head)
        {
        	if(!m[head])
        	{
        		Node* n = new Node(head->val, nullptr, nullptr);
        		
        		m[head] = n;
        	}

        	if(head->random)
        	{
        		if(!m[head->random])
        		{
        			Node* n = new Node(head->random->val, nullptr, nullptr);
        			//n->val = head->random->val;
        			m[head->random] = n;
        		}
        		m[head]->random = m[head->random];
        	}	

        	tail->next = m[head];
        	tail = tail->next;
        	head = head->next;

        }

        return dummy->next;

    }
};
```

居然称为有丝分裂法  
不需要用到hash 

- 将每个复制的放在原节点的后面 
- 然后再把原节点random的拷贝节点 给后一个节点的random
- 最后再分裂，分为原来的和拷贝的 （这里的写法也特别好）

```cpp
class Solution {
public:
    Node* copyRandomList(Node* head) {
        if(!head)
        {
            return nullptr;
        }
        
        Node* cur = head;

        //copy
        while(cur)
        {
        	Node* n = new Node(cur->val, cur->next, nullptr);
        	cur->next = n;
        	cur = n->next;
        }

        //random pointer
        cur = head;
        while(cur)
        {
            if(cur->random)
            {
                cur->next->random = cur->random->next;
            }
        	cur = cur->next->next;
        }

        //split
        Node* ret = head->next;
        cur = head;

        while(cur->next)
        {
        	Node* temp = cur->next;
        	cur->next = temp->next;
        	cur = temp;
        }
        return ret;

    }
};
```


----