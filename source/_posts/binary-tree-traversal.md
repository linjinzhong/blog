---
title: 二叉树遍历、构建、展开
urlname: 二叉树遍历、构建、展开
top: false
categories:
  - 面试
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

## 构建二叉树
1. 根据前序遍历和中序遍历构建
    对于任意一颗树而言，前序遍历的形式总是

        [ 根节点, [左子树的前序遍历结果], [右子树的前序遍历结果] ]

    即根节点总是前序遍历中的第一个节点。而中序遍历的形式总是

        [ [左子树的中序遍历结果], 根节点, [右子树的中序遍历结果] ]
```py
class TreeNode:
    def __init__(self, x):
        self.val = x
        self.left = None
        self.right = None

def dfs(preorder, inorder, map_v2i, p_l, p_r, i_l, i_r):
    if p_l > p_r:
        return None
    
    p_root = p_l  # 根节点在前序遍历的索引
    i_root = map_v2i[preorder[p_root]]  # 根节点在在中序遍历的索引
    num_l = i_root - i_l
    
    root = TreeNode(preorder[p_root])

    root.left = dfs(preorder, inorder, map_v2i, p_l + 1, p_l + num_l, i_l, i_root - 1)
    root.right = dfs(preorder, inorder, map_v2i, p_l + num_l + 1, p_r, i_root + 1, i_r)
    return root
    

def buildTree(preorder: List[int], inorder: List[int]) -> Optional[TreeNode]:
    # 构建中序遍历hash映射
    map_v2i = {v:i for i, v in enumerate(inorder)}
    return dfs(preorder, inorder, map_v2i, 0, len(preorder) - 1, 0, len(inorder) - 1)

```

## 二叉树展开为链表
```py
# 展开后的单链表应该与二叉树 先序遍历 顺序相同
def flatten(root):
    cur = root
    while cur:
        if cur.left:  # 左子结点存在，找到其最右节点（即左子树先序遍历的最后一个元素）
            pre = cur.left
            while pre.right:
                pre = pre.right
            pre.right = cur.right  # 当前节点右子树作为左子树最右节点的右子树
            cur.right = cur.left  # 将当前节点左子树改为右子树，因为是【根左右】关系，左子树转到右子树一样顺序，右子树是左子树最右节点的右子树
            cur.left = None  # 当前节点左子树变为None

```

##  二叉树的最近公共祖先
```py
def lowestCommonAncestor(self, root: 'TreeNode', p: 'TreeNode', q: 'TreeNode') -> 'TreeNode':
    if not root:
        return None
        
    if root == p or root == q:
        return root

    left = self.lowestCommonAncestor(root.left, p, q)
    right = self.lowestCommonAncestor(root.right, p, q)

    if left is not None and right is not None:
        return root
    if left is not None:
        return left
    if right is not None:
        return right
    return None
```


## RERERENCE
[图解 二叉树的四种遍历](https://leetcode.cn/problems/binary-tree-preorder-traversal/solution/tu-jie-er-cha-shu-de-si-chong-bian-li-by-z1m/)
