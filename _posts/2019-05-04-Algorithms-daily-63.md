---
layout:     post
title:      Algorithms daily 63
subtitle:   Binary Tree 小卡片～ Part I #副标题
date:       2019-05-04
author:     ASeeker
header-img: img/lvpishu_1.jpg
catalog: true
tags:
    - Algorithms
    - Leetcode
    - Binary Tree
    
---

## Complicated world
绿皮书这部电影真的很不错   
两个截然不同的灵魂演绎着一段难以忘怀的乐章   
一如香茗，回味悠长   

> Reference[^1] [^2] [^3] [^4] [^5] [^6]


[^1]: 144, http://www.cnblogs.com/grandyang/p/4146981.html
[^2]: 94, http://www.cnblogs.com/grandyang/p/4297300.html
[^3]: 145, http://www.cnblogs.com/grandyang/p/4251757.html
[^4]: 102, http://www.cnblogs.com/grandyang/p/4051321.html
[^5]: 101, http://www.cnblogs.com/grandyang/p/4051715.html
[^6]: 112, http://www.cnblogs.com/grandyang/p/4036961.html


## 144. Binary Tree Preorder Traversal
>Given a binary tree, return the preorder traversal of its nodes' values.  
>See [***leetcode 144***][ref1] for details.   

[ref1]:https://leetcode-cn.com/problems/binary-tree-preorder-traversal/

***My Sol***   
...
 
***Code***

栈

```cpp
class Solution {
public:
    vector<int> preorderTraversal(TreeNode* root) {
        vector<int> res;
        if(!root)
        {
        	return res;
        }

    	stack<TreeNode*> st;
    	st.push(root);

    	while(!st.empty())
    	{
    		TreeNode* n = st.top();
    		st.pop();
    		res.push_back(n->val);

    		if(n->right)
    		{
    			st.push(n->right);
    		}

    		if(n->left)
    		{
    			st.push(n->left);
    		}
    	}

    	return res;
    }
};
```

写一个无脑的递归。。

```cpp
class Solution {
public:
    vector<int> preorderTraversal(TreeNode* root) {
        vector<int> res;
    	preorderTraversalHelper(root, res);
    	return res;

    }

    void preorderTraversalHelper(TreeNode* root, vector<int>& res) {
    	if(!root)
    	{
    		return;
    	}

    	res.push_back(root->val);

    	preorderTraversalHelper(root->left, res);
    	preorderTraversalHelper(root->right, res);
    }

};
```

另一种模版写法，就是不断访问左结点    

```cpp
class Solution {
public:
    vector<int> preorderTraversal(TreeNode* root) {
        vector<int> res;
        stack<TreeNode*> s;
        TreeNode *p = root;
        while (!s.empty() || p) {
            if (p) {
                s.push(p);
                res.push_back(p->val);
                p = p->left;
            } else {
                TreeNode *t = s.top(); s.pop();
                p = t->right;
            }
        }
        return res;
    }
};
```

## 94. Binary Tree Inorder Traversal
>Given a binary tree, return the inorder traversal of its nodes' values.    
>See [***leetcode 94***][ref2] for details.   

[ref1]:https://leetcode-cn.com/problems/binary-tree-preorder-traversal/

***My Sol***   
向左直到底,使得栈中保存的节点都可以被访问    
 
***Code***

```cpp
class Solution {
public:
    vector<int> inorderTraversal(TreeNode* root) {
        vector<int> res;
        stack<TreeNode*> st;
        TreeNode* p = root;

        while(!st.empty() || p)
        {
        	if(p)
        	{
        		if(p->left)
        		{
        			st.push(p);
        			p = p->left;
        		}else{
        			res.push_back(p->val);
        			p = p->right;
        		}
        	}else{
        		TreeNode* n = st.top();
        		st.pop();
        		res.push_back(n->val);
        		p = n->right;
        	}
        }

        return res;
    }
};
```
自己的代码还是冗余了  
经典  
即保证栈中元素都可以访问了  

```cpp
class Solution {
public:
    vector<int> inorderTraversal(TreeNode* root) {
        vector<int> res;
        stack<TreeNode*> s;
        TreeNode *p = root;
        while (!s.empty() || p) {
            if (p) {
                s.push(p);
                p = p->left;
            } else {
                p = s.top(); s.pop();
                res.push_back(p->val);
                p = p->right;
            }
        }
        return res;
    }
};
```

这里还有还有一种序列化的方法，日后填坑···

## 145. Binary Tree Postorder Traversal
>Given a binary tree, return the postorder traversal of its nodes' values.  
>See [***leetcode 145***][ref3] for details.   

[ref3]:https://leetcode-cn.com/problems/binary-tree-postorder-traversal/

***My Sol***   
记录前一个访问的结点,如果当前结点的左或右孩子是之前访问的那个节点，那么说明它的子节点已经被访问了，可以访问自己了；  
否则，该节点还不能被访问，塞入它的右孩子、左孩子
 
***Code***

```cpp
class Solution {
public:
    vector<int> postorderTraversal(TreeNode* root) {
        vector<int> res;
        if(!root)
        {
        	return res;
        }

        stack<TreeNode*> st({root});
        TreeNode* pre = root;

        while(!st.empty())
        {
        	TreeNode* n = st.top();
        	if((!n->left && !n->right) || n->left == pre || n->right == pre)
        	{
        		res.push_back(n->val);
        		pre = n;
        		st.pop();
        	}else{

        		if(n->right)
        		{
        			st.push(n->right);
        		}

        		if(n->left)
        		{
        			st.push(n->left);
        		}
        	}
        }

        return res;
    }
};
```

也可以利用插入到`begin`这一trick，根-右-左的顺序进行遍历   

```cpp
class Solution {
public:
    vector<int> postorderTraversal(TreeNode* root) {
        vector<int> res;
        if(!root)
        {
        	return res;
        }

        stack<TreeNode*> st({root});

        while(!st.empty())
        {
        	TreeNode* n = st.top();
            st.pop();
        	res.insert(res.begin(), n->val);

        	if(n->left)
        	{
        		st.push(n->left);
        	}

        	if(n->right)
        	{
        		st.push(n->right);
        	}

        }

        return res;
    }
};
```

这段代码也可以这样写  

```cpp
class Solution {
public:
    vector<int> postorderTraversal(TreeNode* root) {
        vector<int> res;
        stack<TreeNode*> s;
        TreeNode *p = root;
        while (!s.empty() || p) {
            if (p) {
                s.push(p);
                res.insert(res.begin(), p->val);
                p = p->right;
            } else {
                TreeNode *t = s.top(); s.pop();
                p = t->left;
            }
        }
        return res;
    }
};
```

## 102. Binary Tree Level Order Traversal
>Given a binary tree, return the level order traversal of its nodes' values. (ie, from left to right, level by level).   
>See [***leetcode 102***][ref4] for details.   

[ref4]:https://leetcode-cn.com/problems/binary-tree-level-order-traversal/

***My Sol***   
...
 
***Code***


```cpp
class Solution {
public:
    vector<vector<int>> levelOrder(TreeNode* root) {
        vector<vector<int>> res;
        if(!root)
        {
        	return res;
        }

        queue<TreeNode*> q({root});

        while(!q.empty())
        {
        	vector<int> temp;
        	for(int i = q.size(); i > 0; i--)
        	{
        		TreeNode* n = q.front();
                q.pop();
        		temp.push_back(n->val);
        		if(n->left)
        		{
        			q.push(n->left);
        		}

        		if(n->right)
        		{
        			q.push(n->right);
        		}
        	}
            res.push_back(temp);
        }

        return res;
    }
};
```
 
递归的写法

```cpp
class Solution {
public:
    vector<vector<int>> levelOrder(TreeNode* root) {
        vector<vector<int>> res;
        levelorder(root, 0, res);
        return res;
    }
    void levelorder(TreeNode* node, int level, vector<vector<int>>& res) {
        if (!node) return;
        if (res.size() == level) res.push_back({});
        res[level].push_back(node->val);
        if (node->left) levelorder(node->left, level + 1, res);
        if (node->right) levelorder(node->right, level + 1, res);
    }
};
```



## 104. Maximum Depth of Binary Tree
>Given a binary tree, find its maximum depth.  
>See [***leetcode 104***][ref5] for details.   

[ref5]:https://leetcode-cn.com/problems/maximum-depth-of-binary-tree/

***My Sol***   
...  
迭代层次遍历可能快一点
 
***Code***

```cpp
class Solution {
public:
    int maxDepth(TreeNode* root) {
        if(!root) 
        { return 0; }
        return 1+max(maxDepth(root->left), maxDepth(root->right));
    }
};
```


## 101. Symmetric Tree
>Given a binary tree, check whether it is a mirror of itself (ie, symmetric around its center).  
>See [***leetcode 101***][ref6] for details.   

[ref6]:https://leetcode-cn.com/problems/symmetric-tree/

***My Sol***   
...  
这题之前做过一次，用两个节点进行递归  
 
***Code***

```cpp
class Solution {
public:
    bool isSymmetric(TreeNode* root) {
        if(!root)
        {
        	return true;
        }

        return isSymmetricHelper(root->left, root->right);
    }

    bool isSymmetricHelper(TreeNode* root1, TreeNode* root2) {

    	if(!root1 && !root2)
    	{
    		return true;
    	}else if(root1 && root2) {
    		if(root1->val != root2->val)
    		{
    			return false;
    		}
    		return isSymmetricHelper(root1->left, root2->right) && isSymmetricHelper(root1->right, root2->left);

    	}else{
    		return false;
    	}
    }

};
```

用迭代我想的就是用一个层次遍历，`nullptr`也塞进去，然后从两边开始向中间比较   
博客的方法简洁一点,用两个队列，一个存从右向左，一个存从左向右    

```cpp
class Solution {
public:
    bool isSymmetric(TreeNode* root) {
        if (!root) return true;
        queue<TreeNode*> q1, q2;
        q1.push(root->left);
        q2.push(root->right);
        while (!q1.empty() && !q2.empty()) {
            TreeNode *node1 = q1.front(); q1.pop();
            TreeNode *node2 = q2.front(); q2.pop();
            if (!node1 && !node2) continue;
            if((node1 && !node2) || (!node1 && node2)) return false;
            if (node1->val != node2->val) return false;
            q1.push(node1->left);
            q1.push(node1->right);
            q2.push(node2->right);
            q2.push(node2->left);
        }
        return true;
    }
};
```

## 112. Path Sum
>Given a binary tree and a sum, determine if the tree has a root-to-leaf path such that adding up all the values along the path equals the given sum.   
>See [***leetcode 112***][ref7] for details.   

[ref7]:https://leetcode-cn.com/problems/path-sum/

***My Sol***   
...   
一开始没注意最后的叶子
 
***Code***

```cpp
class Solution {
public:
    bool hasPathSum(TreeNode* root, int sum) {
        if(!root)
        {
        	return false;
        }

        return Helper(root, sum, root->val);
    }

    bool Helper(TreeNode* root, int sum, int curSum) {
    	if(!root->left && !root->right)
    	{
    		return curSum == sum;
    	}

    	return root->left && Helper(root->left, sum, curSum + root->left->val) || root->right && Helper(root->right, sum, curSum + root->right->val);
    }

};
```

博客的代码太简洁了😮

```cpp
class Solution {
public:
    bool hasPathSum(TreeNode* root, int sum) {
        if (!root) return false;
        if (!root->left && !root->right && root->val == sum ) return true;
        return hasPathSum(root->left, sum - root->val) || hasPathSum(root->right, sum - root->val);
    }
};
```

迭代的写法  

```cpp
class Solution {
public:
    bool hasPathSum(TreeNode* root, int sum) {
        if (!root) return false;
        stack<TreeNode*> st{{root}};
        while (!st.empty()) {
            TreeNode *t = st.top(); st.pop();
            if (!t->left && !t->right) {
                if (t->val == sum) return true;
            }
            if (t->right) {
                t->right->val += t->val;
                st.push(t->right);
            }
            if (t->left) {
                t->left->val += t->val;
                st.push(t->left);
            }
        }
        return false;
    }
};
```


----

