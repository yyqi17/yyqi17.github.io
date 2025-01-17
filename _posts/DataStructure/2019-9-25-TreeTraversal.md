---
layout: article
title: 数据结构 树的遍历
tags: DataStructure
categories: DataStructure
sidebar:
  nav: data-structures
mode: immersive
key: post20190925
lightbox: true
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



数据结构之树的遍历，Leetcode
<!--more-->


<br/>

<br/>

## 深度优先搜索


<br/>

### 中序遍历

<br/>


- **递归**

  　　前、中、后序的递归遍历思路都是相同的，调换顺序而已。

  <div class="snippet" markdown="1">
  
  ```c++
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
      
  private:
      vector<int> ans;
      
  public:
      vector<int> inorderTraversal(TreeNode* root) {
          
          if(root) {
              inorderTraversal(root->left);
              ans.push_back(root->val);
              inorderTraversal(root->right);
          }
          
          return ans;
      }
  };
  ```
  </div>

- **迭代**

  　　利用栈来实现迭代，前序和中序总体来讲也是相同的。
  
    　　左-中-右的顺序，因此在迭代的时候每次t不为空，都循环到t子树的最左。到了最左之后弹栈，此时弹出来的是最左节点，读取它，然后看它的右节点。继续一轮循环。

  <div class="snippet" markdown="1">
  
  ```c++
  class Solution {
      
  public:
      vector<int> inorderTraversal(TreeNode* root) {
          
          vector<int> ans;
          stack<TreeNode> s;
          TreeNode* t = root;
          
          while(t != nullptr || s.empty() == false) { 
              
              while(t) {
                  s.push(*t);
                  t = t->left;
              }
              
              ans.push_back(s.top().val);   // 这里不需要判断s.empty()，因为while里面是t非空或s非空
                                            // 若t非空，一定会进前面的while，s中至少有一个t；若s非空，s里一定有内容
              t = s.top().right;
              s.pop();
          }
          
          return ans;
      }
  };
  ```
  </div>
  

<br/>

### 前序遍历

- **递归**

  　　中-左-右的顺序。
  
  <div class="snippet" markdown="1">
  
  ```c++
  class Solution {
  
    private:
      vector<int> ans;
      
    public:
      vector<int> preorderTraversal(TreeNode* root) {
          if(root) {
              ans.push_back(root->val);
              preorderTraversal(root->left);
              preorderTraversal(root->right);
          }
          return ans;
      }
    };
  ```
  </div>

- **迭代**
  
  　　和中序遍历不同点：循环到最左节点的同时，先读取当前节点。
  
  <div class="snippet" markdown="1">
  
  ```c++
  class Solution { 
  
  private:
      vector<int> ans;
      stack<TreeNode*> s;
      
  public:
      vector<int> preorderTraversal(TreeNode* root) {

          TreeNode* p = root;
          
          while(p || !s.empty()) {
          
              while(p) {
                  ans.push_back(p->val);
                  s.push(p);
                  p = p->left;
              }
              
              p = s.top();
              s.pop();
              p = p->right;
          }

          return ans;
      }
  };
  ```
  </div>
  
  

<br/>

### 后序遍历

- **递归**

  　　（ 8 ms	11 MB ）
  
  <div class="snippet" markdown="1">
  
  ```c++
  class Solution {
  private:
      vector<int> ans;

  public:
      vector<int> postorderTraversal(TreeNode* root) {
          if(root) {
              postorderTraversal(root->left);
              postorderTraversal(root->right);
              ans.push_back(root->val);
          }
          return ans;
      }
  };
  ```
  </div>
  
- **迭代**
  
  - 方法一
  
    　　（ 4 ms	9.2 MB ）
  
      　　在前序遍历的基础上改：前序遍历 —— 根-左-右，后序遍历 —— 左-右-根
      
      　　Step1: 将前序中“当前节点插入vector最后”，改为“当前节点插入vector最前”。这样变成了 右-左-根（完全逆序）。
      
      　　Step2: 将左和右的顺序调换
    
    <div class="snippet" markdown="1">
    
    ```c++
    class Solution {
    private:
        vector<int> ans;
        stack<TreeNode*> s;

    public:
        vector<int> postorderTraversal(TreeNode* root) {

            TreeNode* p = root;

            while(p || !s.empty()) {

                while(p) {
                    ans.insert(ans.begin(),p->val);   // 注意这里在前插入的语法
                    s.push(p);
                    p = p->right;   //相比于前序遍历，调换了左和右子树读取的顺序
                }

                p = s.top();
                s.pop();
                p = p->left;
            }

            return ans;
        }
    };
    ```
    </div>
    
  - 方法二
  
    　　（ 4 ms	9.3 MB ）
  
      　　其实和上一种总体来讲是相同的，都是逆序输出，不过具体实现有所不同。这里是利用压栈，先压左再压右，然后弹栈一次，来实现上一方法中Step2的效果。
      
      　　换言之，后序遍历的读取顺序是 —— 从下到上，从左到右；逆序的话就是 —— 从上到下，从右到左。（注意，不能说 广搜 + 逆序输出 = 后序遍历）
      
    <div class="snippet" markdown="1">
    
    ```c++
    class Solution {
    private:
        vector<int> ans;
        stack<TreeNode*> s;

    public:
        vector<int> postorderTraversal(TreeNode* root) {

           if(root == nullptr)
               return ans;

            s.push(root);
            TreeNode* p;

            while(!s.empty()) {

                p = s.top();
                s.pop();
                ans.insert(ans.begin(), p->val);

                if(p->left != nullptr)    // 先压栈左孩子，这样弹栈的时候弹的就是右孩子了
                    s.push(p->left);
                if(p->right != nullptr) 
                    s.push(p->right);
            }

            return ans;
        }
    };
    ```
    </div>
    
  - 方法三
  
    　　（ 4 ms	9.2 MB ）
  
      　　传统方法，比较复杂，但是还是尽量记住
      
    <div class="snippet" markdown="1">
    
    ```c++
    class Solution {
    private:
        vector<int> ans;
        stack<TreeNode*> s;

    public:
        vector<int> postorderTraversal(TreeNode* root) {

            TreeNode* p = root;
            TreeNode* pre = nullptr;

            while(p || !s.empty()) {    // 注意这里外面还有一层循环噢

                while(p) {
                    s.push(p);
                    p = p->left;
                }
                p = s.top();

                // 注意两个指针置为nullptr的时机
                if(!p->right || pre == p->right) {    // 如果已经读取过右子树
                    s.pop();
                    ans.push_back(p->val);
                    pre = p;
                    p = nullptr;
                } else {                                        // 如果没有读过右子树
                    p = p->right;
                    pre = nullptr;
                }
            }
            return ans;
        }
    };
    ```
    </div>
  

<br/>

<br/>

## 广度优先搜索


<br/>

- **递归**

  <div class="snippet" markdown="1">
  
  ```c++
  class Solution {
    
  private:
      vector<vector<int>> levels;

  public:
      vector<vector<int>> levelOrder(TreeNode* root) {

          if(!root)
              return levels;

          helper(root, 0);
          return levels;
      }

      void helper(TreeNode* p, int level) {

          // 如果当前层是第一次被访问到，那么增加一个序列
          if(levels.size() == level) {
              levels.push_back(vector<int>());    // ！！这里，如果用 new vector<int>()，拿到的是一个指针！！
          }

          // 加入当前节点的值
          levels[level].push_back(p->val);

          //递归查看左右孩子
          if(p->left)
              helper(p->left, level+1);
          if(p->right) 
              helper(p->right, level+1);
      }
  };
  ```
  </div>
  
- **迭代**

  <div class="snippet" markdown="1">
  
  ```c++
  class Solution {
    
  private:
      vector<vector<int>> levels;

  public:
      vector<vector<int>> levelOrder(TreeNode* root) {

          if(!root)
              return levels;

          queue<TreeNode*> q;     // 首先根节点加入队列
          q.push(root);

          TreeNode* p;

          while(q.empty() == 0){

              int width = q.size();   // 获得当前层的总节点数
              vector<int> temp;

              for(int i=0; i<width; i++) {
                  p = q.front();
                  temp.push_back(p->val);     // 用一个临时的temp存储每一层
                  q.pop();
                  if(p->left)
                      q.push(p->left);
                  if(p->right)
                      q.push(p->right);
              }

              levels.push_back(temp);          // 最后把当前层的vector temp加入levels
          }

          return levels;
      }

  };
  ```
  </div>

