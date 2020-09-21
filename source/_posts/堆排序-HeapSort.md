---
title: 堆排序 HeapSort
date: 2020-09-21 16:40:43
tags:
  - Sort
  - HeapSort
  - 数据结构
---

# 堆排序
  堆排序是利用堆这种数据结构而设计的一种排序算法，堆排序是一种选择排序，它的最坏，最好，平均时间复杂度均为O(nlogn)，它也是不稳定排序。

<!-- more -->

# 堆
  堆是具有以下性质的完全二叉树：每个结点的值都大于或等于其左右孩子结点的值，称为大顶堆；或者每个结点的值都小于或等于其左右孩子结点的值，称为小顶堆。

# 完全二叉树
  一棵深度为k的有n个结点的二叉树，对树中的结点按从上至下、从左到右的顺序进行编号，如果编号为i（1≤i≤n）的结点与满二叉树中编号为i的结点在二叉树中的位置相同，则这棵二叉树称为完全二叉树。
  完全二叉树的特点：叶子结点只能出现在最下层和次下层，且最下层的叶子结点集中在树的左部。需要注意的是，满二叉树肯定是完全二叉树，而完全二叉树不一定是满二叉树。

# 算法思想
  将待排序序列构造成一个堆，一般升序采用大顶堆，降序采用小顶堆，这里以升序为例，构造成大顶堆后，整个序列的最大值就是堆顶的根节点，将其与末尾的元素交换，此时末尾就是最大值，然后将剩余的n-1个元素重新构造成一个堆，得到次大元素，重复此过程，得到有序序列

# 实现细节
  - 第一次构造大顶堆时，从后往前排查根节点位置的元素，按照其与子节点的大小关系进行调整，第一次得到大顶堆
  - 调整时从根节点往下调整，因为下面的子堆都是调好的大顶堆，记录根节点位置的值，往下搜索二叉树，每次取值更大的那个分支，如果选中的节点的值大于根节点，选中的节点上移，直到没有节点可以上移，将根节点至于此处

# 实现代码
```Java
import java.util.Arrays;

public class HeapSort {
    public static void main(String[] args) {
        int[] num = {3, 5, 7, 6, 9, 4, 1, 2, 8};
        sort(num, 0, num.length);
        System.out.println(Arrays.toString(num));
    }

    public static void sort (int[] num, int st, int length) {
        for (int i = length / 2 - 1; i >= st; i--) {
            adjust(num, i, length);
        }

        for (int i = length - 1; i > st; i--) {
            swap(num, st, i);
            adjust(num, st, i);
        }
    }

    public static void adjust(int[] num, int st, int ed) {
        int tmp = num[st];
        for (int i = st * 2 + 1; i < ed; i = i * 2 + 1) {
            if (i + 1 < ed && num[i] < num[i + 1]) {
                i++;
            }
            if (num[i] > tmp) {
                num[st] = num[i];
                st = i;
            }
            else break;
        }
        num[st] = tmp;
    }

    public static void swap(int[] num, int i, int j) {
        int temp = num[i];
        num[i] = num[j];
        num[j] = temp;
    }
}
```
