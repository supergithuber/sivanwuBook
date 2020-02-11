# 倒数第N个元素

采用两个游标来遍历链表，第1个游标先走N步，然后两个游标同时前进，当第一个游标到最后时，第二个游标就是想要的元素。

```java
public static Link find(Link list, int rPos)
{
	if (list == null || list.get_Next() == null)
	{
		return null;
	}
	int i = 1;
	Link first = list.get_Next();
	Link second = list.get_Next();
	while(true)
	{
		if (i==rPos || first == null) break;
		first = first.get_Next();
		i++;
	}
	if (first == null)
	{
		System.out.println("The length of list is less than " + rPos + ".");
		return null;
	}
	while(first.get_Next() != null)
	{
		first = first.get_Next();
		second = second.get_Next();
	}

	return second;
}
```

