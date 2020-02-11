# 插入排序

基本思想是将一个元素插入到一个已排序的序列中，构成一个更大的序列。直接插入排序和希尔排序（shell）都属于插入排序。

- 直接插入排序

```java
public static void insertSort(int[] arrValue)
{
	if (arrValue == null || arrValue.length < 2) return;
	printArray(arrValue);
	for (int i = 1; i < arrValue.length; i++)
	{
		int temp = arrValue[i];
		int j = i;
		for (j = i; j > 0; j--)
		{
			if (arrValue[j - 1] > temp)
			{
				arrValue[j] = arrValue[j - 1];
			}
			else{
				break;
			}
		}
		arrValue[j] = temp;
		printArray(arrValue);    
	}
}
```

- 希尔（shell）排序，可以看成是分治插入排序

```java
public static void shellSort(int[] arrValue)
{
	if (arrValue == null || arrValue.length < 2) return;
	int length = arrValue.length/2;
	printArray(arrValue);
	while(length >= 1)
	{
		shell(arrValue, length);
		length = length/2;
	}
}

private static void shell(int[] arrValue, int d)
{
	for(int i = d; i < arrValue.length; i++)
	{
		if (arrValue[i] < arrValue[i -d])
		{
			int temp = arrValue[i];
			int j = i;
			while(j >= d)
			{
				if (arrValue[j-d] > temp) 
				{
					arrValue[j] = arrValue[j - d];
					j = j - d;
				}
				else{
					break;
				}                
			}
			arrValue[j] = temp;
		}
		printArray(arrValue);
	}
}
```

