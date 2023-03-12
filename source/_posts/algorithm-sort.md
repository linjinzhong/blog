---
title: 排序算法
urlname: 排序算法
top: false
categories:
  - 算法
tags:
  - 排序算法
author: jesonlin
date: 2022-12-15 12:42:36
update: 2022-12-15 12:42:36
---


十大经典排序算法介绍和实现

<!-- more -->

# 排序算法


## 排序算法介绍
| 排序算法 | 最坏时间复杂度 | 最好时间复杂度 | 平均时间复杂度 | 空间复杂度 | 稳定性 |
| :---: | :---: | :---: | :---: | :---: | :---: |
| 冒泡排序 | O(n^2) | O(n) | O(n^2) | O(1) | 稳定 |
| 选择排序 | O(n^2) | O(n^2) | O(n^2) | O(1) | 不稳定 |
| 插入排序 | O(n^2) | O(n) | O(n^2) | O(1) | 稳定 |
| 希尔排序 | O(nlogn) | O(n) | O(nlogn) | O(1) | 不稳定 |
| 归并排序 | O(nlogn) | O(nlogn) | O(nlogn) | O(n) | 稳定 |
| 快速排序 | O(n^2) | O(nlogn) | O(nlogn) | O(logn) | 不稳定 |
| 堆排序   | O(nlogn) | O(nlogn) | O(nlogn) | O(1) | 不稳定 |
| 计数排序 | O(n+m) | O(n+m) | O(n+m) | O(m) | 稳定 |
| 桶排序   | O(n^2) | O(n+m) | O(n+m) | O(n+m) | 稳定 |
| 基数排序 | O(n x m) | O(n x m) | O(n x m) | O(n+m) | 稳定 |


## 冒泡排序
1. 介绍  
    1. 比较相邻元素，交换；
    2. 第一轮将第n大（最大）元素逐个交换冒泡到位置n（最后）；
    3. 第x轮将第x大元素逐个交换冒泡到位置x；
    4. 第n轮将最1大（最小）元素逐个交换冒泡到位置1；
    5. **（无序区，有序区），从无序区通过相邻交换将最大元素冒泡到有序区前端*。*
2. 复杂度  
    时间复杂度：O(n^2) ~ O(n^2) ~ O(n)  
    空间复杂度：O(1)  
    稳定性：稳定  
3. 代码-python  
    ```python
    #!/bin/python3

    def bubble_sort(arr):
        for i in range(1, len(arr)):
            for j in range(len(arr)-i):
                if arr[j] > arr[j+1]:
                    arr[j], arr[j+1] = arr[j+1], arr[j]
        return arr

    arr = [3,6,9,2,0,1,5,4,8,7]
    print("排序前：%s" % arr)
    sorted_arr = bubble_sort(arr)
    print("排序后：%s" % sorted_arr)
    ```
4. 代码-c++  
    ```c++
    #include<iostream>
    using namespace std;

    //整数或浮点数皆可使用,若要使用类(class)或结构体(struct)时必须重载大于(>)运算符
    template<typename T>
    void BubbleSort(T arr[], int len) {
        for (int i = 1; i < len; i++)
            for (int j = 0; j < len - i; j++)
                if (arr[j] > arr[j+1])
                    swap(arr[j], arr[j+1]);
    }
    template<typename T>
    void PrintArr(T arr[], int len) {
        for (int i = 0; i < len; i++)
            cout << arr[i] << ' ';
        cout << endl;
    }

    int main() {
        int arr[] = {3, 6, 9, 2, 0, 1, 5, 4, 8, 7};
        int len = sizeof(arr) / sizeof(*arr);
        cout << "排序前： ";
        PrintArr(arr, len);
        BubbleSort(arr, len);
        cout << "排序后： ";
        PrintArr(arr, len);
        return 0;
    }
    ```

## 选择排序
1. 介绍  
    1. 在未排序的序列里寻找最小元素索引，交换到起始位置；
    2. 再从剩余未排序元素中寻找最小元素，放到已排序序列的末尾；
    3. 重复2直到最后一个元素；
    4. **（有序区，无序区），从无序区找一个最小的放在有序区的后面，比较多，换得少。**
2. 复杂度  
    时间复杂度：O(n^2) ~ O(n^2) ~ O(n^2)  
    空间复杂度：O(1)  
    稳定性：不稳定  
3. 代码-python  
    ```python
    #!/bin/python3

    def select_sort(arr):
        for i in range(len(arr)-1):
            min_idx = i
            for j in range(i+1, len(arr)):
                if arr[j] < arr[min_idx]:
                    min_idx = j
            if i != min_idx:
                arr[i], arr[min_idx] = arr[min_idx], arr[i]
        return arr

    arr = [3,6,9,2,0,1,5,4,8,7]
    print("排序前：%s" % arr)
    sorted_arr = select_sort(arr)
    print("排序后：%s" % sorted_arr)
    ```
4. 代码-c++  
    ```c++
    #include<iostream>
    using namespace std;

    //整数或浮点数皆可使用,若要使用类(class)或结构体(struct)时必须重载大于(>)运算符
    template<typename T>
    void SelectSort(T arr[], int len) {
        for (int i = 0; i < len - 1; i++) {
            int min_idx = i;
            for (int j = i + 1; j < len; j++)
                if (arr[j] < arr[min_idx])
                    min_idx = j;
            if (i != min_idx)
                swap(arr[i], arr[min_idx]);
        }
    }
    template<typename T>
    void PrintArr(T arr[], int len) {
        for (int i = 0; i < len; i++)
            cout << arr[i] << ' ';
        cout << endl;
    }

    int main() {
        int arr[] = {3, 6, 9, 2, 0, 1, 5, 4, 8, 7};
        int len = sizeof(arr) / sizeof(*arr);
        cout << "排序前： ";
        PrintArr(arr, len);
        SelectSort(arr, len);
        cout << "排序后： ";
        PrintArr(arr, len);
        return 0;
    }
    ```

## 插入排序
1. 介绍  
    1. 先用第一个元素做已排序序列，其余为未排序序列；
    2. 遍历未排序序列，将每个元素插入到之前已排序序列的合适位置；
    3. 插入过程将已排序序列元素逐个后移；
    4. **（有序区，无序区），把无序区的第一个元素插入到有序区的合适位置。**
2. 复杂度  
    时间复杂度：O(n^2) ~ O(n^2) ~ O(n)  
    空间复杂度：O(1)  
    稳定性：稳定  
3. 代码-python  
    ```python
    #!/bin/python3

    def insert_sort(arr):
        for i in range(1, len(arr)):
            pre_idx = i - 1
            cur = arr[i]
            while pre_idx >= 0 and cur < arr[pre_idx]:
                arr[pre_idx+1] = arr[pre_idx]
                pre_idx -= 1
            arr[pre_idx+1] = cur
        return arr

    arr = [3,6,9,2,0,1,5,4,8,7]
    print("排序前：%s" % arr)
    sorted_arr = insert_sort(arr)
    print("排序后：%s" % sorted_arr)
    ```
4. 代码-c++  
    ```c++
    #include<iostream>
    using namespace std;

    //整数或浮点数皆可使用,若要使用类(class)或结构体(struct)时必须重载大于(>)运算符
    template<typename T>
    void InsertSort(T arr[], int len) {
        for (int i = 1; i < len; i++) {
            int pre_idx = i - 1;
            T cur = arr[i];
            while (pre_idx >= 0 && cur < arr[pre_idx]) {
                arr[pre_idx+1] = arr[pre_idx];
                pre_idx--;
            }
            arr[pre_idx+1] = cur;
        }
    }
    template<typename T>
    void PrintArr(T arr[], int len) {
        for (int i = 0; i < len; i++)
            cout << arr[i] << ' ';
        cout << endl;
    }

    int main() {
        int arr[] = {3, 6, 9, 2, 0, 1, 5, 4, 8, 7};
        int len = sizeof(arr) / sizeof(*arr);
        cout << "排序前： ";
        PrintArr(arr, len);
        InsertSort(arr, len);
        cout << "排序后： ";
        PrintArr(arr, len);
        return 0;
    }
    ```


## 希尔排序
1. 介绍  
    1. 也称递减增量排序算法，是插入排序的一种更高效的改进版本；
    2. 先将整个待排序的记录序列分割成为若干子序列分别进行直接插入排序，待整个序列中的记录"基本有序"时，再对全体记录进行依次直接插入排序；
    3. **每一轮按照事先决定的间隔进行插入排序，间隔依次减少直到1。**
2. 复杂度  
    时间复杂度：O(nlogn) ~ O(nlogn) ~ O(n)  
    空间复杂度：O(1)  
    稳定性：不稳定  
3. 代码-python  
    ```python
    #!/bin/python3

    def shell_sort(arr):
        gap = len(arr) // 2
        while gap > 0:
            for i in range(gap, len(arr)):
                pre_idx = i - gap
                cur = arr[i]
                while pre_idx >= 0 and cur < arr[pre_idx]:
                    arr[pre_idx + gap] = arr[pre_idx]
                    pre_idx -= gap
                arr[pre_idx + gap] = cur
            gap = gap // 2
        return arr

    arr = [3, 6, 9, 2, 0, 1, 5, 4, 8, 7]
    print("排序前：%s" % arr)
    sorted_arr = shell_sort(arr)
    print("排序后：%s" % sorted_arr)
    ```
4. 代码-c++  
    ```c++
    #include<iostream>
    using namespace std;

    //整数或浮点数皆可使用,若要使用类(class)或结构体(struct)时必须重载大于(>)运算符
    template<typename T>
    void ShellSort(T arr[], int len) {
        for (int gap = len / 2; gap > 0; gap /= 2) {
            for (int i = gap; i < len; i++) {
                int pre_idx = i - gap;
                T cur = arr[i];
                while (pre_idx >= 0 && cur < arr[pre_idx]) {
                    arr[pre_idx + gap] = arr[pre_idx];
                    pre_idx -= gap;
                }
                arr[pre_idx + gap] = cur;
            }
        }
    }
    template<typename T>
    void PrintArr(T arr[], int len) {
        for (int i = 0; i < len; i++)
            cout << arr[i] << ' ';
        cout << endl;
    }

    int main() {
        int arr[] = {3, 6, 9, 2, 0, 1, 5, 4, 8, 7};
        int len = sizeof(arr) / sizeof(*arr);
        cout << "排序前： ";
        PrintArr(arr, len);
        ShellSort(arr, len);
        cout << "排序后： ";
        PrintArr(arr, len);
        return 0;
    }
    ```


## 归并排序
1. 介绍  
    1. 分治法，自上而下递归或者自下而上的迭代；
    2. **把序列分为两段，递归保证两段有序后再合并有序的两段。**
2. 复杂度  
    时间复杂度：O(nlogn) ~ O(nlogn) ~ O(nlogn)  
    空间复杂度：O(n)  
    稳定性：稳定  
3. 代码-python  
    ```python
    #!/bin/python3

    def merge(arr_left, arr_right):
        arr_res = []
        while arr_left and arr_right:
            if arr_left[0] <= arr_right[0]:
                arr_res.append(arr_left.pop(0))
            else:
                arr_res.append(arr_right.pop(0))
        if arr_left:
            arr_res.extend(arr_left)
        if arr_right:
            arr_res.extend(arr_right)
        return arr_res

    def merge_sort(arr):
        if len(arr) < 2:
            return arr
        middle = len(arr) // 2
        arr_left, arr_right = arr[0:middle], arr[middle:]
        return merge(merge_sort(arr_left), merge_sort(arr_right))

    arr = [3, 6, 9, 2, 0, 1, 5, 4, 8, 7]
    print("排序前：%s" % arr)
    sorted_arr = merge_sort(arr)
    print("排序后：%s" % sorted_arr)
    ```
4. 代码-c++  
    ```c++
    #include<iostream>
    #include<cmath>
    using namespace std;

    //整数或浮点数皆可使用,若要使用类(class)或结构体(struct)时必须重载大于(>)运算符
    template<typename T>
    void MergeSort(T arr[], int len) {
        T *arr_ori = arr;
        T *res = new T[len];
        int gap = 1;
        while (gap < len) {  // 递归每轮
            int p = 0;
            for (int j = 0; j < len; j += gap * 2) {  // 每轮对比组
                int ll = j, lr = j + gap;
                int rl = lr, rr = lr + gap;
                while (rl < len && ll < lr && rl < rr) {
                    if (arr[ll] <= arr[rl]) {
                        res[p++] = arr[ll++];
                    }
                    else {
                        res[p++] = arr[rl++];
                    }
                }
                while (ll < lr && ll < len)
                    res[p++] = arr[ll++];
                while (rl < rr && rl < len)
                    res[p++] = arr[rl++];
            }
            T *tmp = arr;
            arr = res;
            res = tmp;
            gap *= 2;
        }
        if (arr != arr_ori) {
            for (int i = 0; i < len; i++)
                res[i] = arr[i];
            res = arr;
        }
        delete[] res;
    }

    template<typename T>
    void PrintArr(T arr[], int len) {
        for (int i = 0; i < len; i++)
            cout << arr[i] << ' ';
        cout << endl;
    }

    int main() {
        int arr[] = {3, 6, 9, 2, 0, 1, 5, 4, 8, 7};
        int len = sizeof(arr) / sizeof(*arr);
        cout << "排序前： ";
        PrintArr(arr, len);
        MergeSort(arr, len);
        cout << "排序后： ";
        PrintArr(arr, len);
        return 0;
    }
    ```


## 快速排序
1. 介绍  
    1. 冒泡排序基础上的递归分治法；
    2. 从数列中挑出一个元素，称为 "基准"（pivot）；
    3. 重新排序数列，所有元素比基准值小的摆放在基准前面，所有元素比基准值大的摆在基准的后面；
    4. 在这个分区退出之后，该基准就处于数列的中间位置。这个称为分区（partition）操作；    
    5. 递归地（recursive）把小于基准值元素的子数列和大于基准值元素的子数列排序；
    6. **（小区间，基准元素吗，大区间）在区间中随机挑选一个元素作为基准，将小于基准的元素放在基准之前，大于基准的元素放在基准之后，再分别对小数和大数区间进行排序。**
2. 复杂度  
    时间复杂度：O(n2) ~ O(nlogn) ~ O(nlogn)  
    空间复杂度：O(logn)  
    稳定性：不稳定  
3. 代码-python  
    ```python
    #!/bin/python3

    def partition(arr, left, right):
        middle_value = arr[left]  # 取首元素为分割点
        p = left
        for i in range(left + 1, right):
            if arr[i] <= middle_value:
                p += 1
                arr[p], arr[i] = arr[i], arr[p]
        arr[left], arr[p] = arr[p], arr[left]
        return p

    def partition2(arr, left, right):
        middle_value = arr[right - 1]  # 取尾元素尾分割点
        p = left
        for i in range(left, right - 1):
            if arr[i] <= middle_value:
                arr[p], arr[i] = arr[i], arr[p]
                p += 1
        arr[p], arr[right - 1] = arr[right - 1], arr[p]
        return p

    def quick_sort(arr, left, right):
        if left < right:
            middle = partition1(arr, left, right)
            quick_sort(arr, left, middle)
            quick_sort(arr, middle + 1, right)
        return arr

    arr = [3, 6, 9, 2, 0, 1, 5, 4, 8, 7]
    print("排序前：%s" % arr)
    sorted_arr = quick_sort(arr, 0, len(arr))
    print("排序后：%s" % sorted_arr)
    ```
4. 代码-c++  
    ```c++
    #include<iostream>
    using namespace std;

    template<typename T>
    int Partion(T arr[], int left, int right) {
        T middle_value = arr[left];
        int p = left;
        for (int i = left + 1; i < right; i++) {
            if (arr[i] < middle_value) {
                p += 1;
                swap(arr[p], arr[i]);
            }
        }
        swap(arr[p], arr[left]);
        return p;
    }

    //整数或浮点数皆可使用,若要使用类(class)或结构体(struct)时必须重载大于(>)运算符
    template<typename T>
    void QuickSort(T arr[], int left, int right) {
        if (left < right) {
            int middle = Partion(arr, left, right);
            QuickSort(arr, left, middle);
            QuickSort(arr, middle + 1, right);
        }
    }

    template<typename T>
    void PrintArr(T arr[], int len) {
        for (int i = 0; i < len; i++)
            cout << arr[i] << ' ';
        cout << endl;
    }

    int main() {
        int arr[] = {3, 6, 9, 2, 0, 1, 5, 4, 8, 7};
        int len = sizeof(arr) / sizeof(*arr);
        cout << "排序前： ";
        PrintArr(arr, len);
        QuickSort(arr, 0, len);
        cout << "排序后： ";
        PrintArr(arr, len);
        return 0;
    }
    ```


## 堆排序
1. 介绍  
    1. 创建一个最大堆 H[0……n-1]；
    2. 把堆首（最大值）和堆尾互换；
    3. 除去堆尾后调整堆；
    2. 重复2，3；
    6. **（最大堆，有序区）交换堆顶元素与有序区前一个元素，再调整堆。**
2. 复杂度  
    时间复杂度：O(nlogn) ~ O(nlogn) ~ O(nlogn)  
    空间复杂度：O(1)  
    稳定性：不稳定  
3. 代码-python  
    ```python
    #!/bin/python3

    def adjust_heap(arr, head, tail):
        left = head * 2 + 1
        right = left + 1
        if right < tail and arr[right] > arr[left]:
            left = right
        if left < tail and arr[head] < arr[left]:
            arr[head], arr[left] = arr[left], arr[head]
            adjust_heap(arr, left, tail)

    def build_max_heap(arr):
        for i in range(len(arr) // 2 - 1, -1, -1):
            adjust_heap(arr, i, len(arr))

    def heap_sort(arr):
        build_max_heap(arr)
        for i in range(len(arr) - 1, 0, -1):
            arr[0], arr[i] = arr[i], arr[0]
            adjust_heap(arr, 0, i)
        return arr

    arr = [3, 6, 9, 2, 0, 1, 5, 4, 8, 7]
    print("排序前：%s" % arr)
    sorted_arr = heap_sort(arr)
    print("排序后：%s" % sorted_arr)
    ```
4. 代码-c++  
    ```c++
    #include<iostream>
    #include<cmath>
    using namespace std;

    template<typename T>
    int AdjustHeap(T arr[], int head, int tail) {
        int left = head * 2 + 1;
        int right = left + 1;
        if (right < tail && arr[right] > arr[left])
            left = right;
        if (left < tail && arr[head] < arr[left]) {
            swap(arr[head], arr[left]);
            AdjustHeap(arr, left, tail);
        }
    }

    template<typename T>
    int BuildMaxHeap(T arr, int len) {
        for (int i = len / 2 - 1; i > -1; i--) {  // 首元素也要调整
            AdjustHeap(arr, i, len);
        }
    }

    //整数或浮点数皆可使用,若要使用类(class)或结构体(struct)时必须重载大于(>)运算符
    template<typename T>
    void HeapSort(T arr[], int len) {
        BuildMaxHeap(arr, len);
        for (int i = len - 1; i > 0; i--) {  // 最多交换到第1元素
            swap(arr[0], arr[i]);
            AdjustHeap(arr, 0, i);
        }
    }

    template<typename T>
    void PrintArr(T arr[], int len) {
        for (int i = 0; i < len; i++)
            cout << arr[i] << ' ';
        cout << endl;
    }

    int main() {
        int arr[] = {3, 6, 9, 2, 0, 1, 5, 4, 8, 7};
        int len = sizeof(arr) / sizeof(*arr);
        cout << "排序前： ";
        PrintArr(arr, len);
        HeapSort(arr, len);
        cout << "排序后： ";
        PrintArr(arr, len);
        return 0;
    }
    ```


## 计数排序
1. 介绍  
    1. 计数排序不是比较排序，排序的速度快于任何比较排序算法；
    2. 线性时间复杂度的排序，计数排序要求输入的数据必须是有确定范围的整数；
    3. **新建数组统计等于该元素值索引的元素个数i，再反向写回数组。**
2. 复杂度  
    时间复杂度：O(n + m) ~ O(n + m) ~ O(n + m)  
    空间复杂度：O(m)，m是元素最大值
    稳定性：稳定  
3. 代码-python  
    ```python
    #!/bin/python3

    def count_sort(arr):
        len_bucket = max(arr) + 1
        bucket = [0] * len_bucket
        for i in arr:
            bucket[i] += 1
        idx = 0
        for j in range(len_bucket):
            while bucket[j]:
                arr[idx] = j
                bucket[j] -= 1
                idx += 1
        return arr

    arr = [3, 6, 9, 2, 0, 1, 5, 4, 8, 7]
    print("排序前：%s" % arr)
    sorted_arr = count_sort(arr)
    print("排序后：%s" % sorted_arr)
    ```
4. 代码-c++  
    ```c++
    #include<iostream>
    #include<cmath>
    using namespace std;

    //整数或浮点数皆可使用,若要使用类(class)或结构体(struct)时必须重载大于(>)运算符
    template<typename T>
    void CountSort(T arr[], int len) {
        int len_bucket = arr[0];
        for (int i = 1; i < len; i++)
            if (len_bucket < arr[i])
                len_bucket = arr[i];
        int *bucket = new int[len_bucket + 1];
        for (int i = 0; i < len; i++)
            bucket[arr[i]]++;
        int idx = 0;
        for (int j = 0; j < len_bucket + 1; j++)
            while (bucket[j] > 0) {
                arr[idx++] = j;
                bucket[j]--;
            }
        delete[] bucket;
    }

    template<typename T>
    void PrintArr(T arr[], int len) {
        for (int i = 0; i < len; i++)
            cout << arr[i] << ' ';
        cout << endl;
    }

    int main() {
        int arr[] = {3, 6, 9, 2, 0, 1, 5, 4, 8, 7};
        int len = sizeof(arr) / sizeof(*arr);
        cout << "排序前： ";
        PrintArr(arr, len);
        CountSort(arr, len);
        cout << "排序后： ";
        PrintArr(arr, len);
        return 0;
    }
    ```


## 桶排序
1. 介绍  
    1. 根据待排序集合中最大元素和最小元素的差值范围和映射规则，确定申请的桶个数；
    2. 遍历排序序列，将每个元素放到对应的桶里去；
    3. 对不是空的桶进行排序；
    4. 按顺序访问桶，将桶中的元素依次放回到原序列中对应的位置，完成排序。
    5. **新建数组统计等于该元素值索引的元素个数i，再反向写回数组。**
2. 复杂度  
    时间复杂度：O(n^2) ~ O(n+m) ~ O(n+m)  
    空间复杂度：O(n+m)，m个桶
    稳定性：稳定  
3. 代码-python  
    ```python
    #!/bin/python3
    from typing import List

    def bucket_sort(arr: List[int]):
        """桶排序"""
        min_num, max_num = min(arr), max(arr)
        # 桶大小
        bucket_range = (max_num - min_num) / len(arr)
        # 桶数组
        bucket_list = [[] for i in range(len(arr) + 1)]
        # 向桶填数
        for i in arr:
            bucket_list[int((i - min_num) / bucket_range)].append(i)
        # 回填
        left, right = 0, 0
        for i in bucket_list:
            left, right = right, right + len(i)
            arr[left:right] = sorted(i)
        return arr

    arr = [3, 6, 9, 2, 0, 1, 5, 4, 8, 7]
    print("排序前：%s" % arr)
    sorted_arr = bucket_sort(arr)
    print("排序后：%s" % sorted_arr)
    ```
4. 代码-c++  
    ```c++
    #include<iostream>
    #include<cmath>
    #include<vector>
    #include<algorithm>
    using namespace std;

    //整数或浮点数皆可使用,若要使用类(class)或结构体(struct)时必须重载大于(>)运算符
    template<typename T>
    void BucketSort(T arr[], int len) {
        T min_num = arr[0];
        T max_num = arr[0];
        for (int i = 0; i < len; i++) {
            if (arr[i] < min_num)
                min_num = arr[i];
            if (arr[i] > max_num)
                max_num = arr[i];
        }
        double bucket_range = double(max_num - min_num) / len;
        std::vector<std::vector<T> > bucket_list(len + 1);
        for (int i = 0; i < len; i++) {
            bucket_list[(arr[i] - min_num) / bucket_range].push_back(arr[i]);
        }
        int left = 0;
        for (int i = 0; i < len + 1; i++) {
            std::sort(bucket_list[i].begin(), bucket_list[i].end());
            for(int j = 0; j < bucket_list[i].size(); j++) {
                arr[left++] = bucket_list[i][j];
            }
        }
    }

    template<typename T>
    void PrintArr(T arr[], int len) {
        for (int i = 0; i < len; i++)
            cout << arr[i] << ' ';
        cout << endl;
    }

    int main() {
        int arr[] = {3, 6, 9, 2, 0, 1, 5, 4, 8, 7};
        int len = sizeof(arr) / sizeof(*arr);
        cout << "排序前： ";
        PrintArr(arr, len);
        BucketSort(arr, len);
        cout << "排序后： ";
        PrintArr(arr, len);
        return 0;
    }
    ```


## 基数排序
1. 介绍  
    1. 非比较型整数排序算法，其原理是将整数按位数切割成不同的数字，然后按每个位数分别比较；
    2. 由于整数也可以表达字符串（比如名字或日期）和特定格式的浮点数，所以基数排序也不是只能使用于整数。
    3. 取得数组中的最大数，并取得位数；
    4. 对数位较短的数前面补零；
    5. 分配，先从个位开始，根据位值(0-9)分别放到0~9号桶中;
    6. 收集，再将放置在0~9号桶中的数据按顺序放到数组中;
    7. 重复3~4过程，直到最高位，即可完成排序;
    6. ** 按位迭代排序 **
2. 复杂度  
    时间复杂度：O(n^2) ~ O(n+m) ~ O(n+m)  
    空间复杂度：O(n+m)，m个桶
    稳定性：稳定  
3. 代码-python  
    ```python
    #!/bin/python3
    from typing import List

    def radix_sort(arr: List[int]):
        """基数排序"""
        # 记录最大值的位数
        m = len(str(max(arr)))
        for k in range(m):  # m轮排序
            # 如果是数字，建立0-9折十个桶
            bucket_list = [[] for i in range(10)]
            # 如果是26个英文字母字符串，建立26个桶
            # bucket_list = [[] for i in range(26)]

            # 放入桶中
            for a in arr:
                bucket_list[(a // 10**k) % 10].append(a)

            # 从桶中拿出
            arr = [a for bucket in bucket_list for a in bucket]
        return arr

    arr = [3, 6, 9, 2, 0, 1, 5, 4, 8, 7]
    print("排序前：%s" % arr)
    sorted_arr = radix_sort(arr)
    print("排序后：%s" % sorted_arr)
    ```
4. 代码-c++  
    ```c++
    #include<iostream>
    #include<cmath>
    #include<vector>
    #include<algorithm>
    using namespace std;

    //整数或浮点数皆可使用,若要使用类(class)或结构体(struct)时必须重载大于(>)运算符
    template<typename T>
    void RadixSort(T arr[], int len) {
        // 记录最大位数
        T max_num = *max_element(arr, arr + len);
        int m = 0;
        while (max_num > 0) {
            m++;
            max_num /= 10;
        }

        for (int k = 0; k < m; k++) {  // m轮排序
            std::vector<std::vector<T> > bucket_list(10);
            for (int i = 0; i < len; i++)
                bucket_list[int(arr[i] / (pow(10, k))) % 10].push_back(arr[i]);
            int p = 0;
            for (int i = 0; i < 10; i++)
                for (int j = 0; j < bucket_list[i].size(); j++)
                    arr[p++] = bucket_list[i][j];
        }
    }

    template<typename T>
    void PrintArr(T arr[], int len) {
        for (int i = 0; i < len; i++)
            cout << arr[i] << ' ';
        cout << endl;
    }

    int main() {
        int arr[] = {3, 16, 9, 2, 20, 111, 5, 34, 8, 7};
        int len = sizeof(arr) / sizeof(*arr);
        cout << "排序前： ";
        PrintArr(arr, len);
        RadixSort(arr, len);
        cout << "排序后： ";
        PrintArr(arr, len);
        return 0;
    }
    ```


## 总结
1. 桶的概念  
    计数排序：每个桶只存储单一键值；  
    桶排序：每个桶存储一定范围的数值；  
    基数排序：根据键值的每位数字来分配桶；  
2. 关于稳定性
    排序后 2 个相等键值的顺序和排序之前它们的顺序相同；  
    稳定的排序算法：冒泡排序、插入排序、归并排序和基数排序；  
    不是稳定的排序算法：选择排序、快速排序、希尔排序、堆排序；  


## REFERENCE
[十大经典排序算法](https://www.runoob.com/w3cnote/ten-sorting-algorithm.html)
[python实现·十大排序算法](https://www.zhihu.com/column/Use-Python-to-Achieve)
