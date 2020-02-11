# 交换链表中任意两个元素

思路：首先需要保存两个元素的pre节点和next节点，然后分别对pre节点和next节点的Next属性重新赋值。需要注意的是，当两个元素师相邻元素时，需要特殊处理，否则会将链表陷入死循环。



```java
public static void swap(Link list, Link element1, Link element2)
{
	if (list == null || list.get_Next() == null || list.get_Next().get_Next() == null || 
element1 == null || element2 == null || element1 == element2) 
	return;
           
	Link pre1 = null, pre2 = null, next1 = null, next2 = null;
	Link cur1=element1, cur2=element2;
	Link temp = list.get_Next();
	boolean bFound1 = false;
	boolean bFound2 = false;
	while(temp != null)
	{
		if(temp.get_Next() == cur1)
		{
			pre1=temp;
			next1 = temp.get_Next().get_Next();
			bFound1 = true;
		}
		if (temp.get_Next() == cur2)
		{
			pre2 = temp;
			next2 = temp.get_Next().get_Next();
			bFound2=true;
		}
		if (bFound1 && bFound2) break;
		temp = temp.get_Next();
	}
 
	if (cur1.get_Next() == cur2)
	{
		temp = cur2.get_Next();
		pre1.set_Next(cur2);
		cur2.set_Next(cur1);
		cur1.set_Next(temp);
	}
	else if (cur2.get_Next() == cur1)
	{
		temp = cur1.get_Next();
		pre2.set_Next(cur1);
		cur1.set_Next(cur2);
		cur2.set_Next(temp);
	}
	else{
		pre1.set_Next(cur2);
		cur1.set_Next(next2);
		pre2.set_Next(cur1);
		cur2.set_Next(next1);
	}
}
```

