---
layout:     post
title:      Algorithms daily 50
subtitle:   Leetcode 589 590 429 #副标题
date:       2019-04-21
author:     ASeeker
header-img: img/pandas_1.jpeg
catalog: true
tags:
    - Algorithms
    - Leetcode
    - Tree
    
---

>补题  
其实这不是今天做的。。。。  
三道easy N叉树的三种遍历
  
参考博客[^1]

## 589. N-ary Tree Preorder Traversal
>Given an n-ary tree, return the preorder traversal of its nodes' values.   
>See [***leetcode 589***][ref1] for details.   

[ref1]:https://leetcode-cn.com/problems/n-ary-tree-preorder-traversal/

***My Sol***   
stack


***Code***

```cpp
class Solution {
public:
    vector<int> preorder(Node* root) {

    	vector<int> res;

    	stack<Node*> st;

    	if(!root){
    		return res;
    	}

    	st.push(root);

    	while(!st.empty()){
    		Node *t = st.top();
    		st.pop();

    		int n = t->children.size();

    		for(int i = n-1; i >= 0; --i){
    			st.push(t->children[i]);
    		}

    		res.push_back(t->val);

    	}
        return res;
    }
};
```

## 590. N-ary Tree Postorder Traversal
>Given an n-ary tree, return the postorder traversal of its nodes' values.   
>See [***leetcode 590***][ref2] for details.   

[ref2]:https://leetcode-cn.com/problems/n-ary-tree-postorder-traversal/

***My Sol***   
stack


***Code***

```cpp
class Solution {
public:
    vector<int> postorder(Node* root) {
        
        vector<int> res;

        if(!root){
        	return res;
        }

        stack<Node*> st;

        st.push(root);

        while(!st.empty()){
        	Node *n = st.top();
        	if(n->children.empty()){
        		res.push_back(n->val);
        		st.pop();
        	}else{
        		int nums = n->children.size();
        		for(int i = nums-1; i >= 0; --i){
        			st.push(n->children[i]);
        		}
        		n->children.clear();
        	}

        }
        return res;
    }
};
```

另一种迭代解法  
往res头里插就好了 可以这样理解 最先遍历到的塞在最后  

```cpp
class Solution {
public:
    vector<int> postorder(Node* root) {
        if (!root) return {};
        vector<int> res;
        stack<Node*> st{{root}};
        while (!st.empty()) {
            Node *t = st.top(); st.pop();
            res.insert(res.begin(), t->val);
            for (Node* child : t->children) {
                if (child) st.push(child);
            }
        }
        return res;
    }
};
```

## 429. N-ary Tree Level Order Traversal
>Given an n-ary tree, return the level order traversal of its nodes' values. (ie, from left to right, level by level).  
>See [***leetcode 429***][ref3] for details.   

[ref3]:https://leetcode-cn.com/problems/n-ary-tree-level-order-traversal/

***My Sol***   
queue


***Code***

```cpp
class Solution {
public:
    vector<vector<int>> levelOrder(Node* root) {
    	vector<vector<int>> res;
        if(!root){
            return res;
        }
        
    	queue<Node*> q;
    	q.push(root);

    	while(!q.empty()){
            vector<int> temp;
    		for(int i = q.size(); i > 0; --i){
    			
    			Node *n = q.front();
    			temp.push_back(n->val);

    			for(auto a : n->children){
    				q.push(a);
    			}
    			q.pop();
    			
    		}
            res.push_back(temp);
    	}
    	return res;
    }
};
```

博客给的一种递归解法
  
```cpp
class Solution {
public:
    vector<vector<int>> levelOrder(Node* root) {
        vector<vector<int>> res;
        helper(root, 0, res);
        return res;
    }
    void helper(Node* node, int level, vector<vector<int>>& res) {
        if (!node) return;
        if (res.size() <= level) res.resize(res.size() + 1);
        res[level].push_back(node->val);
        for (auto a : node->children) {
            helper(a, level + 1, res);
        }
    }
};
```


[^1]: https://www.cnblogs.com/grandyang/p/9672233.html