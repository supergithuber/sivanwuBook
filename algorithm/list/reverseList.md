# 链表反转

直接循环的方式：

```java
public static Link Reverve(Link list)
	{
	if (list == null || list.get_Next() == null || list.get_Next().get_Next() == null)
	{
		System.out.println("list is null or just contains 1 element, so do not need to reverve.");
		return list;
	}        
	Link current = list.get_Next();
	Link next = current.get_Next();
	current.set_Next(null);
	while(next != null)
	{
		Link temp = next.get_Next();
		next.set_Next(current);
		current = next;
		next = temp;
	}
	list.set_Next(current);

	return list;
}
```

递归方式：

```java
public static Link RecursiveReverse(Link list)
{
	if (list == null || list.get_Next() == null || list.get_Next().get_Next() == null)
	{
		System.out.println("list is null or just contains 1 element, so do not need to reverve.");
		return list;
	}

	list.set_Next(Recursive(list.get_Next()));

	return list;
}

private static Link Recursive(Link list)
{
	if (list.get_Next() == null)
	{
		return list;
	}
	Link temp = Recursive(list.get_Next());
	list.get_Next().set_Next(list);
	list.set_Next(null);

	return temp;
}
```

