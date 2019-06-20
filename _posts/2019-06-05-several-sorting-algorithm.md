---
title:  "几种排序算法"
permalink: /several-sorting-algorithm/
date: 2019-06-05

categories: [基础, 算法]

tags: [sorting, algorithm, basis]
---

[TOC]

# 几种排序算法

遇到一个问题，先尝试用最简单的方法来解决。之后再去进行改进优化。

### 随机数组生成

为了进行对排序算法进行测试，需要生成很多的测试用例，这里使用随机生成的数组。

```java
public static int[] generateRandomArray(int n, int rangeL, int rangeR) {
        int[] arr = new int[n];

        Random random = new Random();

        for (int i = 0; i < n; i++) {
            arr[i] = random.nextInt(rangeR) % (rangeR - rangeL + 1) + rangeL;
          //nextInt(int x) 返回[0,x)之间的 int 类型随机数，
        }

        return arr;
    }
```



## 选择排序(Selection Sort)

每一次从待排序的数据元素中选出最小（或最大）的一个元素，存放在序列的起始位置，然后，再从剩余未排序元素中继续寻找最小（大）元素，然后放到已排序序列的末尾。以此类推，直到全部待排序的数据元素排完

- 选择

选择数组剩余元素中最小的那个元素

- 交换

将此元素放到数组的开始位置，即和首个元素进行交换

- 计数器转换

未排序数组的计数器变更1位

```java
int[] arr = new int[]{12, 4, 25, 14, 1, 33, 31, 53, 13, 9, 42, 15};
for(int i= 0; i < arr.length -1; i++){
	int minIndex = i;
  //选择
	for(int j = i + 1; j < arr.length; j++){
		if(arr[j] < arr[minIndex]){
			minIndex = j;
		}
	}
	//交换
	int temp = arr[i];
	arr[i] = arr[minIndex];
	arr[minIndex] = temp;
}
```



## 插入排序(Insertion Sort)

循环遍历数组中的元素，每个元素和所在位置之前的所有数组元素进行对比，不符合排序规则则进行位置交换，如此遍历后直至和前面元素对比符合排序规则，则停止遍历对比。



```java
//从第二个元素开始遍历插入，直到最后一个元素
for(int i = 1; i < arr.length(); i++){
  //将此元素和之前所有的元素进行比较。从后往前，成功则停止，否则继续比较。最后的比较位置为 j = 1
	for(int j = i; j > 0 &&  arr[j] < arr[j - 1]; j--){
			swap(arr[j], arr[j - 1]);
	}
}
```

改进要点：将每次对比后交换，改成前一个位置数值赋值到后一个位置

```java
for( int i = 1; i < arr.length(); i++){
  //保存当前元素值，即插入值
	T e = arr[i];
	int j;
  //从当前位置开始和前一元素比较，若前一元素大于要插入的值，则将前一元素值赋值到当前位置，代替交换
	for (j = i; j > 0 && arr[j - 1] > e; j--) {
		arr[j] =  arr[j - 1];
	}
  //遍历结束，则前一元素的值小于等于要插入的值，则将插入值赋值到当前位置
	arr[j] = e;
}
```

## 归并排序(Merge Sort)

利用归并的思想实现的排序方法，采用经典的分治(divide-and-conquer)策略，将问题分成一些小的问题然后递归求解，然后将这些答案"修补"到一起，即分而治之。效率为 O(nlogn)



## 快速排序(Quick Sort)

随机选择一个元素，将这个元素放到其应该在排序好的位置上。其之前的元素都小于（大于）该元素，其之后的元素都大于（小于）该元素。再对其之前和之后的元素进行快速排序，直至所有元素都处于正确的位置上。

分为两步：

1. 分区。将随机元素(一般是首个元素)放到其排序好的位置上，即对所有的元素进行小于和大于该元素的分区。
2. 递归。对分区中的元素再进行分区，直至所有的元素都排序好。

分区的思路：

取首个元素 l ，其值为 v ，要对数组进行相对于该元素的分区，需要记录小于和大于该元素的位置的分界点，即索引 j ，而当前要和 l 元素进行比较元素位置，记为 i 。这样在数组中存在两个分区：`arr[l+1...j] < v`  和 `arr[j+1 ...i-1] > v` ，即小于和大于 v 的两个分区。

- 如果当前 i 位置（位于所有分区之后）的元素 e ，其值大于 v ，由于其已经在大于 v 的分区的后面，不需要任何操作，所以只需要即 i++ ，继续比较下一位置上的元素即可。

- 如果当前 i 位置的元素 e ，其值小于 v ，则将 i 元素和 j 元素交换，然后 j++ ，再继续比较下一元素，即 i++ 。
- 所有元素都已经分区完成后，整个数组结构变为了：首个元素即位置为 l 的元素 v ，其之后是小于该 v 值的分区，该分区最后一个元素的位置为 j ，j 位置之后是大于该 v 值的分区。最后，将 l 位置元素与 j 位置元素进行交换，得到了一个根据 v 值来分区的小于和大于分区。



```java
public static void main(String[] args) {

        int[] arr = new int[]{1, 23, 4, 5, 6, 7, 8, 9, 5, 42, 4, 6, 7, 4, 2, 3, 5, 57, 4, 4, 2, 35, 436, 2, 25, 3, 7, 32, 5, 63, 63, 564, 2, 42, 643, 643 , 5346, 34};

        QuickSort quickSort = new QuickSort();
        quickSort.quickSort(arr, 10);

        System.out.println(Arrays.toString(arr));
    }

    public void quickSort(int[] arr, int n) {
        quickSort(arr, 0, arr.length-1);
    }

    public void quickSort(int[] arr, int l, int r) {

        if (l >= r) {
            return;
        }

        int p = partition(arr, l, r);
        quickSort(arr, l , p - 1);
        quickSort(arr, p + 1 , r);

    }

    private int partition(int[] arr, int l, int r) {
        int v = arr[l];
        int j = l;
        int temp;
        for (int i = l + 1; i <= r; i++) {
            if (arr[i] < v) {
                temp = arr[j+1];
                arr[j+1] = arr[i];
                arr[i] = temp;
                j++;
            }
        }
        temp = arr[j];
        arr[j] = arr[l];
        arr[l] = temp;

        return j;
    }
```

