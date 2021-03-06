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



---

## 选择排序(Selection Sort)

#### 百科说法

每一次从待排序的数据元素中选出最小（或最大）的一个元素，存放在序列的起始位置，然后，再从剩余未排序元素中继续寻找最小（大）元素，然后继续放到未排序序列的首位。以此类推，直到全部待排序的数据元素排完

- 选择

选择数组剩余元素中最小的那个元素

- 交换

将此元素放到数组的开始位置，即和首个元素进行交换

- 计数器转换

未排序数组的计数器变更1位

### 总结：

**选择**最小元素，放到数组的首位，继续遍历剩下元素，选择最小元素，放到首位。

![selection-sort]({{site.url}}/images/selection-sort.png)

两个关键点：

1. 数组首个元素的位置的更新变化，要求遍历数组元素
2. 数组剩余未排序元素的遍历，要求遍历所有剩余元素

遍历整个数组（第一次遍历），将首个元素索引作为最小值索引，遍历剩下的所有元素，即首位索引+1至数组结尾。

不断地将遍历元素值与最小元素索引值进行比较（第二次遍历），如果元素值比最小值索引值小，则将该元素索引值赋值到最小值索引。这样剩下所有元素遍历完之后，得到的最小值索引及为剩下元素的最小值索引。

最后将首个元素与最小值索引的元素值进行比较，交换位置。然后首个元素索引+1，继续遍历剩下元素。如此直到将数组所有元素都作为首个元素遍历完成。

这样得到的数组即为排序好的数组，即完成了选择排序。

```java
int[] arr = new int[]{12, 4, 25, 14, 1, 33, 31, 53, 13, 9, 42, 15};
//从未被排序的元素开始遍历
for(int i= 0; i < arr.length; i++){
  //先默认首个元素最小，保存最小元素下标
	int minIndex = i;
  //再遍历首个元素之后的所有元素，不断的将最小值元素下标赋值给最小元素索引变量
	for(int j = i + 1; j < arr.length; j++){
		if(arr[j] < arr[minIndex]){
			minIndex = j;
		}
	}
	//最后将遍历首个元素之后所有元素得到的最小元素下标对应的元素值与首个元素进行交换
  if (minIndex != i){
    int temp = arr[i];
		arr[i] = arr[minIndex];
		arr[minIndex] = temp;
  }
}
```

---

## 插入排序(Insertion Sort)

循环遍历数组中的元素，每个元素和所在位置之前的所有数组元素进行对比，不符合排序规则则进行位置交换，如此遍历后直至和前面元素对比符合排序规则，则停止遍历对比。

![insertion-sort]({{site.url}}/images/insertion-sort.png)

### 插入排序与选择排序最大的不同

- 插入排序在剩余元素与前序排列好的元素进行比较时，遇到比自己小的元素即停止插入。

- 选择排序在剩余元素中必须全部遍历完成后才能得到最小元素



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

---

## 归并排序(Merge Sort)

![nlogn-n2-compare]({{site.url}}/images/nlogn-n2-compare.png)

利用归并的思想实现的排序方法，采用经典的分治(divide-and-conquer)策略，将问题分成一些小的问题然后递归求解，然后将这些答案"修补"到一起，即分而治之。效率为 O(nlogn)。

![merge-sort]({{site.url}}/images/merge-sort.png)

将要排序的数据进行归并，将其一直进行二分，最后得到的分数组进行递归排序归并。在递归过程中一定要注意数据元素所在的索引位置，需要根据其索引值进行数据的比较和赋值。

![merge-sort]({{site.url}}/images/merge-sort-index.png)

```java
private static void sort(int[] arr) {
  //包含左臂右臂，开区间，即 [left, right]
  mergeSort(arr, 0, arr.length - 1);
}

//根据分治后的数据元素索引值进行递归
private static void mergeSort(int[] arr, int leftIndex, int rightIndex) {
  //left索引大于等于right索引即表示已分治排序到最后一层，结束递归
  if (leftIndex >= rightIndex) {
    return;
  }
  //找到中间的索引值，进行分治
  int midIndex = (leftIndex + rightIndex) / 2;
  //递归调用，持续分治和归并
  mergeSort(arr, leftIndex, midIndex);
  mergeSort(arr, midIndex + 1 ,rightIndex);
  //对排序好的分治数组进行归并
  merge(arr, leftIndex, midIndex, rightIndex);
}

//对分治拥有排序好的两部分的数组进行合并，保持顺序
//注意维护索引
private static void merge(int[] arr, int leftIndex, int midIndex, int rightIndex) {
  //创建一个临时空间，与要排序的索引所在数组容量一致
  int[] aux = new int[rightIndex - leftIndex + 1];
  //将索引所在数组元素值赋值到临时数组
  for (int i = leftIndex; i <= rightIndex; i++) {
    aux[i - leftIndex] = arr[i];
  }
  //临时数组的索引，开始位置索引和中间开始位置的索引
  int i = leftIndex, j = midIndex + 1;
  //原数组中的索引，用于赋值
  for (int k = i; k <= rightIndex; k++) {
    //left索引大于中间索引，即左边部分已全部归并到原数组
    if (i > midIndex) {
      arr[k] = aux[j - leftIndex];
      j++;
    } else if (j > rightIndex) { //中间索引大于right索引，即右边部分已全部归并到原数组
      arr[k] = aux[i - leftIndex];
      i++;
    }else if (aux[i - leftIndex] < aux[j - leftIndex]) {//左边值小于右边值，赋值左边到原数组
      arr[k] = aux[i - leftIndex];
      i++;
    }else {//右边值小于左边值，赋值右边到原数组
      arr[k] = aux[j - leftIndex];
      j++;
    }
  }

}
```





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

