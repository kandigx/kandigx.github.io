---
title:  "二分查找法"
permalink: /binary-search/
date: 2019-06-21

categories: [基础, 算法]

tags: [sorting, algorithm, basic, tree, search]
---

[TOC]

# 二分查找法

对于有序数列，才能使用二分查找法。时间复杂度O(logn)



经典实现方式：

```java
    public int binarySearch(int[] arr, int n, int target) {

        int l = 0, r = n -1 ;

        while (l <= r) {
//            int mid = (l + r) / 2;
            int mid = l + (r - l) / 2;
            if (arr[mid] == target) {
                return mid;
            }
            if (target < arr[mid]) {
                r = mid - 1;
            } else {
                r = mid + 1;
            }
        }


        return -1;
    }
```

递归实现方式：

```java

```





二分查找法的变种：floor，ceil

分别找到数组中元素出现的第一次和最后一次的位置。或者在未找到该元素的时候，返回元素应在位置之前的数组元素或之后的数组元素的位置。



## 二分搜索树(Binary Search Tree)

时间复杂度：查找元素、插入元素、删除元素的时间复杂度均为 O(logn)

### 优势

查找表的实现 - 字典数据结构

|            | 查找元素 | 插入元素 | 删除元素 |
| :--------: | :------: | :------: | :------: |
|  普通数组  |   O(n)   |   O(n)   |   O(n)   |
|  顺序数组  | O(logn)  |   O(n)   |   O(n)   |
| 二分搜索树 | O(logn)  | O(logn)  | O(logn)  |

- 高效：

不仅可以查找数据；还可以高效的插入、删除数据 - 动态维护数据

可以方便的回答很多数据之间的关系问题：min，max，floor，ceil，rank，select

### 二叉树

每个节点的键值大于左孩子

每个节点的键值小于右孩子

以左右孩子为根的子树仍为二分搜索树