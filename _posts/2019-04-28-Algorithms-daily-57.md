---
layout:     post
title:      Algorithms daily 57
subtitle:   Array and String 小卡片～  #副标题
date:       2019-04-28
author:     ASeeker
header-img: img/bobo_1.jpeg
catalog: true
tags:
    - Algorithms
    - Leetcode
    - Array
    - String
    
---

## 致敬波波维奇！致敬马刺！

今天做下数组和字符串这张小卡片，复习复习基础芝士，里面有很多的easy题    
> 参考博客[^1] [^2] [^3] [^4] [^5] [^6]

[^1]: 724, http://www.cnblogs.com/grandyang/p/7865693.html
[^2]: 498, http://www.cnblogs.com/grandyang/p/6414461.html
[^3]: 67, http://www.cnblogs.com/grandyang/p/4084971.html
[^4]: 14, http://www.cnblogs.com/grandyang/p/4606926.html
[^5]: 167, http://www.cnblogs.com/grandyang/p/5185815.html
[^6]: 151, http://www.cnblogs.com/grandyang/p/4606676.html

## 724. Find Pivot Index
>Given an array of integers nums, write a method that returns the "pivot" index of this array.  
We define the pivot index as the index where the sum of the numbers to the left of the index is equal to the sum of the numbers to the right of the index.  
If no such index exists, we should return -1. If there are multiple pivot indexes, you should return the left-most pivot index.  
>See [***leetcode 724***][ref1] for details.   

[ref1]:https://leetcode-cn.com/problems/find-pivot-index/

***My Sol***   
前半部分逐渐加 后半部分逐渐减，为操作一致，塞一个0到最前面


***Code***

```cpp
class Solution {
public:
    int pivotIndex(vector<int>& nums) {
        long long remainSums = 0, curSums = 0;
        for(auto num : nums){
            remainSums += num;
        }

        nums.insert(nums.begin(), 0);
        int n = nums.size();
        for(int i = 0; i < n-1; ++i){
            curSums += nums[i];
            remainSums -= nums[i+1];
            if(remainSums == curSums){
                return i;
            }
        }

        return -1;
    }
};
```
博客的解答，简洁  
`accumulate` 

```cpp
class Solution {
public:
    int pivotIndex(vector<int>& nums) {
        int sum = accumulate(nums.begin(), nums.end(), 0);
        int curSum = 0, n = nums.size();
        for (int i = 0; i < n; ++i) {
            if (sum - nums[i] == 2 * curSum) return i;
            curSum += nums[i];
        }
        return -1;
    }
};
```


## 747. Largest Number At Least Twice of Others
>In a given integer array nums, there is always exactly one largest element.  
Find whether the largest element in the array is at least twice as much as every other number in the array.  
If it is, return the index of the largest element, otherwise return -1.  
>See [***leetcode 747***][ref2] for details.   

[ref2]:https://leetcode-cn.com/problems/largest-number-at-least-twice-of-others

***My Sol***   
找出俩最大的就可以啦，注意下空和一个的特殊情况


***Code***

```cpp
class Solution {
public:
    int dominantIndex(vector<int>& nums) {
        
        if(nums.empty()){
            return -1;
        }
        else if(nums.size() == 1){
            return 0;
        }
        
        int max1 = INT_MIN, max2 = INT_MIN;
        int index;

        for(int i = 0; i < nums.size(); ++i){
            max2 = max(max2,nums[i]);

            if(max1 < max2){
                max2 = max1;
                max1 = nums[i];
                index = i;
            }

        }

        return max1 >= 2*max2 ? index : -1;

    }
};
```

## 498. Diagonal Traverse
>Given a matrix of M x N elements (M rows, N columns), return all elements of the matrix in diagonal order as shown in the below image.  
>See [***leetcode 498***][ref3] for details.   

[ref3]:https://leetcode-cn.com/problems/diagonal-traverse/

***Sol***   
这题自己写的代码太烂了。。直接就用博客的思路再写一遍。。  
解法一和解法二其实差别不大，清晰下面两个方面就ok啦  

- 有两种对角线的遍历方式
- 每种遍历的方式会有两种下标越界的情况，m和n的越界比0更重要些，先处理    

解法一是越界了再去改，解法二是到边界了就修改


***Code***

```cpp
class Solution {
public:
    vector<int> findDiagonalOrder(vector<vector<int>>& matrix) {

    if(matrix.empty() || matrix[0].empty()){
        return {};
    }
     
    int m = matrix.size(), n = matrix[0].size();
    vector<int> res(m*n);

    int x = 0, y = 0;

    for(int i = 0; i < m*n; i++){
        res[i] = matrix[x][y];
        //even
        if((x+y)%2 == 0){
            //two conditions, when encount edge, so as odd
            if(y == n-1){
                x++;
            }else if(x == 0){
                y++;
            }else{
                x--;
                y++;
            }
        //odd
        }else{
            if(x == m-1){
                y++;
            }else if(y == 0){
                x++;
            }else{
                x++;
                y--;
            }
        }
    }

    return res;

    }
};
```

## 118. Pascal's Triangle
>Given a non-negative integer numRows, generate the first numRows of Pascal's triangle.  
>See [***leetcode 118***][ref4] for details.   

[ref4]:https://leetcode-cn.com/problems/pascals-triangle/

***My Sol***   
可见今天做的题多基础了。。

***Code***

```cpp
class Solution {
public:
    vector<vector<int>> generate(int numRows) {

        vector<vector<int>> res;

        for(int i = 1; i <= numRows; ++i){
            vector<int> temp(i);
            temp[0] = temp[i-1] = 1;
            for(int j = 1; j < i-1; j++){
                temp[j] = res[i-2][j-1] + res[i-2][j];
            }
            res.push_back(temp);

        }
        return res;
    }
};
```

## 561. Array Partition I
>Given an array of 2n integers, your task is to group these integers into n pairs of integer, say (a1, b1), (a2, b2), ..., (an, bn) which makes sum of min(ai, bi) for all i from 1 to n as large as possible.  
>See [***leetcode 561***][ref5] for details.   

[ref5]:https://leetcode-cn.com/problems/array-partition-i

***My Sol***   
没太明白为什么放在了双指针里。。 排序组pair，贪心取前面一个  

***Code***

```cpp
class Solution {
public:
    int arrayPairSum(vector<int>& nums) {
        
        long long sum = 0;
        sort(nums.begin(), nums.end());
        for(int i = 0; i < nums.size(); i+=2){
            sum += nums[i];
        }
        return sum;

    }
};
```

## 67. Add Binary
>Given two binary strings, return their sum (also a binary string).  
The input strings are both non-empty and contains only characters 1 or 0.  
>See [***leetcode 67***][ref6] for details.   

[ref6]:https://leetcode-cn.com/problems/add-binary/

***My Sol***   
把自己的代码代码贴出来真的是有点不好意思  
慢慢加慢慢加 

***Code***

```cpp
class Solution {
public:
    string addBinary(string a, string b) {
        
        bool next = false;
        string res;

        int i = 1, n1 = a.size(), n2 = b.size();
        int n = min(n1,n2);

        while(i <= n){

            int cnt = 0;
            if(a[n1-i] == '1'){
                cnt++;
            }
            if(b[n2-i] == '1'){
                cnt++;
            }
            if(next){
                cnt++;
                next = false;
            }
            if(cnt >= 2){
                next = true;
            }
            res.insert(res.begin(), cnt%2 + '0');
            ++i;
        }

        //just use a
        if(n1 < n2){
            n1 = n2;
            a = b;
        }

        while(i <= n1){
            int cnt = 0;
            if(a[n1-i] == '1'){
                cnt++;
            }
            if(next){
                cnt++;
                next = false;
            }
            if(cnt >= 2){
                next = true;
            }
            res.insert(res.begin(), cnt%2 + '0');
            ++i;
        }

        if(next){
            res.insert(res.begin(),'1');
        }

        return res;

    }
};
```

还是来愉快的看一下博客给的代码吧～  
来个判断，如果已经到头了，就给0好咯  

```cpp
class Solution {
public:
    string addBinary(string a, string b) {
        string res = "";
        int m = a.size() - 1, n = b.size() - 1, carry = 0;
        while (m >= 0 || n >= 0) {
            int p = m >= 0 ? a[m--] - '0' : 0;
            int q = n >= 0 ? b[n--] - '0' : 0;
            int sum = p + q + carry;
            res = to_string(sum % 2) + res;
            carry = sum / 2;
        }
        return carry == 1 ? "1" + res : res;
    }
};
```

## 28. Implement strStr()
>Implement strStr().  
Return the index of the first occurrence of needle in haystack, or -1 if needle is not part of haystack.  
>See [***leetcode 28***][ref7] for details.   

[ref7]:https://leetcode-cn.com/problems/implement-strstr/

***My Sol***   
提高代码熟练度 

***Code***

```cpp
class Solution {
public:
    int strStr(string haystack, string needle) {
        
        int i = 0, n = haystack.size(), s = needle.size();

        while(i+s-1 < n){

            int j = 0;
            while(j<s && haystack[i+j] == needle[j]){
                j++;
            }
            if(j == s){
                return i;
            }
            i++;
        }

        return -1;

    }
};
```

## 14. Longest Common Prefix
>Write a function to find the longest common prefix string amongst an array of strings.  
If there is no common prefix, return an empty string "".  
>See [***leetcode 14***][ref8] for details.   

[ref8]:https://leetcode-cn.com/problems/longest-common-prefix/

***My Sol***   
用博客的思路重写一下  
可以直接用substr()方法返回串，这样就不要string不断的加了  

还有一种更巧妙的方法 先排序 然后比较第一个串和最后一个串相同的，因为排序会使相同的串都靠在一起

***Code***

```cpp
class Solution {
public:
    string longestCommonPrefix(vector<string>& strs) {
        
        if(strs.empty()){
            return "";
        }
        int n = strs.size();

        for(int i = 0; i < strs[0].size(); ++i){
            for(int j = 1; j < n; ++j){
                if(i+1 > strs[j].size() || strs[0][i] != strs[j][i]){
                    return strs[0].substr(0,i);
                }
            }
        }

        return strs[0];
    }
};
```

先排下序

```cpp
class Solution {
public:
    string longestCommonPrefix(vector<string>& strs) {
        
        if(strs.empty()){
            return "";
        }

        sort(strs.begin(), strs.end());

        int i = 0;
        while(i < strs[0].size() && i < strs.back().size() && strs[0][i] == strs.back()[i]){
            i++;
        }

        return strs[0].substr(0,i);

    }
};
```

## 344. Reverse String
>Write a function that reverses a string. The input string is given as an array of characters char[].  
Do not allocate extra space for another array, you must do this by modifying the input array in-place with O(1) extra memory.  
You may assume all the characters consist of printable ascii characters.  
>See [***leetcode 344***][ref9] for details.   

[ref9]:https://leetcode-cn.com/problems/reverse-string/

***My Sol***   
经典的双指针了  	

***Code***

```cpp
class Solution {
public:
    void reverseString(vector<char>& s) {
        
        int right = s.size()-1, left = 0;

        while(left < right){
            swap(s[left], s[right]);
            left++; 
            right--;
        }

    }
};
```

## 167. Two Sum II - Input array is sorted
>Given an array of integers that is already sorted in ascending order, find two numbers such that they add up to a specific target number.  
The function twoSum should return indices of the two numbers such that they add up to the target, where index1 must be less than index2.  
>See [***leetcode 167***][ref10] for details.   

[ref10]:https://leetcode-cn.com/problems/two-sum-ii-input-array-is-sorted/

***My Sol***   
hash  
或者遍历第一个数，二分找一下第二个数 题目保证说一定有解。。 	

***Code***

```cpp
class Solution {
public:
    vector<int> twoSum(vector<int>& numbers, int target) {
        
        vector<int> res;
        int n = numbers.size();

        for(int i = 0; i < n-1; ++i){
            int tar = target - numbers[i];
            int left = i+1, right = n-1;

            while(left <= right){
                int mid = (left+right)/2;
                if(numbers[mid] == tar){
                    return vector<int>({i+1,mid+1});
                }else if(numbers[mid] > tar){
                    right = mid-1;
                }else{
                    left = mid+1;
                }
            }
        }
        return res;
    }
};
```

看了博客才知道可以用双指针做，O(n)  
至于为啥可以，自己思考了一下，可以从一个指针开始考虑，假如说a是前面一个，b是后面一个；如果要有a、b中的任何一个，你只能根据和target的大小开始动另一个，不然永远达不到 target了，有一点贪心的味道在里面。  

```cpp
class Solution {
public:
    vector<int> twoSum(vector<int>& numbers, int target) {
       int left = 0, right = numbers.size()-1;

       while(left < right){
            int cur = numbers[left] + numbers[right];
            if(cur == target){
                break;
            }else if(cur > target){
                right--;
            }else{
                left++;
            }
       }
       return vector<int>({left+1,right+1});
    }
};
```

## 27. Remove Element
>Given an array nums and a value val, remove all instances of that value in-place and return the new length.  
Do not allocate extra space for another array, you must do this by modifying the input array in-place with O(1) extra memory.  
The order of elements can be changed. It doesn't matter what you leave beyond the new length.
>See [***leetcode 27***][ref11] for details.   

[ref11]:https://leetcode-cn.com/problems/remove-element/

***My Sol***   
经典的快慢双指针 	

***Code***



```cpp
class Solution {
public:
    int removeElement(vector<int>& nums, int val) {
        int k = 0;

        for(int i = 0; i < nums.size(); ++i){
            if(nums[i] != val){
                nums[k++] = nums[i];
            }
        }
        return k;
    }
};
```

## 485. Max Consecutive Ones
>Given a binary array, find the maximum number of consecutive 1s in this array.  
>See [***leetcode 485***][ref12] for details.   

[ref12]:https://leetcode-cn.com/problems/max-consecutive-ones/

***My Sol***   
......就不说什么了吧	

***Code***

```cpp
class Solution {
public:
    int findMaxConsecutiveOnes(vector<int>& nums) {
        int res = 0;
        int i = 0;

        while(i < nums.size()){

            if(nums[i] == 1){
                int j = i+1;
                while(j < nums.size() && nums[j] == 1){
                    j++;
                }
                res = max(res, j-i);
                i = j;
            }else{
                i++;
            }

        }
        return res;
    }
};
```

## 209. Minimum Size Subarray Sum
>Given an array of n positive integers and a positive integer s, find the minimal length of a contiguous subarray of which the sum ≥ s. If there isn't one, return 0 instead.  
>See [***leetcode 209***][ref13] for details.   

[ref13]:https://leetcode-cn.com/problems/minimum-size-subarray-sum/

***My Sol***   
这题以前貌似做过一次，也是经典的俩指针。以第一个指针开始，sum还没有achieve第二个指针就向前走；sum到达s就可以停止第二个指针继续向前了，这时让第一个指针往前走，和之前一样找以此开头的最短的。

***Code***

```cpp
class Solution {
public:
    int minSubArrayLen(int s, vector<int>& nums) {
        int res = nums.size()+1;
        int i = 0, j = 0;
        if(nums.empty()){
            return 0;
        }
        int curSum = nums[0];

        while(i < nums.size()){
            if(curSum >= s){
                curSum -= nums[i];
                res = min(res, j-i+1);
                i++;
            }else{
                j++;
                if(j == nums.size()){
                    break;
                }
                curSum += nums[j];
            }

        }
        return res == nums.size()+1 ? 0 : res;
    }
};
```

## 119. Pascal's Triangle II
>Given a non-negative index k where k ≤ 33, return the kth index row of the Pascal's triangle.  
Note that the row index starts from 0.  
>See [***leetcode 119***][ref15] for details.   

[ref15]:https://leetcode-cn.com/problems/pascals-triangle-ii/

***My Sol***   
和很多dp优化空间一样，为了防止被覆盖，从后往前写

***Code***

```cpp
class Solution {
public:
    vector<int> getRow(int rowIndex) {
        
        vector<int> res(rowIndex+1,0);
        res[0] = 1;
        for(int i = 0; i < rowIndex+1; ++i){
            for(int j = i; j >= 1; j--){
                res[j] += res[j-1];
            }
        }

        return res;
    }
};
```

## 151. Reverse Words in a String
>Given an input string, reverse the string word by word.  
>See [***leetcode 151***][ref16] for details.   

[ref16]:https://leetcode-cn.com/problems/reverse-words-in-a-string/

***My Sol***   
最菜🐔的写法惹 插入到头

***Code***

```cpp
class Solution {
public:
    string reverseWords(string s) {
        
        string res = "";
        string temp = "";
        int i = 0;

        while(i < s.size()){
            if(s[i] == ' '){
                i++;
                continue;
            }

            int j = i+1;
            while(j < s.size() && s[j] != ' '){
                j++;
            }

            if(!res.empty()){
                //res = ' ' + res;
                res.insert(res.begin(), ' ');
            }
            res = s.substr(i, j-i) + res;
            //res.insert(res.begin(), s.substr(i, j-i));

            i = j;

        }
        return res;
    }
};
```

这种`res += s`特别耗时 看了下博客，翻转两次，先翻转整个串，然后在翻转各个单词   感觉今天脑子已经转不动了。。。写完这个代码剩下的明天再写吧。。。

```cpp
class Solution {
public:
    string reverseWords(string s) {
        int n = s.size(), index = 0, i = 0;
        reverse(s.begin(), s.end());

        while(i < n){

            if(s[i] == ' '){
                i++;
                continue;
            }

            if(index != 0){
                s[index++] = ' ';
            }

            int j = i;
            while(j < n && s[j] != ' '){
                s[index++] = s[j++];
            }
            reverse(s.begin()+index-(j-i), s.begin()+index);
            i = j;
        }
        s.resize(index);
        return s;
    }
};
```

 ----