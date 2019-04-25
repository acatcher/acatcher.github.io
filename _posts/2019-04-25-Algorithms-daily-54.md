---
layout:     post
title:      Algorithms daily 54
subtitle:   Leetcode 701 703 235 #副标题
date:       2019-04-25
author:     ASeeker
header-img: img/cat_2.jpeg
catalog: true
tags:
    - Algorithms
    - Leetcode
    - Tree
    - Heap
    
---

参考博客[^1] [^2] [^3] [^4]



## 701. Insert into a Binary Search Tree
>Given the root node of a binary search tree (BST) and a value to be inserted into the tree, insert the value into the BST. Return the root node of the BST after the insertion. It is guaranteed that the new value does not exist in the original BST.  
Note that there may exist multiple valid ways for the insertion, as long as the tree remains a BST after insertion. You can return any of them.    
>See [***leetcode 701***][ref1] for details.   

[ref1]:https://leetcode-cn.com/problems/insert-into-a-binary-search-tree/

***My Sol***   
简单的递归寻找位置即可  


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
    TreeNode* insertIntoBST(TreeNode* root, int val) {
  		      
  		if(!root){
  			return new TreeNode(val);
  		}

  		insertIntoBSTHelper(root,val,nullptr,0);
  		return root;
    }

    void insertIntoBSTHelper(TreeNode* root, int val, TreeNode *pre, int d){

    	if(!root){
    		TreeNode *t = new TreeNode(val);
    		if(d == 0){
    			pre->left = t;
    		}else if(d == 1){
    			pre->right = t;
    		}
    		return;
    	}

    	if(root->val > val){
    		insertIntoBSTHelper(root->left, val, root, 0);
    	}else{
    		insertIntoBSTHelper(root->right, val, root, 1);
    	}

    }

};
```

***Other Sol from blogs***

姜还是老的辣  
only四行  

```cpp
class Solution {
public:
    TreeNode* insertIntoBST(TreeNode* root, int val) {
        if (!root) return new TreeNode(val);
        if (root->val > val) root->left = insertIntoBST(root->left, val);
        else root->right = insertIntoBST(root->right, val);
        return root;
    }
};
```

迭代解法  
速度快了很多  

```cpp
class Solution {
public:
    TreeNode* insertIntoBST(TreeNode* root, int val) {
  		      
  		if(!root){
  			return new TreeNode(val);
  		}

  		TreeNode *p = root;

  		
  		while(true){

  			if(p->val < val){
  				if(p->right)
  					p = p->right;
  				else
  					break;
  			}else{
  				if(p->left)
  					p = p->left;
  				else
  					break;
  			}
  		}

  		if(p->val < val){
  			p->right = new TreeNode(val); 
  		}else{
  			p->left = new TreeNode(val); 
  		}

  		return root;

    }

};
```

## 703. Kth Largest Element in a Stream
>Design a class to find the kth largest element in a stream. Note that it is the kth largest element in the sorted order, not the kth distinct element.  
Your KthLargest class will have a constructor which accepts an integer k and an integer array nums, which contains initial elements from the stream. For each call to the method KthLargest.add, return the element representing the kth largest element in the stream.  
>See [***leetcode 703***][ref3] for details.   

[ref3]:https://leetcode-cn.com/problems/kth-largest-element-in-a-stream/

***My Sol***   
一道easy题 二分 找到位置 插入进去  


***Code***

```cpp
class KthLargest {
public:
	int k;
	vector<int> nums;
    
    KthLargest(int k, vector<int>& nums) {
        this->k = k;
    	this->nums = nums;
    	sort(this->nums.begin(), this->nums.end());
    	
    }
    
    int add(int val) {

    	int left = 0, right = nums.size();

    	while(left < right){
    		int mid = (left + right)/2;
    		if(nums[mid] > val){
    			right = mid;
    		}else{
    			left = mid+1;
    		}
    	}
        
    	this->nums.insert(this->nums.begin()+right, val);
        int n = nums.size();
    	return nums[n-k];

    }
};
```
***Other Sol from blogs***

二分解法显然慢了很多，可以使用一个优先队列，只保留k个即可  
实际上就是说自己之前的解法中可以舍弃掉前`n-k`个数  

```cpp
class KthLargest {
public:
	int k;
	priority_queue<int, vector<int>, greater<int>> q;
    
    KthLargest(int k, vector<int>& nums) {
        this->k = k;

        for(auto a : nums){
            q.push(a);
        }

        while(q.size() > k){
            q.pop();
        }

    }
    
    int add(int val) {

        q.push(val);
        if(q.size()>k)
            q.pop();
        return q.top();

    }
};
```

***priority_queue 总结***

基本操作都是一样的
    
> - top 
 empty 
 size 
 push 
 pop

`priority_queue<Type, Container, Functional>`  
默认用operator<，队头元素最大

`priority_queue<int, vector<int>, greater<int> > p;`
最小堆  

自定义函数  

```cpp
struct Node{
	int x,y;
	Node(int a=0, int b=0):
		x(a), y(b) {}
};
 
struct cmp{
	bool operator()(Node a, Node b){
		if(a.x == b.x)	return a.y>b.y;
		return a.x>b.x;
	}
};
 
int main(){
	priority_queue<Node, vector<Node>, cmp>p;
	
}
```

也可以在类中直接重载运算符  

```cpp
struct tmp1 //运算符重载< 
{ 
    int x; 
    tmp1(int a) {x = a;} 
    bool operator<(const tmp1& a) const { 
        return x < a.x; //大顶堆 
    } 
};

```

## 235. Lowest Common Ancestor of a Binary Search Tree
>Given a binary search tree (BST), find the lowest common ancestor (LCA) of two given nodes in the BST.  
>See [***leetcode 235***][ref4] for details.   

[ref4]:https://leetcode-cn.com/problems/lowest-common-ancestor-of-a-binary-search-tree/

***My Sol***   
看这俩数字是不是在节点的两侧 如果是，那返回这个节点就成啦  
不是，则根据大小往左/右子树里面去找    


***Code***



```cpp
class Solution {
public:
    TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {
        
        int smallValue = p->val < q->val ? p->val : q->val;
        int largeValue = p->val > q->val ? p->val : q->val;

        while(true){

            if(root->val >= smallValue && root->val <= largeValue){
                return root;
            }else if(root->val < smallValue){
                root = root->right;
            }else{
                root = root->left;
            }

        }
    }
};
```


[^1]: https://www.cnblogs.com/grandyang/p/9914546.html

[^2]: https://www.cnblogs.com/grandyang/p/9941357.html
[^3]: https://blog.csdn.net/xiaoquantouer/article/details/52015928

[^4]: https://blog.csdn.net/weixin_36888577/article/details/79937886
