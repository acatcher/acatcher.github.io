---
layout:     post
title:      Algorithms daily 62
subtitle:   Binary Search 小卡片～  #副标题
date:       2019-05-03
author:     ASeeker
header-img: img/shijing_2.jpeg
catalog: true
tags:
    - Algorithms
    - Leetcode
    - Binary Search
    
---

## 瞻彼淇奥，绿竹猗猗。有匪君子，如切如磋，如琢如磨。瑟兮僩兮，赫兮咺兮。有匪君子，终不可谖兮。
--《国风·卫风·淇奥》

>二分里面的题目难度逐渐上来了，花了不少时间  
> Reference[^1] [^2] [^3] [^4] [^5] [^6] [^7] [^8] [^9] [^10] [^11] [^12]

[^1]: https://leetcode-cn.com/explore/learn/card/binary-search/210/template-ii/839/
[^2]: 33, http://www.cnblogs.com/grandyang/p/4325648.html
[^3]: 34, http://www.cnblogs.com/grandyang/p/4409379.html  
[^4]: 658, http://www.cnblogs.com/grandyang/p/7519466.html
[^5]: 153, http://www.cnblogs.com/grandyang/p/4032934.html
[^6]: 367, http://www.cnblogs.com/grandyang/p/5619296.html
[^7]: 744, http://www.cnblogs.com/grandyang/p/8284940.html
[^8]: 50, http://www.cnblogs.com/grandyang/p/4383775.html
[^9]: 287, http://www.cnblogs.com/grandyang/p/4843654.html
[^10]: 4, http://www.cnblogs.com/grandyang/p/4465932.html
[^11]: 11, http://www.cnblogs.com/grandyang/p/8627783.html
[^12]: 410, http://www.cnblogs.com/grandyang/p/5933787.html

## 704. Binary Search
>Given a sorted (in ascending order) integer array nums of n elements and a target value, write a function to search target in nums. If target exists, then return its index, otherwise return -1  
>See [***leetcode 704***][ref1] for details.   

[ref1]:https://leetcode-cn.com/problems/binary-search/

***My Sol***   
...
 
***Code***

```cpp
class Solution {
public:
    int search(vector<int>& nums, int target) {
        
        int left = 0, right = nums.size()-1;

        while(left < right)
        {
            int mid = (left + right)/2;

            if(nums[mid] == target)
            {
                return mid;
            }else if(nums[mid] > target){
                right = mid;
            }else{
                left = mid + 1;
            }
        }
        return nums[right] == target ? right : -1;
    }
};
```

## 二分查找模板 I

```cpp
int binarySearch(vector<int>& nums, int target){
  if(nums.size() == 0)
    return -1;

  int left = 0, right = nums.size() - 1;
  while(left <= right){
    // Prevent (left + right) overflow
    int mid = left + (right - left) / 2;
    if(nums[mid] == target){ return mid; }
    else if(nums[mid] < target) { left = mid + 1; }
    else { right = mid - 1; }
  }

  // End Condition: left > right
  return -1;
}
```


## 69. Sqrt(x)
>Implement int sqrt(int x).  
>See [***leetcode 69***][ref2] for details.   

[ref2]:https://leetcode-cn.com/problems/sqrtx/

***My Sol***   
注意一下用`x/mid`, 不然要用long  
这里求平方根有一个著名的`牛顿迭代法`，日后填坑   
 
***Code***

```cpp
class Solution {
public:
    int mySqrt(int x) {
        int left = 1, right = x/2+1;

        while(left <= right)
        {
            int mid = left +(right-left)/2;
            int temp = x/mid;
            if(temp == mid)
            {
                return temp;
            }else if(temp > mid){
                left = mid+1;
            }else{
                right = mid-1;
            }
        }
        return right;
    }
};
```


## 374. Guess Number Higher or Lower
>We are playing the Guess Game. The game is as follows:  
I pick a number from 1 to n. You have to guess which number I picked.  
>See [***leetcode 374***][ref3] for details.   

[ref3]:https://leetcode-cn.com/problems/guess-number-higher-or-lower/

***My Sol***   
.... 
 
***Code***



```cpp
class Solution {
public:
    int guessNumber(int n) {
        
        int left = 1, right = n;
        int mid = 0;

        while(left <= right)
        {
            mid = left + (right-left)/2;
            if(guess(mid) == 0)
            {
                break;
            }else if(guess(mid) == 1){
                left = mid+1;
            }else{
                right = mid-1;
            }
        }
        return mid;

    }
};
```

## 33. Search in Rotated Sorted Array
>Suppose an array sorted in ascending order is rotated at some pivot unknown to you beforehand.  
(i.e., [0,1,2,4,5,6,7] might become [4,5,6,7,0,1,2]).  
You are given a target value to search. If found in the array return its index, otherwise return -1.  
>See [***leetcode 33***][ref4] for details.   

[ref4]:https://leetcode-cn.com/problems/search-in-rotated-sorted-array/

***My Sol***   
两种情况  

- left和right已经sort了
- 未sort
	- mid 在大的部分
	- mid 在小的部分  
 
***Code***


```cpp
class Solution {
public:
    int search(vector<int>& nums, int target) {
        int left = 0, right = nums.size()-1;

        while(left <= right)
        {
            int mid = left + (right-left)/2;
            if(nums[mid] == target)
            {
                return mid;
            }

            //if in sorted
            if(nums[left] <= nums[right])
            {
                if(nums[mid] < target)
                {
                    left = mid+1;
                }else{
                    right = mid-1;
                }
            }else{
                //if in rotated
                
                if(target > nums[mid])
                {

                    if(nums[mid] > nums[right]){
                        left = mid + 1;

                    }else{
                        if(target > nums[right])
                        {
                            right = mid-1;
                        }else{
                            left = mid + 1;
                        }
                    }
  
                }else{
                    //in left or in right

                    if(nums[mid] > nums[right]){
                        if(target >= nums[left])
                        {
                            right = mid-1;
                        }else{
                            left = mid+1;
                        }
                    }else{
                        right = mid-1;
                    }
                    
                }
            }
        }
        return -1;
    }
};
```

博客给了一个更棒的思路，需要仔细观察  
0　　1　　2　　 `4　　5　　6　　7`

7　　0　　1　　 `2　　4　　5　　6`

6　　7　　0　　 `1　　2　　4　　5`

5　　6　　7　　 `0　　1　　2　　4`

`4　　5　　6　　7`　　0　　1　　2

`2　　4　　5　　6`　　7　　0　　1

`1　　2　　4　　5`　　6　　7　　0

**如果中间的数小于最右边的数，则右半段是有序的，若中间数大于最右边数，则左半段是有序的**

```cpp
class Solution {
public:
    int search(vector<int>& nums, int target) {
        int left = 0, right = nums.size() - 1;
        while (left <= right) {
            int mid = left + (right - left) / 2;
            if (nums[mid] == target) return mid;
            else if (nums[mid] < nums[right]) {
                if (nums[mid] < target && nums[right] >= target) left = mid + 1;
                else right = mid - 1;
            } else {
                if (nums[left] <= target && nums[mid] > target) right = mid - 1;
                else left = mid + 1;
            }
        }
        return -1;
    }
};
```

## 二分查找模板 II

```cpp
int binarySearch(vector<int>& nums, int target){
  if(nums.size() == 0)
    return -1;

  int left = 0, right = nums.size();
  while(left < right){
    // Prevent (left + right) overflow
    int mid = left + (right - left) / 2;
    if(nums[mid] == target){ return mid; }
    else if(nums[mid] < target) { left = mid + 1; }
    else { right = mid; }
  }

  // Post-processing:
  // End Condition: left == right
  if(left != nums.size() && nums[left] == target) return left;
  return -1;
}
```
当前索引及其直接右邻居索引

- 查找元素的直接右邻居。
- 使用元素的右邻居来确定是否满足条件，并决定是向左还是向右。
- 保证查找空间在每一步中至少有 2 个元素。
- 需要进行后处理。 当你剩下 1 个元素时，循环 / 递归结束。 需要评估剩余元素是否符合条件。



## 278. First Bad Version
>You are a product manager and currently leading a team to develop a new product. Unfortunately, the latest version of your product fails the quality check. Since each version is developed based on the previous version, all the versions after a bad version are also bad.  
Suppose you have n versions [1, 2, ..., n] and you want to find out the first bad one, which causes all the following ones to be bad.  
You are given an API bool isBadVersion(version) which will return whether version is bad. Implement a function to find the first bad version. You should minimize the number of calls to the API.  
>See [***leetcode 278***][ref5] for details.   

[ref5]:https://leetcode-cn.com/problems/first-bad-version/

***My Sol***   
虽然说很简单，但对于理解模版ii很有帮助    
 
***Code***

```cpp
class Solution {
public:
    int firstBadVersion(int n) {
        int left = 1, right = n;

        while(left < right)
        {
            int mid = left + (right-left)/2;
            if(isBadVersion(mid)){
                right = mid;
            }else{
                left = mid + 1;
            }
        }

        return left;
    }
};
```



## 162. Find Peak Element
>A peak element is an element that is greater than its neighbors.  
>See [***leetcode 162***][ref6] for details.   

[ref6]:https://leetcode-cn.com/problems/find-peak-element/

***My Sol***   
就是二分了    
 
***Code***

```cpp
class Solution {
public:
    int findPeakElement(vector<int>& nums) {
        int left = 0, right = nums.size()-1;

        while(left < right)
        {
            int mid = left + (right-left)/2;

            if(nums[mid] < nums[mid+1]){
                left = mid+1;
            }else{
                right = mid;
            }
        }
        return left;
    }
};
```


## 153. Find Minimum in Rotated Sorted Array
>Suppose an array sorted in ascending order is rotated at some pivot unknown to you beforehand.  
(i.e.,  [0,1,2,4,5,6,7] might become  [4,5,6,7,0,1,2]).  
Find the minimum element.  
You may assume no duplicate exists in the array.  
>See [***leetcode 153***][ref7] for details.   

[ref7]:https://leetcode-cn.com/problems/find-minimum-in-rotated-sorted-array/

***My Sol***   
still bs    
 
***Code***

```cpp
class Solution {
public:
    int findMin(vector<int>& nums) {
        int left = 0, right = nums.size()-1;

        while(left < right)
        {
            int mid = left + (right-left)/2;
            if(nums[left] < nums[right]){
                return nums[left];
            }else{
                if(nums[mid] >= nums[left])
                {
                    left = mid + 1;
                }else{
                    right = mid;
                }
            }
        }

        return nums[left];
    }
};
```

博客用了模版iii的解法

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


##  二分查找模板 III

```cpp
int binarySearch(vector<int>& nums, int target){
    if (nums.size() == 0)
        return -1;

    int left = 0, right = nums.size() - 1;
    while (left + 1 < right){
        // Prevent (left + right) overflow
        int mid = left + (right - left) / 2;
        if (nums[mid] == target) {
            return mid;
        } else if (nums[mid] < target) {
            left = mid;
        } else {
            right = mid;
        }
    }

    // Post-processing:
    // End Condition: left + 1 == right
    if(nums[left] == target) return left;
    if(nums[right] == target) return right;
    return -1;
}
```

当前索引 及其左右邻居

- 搜索条件需要访问元素的直接左右邻居。
- 使用元素的邻居来确定它是向右还是向左。
- 每个步骤中至少有 3 个元素。
- 需要进行后处理。 当剩下 2 个元素时，结束。 需要评估其余元素是否符合条件。


## 34. Find First and Last Position of Element in Sorted Array
>Given an array of integers nums sorted in ascending order, find the starting and ending position of a given target value.  
Your algorithm's runtime complexity must be in the order of O(log n).  
If the target is not found in the array, return [-1, -1].  
>See [***leetcode 34***][ref8] for details.   

[ref8]:https://leetcode-cn.com/problems/find-first-and-last-position-of-element-in-sorted-array/

***My Sol***   
用了一个trick  
算mid的时候+1，这样就可以保留`left=mid`了   
 
***Code***

```cpp
class Solution {
public:
    vector<int> searchRange(vector<int>& nums, int target) {
        
        vector<int> res(2,-1);

        int left = 0, right = nums.size();
        //first
        while(left < right)
        {
            int mid = left + (right-left)/2;

            if(nums[mid] >= target)
            {
                right = mid;
            }else{
                left = mid + 1;
            }
        }

        if(left == nums.size() || nums[right] != target)
        {
            return res;
        }else{
            res[0] = left;
        }


        left = -1, right = nums.size()-1;
        //second
        while(left < right)
        {
            int mid = left + (right-left)/2 + 1;

            if(nums[mid] <= target)
            {
                left = mid;
            }else{
                right = mid-1;
            }
        }

        res[1] = left;

        return res;
    }
};
```

博主的写法也很好  

```cpp
class Solution {
public:
    vector<int> searchRange(vector<int>& nums, int target) {
        vector<int> res(2, -1);
        int left = 0, right = nums.size() - 1;
        while (left < right) {
            int mid = left + (right - left) / 2;
            if (nums[mid] < target) left = mid + 1;
            else right = mid;
        }
        if (nums[right] != target) return res;
        res[0] = right;
        right = nums.size();
        while (left < right) {
            int mid = left + (right - left) / 2;
            if (nums[mid] <= target) left = mid + 1;
            else right= mid;
        }
        res[1] = left - 1;
        return res;
    }
};
```

## 658. Find K Closest Elements
>Given a sorted array, two integers k and x, find the k closest elements to x in the array. The result should also be sorted in ascending order. If there is a tie, the smaller elements are always preferred.  
>See [***leetcode 658***][ref9] for details.   

[ref9]:https://leetcode-cn.com/problems/find-k-closest-elements/

***My Sol***   
这题也做过一次,不过还是用的较low的方法做的😭  
先找到距离最近的位置 然后往两边找    
左右都有可能保留下来  
所以用到模版iii   
 
***Code***

```cpp
class Solution {
public:
    vector<int> findClosestElements(vector<int>& arr, int k, int x) {
        int n = arr.size();
        int left = 0, right = n-1;
        int index = -1;
        vector<int> res;

        while(left + 1 < right)
        {
            int mid = left + (right-left)/2;
            if(arr[mid] == x)
            {
                index = mid;
                break;
            }else if(arr[mid] < x){
                left = mid;
            }else{
                right = mid;
            }
        }

        if(index == -1){
            int dis1 = abs(arr[left]-x), dis2 = abs(arr[right]-x);
            index = dis1 <= dis2 ? left : right;
        }

        k--;
        res.push_back(arr[index]);
        int index1 = index-1, index2 = index+1;

        
        while(k > 0)
        {
            int nums1 = index1 >= 0 ? abs(arr[index1] - x) : INT_MAX; 
            int nums2 = index2 < n ? abs(arr[index2] - x) : INT_MAX;
            
            if(nums1 <= nums2)
            {
                res.insert(res.begin(),arr[index1--]);
            }else{
                res.push_back(arr[index2++]);
            }
            k--;
        }

        return res;

    }
};
```

暂且称呼为`滑窗`的做法吧
mid xxxxxx mid+k  
中间都是一样的  
看mid和mid+k 谁更接近x  

```cpp
class Solution {
public:
    vector<int> findClosestElements(vector<int>& arr, int k, int x) {
        int left = 0, right = arr.size() - k;
        while (left < right) {
            int mid = left + (right - left) / 2;
            if (x - arr[mid] > arr[mid + k] - x) left = mid + 1;
            else right = mid;
        }
        return vector<int>(arr.begin() + left, arr.begin() + left + k);
    }
};
```

## 50. Pow(x, n)
>Implement pow(x, n), which calculates x raised to the power n (xn).  
>See [***leetcode 50***][ref10] for details.   

[ref10]:https://leetcode-cn.com/problems/powx-n/

***My Sol***   
pow pow    
 
***Code***

```cpp
class Solution {
public:
    double myPow(double x, int n) {
        double res = 1;

        for(int i = n; i != 0; i /= 2)
        {
            if(i & 1)
            {
                res *= x;
            }
            x *= x;
        }
        return n < 0 ? 1/res : res;
    }
};
```

递归解法

```cpp
class Solution {
public:
    double myPow(double x, int n) {
        if (n == 0) return 1;
        double half = myPow(x, n / 2);
        if (n % 2 == 0) return half * half;
        if (n > 0) return half * half * x;
        return half * half / x;
    }
};
```


## 367. Valid Perfect Square
>Given a positive integer num, write a function which returns True if num is a perfect square else False.
>See [***leetcode 367***][ref11] for details.   

[ref11]:https://leetcode-cn.com/problems/valid-perfect-square/

***My Sol***   
为了不用long。。。。
pow pow    
 
***Code***

```cpp
class Solution {
public:
    bool isPerfectSquare(int num) {
        if(num == 1)
        {
            return true;
        }
        
        int left = 1, right = num-1;

        while(left <= right)
        {
            int mid = left + (right-left)/2;
            int temp = num/mid;

            if(temp == mid)
            {
                int res = temp*temp;
                if(res == num){
                    return true;
                }else if(res < num)
                {
                    left = mid+1;
                }else{
                    right = mid-1;
                }
            }else if(temp > mid){
                left = mid + 1;
            }else{
                right = mid-1;
            }
        } 

        return false;
    }
};
```

偏数学的解法，完全平方数由连续的奇数组成  
`1+3+...+(2n-1) = (2n-1 + 1)n/2 = n*n`

```cpp
class Solution {
public:
    bool isPerfectSquare(int num) {
        int i = 1;
        while (num > 0) {
            num -= i;
            i += 2;
        }
        return num == 0;
    }
};
```


## 744. Find Smallest Letter Greater Than Target
>Given a list of sorted characters letters containing only lowercase letters, and given a target letter target, find the smallest element in the list that is larger than the given target.  
Letters also wrap around. For example, if the target is target = 'z' and letters = ['a', 'b'], the answer is 'a'.  
>See [***leetcode 744***][ref12] for details.   

[ref12]:https://leetcode-cn.com/problems/find-smallest-letter-greater-than-target/

***My Sol***   
right可以mid    
 
***Code***

```cpp
class Solution {
public:
    char nextGreatestLetter(vector<char>& letters, char target) {
        int left = 0, right = letters.size();

        while(left < right)
        {
            int mid = left + (right-left)/2;

            if(letters[mid] > target)
            {
                right = mid;
            }else{
                left = mid + 1;
            }
        }

        return right == letters.size() ? letters[0] : letters[right];
    }
};
```
cpp的`upper_bound`函数，找第一个大于目标值的数字，如果返回end()，说明没找到

```cpp
class Solution {
public:
    char nextGreatestLetter(vector<char>& letters, char target) {
        auto it = upper_bound(letters.begin(), letters.end(), target);
        return it == letters.end() ? *letters.begin() : *it;
    }
};
```

## 154. Find Minimum in Rotated Sorted Array II
>Suppose an array sorted in ascending order is rotated at some pivot unknown to you beforehand.  
(i.e.,  [0,1,2,4,5,6,7] might become  [4,5,6,7,0,1,2]).  
Find the minimum element.  
The array may contain duplicates.  
>See [***leetcode 152***][ref13] for details.   

[ref13]:https://leetcode-cn.com/problems/find-minimum-in-rotated-sorted-array-ii/

***My Sol***   
right--   
 
***Code***

```cpp
class Solution {
public:
    int findMin(vector<int>& nums) {
        
        int left = 0, right = nums.size()-1;

        while(left < right)
        {
            if(nums[left] < nums[right])
            {
                return nums[left];
            }else if(nums[left] == nums[right]){
                right--;
            }else{
                int mid = left + (right-left)/2;

                if(nums[mid] >= nums[left])
                {
                    left = mid+1;
                }else{
                    right = mid;
                }
            }
        }
        return nums[right];
    }
};
```

## 287. Find the Duplicate Number
>Given an array nums containing n + 1 integers where each integer is between 1 and n (inclusive), prove that at least one duplicate number must exist. Assume that there is only one duplicate number, find the duplicate one.  
>See [***leetcode 287***][ref14] for details.   

[ref14]:https://leetcode-cn.com/problems/find-the-duplicate-number/

***Sol***   
经典的一道题   
三种方法  
 
***Code***

二分 `1...n` 
判断`nums`中`<=mid`的个数 

```cpp
class Solution {
public:
    int findDuplicate(vector<int>& nums) {
        int left = 1, right = nums.size()-1;

        while(left < right)
        {
            int mid = left + (right-left)/2;

            int cnt = 0;
            count(nums, mid, cnt);

            if(cnt <= mid){
                left = mid + 1;
            }else{
                right = mid;
            }
        }
        return right;
    }

    void count(vector<int>& nums, int mid, int& cnt)
    {

        for(auto num: nums)
        {
            if(num <= mid)
            {
                cnt++;
            }
        }
    }

};
```

快慢指针   
n个数放在n+1个格子里，必有数重复；  
有数字相同，故通过数字作为索引来向前run，必能成环。  

```cpp
class Solution {
public:
    int findDuplicate(vector<int>& nums) {
        int slow = 0, fast = 0;

        while(true)
        {
            slow = nums[slow];
            fast = nums[nums[fast]];
            if(slow == fast){
                break;
            }
        }

        fast = 0;
        while(true)
        {
            slow = nums[slow];
            fast = nums[fast];
            if(slow == fast){
                break;
            }
        }

        return slow;
    }
};
```

bit位操作  
某位上无重复时有1的个数与实际1的个数相比  
大于则该位为1

```cpp
class Solution {
public:
    int findDuplicate(vector<int>& nums) {
        int n = nums.size();
        int res = 0;    

        for(int i = 0; i < 32; ++i)
        {
            int bit = 1<<i, cnt1 = 0, cnt2 = 0;
            for(int j = 0; j < n; j++)
            {
                if(j&bit)
                {
                    cnt1++;
                }

                if(nums[j]&bit)
                {
                    cnt2++;
                }
            }

            res += cnt2 > cnt1 ? bit : 0;
        }
        return res;
    }
};
```

## 4. Median of Two Sorted Arrays
>There are two sorted arrays nums1 and nums2 of size m and n respectively.  
Find the median of the two sorted arrays. The overall run time complexity should be O(log (m+n)).  
>See [***leetcode 4***][ref15] for details.   

[ref15]:https://leetcode-cn.com/problems/median-of-two-sorted-arrays/

***Sol***   
做过一次，不过没能想起来。。。。  
找第k个数  
二分k 
 
***Code***

```cpp
class Solution {
public:
    double findMedianSortedArrays(vector<int>& nums1, vector<int>& nums2) {
        
        int n1 = nums1.size(), n2 = nums2.size();
        if((n1+n2)%2){
            return (findMedianSortedArraysHelper(nums1, nums2, (n1+n2)/2+1, 0, 0));
        }else{
            return (findMedianSortedArraysHelper(nums1, nums2, (n1+n2)/2+1, 0, 0) + findMedianSortedArraysHelper(nums1, nums2, (n1+n2)/2, 0, 0))/2;
        }

    }

    double findMedianSortedArraysHelper(vector<int>& nums1, vector<int>& nums2, int k, int start1, int start2)
    {

        if(start1 == nums1.size())
        {
            return nums2[start2+k-1];
        }else if(start2 == nums2.size()){
            return nums1[start1+k-1];
        }

        if(k==1){
            return min(nums1[start1], nums2[start2]);
        }

        int index1 = start1 + k/2 - 1;
        int index2 = start2 + k/2 - 1;
        int median1 = index1 < nums1.size() ? nums1[index1] : INT_MAX;
        int median2 = index2 < nums2.size() ? nums2[index2] : INT_MAX;

        if(median1 > median2){
            return findMedianSortedArraysHelper(nums1, nums2, k-k/2, start1, start2+k/2);
        }else{
            return findMedianSortedArraysHelper(nums1, nums2, k-k/2, start1+k/2, start2);
        }
    }

};
```

## 719. Find K-th Smallest Pair Distance
>Given an integer array, return the k-th smallest distance among all the pairs. The distance of a pair (A, B) is defined as the absolute difference between A and B.  
>See [***leetcode 719***][ref16] for details.   

[ref16]:https://leetcode-cn.com/problems/find-k-th-smallest-pair-distance/

***Sol***   
很nice很nice的一道题  
二分最大距离  
对于小于每个距离有多少pair，是一个双指针
 
***Code***

```cpp
class Solution {
public:
    int smallestDistancePair(vector<int>& nums, int k) {
        sort(nums.begin(), nums.end());

        int left = 0, right = nums.back() - nums[0];

        while(left < right)
        {
            int mid = left + (right-left)/2;

            int cnt = MidCnt(mid, nums);

            if(cnt < k){
                left = mid + 1;
            }else{
                right = mid;
            }
        }
        return right;
    }


    int MidCnt(int mid, vector<int>& nums){
        int res = 0;
        //two pointers actually
        int start = 0;
        for(int i = 1; i < nums.size(); ++i){
            while(start < nums.size() && nums[i]-nums[start] > mid){
                start++;
            }
            res += (i-start);
        }
        return res;
    }


};
```

博客里的桶排序现在已经不能过oj了。。。  
但不失增长一下经验  

```cpp
class Solution {
public:
    int smallestDistancePair(vector<int>& nums, int k) {
        int n = nums.size(), N = 1000000;
        vector<int> cnt(N, 0);
        for (int i = 0; i < n; ++i) {
            for (int j = i + 1; j < n; ++j) {
                ++cnt[abs(nums[i] - nums[j])];
            }
        }
        for (int i = 0; i < N; ++i) {
            if (cnt[i] >= k) return i;
            k -= cnt[i];
        }
        return -1;
    }
};
```


## 410. Split Array Largest Sum
>Given an array which consists of non-negative integers and an integer m, you can split the array into m non-empty continuous subarrays. Write an algorithm to minimize the largest sum among these m subarrays.  
>See [***leetcode 410***][ref17] for details.   

[ref17]:https://leetcode-cn.com/problems/split-array-largest-sum/

***Sol***   
也是很好的一道题  
和上面的题二分策略一样 对Largest Sum进行二分   
重点在于split的贪心，得到最少的组数。

- 如果最少的组数还要大于m，那么肯定mid小了；  
- 如果小于等于，那么可以小于等于当前的mid；
 
***Code***


```cpp
class Solution {
public:
    int splitArray(vector<int>& nums, int m) {
        long long left = 0, right = 0;

        for(int num : nums)
        {
        	left = max((int)left, num);
            right += num;
        }

        while(left < right)
        {
        	long long mid = left + (right-left)/2;
        	if(makeCheck(nums, m, mid)){
        		right = mid;
        	}else{
        		left = mid + 1;
        	}
        }
        return right;
    }

    bool makeCheck(vector<int>& nums, int m, int mid){
    	long long curSum = 0;
        int cnt = 1;

    	for(auto num : nums){
    		curSum += num;

    		if(curSum > mid)
    		{
    			cnt++;
    			curSum = num;
    			if(cnt > m){
    				return false;
    			}
    		}
    	}

    	return true;

    }
};
```

dp解法  
`dp[i][j]` 表示j个数分为`i`组的结果  
所以可以用`dp[i-1][k]`分为了`i-1`组,再加上`k+1···j`这一组   
遍历一下`k`就可以了，`dp[i][j]`取`dp[i-1][k]`和`sum[j]-sum[k]`的最大值  

建立累积和数组方便运算  

dp还可以继续优化，如`dp[0][0] = 0` ，以及空间上的优化，下次再写的时候改进一下～🤗


```cpp
class Solution {
public:
    int splitArray(vector<int>& nums, int m) {
        
        int n = nums.size();
        vector<vector<long long>> dp(m+1, vector<long long>(n+1, INT_MAX));
        vector<long long> sum({0});

        for(auto num : nums)
        {
        	sum.push_back(sum.back()+num);
        }

        for(int i = 1; i <= n; ++i)
        {
        	dp[1][i] = sum[i];

        }

        for(int i = 2; i <= m; ++i)
        {
        	for(int j = i; j <= n; ++j)
        	{
        		for(int k = i-1; k < j; ++k)
        		{
        			long long temp = max(dp[i-1][k], sum[j] - sum[k]);
        			dp[i][j] = min(dp[i][j], temp);
        		}
        	}
        }

        return dp[m][n];

    }
};
```

终于打卡二分。。。。完结撒花🎉   
以后还要多多回顾🤔

----