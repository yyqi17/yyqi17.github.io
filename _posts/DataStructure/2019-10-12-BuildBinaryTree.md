---
layout: article
title: 数据结构 构造二叉树
tags: DataStructure
categories: DataStructure
sidebar:
  nav: data-structures
mode: immersive
key: post20191012
lightbox: true
header:
  theme: dark
article_header:
  type: overlay
  theme: dark
  background_color: '#203028'
  background_image:
    gradient: 'linear-gradient(135deg, rgba(94, 40, 161 , .4), rgba(217, 169, 59, .4))'
    src: https://s2.ax1x.com/2019/10/12/uXAhUU.jpg
---

从前序与中序、中序与后序序列构造
<!--more-->

<br/>

## 一、从前序与中序序列构造二叉树

[**LeetCode 105**](https://leetcode-cn.com/problems/construct-binary-tree-from-preorder-and-inorder-traversal/)

你可以假设树中<u>没有重复的元素</u>。

**思路**

　　前序序列 根-左-右，因此<u>第一个节点是根节点</u>。利用根节点的值，可以从中序序列 左-根-右 中，<u>划分出左子树和右子树</u>。

> preorder = \[3,9,20,15,7\]　　　　　　　　3
>
> inorder = \[9,3,15,20,7\]　　　　　　　/　　　\
>
>　　　　　　　　　　　　　　　　9　　　　20
>
>　　　　　　　　　　　　　　　　　　　/　　　　\
>
>　　　　　　　　　　　　　　　　　　15　　　　　7


　　具体实现时，前序序列用一个递增的 preIndex 读取即可，因为每次递归都是先进入左子树，中-左-右的顺序可以保证每次读到的都是正确位置的根节点。

　　而中序序列用字典存放，key-节点的值，value-节点在中序序列的下标。前序读到节点 v 时，可以根据中序序列判断出 v 的左右子树，同时也是判断递归出口。（当前节点为空即是递归的出口）

<div class="snippet" markdown="1">

```c++
class Solution {
    
private:
    vector<int> preOrder;
    vector<int> inOrder;
    int preIndex;
    map<int, int> inorderDic;
    
public:
    TreeNode* buildTree(vector<int>& preorder, vector<int>& inorder) {
        
        this->preOrder = preorder;
        this->inOrder = inorder;
        
        preIndex = 0;
        for(int i=0; i<inorder.size(); i++) {
            inorderDic[inorder[i]] = i;   // 题目规定没有重复的元素，所以可以从字典找
        }
        
        return buildTree(0, inorder.size() - 1);  // 前面都是预处理，从这里开始正式进入算法核心
    }
    
    TreeNode* helper(int start, int end) {
        
        if(start > end)     // start 和 end 其实只有判断递归终点的作用
            return nullptr;
        
        int root = preOrder[preIndex];
        preIndex++;                      // 因为先递归进入左子树，所以preIndex顺序向下取，可以保证取到的都是正确的根
        int inIndex = inorderDic[root]; 
        
        TreeNode* t = new TreeNode(root);
        t->left = helper(start, inIndex-1);  // 左子树从start到中序序列当前位置的前面
        t->right = helper(inIndex+1, end);   // 右子树从中序序列当前位置的后面到end
        
        return t;
    }  
};
```

</div>


<br/>

## 二、从中序与后序序列构造二叉树

[**LeetCode 106**](https://leetcode-cn.com/problems/construct-binary-tree-from-inorder-and-postorder-traversal/)

**思路**



> postorder = \[9,15,7,20,3\]　　　　　　　　3
>
> inorder = \[9,3,15,20,7\]　　　　　　　　/　　\
>
>　　　　　　　　　　　　　　　　　9　　　20
>
>　　　　　　　　　　　　　　　　　　　/　　　　\
>
>　　　　　　　　　　　　　　　　　　15　　　　　7

　　本质上和前序、中序构造二叉树相同，后序为 左-右-根，因此我们从后向前读取，且递归时先进右子树，再进左子树。

<div class="snippet" markdown="1">

```c++
class Solution {
    
private:
    vector<int> inOrder;
    vector<int> postOrder;
    map<int, int> inorderMap;
    int postIndex;
    
public:
    TreeNode* buildTree(vector<int>& inorder, vector<int>& postorder) {
        
        this->inOrder = inorder;
        this->postOrder = postorder;
        
        postIndex = postorder.size()-1;
        for(int i=0; i<inorder.size(); i++) {
            inorderMap[inorder[i]] = i;
        }
        
        return helper(0, inorder.size()-1);
    }
    
    TreeNode* helper(int start, int end) {
        
        if(start > end)
            return nullptr;
        
        int root = postOrder[postIndex];
        postIndex--;
        int inIndex = inorderMap[root];
        
        TreeNode* t = new TreeNode(root);
        t->right = helper(inIndex+1, end);
        t->left = helper(start, inIndex-1);
        
        return t;
    }
};
```

</div>

<br/>
