---
layout:     post
title:      Algorithms daily 61
subtitle:   Hash Table 小卡片～  #副标题
date:       2019-05-02
author:     ASeeker
header-img: img/shijing_1.jpg
catalog: true
tags:
    - Algorithms
    - Leetcode
    - Hash Table
    
---

## 泛彼柏舟，亦泛其流。耿耿不寐，如有隐忧。微我无酒，以遨以游。
--《国风·邶风·柏舟》

> 参考博客[^1] [^2] [^3] [^4] [^5] [^6] [^7] [^8]

[^1]: 705, https://www.cnblogs.com/grandyang/p/9966807.html
[^2]: 使用哈希集查重, https://leetcode-cn.com/explore/learn/card/hash-table/204/practical-application-hash-set/804/
[^3]: 349, http://www.cnblogs.com/grandyang/p/5507129.html
[^4]: 202, http://www.cnblogs.com/grandyang/p/4447233.html
[^5]: 49, http://www.cnblogs.com/grandyang/p/4385822.html
[^6]: 652, http://www.cnblogs.com/grandyang/p/7500082.html
[^7]: 454, http://www.cnblogs.com/grandyang/p/6073317.html
[^8]: 380, http://www.cnblogs.com/grandyang/p/5740864.html


## 705. Design HashSet
>Design a HashSet without using any built-in hash table libraries.  
See [***leetcode 705***][ref1] for details.   

[ref1]:https://leetcode-cn.com/problems/design-hashset/

***My Sol***   
题目给了一个范围`[0, 1000000]`，直接就开了个这么大的数组
 
***Code***

```cpp
class MyHashSet {
public:
    /** Initialize your data structure here. */
    MyHashSet() {
        v.resize(1000001, 0);
    }
    
    void add(int key) {
        v[key] = 1;
    }
    
    void remove(int key) {
        v[key] = 0;
    }
    
    /** Returns true if this set contains the specified element */
    bool contains(int key) {
        return v[key] == 1;
    }

private:
    vector<int> v;

};
```

也可以先开个`1000*1000`二维数组的第一维，然后`/1000`有之后再开第二维1000  

```cpp
class MyHashSet {
public:
    /** Initialize your data structure here. */
    MyHashSet() {
        mod = 1000;
        v.resize(mod, vector<int>());
    }
    
    void add(int key) {

        int index = key/mod;
        if(v[index].empty())
        {
            v[index].resize(mod,0);
        }
        v[index][key%mod] = 1;
    }
    
    void remove(int key) {
        int index = key/mod;
        if(!v[index].empty())
        {
            v[index][key%mod] = 0;
        }
    }
    
    /** Returns true if this set contains the specified element */
    bool contains(int key) {
        int index = key/mod;
        return !v[index].empty() && v[index][key%mod];
    }

private:
    vector<vector<int>> v;
    int mod;
};
```

## 706. Design HashMap
>Design a HashMap without using any built-in hash table libraries.  
>See [***leetcode 706***][ref2] for details.   

[ref2]:https://leetcode-cn.com/problems/design-hashmap/

***My Sol***   
其实和上面一题 一mu一样  
 
***Code***

```cpp
class MyHashMap {
public:
    /** Initialize your data structure here. */
    MyHashMap() {
        mod = 1000;
        v.resize(1000,vector<int>());
    }
    
    /** value will always be non-negative. */
    void put(int key, int value) {
        int hashKey = key%1000;
        if(v[hashKey].empty())
        {
            v[hashKey].resize(mod,-1);
        }
        v[hashKey][key/1000] = value;
    }
    
    /** Returns the value to which the specified key is mapped, or -1 if this map contains no mapping for the key */
    int get(int key) {
        int hashKey = key%1000;
        if(v[hashKey].empty())
        {
            return -1;
        }
        return v[hashKey][key/1000];
    }
    
    /** Removes the mapping of the specified value key if this map contains a mapping for the key */
    void remove(int key) {
        int hashKey = key%1000;
        if(!v[hashKey].empty())
        {
            v[hashKey][key/1000] = -1;
        }
    }

private:
    vector<vector<int>> v;
    int mod;
};
```



# i 使用哈希集查重

```cpp
/*
 * Template for using hash set to find duplicates.
 */
bool findDuplicates(vector<Type>& keys) {
    // Replace Type with actual type of your key
    unordered_set<Type> hashset;
    for (Type key : keys) {
        if (hashset.count(key) > 0) {
            return true;
        }
        hashset.insert(key);
    }
    return false;
}
```


## 349. Intersection of Two Arrays
>Given two arrays, write a function to compute their intersection.  
>See [***leetcode 349***][ref3] for details.   

[ref3]:https://leetcode-cn.com/problems/intersection-of-two-arrays/

***My Sol***   
可以hash，但没必要。。。。  
喜欢这样先排序 
 
***Code***

```cpp
class Solution {
public:
    vector<int> intersection(vector<int>& nums1, vector<int>& nums2) {
        set<int> res;
        sort(nums1.begin(), nums1.end());
        sort(nums2.begin(), nums2.end());

        int i = 0, j = 0, m = nums1.size(), n = nums2.size();

        while(i < m && j < n)
        {
            if(nums1[i] == nums2[j])
            {
                res.insert(nums1[i]);
                i++;
                j++;
            }else if(nums1[i] < nums2[j]){
                i++;
            }else{
                j++;
            }
        }

        return vector<int>(res.begin(), res.end());
    }
};
```

直接用set简洁点  


```cpp
class Solution {
public:
    vector<int> intersection(vector<int>& nums1, vector<int>& nums2) {
        set<int> s(nums1.begin(), nums1.end()), res;
        for (auto a : nums2) {
            if (s.count(a)) res.insert(a);
        }
        return vector<int>(res.begin(), res.end());
    }
};

```

## 202. Happy Number
>Write an algorithm to determine if a number is "happy".   
A happy number is a number defined by the following process: Starting with any positive integer, replace the number by the sum of the squares of its digits, and repeat the process until the number equals 1 (where it will stay), or it loops endlessly in a cycle which does not include 1. Those numbers for which this process ends in 1 are happy numbers.   
>See [***leetcode 202***][ref4] for details.   

[ref4]:https://leetcode-cn.com/problems/happy-number/

***My Sol***   
用set判断有没有进入循环  
 
***Code***

```cpp
class Solution {
public:
    bool isHappy(int n) {
        set<int> s;

        while(n != 1 && s.count(n) <= 0)
        {
            s.insert(n);
            int next = 0;
            while(n)
            {
                next += pow((n%10), 2);
                n /= 10;
            }
            n = next;

        }
        
        if(n == 1){
            return true;
        }else{
            return false;
        }

    }
};
```

快慢指针  这个思路很牛X啊   

```cpp
class Solution {
public:
    bool isHappy(int n) {
        int slow = n, fast = n;

        while(true)
        {
            slow = findNext(slow);
            fast = findNext(fast);
            fast = findNext(fast);
            if(slow == fast)
            {
                break;
            }
        }

        return slow == 1;

    }

    int findNext(int n){
        int res = 0;

        while(n > 0)
        {
            res += pow(n%10, 2);
            n /= 10;
        }

        return res;
    }

};
```

# ii 哈希映射 提供更多信息
提供更多信息

```cpp
/*
 * Template for using hash map to find duplicates.
 * Replace ReturnType with the actual type of your return value.
 */
ReturnType aggregateByKey_hashmap(vector<Type>& keys) {
    // Replace Type and InfoType with actual type of your key and value
    unordered_map<Type, InfoType> hashtable;
    for (Type key : keys) {
        if (hashmap.count(key) > 0) {
            if (hashmap[key] satisfies the requirement) {
                return needed_information;
            }
        }
        // Value can be any information you needed (e.g. index)
        hashmap[key] = value;
    }
    return needed_information;
}
```

## 205. Isomorphic Strings
>Given two strings s and t, determine if they are isomorphic.  
Two strings are isomorphic if the characters in s can be replaced to get t.  
>See [***leetcode 205***][ref5] for details.   

[ref5]:https://leetcode-cn.com/problems/isomorphic-strings/

***My Sol***   
两种思路，一种记录第一次出现的位置  
一种记录替换的字符   
 
***Code***

```cpp
class Solution {
public:
    bool isIsomorphic(string s, string t) {
        if(s.size() != t.size())
        {
            return false;
        }

        unordered_map<char, char> m;
        unordered_set<char> us;

        for(int i = 0; i < s.size(); ++i)
        {
            if(m.count(s[i]))
            {
                if(m[s[i]] != t[i])
                {
                    return false;
                }

            }else{
                if(us.count(t[i]) > 0)
                {
                    return false;
                }
                us.insert(t[i]);
                m[s[i]] = t[i];
            }

        }
        return true;
    }
};
```

位置  

```cpp
class Solution {
public:
    bool isIsomorphic(string s, string t) {
        vector<int> v1(256, 0), v2(256, 0);

        for(int i = 0; i < s.size(); ++i)
        {
            if(v1[s[i]] != v2[t[i]])
            {
                return false;
            }

            if(v1[s[i]] == 0)
                v1[s[i]] = v2[t[i]] = i+1;
        }

        return true;

    }
};
```

## 599. Minimum Index Sum of Two Lists
>Suppose Andy and Doris want to choose a restaurant for dinner, and they both have a list of favorite restaurants represented by strings.   
You need to help them find out their common interest with the least list index sum. If there is a choice tie between answers, output all of them with no order requirement. You could assume there always exists an answer.  
>See [***leetcode 599***][ref6] for details.   

[ref6]:https://leetcode-cn.com/problems/minimum-index-sum-of-two-lists/

***My Sol***   
....  
 
***Code***

```cpp
class Solution {
public:
    vector<string> findRestaurant(vector<string>& list1, vector<string>& list2) {
        unordered_map<string, int> m;
        vector<string> res;
        int minIndex = INT_MAX;

        for(int i = 0; i < list1.size(); ++i)
        {
            m[list1[i]] = i;
        }

        for(int i = 0; i < list2.size(); ++i)
        {
            if(m.count(list2[i])){
                int temp = m[list2[i]] + i;

                if(minIndex == temp){
                    res.push_back(list2[i]);
                }else if(minIndex > temp){
                    minIndex = temp;
                    res.clear();
                    res.push_back(list2[i]);
                }
            }
        }
        return res;
    }
};
```

# iii 哈希映射 按键聚合
按键聚合

```cpp
ReturnType aggregateByKey_hashmap(vector<Type>& keys) {
    // Replace Type and InfoType with actual type of your key and value
    unordered_map<Type, InfoType> hashtable;
    for (Type key : keys) {
        if (hashmap.count(key) > 0) {
            update hashmap[key];
        }
        // Value can be any information you needed (e.g. index)
        hashmap[key] = value;
    }
    return needed_information;
}
```

## 219. Contains Duplicate II
>Given an array of integers and an integer k, find out whether there are two distinct indices i and j in the array such that nums[i] = nums[j] and the absolute difference between i and j is at most k.  
>See [***leetcode 219***][ref7] for details.   

[ref7]:https://leetcode-cn.com/problems/contains-duplicate-ii/

***My Sol***   
对应卡片里的第二个场景 按键聚合 听起来很高大上， 按我的理解是在解的过程中对该键的值可以进行更新  
前面还有一类的俩题之前几天刚刚做过，就不再写了，为`387 字符串中的第一个唯一字符` 和 `350 两个数组的交集 II`
 
***Code***

```cpp
class Solution {
public:
    bool containsNearbyDuplicate(vector<int>& nums, int k) {
        unordered_map<int, int> m;

        for(int i = 0; i < nums.size(); ++i)
        {
            if(m.count(nums[i]))
            {
                if(i - m[nums[i]] <= k)
                {
                    return true;
                }else{
                    m[nums[i]] = i;
                }
            }else{
                m[nums[i]] = i;
            }
        }

        return false;
    }
};
```

# iv 设计键

## 49. Group Anagrams
>Given an array of strings, group anagrams together.  
>See [***leetcode 49***][ref8] for details.   

[ref8]:https://leetcode-cn.com/problems/group-anagrams/

***My Sol***   
思路很简单 排下序就可以了  
这道题自己之前做过一次 看了下之前的代码，真的是不忍直视🙃  
 
***Code***

```cpp
class Solution {
public:
    vector<vector<string>> groupAnagrams(vector<string>& strs) {
        vector<vector<string>> res;
        unordered_map<string, int> m;

        int i = 0;

        for(auto& a : strs)
        {
            string temp = a;
            sort(temp.begin(), temp.end());

            if(m.count(temp) <= 0)
            {
                m[temp] = i++;
                res.push_back({a});

            }else{
                res[m[temp]].push_back(a);
            }
        }

        return res;
    }
};
```

博客里面给了另一种设计键的方法，利用每种字母出现的个数，这样就不用排序了  

```cpp
class Solution {
public:
    vector<vector<string>> groupAnagrams(vector<string>& strs) {
        vector<vector<string>> res;
        unordered_map<string, vector<string>> m;
        for (string str : strs) {
            vector<int> cnt(26, 0);
            string t = "";
            for (char c : str) ++cnt[c - 'a'];
            for (int d : cnt) t += to_string(d) + "/";
            m[t].push_back(str);
        }
        for (auto a : m) {
            res.push_back(a.second);
        }
        return res;
    }
};
```

## 36. Valid Sudoku
>Determine if a 9x9 Sudoku board is valid.  
>See [***leetcode 36***][ref9] for details.   

[ref9]:https://leetcode-cn.com/problems/valid-sudoku/

***My Sol***   
空间暴力解决。。 
 
***Code***

```cpp
class Solution {
public:
    bool isValidSudoku(vector<vector<char>>& board) {
        
        vector<vector<bool>> rowCheck(9, vector<bool>(9,false));
        vector<vector<bool>> lineCheck(9, vector<bool>(9,false));
        vector<vector<bool>> cellCheck(9, vector<bool>(9,false));

        for (int i = 0; i < 9; ++i)
        {
            for (int j = 0; j < 9; ++j)
            {
                if(board[i][j] >= '1' && board[i][j] <= '9')
                {
                    int num = board[i][j] - '1';
                    if(rowCheck[i][num] || lineCheck[j][num] || cellCheck[i/3*3+j/3][num])
                    {
                        return false;
                    }

                    rowCheck[i][num] = true;
                    lineCheck[j][num] = true;
                    cellCheck[i/3*3+j/3][num] = true;
                }

            }
        }

        return true;
    }
};
```

## 652. Find Duplicate Subtrees
>See [***leetcode 652***][ref10] for details.   

[ref10]:https://leetcode-cn.com/problems/find-duplicate-subtrees/submissions/

***My Sol***   
自己的解法TLE了，确实复杂度太高了，每个结点都要比一下有相同值的树结构  
无奈之下 只好去喵一下博客，不得不佩服  
在遍历的过程中，用遍历得到的字符串做为键  
 
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
    vector<TreeNode*> findDuplicateSubtrees(TreeNode* root) {
        vector<TreeNode*> res;
        unordered_map<string, int> m;

        helper(res, m, root);

        return res;

    }

    string helper(vector<TreeNode*>& res, unordered_map<string, int>& m, TreeNode* root){
        if(!root)
        {
            return "#";
        }

        string temp = to_string(root->val) + "," + helper(res, m, root->left) + "," + helper(res, m, root->right);

        if(m[temp]++ == 1)
        {
            res.push_back(root);
        }

        return temp;

    }

};
```


## 771. Jewels and Stones
>You're given strings J representing the types of stones that are jewels, and S representing the stones you have.  Each character in S is a type of stone you have.  You want to know how many of the stones you have are also jewels.  
The letters in J are guaranteed distinct, and all characters in J and S are letters. Letters are case sensitive, so "a" is considered a different type of stone from "A".  
>See [***leetcode 771***][ref11] for details.   

[ref11]:https://leetcode-cn.com/problems/jewels-and-stones/

***My Sol***   
... 
 
***Code***

```cpp
class Solution {
public:
    int numJewelsInStones(string J, string S) {
        
        unordered_set<char> s;
        int res = 0;

        for(auto a : J)
        {
            s.insert(a);
        }

        for(auto a : S)
        {
            if (s.count(a))
            {
                res++;
            }
        }

        return res;
    }
};
```

## 3. Longest Substring Without Repeating Characters
>Given a string, find the length of the longest substring without repeating characters.  
>See [***leetcode 3***][ref12] for details.   

[ref12]:https://leetcode-cn.com/problems/longest-substring-without-repeating-characters/

***My Sol***   
用了dp  
hash保存字符最后一次出现的索引 
 
***Code***

```cpp
class Solution {
public:
    int lengthOfLongestSubstring(string s) {
        
        unordered_map<char,int> m;
        int res = 0;
        int dp = 0;

        for (int i = 0; i < s.size(); ++i)
        {
            /* code */
            if(m.count(s[i]) <= 0)
            {
                dp++;
            }else{
                dp = min(dp+1, i-m[s[i]]);
            }

            res = max(res, dp);
            m[s[i]] = i;
        }
        return res;
    }
};
```

嘻嘻嘻😁 用256长的数组优化了一下  

```cpp
class Solution {
public:
    int lengthOfLongestSubstring(string s) {
        
        vector<int> m(256,-1);
        int res = 0;
        int dp = 0;

        for (int i = 0; i < s.size(); ++i)
        {
            dp = min(dp+1, i-m[s[i]]);
            res = max(res, dp);
            m[s[i]] = i;
        }
        return res;
    }
};
```

## 454. 4Sum II
>Given four lists A, B, C, D of integer values, compute how many tuples (i, j, k, l) there are such that A[i] + B[j] + C[k] + D[l] is zero.   
>See [***leetcode 454***][ref13] for details.   

[ref13]:https://leetcode-cn.com/problems/4sum-ii/

***Sol***   
看了下博客  
先`n^2`算个和，然后再hash，有点分治的感觉  
hash查找也需要log时间。。。 个人感觉复杂度其实是`O(n^2logn)`
 
***Code***


```cpp
class Solution {
public:
    int fourSumCount(vector<int>& A, vector<int>& B, vector<int>& C, vector<int>& D) {
        unordered_map<int,int> m;
        int res = 0;

        for(auto a : A)
        {
            for(auto b : B)
            {
                m[a+b]++;
            }
        }

        for(auto c : C)
        {
            for(auto d : D)
            {
                res += m[-(c+d)];
            }
        }

        return res;

    }
};
```

## 347. Top K Frequent Elements
>Given a non-empty array of integers, return the k most frequent elements.
>See [***leetcode 347***][ref14] for details.   

[ref14]:https://leetcode-cn.com/problems/top-k-frequent-elements/

***Sol***   
hash存一下就可以了  
 
***Code***

```cpp
class Solution {
public:
    vector<int> topKFrequent(vector<int>& nums, int k) {
        unordered_map<int,int> m;
        priority_queue<pair<int,int>> q;
        vector<int> res;

        for(auto num : nums)
        {
            m[num]++;
        }

        for(auto& p : m)
        {
            q.push({p.second,p.first});
        }

        while(k > 0)
        {
            res.push_back(q.top().second);
            q.pop();
            k--;
        }

        return res;
    }
};
```

默默的留下三个设计题明天写咯。。。  

day2的故事 51假期🏖继续美美的睡了个懒觉😴 起床填坑ing··· 

## 380. Insert Delete GetRandom O(1)
>Design a data structure that supports all following operations in average O(1) time.  
>See [***leetcode 380***][ref15] for details.   

[ref15]:https://leetcode-cn.com/problems/insert-delete-getrandom-o1/

***Sol***   
咋都默认hash O(1)呢。。  
关键问题就是随机返回值了，所以另外配一个数组    
 
***Code***

```cpp
class RandomizedSet {
public:
    /** Initialize your data structure here. */
    RandomizedSet() {
        
    }
    
    /** Inserts a value to the set. Returns true if the set did not already contain the specified element. */
    bool insert(int val) {
        if(m.count(val))
        {
            return false;
        }
        m[val] = size++;
        nums.push_back(val);
        return true;

    }
    
    /** Removes a value from the set. Returns true if the set contained the specified element. */
    bool remove(int val) {
        if(m.count(val) <= 0)
        {
            return false;
        }
        m[nums[size-1]] = m[val];
        nums[m[val]] = nums[size-1];
        nums.pop_back();
        size--;
        m.erase(val);
        return true;

    }
    
    /** Get a random element from the set. */
    int getRandom() {
        return nums[rand()%size];
    }

private:
    vector<int> nums;
    unordered_map<int,int> m;
    int size = 0;

};
```


----