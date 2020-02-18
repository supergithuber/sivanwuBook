# 删除指定节点

可以分情况讨论，如果指定节点不是尾节点，那么可以采用取巧的方式，将指定节点的值修改为下一个节点的值，将指定节点的Next属性设置为Next.Next；但如果指定节点为尾节点，那么只能是从头开始遍历。

```java
public static void delete(Link list, Link element)
{
	if (element.get_Next() != null)
	{
		element.set_Value(element.get_Next().get_Value());
		element.set_Next(element.get_Next().get_Next());
	}
	else{
		Link current = list.get_Next();
		while(current.get_Next() != element)
		{
			current = current.get_Next();
		}
		current.set_Next(null);
	}
}
```

