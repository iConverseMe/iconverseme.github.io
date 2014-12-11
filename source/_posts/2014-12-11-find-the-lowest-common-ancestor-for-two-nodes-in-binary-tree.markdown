---
layout: post
title: "Find the Lowest Common Ancestor for Two Nodes in Binary Tree"
date: 2014-12-11 22:34:40 +0800
comments: true
categories: 
---
Find the Lowest Common Ancestor for two TreeNodes in Binary Tree is a very common question, called LCA problem.

For binary tree root, find the LCA of k1 & k2.

**Solition 1:** The basic solution is use two list to save the path from root to k1, and the path from root to k2, then compare the two lists. I have implement this use stack instead of list, for stack is First-In-Last-Out, so the first common node is the LCA of k1 & k2 when pop the nodes.
```
public boolean findPath(TreeNode root, int val, Stack<Integer> s) {
		if(root == null) return false;
		if(root.val == val) return true;
		s.push(root.val);

		if((root.left && findPath(root.left, val, s)) || (root.right && findPath(root.right, val, s)))
			return true;
		return false;
}

// use two stacks to save the path root -> node
// output stack s1 & s2, the first common node
// return the first common node
public int findLastestCommon(TreeNode root, TreeNode p, TreeNode q) {
		Stack<Integer> s1 = new Stack<Integer>();
		Stack<Integer> s2 = new Stack<Integer>();

		findPath(root, p.val, s1);
		findPath(root, q.val, s2);

		while(!s1.isEmpty() && !s2.isEmpty()) {
			int k1 = s1.pop();
			int k2 = s2.pop();
			if(k1 == k2) {
				return k1;
			}
		}
		return root.val;
}
```

**Solution 2:** Recursively find the LCA of k1 & k2, if they two exist diff LCA, so return root. If there exists k1 == root.val or k2 == root.val, the LCA of k1 & k2 is root absolutely.
```
// value k1 & k2 compare with root
// if k1 == root or k2 == root, return root
public TreeNode findLCA(TreeNode root, int k1, int k2) {
		if(root == null) return root;
		if(root.val == k1 || root.val == k2) return root;
		
		TreeNode left = findLCA(root.left, k1, k2);
		TreeNode right = findLCA(root.right, k1, k2);
		
		if(left != null && right != null) return root;
		return left == null ? right : left;
}
```
For solution 2, when the tree is Binary Search Tree(BST), we can first check when k1 & k2 are in the same tree(left tree or right tree), if they are in different sub-tree, the LCA of k1 & k2 is root absolutely.
```
// find node k1 & k2's lowest common ancestor
// if k1 & k2 both bigger than root, the LCA is in the right tree
// if k1 & k2 both less than root, the LCA is in the left tree
public TreeNode findLCAinBST(TreeNode root, TreeNode k1, TreeNode k2) {
		if(root == null) return root;
		if(k1 == null || k2 == null) return root;
		
		if(k1.val < root.val && k2.val < root.val) 
			return findLCAinBST(root.left, k1, k2);

		if(k1.val > root.val && k2.val > root.val) 
			return findLCAinBST(root.left, k1, k2);
		return root;
}
```
