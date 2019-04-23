---
layout:     post
title:      Algorithms daily 52
subtitle:   Leetcode 60 78 54 143 494  #副标题
date:       2019-04-23
author:     ASeeker
header-img: img/mieba_1.jpg
catalog: true
tags:
    - Algorithms
    - Leetcode
    - Linked List
    - Depth-first Search
    - Dynamic Programming
    - Array
    - Backtracking
    
---



## 60. Permutation Sequence
>The set [1,2,3,...,n] contains a total of n! unique permutations.  
By listing and labeling all of the permutations in order, we get the following sequence for n = 3:  
"123"  
"132"  
"213"  
"231"  
"312"  
"321"  
Given n and k, return the kth permutation sequence.   
Note:  
Given n will be between 1 and 9 inclusive.  
Given k will be between 1 and n! inclusive.  
>See [***leetcode 60***][ref1] for details.   

[ref1]:https://leetcode.com/problems/permutation-sequence/

***My Sol***   
按照树每层算它的位置  
然后从string里面抹掉这个位置的数  
这题自己找了一会的bug，注意下标-1  

**补充一下string的三种用法：**

- erase(pos,n); 删除从pos开始的n个字符，比如erase(0,1)就是删除第一个字符
- erase(position);删除position处的一个字符(position是个string类型的迭代器)
- erase(first,last);删除从first到last之间的字符（first和last都是迭代器）


***Code***

```cpp
class Solution {
public:
    string getPermutation(int n, int k) {
        
        string res, s;
        int times=1;
        
        
        init(n,s,times);
        int level = n-1;
        
        k--;
        while(k > 0 && time > 0){
  
            int pos = k / times;
            res += s[pos];
            s.erase(s.begin()+pos);
            k = k%times;
            times /= level;
            level--;
        }
        res+=s;
        return res;
        
    }
    
    void init(int n, string &s, int &times){
        
        int i = 1;
        while(i<=n){
            s += '0'+i;
            ++i;
        }
        
        i = 1;
        while(i<n){
            times *= i;
            i++;
        }
        
    }
    
};
```
这题自己的思路也是和[博主][ref2]一样哒^_^  
不过呢。。就不说后面半句了(说的太多了) 看代码吧～

[ref2]:http://www.cnblogs.com/grandyang/p/4358678.html

```cpp
class Solution {
public:
    string getPermutation(int n, int k) {
        string res;
        string num = "123456789";
        vector<int> f(n, 1);
        for (int i = 1; i < n; ++i) f[i] = f[i - 1] * i;
        --k;
        for (int i = n; i >= 1; --i) {
            int j = k / f[i - 1];
            k %= f[i - 1];
            res.push_back(num[j]);
            num.erase(j, 1);
        }
        return res;
    }
};
```


## 78. Subsets
>Given a set of distinct integers, nums, return all possible subsets (the power set).  
Note: The solution set must not contain duplicate subsets.  
>See [***leetcode 78***][ref3] for details.   

[ref3]:https://leetcode.com/problems/subsets/

***My Sol***   
经典的一道题    
用了一发最简单的回溯偷个懒  


***Code***

```cpp
class Solution {
public:
    vector<vector<int>> subsets(vector<int>& nums) {
        
    	int n = nums.size();
    	vector<vector<int>> res;
    	vector<int> temp;

    	subsetsHelper(res, nums, temp, n, 0);
    	return res;

    }

    void subsetsHelper(vector<vector<int>> &res, vector<int>& nums, vector<int> temp, int n, int level){

    	if(level == n){
    		res.push_back(temp);
    		return;
    	}

    	temp.push_back(nums[level]);
    	subsetsHelper(res, nums, temp, n, level+1);
    	temp.pop_back();
    	subsetsHelper(res, nums, temp, n, level+1);


    }

};
```
下面俩代码来源于[博客][ref4]  

[ref4]:http://www.cnblogs.com/grandyang/p/4309345.html  

经典的非递归写法  

```cpp
// Non-recursion
class Solution {
public:
    vector<vector<int> > subsets(vector<int> &S) {
        vector<vector<int> > res(1);
        sort(S.begin(), S.end());
        for (int i = 0; i < S.size(); ++i) {
            int size = res.size();
            for (int j = 0; j < size; ++j) {
                res.push_back(res[j]);
                res.back().push_back(S[i]);
            }
        }
        return res;
    }
};
```

经典的利用二进制  

```cpp
class Solution {
public:
    vector<vector<int> > subsets(vector<int> &S) {
        vector<vector<int> > res;
        sort(S.begin(), S.end());
        int max = 1 << S.size();
        for (int k = 0; k < max; ++k) {
            vector<int> out = convertIntToSet(S, k);
            res.push_back(out);
        }
        return res;
    }
    vector<int> convertIntToSet(vector<int> &S, int k) {
        vector<int> sub;
        int idx = 0;
        for (int i = k; i > 0; i >>= 1) {
            if ((i & 1) == 1) {
                sub.push_back(S[idx]);
            }
            ++idx;
        }
        return sub;
    }
};
```


## 54. Spiral Matrix
>Given a matrix of m x n elements (m rows, n columns), return all elements of the matrix in spiral order.
>See [***leetcode 54***][ref5] for details.   

[ref5]:https://leetcode.com/problems/spiral-matrix/

***My Sol***   
分为四个部分削减矩阵，递归，知道矩阵没有了  


***Code***


```cpp
class Solution {
public:
    vector<int> spiralOrder(vector<vector<int>>& matrix) {

    	vector<int> res;
    	if(matrix.empty() || matrix[0].empty()){
    		return res;
    	}

        int bottom = matrix.size()-1, right = matrix[0].size()-1;

        spiralOrderHelper(matrix, res, 0, 0, bottom, 0, right);

        return res;
    }

    void spiralOrderHelper(vector<vector<int>>& matrix, vector<int> &res, int mode, int top, int bottom, int left, int right){

    	if(top > bottom || left > right){
    		return;
    	}

    	if(mode == 0){
    		for(int i = left; i <= right; ++i){
    			res.push_back(matrix[top][i]);
    		}
    		spiralOrderHelper(matrix, res, (mode+1)%4, top+1, bottom, left, right);
    	}else if(mode == 1){
    		for(int i = top; i <= bottom; ++i){
    			res.push_back(matrix[i][right]);
    		}
    		spiralOrderHelper(matrix, res, (mode+1)%4, top, bottom, left, right-1);
    	}else if(mode == 2){
    		for(int i = right; i >= left; --i){
    			res.push_back(matrix[bottom][i]);
    		}
    		spiralOrderHelper(matrix, res, (mode+1)%4, top, bottom-1, left, right);
    	}else{
    		for(int i = bottom; i >= top; --i){
    			res.push_back(matrix[i][left]);
    		}
    		spiralOrderHelper(matrix, res, (mode+1)%4, top, bottom, left+1, right);
    	}

    }

};
```

[博主的解法一][ref6]先算出了有多少圈，这种方法有点绕，第二种解法与我的思路一致，不过代码呜呜呜写的比我简洁多了

```cpp
class Solution {
public:
    vector<int> spiralOrder(vector<vector<int>>& matrix) {
        if (matrix.empty() || matrix[0].empty()) return {};
        int m = matrix.size(), n = matrix[0].size();
        vector<int> res;
        int up = 0, down = m - 1, left = 0, right = n - 1;
        while (true) {
            for (int j = left; j <= right; ++j) res.push_back(matrix[up][j]);
            if (++up > down) break;
            for (int i = up; i <= down; ++i) res.push_back(matrix[i][right]);
            if (--right < left) break;
            for (int j = right; j >= left; --j) res.push_back(matrix[down][j]);
            if (--down < up) break;
            for (int i = down; i >= up; --i) res.push_back(matrix[i][left]);
            if (++left > right) break;
        }
        return res;
    }
};
```

[ref6]:http://www.cnblogs.com/grandyang/p/4362675.html


## 143. Reorder List
>Given a singly linked list L: L0→L1→…→Ln-1→Ln,
reorder it to: L0→Ln→L1→Ln-1→L2→Ln-2→…  
You may not modify the values in the list's nodes, only nodes itself may be changed.  
>See [***leetcode 143***][ref7] for details.   

[ref7]:https://leetcode-cn.com/problems/reorder-list/

***My Sol***   
自己的思路是先用快慢指针分出前半部分和后半部分  
然后将后半部分倒序  
最后再挨个插入


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
    void reorderList(ListNode* head) {
        
        if(!head || !head->next){
            return;
        }

    	ListNode *fast = head;
    	ListNode *slow = head;

    	while(fast->next && fast->next->next){
    		slow = slow->next;
    		fast = fast->next->next;
    	}

    	//reverse the latter half part
    	ListNode *reverseTemp = slow->next;
    	slow->next = NULL;
    	while(reverseTemp){
    		ListNode *temp = reverseTemp->next;
    		reverseTemp->next = slow->next;
    		slow->next = reverseTemp;
    		reverseTemp = temp;
    	}

    	//reorder
    	ListNode *frontCur = head;
    	ListNode *latterCur = slow->next;

    	while(true){
    		ListNode *frontTemp = frontCur->next;
    		ListNode *latterTemp = latterCur->next;
    		if(latterTemp){
	    		frontCur->next = latterCur;
	    		latterCur->next = frontTemp;
	    		frontCur = frontTemp;
	    		latterCur = latterTemp;
    		}else{
    			frontCur->next = latterCur;
	    		
    			if(frontCur != slow){
    				latterCur->next = frontTemp;
	    			frontTemp->next = NULL;
    			}else{
    				latterCur->next = NULL;
    			}
    			break;
    		}

    	}

    }
};
```

[博主的第一种解法][ref8]思路上是一样的，写法上直接将两部分链表分开了 这样对于最后的插入判断方便了一点  

[ref8]:http://www.cnblogs.com/grandyang/p/4254860.html

```cpp
class Solution {
public:
    void reorderList(ListNode *head) {
        if (!head || !head->next || !head->next->next) return;
        ListNode *fast = head, *slow = head;
        while (fast->next && fast->next->next) {
            slow = slow->next;
            fast = fast->next->next;
        }
        ListNode *mid = slow->next;
        slow->next = NULL;
        ListNode *last = mid, *pre = NULL;
        while (last) {
            ListNode *next = last->next;
            last->next = pre;
            pre = last;
            last = next;
        }
        while (head && pre) {
            ListNode *next = head->next;
            head->next = pre;
            pre = pre->next;
            head->next->next = next;
            head = next;
        }
    }
};
```

第二种写法妙很多，用了一个栈，然后挨个出栈插入  
比我那不堪入目的代码好很多。。。。

```cpp
class Solution {
public:
    void reorderList(ListNode *head) {
        if (!head || !head->next || !head->next->next) return;
        stack<ListNode*> st;
        ListNode *cur = head;
        while (cur) {
            st.push(cur);
            cur = cur->next;
        }
        int cnt = ((int)st.size() - 1) / 2;
        cur = head;
        while (cnt-- > 0) {
            auto t = st.top(); st.pop();
            ListNode *next = cur->next;
            cur->next = t;
            t->next = next;
            cur = next;
        }
        st.top()->next = NULL;
    }
};
```





## 494. Target Sum
>You are given a list of non-negative integers, a1, a2, ..., an, and a target, S. Now you have 2 symbols + and -. For each integer, you should choose one from + and - as its new symbol.  
Find out how many ways to assign symbols to make sum of integers equal to target S.  
>See [***leetcode 494***][ref9] for details.   

[ref9]:https://leetcode-cn.com/problems/target-sum/

***My Sol***   
一个dfs的解法


***Code***

```cpp
class Solution {
public:
    int findTargetSumWays(vector<int>& nums, int S) {
    	int res = 0;
    	int allSum = 0;
    	int n = nums.size();
        findTargetSumWaysHelper(res, allSum, nums, S, 0, n);
        return res;
    }

    void findTargetSumWaysHelper(int &res, int allSum, vector<int> &nums, int S, int level, int n){
    	if(level == n){
    		if(allSum == S){
    			res++;
    		}
    		return;
    	}

    	findTargetSumWaysHelper(res, allSum + nums[level], nums, S, level+1, n);
    	
    	findTargetSumWaysHelper(res, allSum - nums[level], nums, S, level+1, n);
        

    }

};
```


做的时候没明白为什么给的related topic里面有dp  
so 还是喵了一眼	[博客][ref10]  
原来可以用dp，即存储一些之前的信息来减少遍历    
 
[ref10]:http://www.cnblogs.com/grandyang/p/6395843.html


```cpp
class Solution {
public:
    int findTargetSumWays(vector<int>& nums, int S) {
    	int n = nums.size();
    	vector<unordered_map<int,int>> dp(n);

    	return findTargetSumWaysHelper(dp,nums,S,0,0);

    }

    int findTargetSumWaysHelper(vector<unordered_map<int,int>> &dp, vector<int>& nums, int S, int curSum, int level){
    	if(level == nums.size()){
    		return curSum == S;
    	}

    	//same level and sum is same, use the previous result
    	if(dp[level].count(curSum)){
    		return dp[level][curSum];
    	}

    	int cnt1 = findTargetSumWaysHelper(dp,nums,S,curSum+nums[level],level+1);
    	int cnt2 = findTargetSumWaysHelper(dp,nums,S,curSum-nums[level],level+1);

    	return dp[level][curSum] = cnt1+cnt2;

    }
};
```
  
也可以直接用dp的写法来做  

```cpp
class Solution {
public:
    int findTargetSumWays(vector<int>& nums, int S) {
    	int n = nums.size();
    	unordered_map<int,int>dp;

    	dp[0] = 1;

    	for(int i = 0; i < n; ++i){
    		unordered_map<int, int> nextdp;
    		for(auto p : dp){
    			nextdp[p.first+nums[i]] += p.second;
    			nextdp[p.first-nums[i]] += p.second;
    		}
    		dp = nextdp;
    	}

    	return dp[S];

    }
};
```

## 写在最后  

明天凌晨就要上映妇联4了  
自己最期待的就是灭霸这一角色的反转了  
去年匆匆看了一下3，以我的感受，灭霸的形象使得这一系列变得deep而非一系列打着高科技超幻想旗号的爽片  
这是一个让人难以评述的角色，人们会害怕他，会恨他湮灭了无数不离不弃的生命   
一个宇宙的领袖，他肩负着常人难以想象的担当与远见，为了让宇宙更长远，为了生命得以延续，为了让人们在生死离别之时没有痛苦，他也并不想这样做，却也是不得已···  
夕阳西下之时，他的内心或许在寻找慰藉，或许在想他的女儿，又或许在忏悔，他终于可以放下背负的一切，他内心的孤独与哀伤没有人能够体会···

