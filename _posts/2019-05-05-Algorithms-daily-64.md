---
layout:     post
title:      Algorithms daily 64
subtitle:   Binary Tree 小卡片～ Part II #副标题
date:       2019-05-05
author:     ASeeker
header-img: img/shijing_3.jpeg
catalog: true
tags:
    - Algorithms
    - Leetcode
    - Binary Tree
    
---

## 昔我往矣,杨柳依依。今我来思,雨雪霏霏。
--《诗经·小雅·采薇》

>这一段时间其他事情有点多，每天的刷题量要小一点了  
> Reference[^1]  

[^1]: 116, http://www.cnblogs.com/grandyang/p/4288151.html

## 106. Construct Binary Tree from Inorder and Postorder Traversal
>Given inorder and postorder traversal of a tree, construct the binary tree.  
>See [***leetcode 106***][ref1] for details.   

[ref1]:https://leetcode-cn.com/problems/construct-binary-tree-from-inorder-and-postorder-traversal/

***My Sol***   
后序的最后一个确定根   
中序确定左右子树   
 
***Code***



```cpp
class Solution {
public:
    TreeNode* buildTree(vector<int>& inorder, vector<int>& postorder) {
        return Helper(inorder, postorder, 0, inorder.size()-1, 0, postorder.size()-1);
    }

    TreeNode* Helper(vector<int>& inorder, vector<int>& postorder, int start1, int end1, int start2, int end2) {
    	if(start1 > end1)
    	{
    		return nullptr;
    	}

    	TreeNode* n = new TreeNode(postorder[end2]);

    	int i = start1;
    	for(; i <= end1; ++i)
    	{
    		if(inorder[i] == postorder[end2])
    		{
    			break;
    		}
    	}

    	n->left = Helper(inorder, postorder, start1, i-1, start2, start2+i-1-start1);
    	n->right = Helper(inorder, postorder, i+1, end1, start2+i-start1, end2-1);
        
        return n;

    }

};
```



## 105. Construct Binary Tree from Preorder and Inorder Traversal
>Given preorder and inorder traversal of a tree, construct the binary tree.   
>See [***leetcode 105***][ref2] for details.   

[ref2]:https://leetcode-cn.com/problems/construct-binary-tree-from-preorder-and-inorder-traversal/

***My Sol***   
和上一题几乎一样  
只是把后序确定的根改成用前序的第一个确定根     
中序确定左右子树   
 
***Code***

```cpp
class Solution {
public:
    TreeNode* buildTree(vector<int>& preorder, vector<int>& inorder) {
        return Helper(preorder, inorder, 0, preorder.size()-1, 0, inorder.size()-1);
    }

    TreeNode* Helper(vector<int>& preorder, vector<int>& inorder, int start1, int end1, int start2, int end2) {

    	if(start1 > end1)
    	{
    		return nullptr;
    	}

    	TreeNode* n = new TreeNode(preorder[start1]);

    	int i = start2;

    	for(; i <= end2; i++)
    	{
    		if(inorder[i] == preorder[start1])
    		{
    			break;
    		}
    	}

    	n->left = Helper(preorder, inorder, start1+1, start1+i-start2, start2, i-1);
    	n->right = Helper(preorder, inorder, start1+i-start2+1, end1, i+1, end2);

    	return n;

    }

};
```


## 116. Populating Next Right Pointers in Each Node
>You are given a perfect binary tree where all leaves are on the same level, and every parent has two children.  
>Populate each next pointer to point to its next right node. If there is no next right node, the next pointer should be set to NULL.  
Initially, all next pointers are set to NULL.  
See [***leetcode 116***][ref3] for details.   

[ref3]:https://leetcode-cn.com/problems/populating-next-right-pointers-in-each-node/

***My Sol***   
我的解法还是复杂了一点，递归了相邻的两个节点，会有重复计算   
 
***Code***

```cpp
class Solution {
public:
    Node* connect(Node* root) {
        if(root)
        {
        	Helper(root->left, root->right);
        }
        
        return root;
    }

    void Helper(Node* root1, Node* root2) {

    	if(!root1)
    	{
    		return;
    	}

    	root1->next = root2;

    	Helper(root1->left, root1->right);
    	Helper(root1->right, root2->left);
    	Helper(root2->left, root2->right);

    }

};
```

直接用一个结点的`left`和`right`,然后通过`root`的next

```cpp
class Solution {
public:
    Node* connect(Node* root) {
        if(!root || !root->left)
        {
        	return root;
        }

        root->left->next = root->right;
        root->right->next = root->next ? root->next->left : nullptr;

        connect(root->left);
        connect(root->right);
        
        return root;
    }
};
```

这种解法很棒  
cur在上面一层自左向右移动  

```cpp
class Solution {
public:
    Node* connect(Node* root) {
        if (!root) return NULL;
        Node *start = root, *cur = NULL;
        while (start->left) {
            cur = start;
            while (cur) {
                cur->left->next = cur->right;
                if (cur->next) cur->right->next = cur->next->left;
                cur = cur->next;
            }
            start = start->left;
        }
        return root;
    }
};
```



----