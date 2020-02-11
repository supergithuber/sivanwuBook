---
description: 二叉树的数据结构
---

# 二叉树

　　首先，我们来对树进行定义：树是n（n>= 0）个节点的有限集。在任何一个非空树中：（1）有且仅有一个特定的称为“根”的节点；（2）当n>1时，其余节点可分为m（m>0）个互相相关的有限集T1、T2、T3……，其中每一个集合本身又是一棵树，并且称为根的子树。



```java
public class BinNode {
	private int m_Value;
	private BinNode m_Left;
	private BinNode m_Right;
	public void setValue(int m_Value) {
		this.m_Value = m_Value;
	}
	public int getValue() {
		return m_Value;
	}
	public void setLeft(BinNode m_Left) {
		this.m_Left = m_Left;
	}
	public BinNode getLeft() {
		return m_Left;
	}
	public void setRight(BinNode m_Right) {
		this.m_Right = m_Right;
	}
	public BinNode getRight() {
		return m_Right;
	}

	public boolean isLeaf()
	{
		return m_Left == null && m_Right == null;
	}
}
```

