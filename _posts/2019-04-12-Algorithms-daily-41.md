---
layout:     post
title:      Algorithms daily 41
subtitle:   Leetcode 153 392  #副标题
date:       2019-04-12
author:     ASeeker
header-img: img/dog_1.jpg
catalog: true
tags:
    - Algorithms
    - Leetcode
    - Binary Search
    
---


## 153. Find Minimum in Rotated Sorted Array
>Suppose an array sorted in ascending order is rotated at some pivot unknown to you beforehand.  
(i.e.,  [0,1,2,4,5,6,7] might become  [4,5,6,7,0,1,2]).  
Find the minimum element.  
You may assume no duplicate exists in the array.  
See [***leetcode 334***][ref1] for details.   

[ref1]:https://leetcode.com/problems/find-minimum-in-rotated-sorted-array/submissions/


***My Sol***   
二分,中点有三种情况  

* 一种是最简单的情况，比如最后到了01234，就找到了    
* 一种是落在较大的那部分中  
* 还有一种就是落在了较小的那部分中  


***Code***

```cpp
class Solution {
public:
    int findMin(vector<int>& nums) {
        
        int left = 0, right = nums.size()-1;
        
        while(left < right){
            
            int mid = left + (right-left)/2;
            
            if(nums[mid] <= nums[left] && nums[mid] < nums[right]){
                //450[1]234
                right = mid;
            }else if(nums[mid] >= nums[left] && nums[mid] > nums[right]){
                //456[7]012
                left = mid + 1;
            }else if(nums[mid] >= nums[left] && nums[mid] < nums[right]){
                //01234
                break;
            }
            
        }
        
        return nums[left];
    }
};
```

看了一下的[博客][ref2]的另一种解法  
思路也差不多  
他是只和左边的比  
然后left、right变化时mid不加1，同时判断条件也修改了一下  

[ref2]:http://www.cnblogs.com/grandyang/p/4032934.html


```cpp
class Solution {
public:
    int findMin(vector<int> &num) {
        int left = 0, right = num.size() - 1;
        if (num[left] > num[right]) {
            while (left != (right - 1)) {
                int mid = (left + right) / 2;
                if (num[left] < num[mid]) left = mid;
                else right = mid;
            }
            return min(num[left], num[right]);
        }
        return num[0];
    }
};
```

## 392. Is Subsequence
>Given a string s and a string t, check if s is subsequence of t.  
You may assume that there is only lower case English letters in both s and t. t is potentially a very long (length ~= 500,000) string, and s is a short string (<=100).  
A subsequence of a string is a new string which is formed from the original string by deleting some (can be none) of the characters without disturbing the relative positions of the remaining characters. (ie, "ace" is a subsequence of "abcde" while "aec" is not).  
See [***leetcode 392***][ref3] for details.   

[ref3]:https://leetcode.com/problems/is-subsequence/


***My Sol***   
对于每个char，贪心找到第一个匹配的字符，记录一下位置给下次使用  


***Code***


```cpp
class Solution {
public:
    bool isSubsequence(string s, string t) {
        
        int ns = s.size(), nt = t.size();
        
        //vector<int> dp(ns+1,-1);
        int lastPos = -1;
        
        for(int i = 1; i <= ns; ++i){
            
            int j = lastPos + 1;
            //try to find char
            for(; j < nt; ++j){
                if(t[j] == s[i-1]){
                    //save the position
                    lastPos = j;
                    break;
                }
            }
            //do not find
            if(j == nt){
                return false;
            } 
        }
        
        return true;
        
    }
};
```

[博客][ref4]给的code还是更胜一筹啊,思路很棒  
让两个索引递增

[ref4]:http://www.cnblogs.com/grandyang/p/5842033.html

```cpp
class Solution {
public:
    bool isSubsequence(string s, string t) {
        int i = 0;
        for (int j = 0; j < t.size() && i < s.size(); ++j) {
            if (s[i] == t[j]) ++i;
        }
        return i == s.size();
    }
};
```

一开始没明白为啥这题relate到了二分  
看了[discussion里面的follow-up解法才明白]  
对于如果有大量的需要检测，先建立一个数组存储s中26个字母的所有位置   
然后对t中各个位置进行二分查找，确实速度快的多  
这里也碰到了cpp里面的`upper_bound`,之前还没看过hhh

```cpp
class Solution {
public:
    bool isSubsequence(string s, string t) {
        //build a record the index of each char in t
        vector<vector<int>> record(26);
        //add indexs
        for(int i = 0; i < t.size(); i++) {
            record[t[i]-'a'].push_back(i);
        }
        //check if each char in s is in the legal place
        int index = -1;
        for(int i = 0; i < s.size(); i++) {
            auto iter = upper_bound(record[s[i]-'a'].begin(), record[s[i]-'a'].end(), index);
            if(iter == record[s[i]-'a'].end()) return false;
            index = *iter;
        }
        return true;
    }
};
```