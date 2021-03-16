---
layout: article
title: 数据结构 二叉搜索树
tags: 数据结构 LeetCode 树 搜索树
categories: DataStructure
sidebar:
  nav: data-structures
key: post20191009
lightbox: true
mode: immersive
header:
  theme: dark
article_header:
  type: overlay
  theme: dark
  background_color: '#203028'
  background_image:
    gradient: 'linear-gradient(135deg, rgba(34, 139, 125 , .2), rgba(139, 34, 139, .2))'
    src: https://s2.ax1x.com/2019/10/11/uH4v79.png
---

包括二叉搜索树的简介、验证

<!--more-->



<br/>

## 一、概念

　　1. 节点的左子树**只**包含小于当前节点的数
  
　　2. 节点的右子树**只**包含大于当前节点的数
  
　　3. 所有的左子树和右子树自身也是一个二叉搜索树
  
<br/>

<br/>

## 二、验证二叉搜索树

　　[**LeetCode 98**](https://leetcode-cn.com/problems/validate-binary-search-tree/)
  
<br/>

### 1、迭代
  
　　这里C++迭代的时候，相比于Java和python有一个不方便的地方：Java和python在helper函数的参数可以直接用int，其存在null的int；而C++中int没有null值，因此只能用指针。
    
<div class="snippet" markdown="1">

```c++
class Solution {
public:
    bool isValidBST(TreeNode* root) {

        return helper(root, nullptr, nullptr);

    }

    bool helper(TreeNode* p, int* lower, int* higher) {

        if(p == nullptr)
            return true;

        int val = p->val;
        if(lower != nullptr && val <= *lower)    return false;
        if(higher != nullptr && val >= *higher)   return false;

        if(!helper(p->left, lower, &val))     return false;
        if(!helper(p->right, &val, higher))   return false;

        return true;
    }
};
```
</div>

<br/>
  
### 2、中序遍历

　　中序遍历是<u>左-中-右</u>的顺序，对于二叉搜索树来说，遍历到的每一个元素都应该比上一个大、比下一个小。
    
　　这里我们可以简化一下，只要确保每次新读到的都比上一个大，就可以保证它是二叉搜索树。因此只需要保存前一节点的信息。
   
　　在具体实现时，有一个需要注意的地方：<mark>INT_MIN = -2147483648</mark>，<mark>INT_LONG = -9223372036854775808</mark>。这里需要用long类型，因为测试样例中有小于INT_MIN的例子。
    
<div class="snippet" markdown="1">

```c++
class Solution {
public:
    bool isValidBST(TreeNode* root) {

        stack<TreeNode*> s;
        long pre = LONG_MIN;
        TreeNode* p = root;

        while(p || !s.empty()) {

            while(p) {
                s.push(p);
                p = p->left;
            }

            p = s.top();
            s.pop();

            // 下面这里就是验证BST的逻辑，如果当前节点小于等于前面读取到的节点，则不是BST
            if(p->val <= pre)   return false;

            pre = p->val;       // 千万不要忘记更新
            p = p->right;
        }
        return true;
    }
};
```
</div>
    
<br/>

<br/>

## 三、共多少种二叉搜索树

　　[**LeetCode 96**](https://leetcode-cn.com/problems/unique-binary-search-trees/)
  
　　给定一个整数 n，求以 1 ... n 为节点组成的二叉搜索树有多少种？

<br/>

### 1、动态规划

　　对于一个序列 1 ... n，想要统计他们能组成多少种二叉搜索树，我们可以轮流让 1 ... n 作为根节点。如 i 作为根，那么它的左子树就是由 1 ... i-1 组成的二叉搜索树，右子树同理。因此，我们可以递归地从小树构建大树。
  
　　这里需要说明的是，i 作为根时，其右子树由 i+1 ... n 组成，但显而易见，他们组合的二叉搜索树数量和 1 ... n-i 组合的是相同的。
  
　　问题可以分解为规模更小的子问题，因此可以采用动态规划的方法 —— 存储并复用子问题的解，而不是递归地（即重复地）调用。
  
<br/>

**算法**

　　我们定义两个函数：
  
　　　　- G(n)    —— 长度为 n 的序列能构成的二叉搜索树数量
    
　　　　- F(i,n)  —— 以 i 为根节点、长度为n的序列构成的二叉搜索树数量
    
　　<u>G(n) 是我们最后解决问题需要的函数</u>。
  
　　根据前面的分析，G(n) 通过遍历所有节点作为根节点求和得到：
  <center>
  $$ G(n) = \sum_{i=1}^nF(i,n) $$
  </center>
  
　　而对于初始的特殊值，空树或只有根时，则只有一种可能情况：
  <center>
  $$ G(0) = 1;\quad G(1) = 1 $$
  </center>
  
　　对于 F(i,n)，它是左右子树个数的笛卡尔积。如 F(3,7)，是以3为根节点、长度为7的序列能组成的搜索树数量。左子树从 \[1,2\] 构建，右子树从 \[4,5,6,7\]构建。
  
　　我们发现，\[1,2\] 构建的左子树数量可以用 G(2) 来表示，\[4,5,6,7\] 构建的右子树数量可以用 G(4) 来表示。那么 F(3,7) = G(2) $\times$ G(4)。可以概括得到如下公式：
  <center> 
  $$ F(i,n) = G(i-1) \times G(n-i) $$
</center>
  
　　和公式1结合，就可以得到**最终 G(n) 的递归表达式**：
  <center>
  $$
  G(n) = \sum_{i=1}^n G(i-1) \times G(n-i)
  $$
  </center>
  
　　在实际实现中，我们将 G 从小到大计算，并依次存储下来。这里C++实现的时候采用vector会更方便安全。
    

<div class="snippet" markdown="1">

```c++
class Solution {
public:
    int numTrees(int n) {
        
        vector<int> G(n+1, 0);  // C++中最好用vector而不是int[]
        G[0] = 1;
        G[1] = 1;
        
        for(int i=2; i<=n; i++) {
            for(int j=1; j<=i; j++) {
                G[i] += G[j-1] * G[i-j];
            }
        }
        
        return G[n];
    }
};
```

</div>

<br/>

### 2、数学演绎法 卡特兰数

　　G(n) 的值实际是卡特兰数 C(n)，卡特兰数的一个便于计算的定义如下：
  
  <center>
  $$
  C_0 = 1, \quad C_{n+1} = \frac{2(2n+1)}{n+2} C_n
  $$
  </center>

<div class="snippet" markdown="1">

```c++
class Solution {
public:
    int numTrees(int n) {
        
        long c = 1;       // 这里最好用long存储，避免溢出
        
        for(int i=0; i<n; i++) {
            c = c * 2 * (2*i+1) / (i+2);  // c 放在最前面乘，不要放在 (i+2) 后面
        }
        
        return c;
    }
};
```

</div>


