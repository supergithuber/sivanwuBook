# 遍历

- 前序遍历

递归版本

```java
public static void preOrder(BinNode tree)
{
	if (tree == null)
	{
		return;
	}
System.out.println(tree.getValue());
preOrder(tree.getLeft());
preOrder(tree.getRight());
}
```

非递归版本

```java
public static void preOrder2(BinNode tree)
{
	if (tree == null) return;
	Stack stack = new Stack(10);
	BinNode temp = tree;
	while(temp != null)
	{
		System.out.println(temp.getValue());
		if (temp.getRight() != null) stack.push(temp.getRight());
		temp = temp.getLeft();
	}
	while(stack.get_Count() > 0)
	{
		temp = (BinNode)stack.pop();
		System.out.println(temp.getValue());
		while(temp != null)
		{
			if (temp.getRight() != null)
			{
				stack.push(temp.getRight());
			}
			temp = temp.getLeft();
		}
	}
}
```



- 中序遍历

递归版本

```java
public static void inOrder(BinNode tree)
{
	if (tree == null)
	{
		return;
	}
	inOrder(tree.getLeft());
	System.out.println(tree.getValue());
	inOrder(tree.getRight());
}
```

非递归版本

```java
public static void inOrder2(BinNode tree)
{
	if (tree == null) return;
	Stack stack = new Stack(10);
	BinNode temp = tree;
	while(temp != null)
	{
		stack.push(temp);
		temp = temp.getLeft();
	}
	while(stack.get_Count() > 0)
	{
		temp = (BinNode)stack.pop();
		System.out.println(temp.getValue());
		if (temp.getRight() != null)
		{
			temp = temp.getRight();
			stack.push(temp);
			while(temp != null)
			{
				if (temp.getLeft() != null)
				{
					stack.push(temp.getLeft());
				}
				temp = temp.getLeft();
			}
		}            
	}        
}
```



- 后序遍历

递归版本

```java
public static void postOrder(BinNode tree)
{
	if (tree == null)
	{
		return;
	}
	postOrder(tree.getLeft());
	postOrder(tree.getRight());
	System.out.println(tree.getValue());
}
```

非递归版本

```java
public static void postOrder2(BinNode tree)
{
	if (tree == null) return;
	Stack stack = new Stack(10);
	BinNode temp = tree;
	while(temp != null)
	{
		stack.push(temp);
		temp = temp.getLeft();
	}

	while(stack.get_Count() > 0)
	{
		BinNode lastVisited = temp;
		temp = (BinNode)stack.pop();
		if (temp.getRight() == null || temp.getRight() == lastVisited)
		{
			System.out.println(temp.getValue());
		}
		else if (temp.getLeft() == lastVisited)
		{
			stack.push(temp);
			temp = temp.getRight();
			stack.push(temp);
			while(temp != null)
			{
				if (temp.getLeft() != null)
				{
					stack.push(temp.getLeft());
				}
				temp = temp.getLeft();
			}
		}
	}        
}
```

