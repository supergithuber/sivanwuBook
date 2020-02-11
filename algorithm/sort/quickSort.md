---
description: 快速排序的Java实现
---

# 快速排序

```java
public static void quickSort(int[] arrValue, int left, int right)
{
    if(left < right)
    {
        int i = division(arrValue, left, right);
        quickSort(arrValue, left, i - 1);
        quickSort(arrValue, i + 1, right);
    }
}

private static int division(int[] arrValue, int left, int right)
{
    int baseValue = arrValue[left];
    int midPos = left;
    printArray(arrValue);
    for (int i = left + 1; i <= right; i++)
    {
        if(arrValue[i] < baseValue) midPos++;
    }

    if (midPos == left)
    {
        return midPos;
    }

    arrValue[left] = arrValue[midPos]; 
    arrValue[midPos] = baseValue;

    if (midPos == right)
    {
        return midPos;
    }
    for (int i = left; i < midPos; i++)
    {
        if (arrValue[i] > baseValue)
        {
            for (int j = right; j > midPos; j--)
            {
                if (arrValue[j] < baseValue)
                {
                    int temp = arrValue[i];
                    arrValue[i] = arrValue[j];
                    arrValue[j] = temp;
                    right--;
                    break;
                }
            }
        }
    }

    return midPos;
}
```

