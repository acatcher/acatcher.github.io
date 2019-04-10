---
layout:     post
title:      Algorithms daily 39
subtitle:   Leetcode 34 658 162  #副标题
date:       2019-04-10
author:     ASeeker
header-img: img/view_2.jpg
catalog: true
tags:
    - Algorithms
    - Leetcode
    - Binary Search
    
---

## 34. Find First and Last Position of Element in Sorted Array
>Given an array of integers nums sorted in ascending order, find the starting and ending position of a given target value.  
Your algorithm's runtime complexity must be in the order of O(log n).  
If the target is not found in the array, return [-1, -1].  
>See [***leetcode 34***][ref1] for details.   

[ref1]:https://leetcode.com/problems/find-first-and-last-position-of-element-in-sorted-array/


***My Sol***  
即找target最先出现的位置和最后出现的位置  
=加在不同的地方  
这题交了四发才过。。 太难受了。。  
先是size()忘了-1  
然后判断条件还是要注意一下 `left == nums.size() || nums[left] != target`  


***Code***


```cpp
class Solution {
public:
    vector<int> searchRange(vector<int>& nums, int target) {

        vector<int> res(2,-1);
        
        if(nums.empty()){
            return res;
        }
        
        int left = 0, right = nums.size()-1;
        
        //find first
        while(left <= right){
            int median = (left+right)/2;
            
            if(nums[median] >= target){
                right = median-1;
            }else{
                left = median+1;
            }
        }
        
        if(left == nums.size() || nums[left] != target){
            return res;
        }else{
            res[0] = left;
        }
        
        //find last
        left = 0, right = nums.size()-1;
        
        //find first
        while(left <= right){
            int median = (left+right)/2;
            
            if(nums[median] <= target){
                left = median+1;
            }else{
                right = median-1;
            }
        }

        res[1] = right;
        return res;
  
    }
};
```

学习一些大神的代码 来源[leetcode的discussion][ref2]  
思路大体一致 不过他在第二次时缩短了区间  
然后由于他的区间移动不同，还有一个mid+1的trick 防止区间卡住  

[ref2]:https://leetcode.com/problems/find-first-and-last-position-of-element-in-sorted-array/discuss/14699/Clean-iterative-solution-with-two-binary-searches-(with-explanation)

```cpp
vector<int> searchRange(int A[], int n, int target) {
    int i = 0, j = n - 1;
    vector<int> ret(2, -1);
    // Search for the left one
    while (i < j)
    {
        int mid = (i + j) /2;
        if (A[mid] < target) i = mid + 1;
        else j = mid;
    }
    if (A[i]!=target) return ret;
    else ret[0] = i;
    
    // Search for the right one
    // We don't have to set i to 0 the second time.
    j = n-1; 
    while (i < j)
    {
    	// Make mid biased to the right
        int mid = (i + j) /2 + 1;	
        if (A[mid] > target) j = mid - 1; 
        // So that this won't make the search range stuck. 
        else i = mid;				
    }
    ret[1] = j;
    return ret; 
}
```




## 658. Find K Closest Elements

>Given a sorted array, two integers k and x, find the k closest elements to x in the array. The result should also be sorted in ascending order. If there is a tie, the smaller elements are always preferred.  
>See [***leetcode 658***][ref3] for details.     

[ref3]:https://leetcode.com/problems/find-k-closest-elements/


***Sol***  
这题自己没啥好的思路 就是最简单的那种思路。。 这里就不再写了  
[博客][ref4]和[discussion][ref5]里看到有两种比较厉害的解法
两种解法都用到了一个核心判断方法`x-res[0] <= res.back() - x`  
一个是利用上面那个判断进行删除头尾元素，直到数组的数量等于k  
方法二就比较巧妙了，因为是连续的区间，转化为来寻找区间的头，然后对区间的头位置进行二分，那么怎么判断这个区间呢，如果后面的比前面的更靠近，那么头就要向后移，反之就要向前找   

 [ref4]:http://www.cnblogs.com/grandyang/p/7519466.html
 [ref5]:https://leetcode.com/problems/find-k-closest-elements/discuss/106426/JavaC%2B%2BPython-Binary-Research-O(log(N-K)-%2B-K)


***Code***

删除方法

```cpp
class Solution {
public:
    vector<int> findClosestElements(vector<int>& arr, int k, int x) {
        
        vector<int> res = arr;
        
        while(res.size() > k){
            if(x-res[0] <= res.back() - x){
                res.pop_back();
            }else{
                res.erase(res.begin());
            }
        }
        return res;
    }
};
```

寻找区间首元素方法  


```cpp
class Solution {
public:
    vector<int> findClosestElements(vector<int>& arr, int k, int x) {
        
        int left = 0, right = arr.size()-k;
        
        while(left < right){
            int median = (left+right)/2;
            
            if(x-arr[median] <= arr[median+k]-x){
                right = median;
            }else{
                left = median + 1;
            }
        }
        
        return vector<int>(arr.begin()+left,arr.begin()+left+k);
    }
};
```

## 162. Find Peak Element
>A peak element is an element that is greater than its neighbors.  
Given an input array nums, where nums[i] ≠ nums[i+1], find a peak element and return its index.  
The array may contain multiple peaks, in that case return the index to any one of the peaks is fine.  
You may imagine that nums[-1] = nums[n] = -∞.  
>See [***leetcode 162***][ref6] for details.     

[ref6]:https://leetcode.com/problems/find-peak-element/


***Sol***   
[参考here。。。][ref7] 刚开始做二分是真没啥思路  
与后面一个元素比较，如果大（…）；如果小（…）  


 
[ref7]:http://www.cnblogs.com/grandyang/p/4217175.html

***Code***



```cpp
class Solution {
public:
    int findPeakElement(vector<int>& nums) {
 
        int left = 0, right = nums.size()-1;
        while(left < right){
            int mid = (left + right)/2;
            if(nums[mid] < nums[mid+1]){
                left = mid + 1;
            }else{
                right = mid;
            }
        }
        return left;
    }
};
```

皮一下改个和mid-1比较hhh  

```cpp
class Solution {
public:
    int findPeakElement(vector<int>& nums) {
 
        int left = 0, right = nums.size()-1;
        while(left < right){
            int mid = (left + right)/2 + 1;
            if(nums[mid] < nums[mid-1]){
                right = mid - 1;
            }else{
                left = mid;
            }
        }
        return left;
    }
};
```
当然了 博客里也有一个非二分的另一个思路 也有学习的价值  
你不是要找peak嘛，就让你递增，不过一旦你递减就out了（find it）  

```cpp
class Solution {
public:
    int findPeakElement(vector<int>& nums) {
 
        int i;
        for(i = 1; i < nums.size(); ++i){
            if(nums[i] < nums[i-1]){
                break;
            }
        }
        return i-1;
    }
};
```