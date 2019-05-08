---
layout:     post
title:      Algorithms daily 65
subtitle:   Binary Tree 小卡片～ Part III #副标题
date:       2019-05-06
author:     ASeeker
header-img: img/titanic_1.jpeg
catalog: true
tags:
    - Algorithms
    - Leetcode
    - Binary Tree
    
---

## To make each day count. 

> Reference[^1] [^2] [^3]


[^1]: 117, http://www.cnblogs.com/grandyang/p/4290148.html
[^2]: 236, http://www.cnblogs.com/grandyang/p/4641968.html
[^3]: 297, http://www.cnblogs.com/grandyang/p/4913869.html


## 117. Populating Next Right Pointers in Each Node II
>Populate each next pointer to point to its next right node. If there is no next right node, the next pointer should be set to NULL.   
>See [***leetcode 117***][ref1] for details.   

[ref1]:https://leetcode-cn.com/problems/populating-next-right-pointers-in-each-node-ii/

***My Sol***   
根据上一级找到next结点  
这题找bug找了我好久😭。。。所以敲黑板啦   
最后才发现递归的时候要先右再左，因为如果先递归左树，拿不到上一层next的完整信息    
 
***Code***

```cpp

class Solution {
public:
    Node* connect(Node* root) {
        
        if(!root || (!root->left && !root->right))
        {
        	return root;
        }

        Node* childNext = nullptr;
        Node* parentNext = root->next;

        while(parentNext)
        {
        	if(parentNext->left)
        	{
        		childNext = parentNext->left;
        		break;
        	}
        	if(parentNext->right)
        	{
        		childNext = parentNext->right;
        		break;
        	}
        	parentNext = parentNext->next;
        }


        if(root->left && root->right)
        {
        	root->left->next = root->right;
        	root->right->next = childNext;
        	connect(root->right);
        	connect(root->left);
        }else if(root->left){
        	root->left->next = childNext;
        	connect(root->left);
        }else{
        	root->right->next = childNext;
        	connect(root->right);
        }

        return root;
    }
};
```

简洁简洁  

```cpp
class Solution {
public:
    Node* connect(Node* root) {
        if (!root) return NULL;
        Node *p = root->next;
        while (p) {
            if (p->left) {
                p = p->left;
                break;
            }
            if (p->right) {
                p = p->right;
                break;
            }
            p = p->next;
        }
        if (root->right) root->right->next = p; 
        if (root->left) root->left->next = root->right ? root->right : p; 
        connect(root->right);
        connect(root->left);
        return root;
    }
};
```

这种迭代的写法很棒  
在前面加一个dummy结点固定，然后另一个指针移动；当这一层的都搞完了，就可以拿`dummy->next` 给下一层用了。


```cpp
class Solution {
public:
    Node* connect(Node* root) {
        Node* head = root;
        Node* dummy = new Node(0, nullptr, nullptr, nullptr);
        Node* cur = dummy;

        while(root)
        {
        	if(root->left)
        	{
        		cur->next = root->left;
        		cur = cur->next;
        	}

        	if(root->right)
        	{
        		cur->next = root->right;
        		cur = cur->next;
        	}

        	root = root->next;
        	if(!root)
        	{
        		root = dummy->next;
        		dummy->next = nullptr;
        		cur = dummy;
        	}
        }
        return head;
    }
};
```


## 236. Lowest Common Ancestor of a Binary Tree
>Given a binary tree, find the lowest common ancestor (LCA) of two given nodes in the tree.  
>See [***leetcode 236***][ref2] for details.   

[ref2]:https://leetcode-cn.com/problems/lowest-common-ancestor-of-a-binary-tree/

***My Sol***   
这题自己之前做过一次  
如果在左右两个子树中，访问根；否则，返回左或右；  
至于该节点如果恰好就是要找的，那么直接返回就好了。 
 
***Code***

```cpp
class Solution {
public:
    TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {
        if(!root)
        {
        	return root;
        }

        if(root->val == p->val || root->val == q->val)
        {
        	return root;
        }

        TreeNode* n1 = lowestCommonAncestor(root->left, p, q);
        TreeNode* n2 = lowestCommonAncestor(root->right, p, q);

        if(n1 && n2)
        {
        	return root;
        }else{
        	return n1? n1 : n2;
        }

    }
};
```

博客把代码稍微优化了一下   
如果返回的既不是p也不是q，那么就已经找到了，不必调用右了。  


```cpp
class Solution {
public:
    TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {
       if (!root || p == root || q == root) return root;
       TreeNode *left = lowestCommonAncestor(root->left, p, q);
       if (left && left != p && left != q) return left;
       TreeNode *right = lowestCommonAncestor(root->right, p , q);
　　　　if (left && right) return root;
       return left ? left : right;
    }
};
```


## 297. Serialize and Deserialize Binary Tree
>Serialization is the process of converting a data structure or object into a sequence of bits so that it can be stored in a file or memory buffer, or transmitted across a network connection link to be reconstructed later in the same or another computer environment.  
Design an algorithm to serialize and deserialize a binary tree. There is no restriction on how your serialization/deserialization algorithm should work. You just need to ensure that a binary tree can be serialized to a string and this string can be deserialized to the original tree structure.   
See [***leetcode 297***][ref3] for details.   

[ref3]:https://leetcode-cn.com/problems/serialize-and-deserialize-binary-tree/

***Sol***   
 这个设计题不太熟悉。。 直接看了下博客  
 用了`ostringstream`和`istringstream`很方便  
 用`#`标记空节点 遍历还是用的先序
 
***Code***

```cpp
class Codec {
public:

    // Encodes a tree to a single string.
    string serialize(TreeNode* root) {
        ostringstream out;
        serialize(root, out);
        return out.str();
    }

    // Decodes your encoded data to tree.
    TreeNode* deserialize(string data) {
        istringstream in(data);
        return deserialize(in);
    }

private:

	void serialize(TreeNode* root, ostringstream& out) {
		if(!root)
		{
			out << "# ";
		}else{
			out << root->val << " ";
			serialize(root->left, out);
			serialize(root->right, out);
		}
	}

	TreeNode* deserialize(istringstream &in)
	{
		string val;
		in >> val;

		if(val == "#")
		{
			return nullptr;
		}else{
			TreeNode* n = new TreeNode(stoi(val));
			n->left = deserialize(in);
			n->right = deserialize(in);
			return n;
		}
	}

};
```

也可以用迭代的方法写  

```cpp
class Codec {
public:
    // Encodes a tree to a single string.
    string serialize(TreeNode* root) {
        ostringstream out;
        queue<TreeNode*> q;
        if (root) q.push(root);
        while (!q.empty()) {
            TreeNode *t = q.front(); q.pop();
            if (t) {
                out << t->val << ' ';
                q.push(t->left);
                q.push(t->right);
            } else {
                out << "# ";
            }
        }
        //cout << out.str() << endl;
        return out.str();
    }
    // Decodes your encoded data to tree.
    TreeNode* deserialize(string data) {
        if (data.empty()) return nullptr;
        istringstream in(data);
        queue<TreeNode*> q;
        string val;
        in >> val;
        TreeNode *res = new TreeNode(stoi(val)), *cur = res;
        q.push(cur);
        while (!q.empty()) {
            TreeNode *t = q.front(); q.pop();
            in >> val;
            if (val != "#") {
                cur = new TreeNode(stoi(val));
                q.push(cur);
                t->left = cur;
            }
            in >> val;
            if (val != "#") {
                cur = new TreeNode(stoi(val));
                q.push(cur);
                t->right = cur;
            }
        }
        return res;
    }
};
```


----