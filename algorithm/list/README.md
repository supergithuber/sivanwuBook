---
description: 单链表的数据结构
---

# 链表

```java
public class Link {
	private int value;
	private Link next;
	public void set_Value(int m_Value) {
		this.value = m_Value;
	}
	public int get_Value() {
		return value;
	}
	public void set_Next(Link m_Next) {
		this.next = m_Next;
	}
	public Link get_Next() {
		return next;
	}
}
```

