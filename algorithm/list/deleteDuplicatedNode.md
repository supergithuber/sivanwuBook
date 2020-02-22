# 删除重复节点

采用hashtable来存取链表中的元素，遍历链表，当指定节点的元素在hashtable中已经存在，那么删除该节点。

```java
public static void removeDuplicate(Link list)
{
	if (list == null || list.get_Next() == null || list.get_Next().get_Next() == null) return;
	Hashtable table = new Hashtable();
	Link cur = list.get_Next();
	Link next = cur.get_Next();
	table.put(cur.get_Value(), 1);
	while(next != null)
	{
		if (table.containsKey(next.get_Value()))
		{
			cur.set_Next(next.get_Next());
			next = next.get_Next();                 
		}
		else{
			table.put(next.get_Value(), 1);
			cur= next;
			next = next.get_Next();
		}
     
	}        
}
```

