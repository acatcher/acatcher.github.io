---
layout:     post
title:      Algorithms daily 58
subtitle:   Algorithm 初级小卡片～ Part I  #副标题
date:       2019-04-29
author:     ASeeker
header-img: img/zhizhuxia_1.jpg
catalog: true
tags:
    - Algorithms
    - Leetcode
    - Array
    
---

虽然一堆easy题，但还是有蛮多让人意想不到精彩解法  
今天还幸运的碰到了leetcode的开山之题哈哈哈哈

> 参考博客[^1] [^2] [^3]

[^1]: 122, http://www.cnblogs.com/grandyang/p/4280803.html
[^2]: 217, http://www.cnblogs.com/grandyang/p/4537029.html
[^3]: http://www.cnblogs.com/grandyang/p/4130577.html

## 122. Best Time to Buy and Sell Stock II
>Say you have an array for which the ith element is the price of a given stock on day i.  
Design an algorithm to find the maximum profit. You may complete as many transactions as you like (i.e., buy one and sell one share of the stock multiple times).  
Note: You may not engage in multiple transactions at the same time (i.e., you must sell the stock before you buy again).  
>See [***leetcode 122***][ref1] for details.   

[ref1]:https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock-ii/

***My Sol***   
贪心  
一直涨就一直不卖，一跌就在前一天卖  
可以用简单的数学证明，两种情况  

- 1 2 3 4
- 1 5 3 10

***Code***

```cpp
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        if(prices.empty()){
        	return 0;
        }

        int buy = prices[0], sell = prices[0];
        int revenue = 0;

        for(int i = 1; i < prices.size(); ++i){

        	if(prices[i] >= sell){
        		sell = prices[i];
        		continue;
        	}
        	revenue += sell-buy;
        	buy = sell = prices[i];
        }
        revenue += sell-buy;

        return revenue;
    }
};
```

姜还是老的辣。。。  
一直赚钱就一直加好咯。。。  

```cpp
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        int res = 0, n = prices.size();
        for (int i = 0; i < n - 1; ++i) {
            if (prices[i] < prices[i + 1]) {
                res += prices[i + 1] - prices[i];
            }
        }
        return res;
    }
};
```

## 217. Contains Duplicate
>Given an array of integers, find if the array contains any duplicates.  
Your function should return true if any value appears at least twice in the array, and it should return false if every element is distinct.  
>See [***leetcode 217***][ref2] for details.   

[ref2]:https://leetcode-cn.com/problems/contains-duplicate/

***My Sol***   
这道题可以比谁写的更短。。。

***Code***  
最朴素的写法

```cpp
class Solution {
public:
    bool containsDuplicate(vector<int>& nums) {
        
    	set<int> s;

    	for(auto& num : nums){
    		if(s.count(num)){
    			return true;
    		}
    		s.insert(num);
    	}
    	return false;

    }
};
```

直接建一个完整的set

```cpp
class Solution {
public:
    bool containsDuplicate(vector<int>& nums) {
        
    	set<int> s(nums.begin(), nums.end());
    	return s.size() != nums.size();

    }
};
```

先排序

```cpp
class Solution {
public:
    bool containsDuplicate(vector<int>& nums) {
        
        sort(nums.begin(), nums.end());

        for(int i = 1; i < nums.size(); ++i){
        	if(nums[i] == nums[i-1]){
        		return true;
        	}
        }

        return false;
    }
};
```

## 136. Single Number
>Given a non-empty array of integers, every element appears twice except for one. Find that single one.   
>See [***leetcode 136***][ref3] for details.   

[ref3]:https://leetcode-cn.com/problems/single-number/

***My Sol***   
上来就&  
完全忘了^

***Code***  
`&`

```cpp
class Solution {
public:
    int singleNumber(vector<int>& nums) {
        
        int res = 0;
        int bit = 1;

        for(int i = 0; i < 32; ++i){
        	int cnt = 0;
        	for(auto& num : nums){
        		if(num & bit){
        			cnt++;
        		}
        	}
            
        	if(cnt&1){
        		res += bit;
        	}
            
            if(i != 31)
        	    bit <<= 1;
        }
        return res;
    }
};
```

`^`

```cpp
class Solution {
public:
    int singleNumber(vector<int>& nums) {
        
        int res = 0;
        for(auto& num : nums){
        	res ^= num;
        }
        return res;
    }
};
```

## 350. Intersection of Two Arrays II
>Given two arrays, write a function to compute their intersection.  
>See [***leetcode 350***][ref4] for details.   

[ref4]:https://leetcode-cn.com/problems/intersection-of-two-arrays-ii/

***My Sol***   
先排序，然后两根指针逐渐加  

***Code*** 

```cpp
class Solution {
public:
    vector<int> intersect(vector<int>& nums1, vector<int>& nums2) {
        
    	sort(nums1.begin(), nums1.end());
    	sort(nums2.begin(), nums2.end());

    	int i = 0, j = 0;
    	vector<int> res;

    	while(i < nums1.size() && j < nums2.size()){

    		if(nums1[i] == nums2[j]){
    			res.push_back(nums1[i]);
    			i++;
    			j++;
    		}else if(nums1[i] > nums2[j]){
    			j++;
    		}else{
    			i++;
    		}
    	}

    	return res;

    }
};
```

最普通的hash的写法

```cpp
class Solution {
public:
    vector<int> intersect(vector<int>& nums1, vector<int>& nums2) {
        
    	unordered_map<int,int> m;
    	vector<int> res;
    	for(auto& num : nums1){
    		m[num]++;
    	}

    	for(auto& num: nums2){
    		if(m[num]-- > 0){
    			res.push_back(num);
    		}
    	}
    	return res;
    }
};
```

## 1. Two Sum
>Given an array of integers, return indices of the two numbers such that they add up to a specific target.  
You may assume that each input would have exactly one solution, and you may not use the same element twice.  
>See [***leetcode 1***][ref5] for details.   

[ref5]:https://leetcode-cn.com/problems/two-sum/

***My Sol***   
leetcode的开山之题，可以说是代码界的`abandon`了，hhhh开个玩笑   
碰到sum就用hash  滑稽.jpg

***Code*** 

```cpp
class Solution {
public:
    vector<int> twoSum(vector<int>& nums, int target) {
        unordered_map<int,int> m;

        for(int i = 0; i < nums.size(); ++i){
        	int another = target - nums[i];

        	if(m[another]){
        		return {i, m[another]-1};
        	}
        	m[nums[i]] = i+1;
        }
        return {};
    }
};
```

----