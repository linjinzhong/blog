---
title: 二叉树遍历
urlname: 二叉树遍历
top: false
categories:
  - 算法
tags:
  - 二叉树遍历
author: jesonlin
date: 2023-05-17 11:41:31
updated: 2023-05-17 11:41:31
---

二叉树的遍历包括深度遍历和广度遍历。深度遍历有前序、中序以及后序三种遍历方法，广度遍历即我们平常所说的层次遍历。

<!-- more -->


## 深度遍历
0. 基础
    ![二叉树](/二叉树.png)

    已知中序遍历和另一种遍历顺序，则可把这棵树构造出来：由先序遍历或后序遍历知根节点，而中序遍历中根节点左端的为左子树，右端的右子树，再由先序遍历或后序遍历知子根节点，递归地构造子树。

1. 前序遍历-[父-左-右]-[E, B, A, D, C, G, F, I, H]
```py
def dfs(root, res):
    if not root:
        return
    res.append(root.val)  # 将根节点加入结果
    dfs(root.left, res)  # 递归左节点
    dfs(root.right, res)  # 递归右节点

def iterative(root, res):
    if not root:
        return
    cur, stack = root, []
    while stack or cur:
        while cur:  # 根节点及其左孩子入栈
            res.append(cur.val)  # 加入结果
            stack.append(cur)
            cur = cur.left
        tmp = stack.pop()  # 每弹出一个元素，就到该元素的右子节点处理
        cur = tmp.right


# 初始化栈，并将根节点入栈；
# 当栈不为空时：
    # 弹出栈顶元素 node，并将值添加到结果中；
    # 如果 node 的右子树非空，将右子树入栈；
    # 如果 node 的左子树非空，将左子树入栈；
def iterative_2(root, res):
    if not root:
        return
    stack = [root]
    while stack:
        cur = stack.pop()
        res.append(cur.val)  # 根节点加入结果
        if cur.right:  # 右子节点入栈
            stack.append(cur.right)
        if cur.left:   # 左子节点入栈
            stack.append(cur.left)

def pre_order_traversal(root):
    res = []
    # dfs(root, res)
    iterative(root, res)
    return res
```

2. 中序遍历-[左-父-右]-[A, B, C, D, E, F, G, H, I]
```py
def dfs(root, res):
    if not root:
        return
    dfs(root.left, res)  # 递归左节点
    res.append(root.val)  # 将根节点加入结果
    dfs(root.right, res)  # 递归右节点

def iterative(root, res):
    cur, stack = root, []
    while stack or cur:
        while cur:  # 左子节点不停入栈，到达最左端的叶子节点
            stack.append(cur)
            cur = cur.left
        tmp = stack.pop  # 出栈
        res.append(tmp.val)  # 加入结果
        cur = tmp.right  # 处理右子节点
    return res 

def in_order_traversal(root):
    res = []
    # dfs(root, res)
    iterative(root, res)
    return res
```

3. 后序遍历-[左-右-父]-[A, C, D, B, F, H, I, G, E]
```py
def dfs(root, res):
    if not root:
        return
    dfs(root.left, res)  # 递归左节点
    dfs(root.right, res)  # 递归右节点
    res.append(root.val)  # 将根节点加入结果

def iterative(root, res):  # 与前序遍历相反操作在结果取反
    if not root:
        return
    cur, stack = root, []
    while stack or cur:
        while cur:  # 根节点及其右孩子入栈
            res.append(cur.val)  # 加入结果
            stack.append(cur)
            cur = cur.right
        tmp = stack.pop()  # 每弹出一个元素，就到该元素的左子节点处理
        cur = tmp.left
    res.reverse()  # 原地反转

def post_order_traversal(root):
    res = []
    # dfs(root, res)
    iterative(root, res)
    return res
```

## 广度遍历
1. 层序遍历
```py
def level_order_traversal(root):
    if not root:
        return []

    que, res = [root], []
    while que:
        n = len(que)  # 每层数量
        level = []  # 每层结果
        for i in range(n):
            cur = que.pop(0)
            level.append(cur.val)
            if cur.left:
                que.append(cur.left)
            if cur.right:
                que.append(cur.right)
        res.append(level)
    return res
```


## RERERENCE
[图解 二叉树的四种遍历](https://leetcode.cn/problems/binary-tree-preorder-traversal/solution/tu-jie-er-cha-shu-de-si-chong-bian-li-by-z1m/)
