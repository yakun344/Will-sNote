# Sorting Algorithms 常见排序算法实现
_update Aug 13, 2019_

---

```java
// Implement sorting algorithm

import java.util.*;
import java.util.stream.*;

public class Sorting {
    public static void main(String[] args) {
        // test here
        int[] arr1 = {0, 1, 2, 3, 4, 5, 6};
        int[] arr2 = {6, 5, 4, 3, 2, 1, 0};
        int[] arr3 = {34, 5, 12, 12, 59, 846, 1323, 22, 4, 22, 8};
        int[] arr4 = {2};
        int[] arr5 = {5, 4};

        // testInsertionSort(arr1);
        // testInsertionSort(arr2);
        // testInsertionSort(arr3);
        // testInsertionSort(arr4);
        // testInsertionSort(arr5);

        // testMergeSort(arr1);
        // testMergeSort(arr2);
        // testMergeSort(arr3);
        // testMergeSort(arr4);
        // testMergeSort(arr5);

        testQuickSort(arr1);
        testQuickSort(arr2);
        testQuickSort(arr3);
        testQuickSort(arr4);
        testQuickSort(arr5);

        // testBubbleSort(arr1);
        // testBubbleSort(arr2);
        // testBubbleSort(arr3);
        // testBubbleSort(arr4);
        // testBubbleSort(arr5);

        // testSelectionSort(arr1);
        // testSelectionSort(arr2);
        // testSelectionSort(arr3);
        // testSelectionSort(arr4);
        // testSelectionSort(arr5);

        // testCountingSort(arr1);
        // testCountingSort(arr2);
        // testCountingSort(arr3);
        // testCountingSort(arr4);
        // testCountingSort(arr5);
    }

// Counting Sort ********************************************
    // stable counting sort
    public static void testCountingSort(int[] arr) {
        int[] result = countingSort(arr);
        System.out.print("countingSort output: { ");
        for (int n : result) {
            System.out.print(n + " ");
        }
        System.out.print(" }\n");
    }
    public static int[] countingSort(int[] arr) {
        int[] ret = new int[arr.length];
        // 先建一个长度等于数据范围的数组
        int min = arr[0], max = arr[0];
        for (int i : arr) {
            if (i > max) max = i;
            else if (i < min) min = i;
        }
        int[] count = new int[max - min + 1];
        // 数组中每个位置存放input array中对应元素出现的次数
        for (int i : arr) {
            count[i - min]++;
        }
        // 为了stable，将count数组转换为原始count数组的preSum数组, 到这里为止，解释一下：
        // 现在count 数组中的 count[i] 表示原数组arr中有 count[i] 个数小于等于 i+min;
        // 则我们可知原数组中位置最靠右的 i+min, 它在结果中的位置一定是 index=count[i]-1
        for (int i = 1; i < count.length; ++i) {
            count[i] += count[i - 1];
        }
        // 根据如上理由，ret中的数 x（最靠右的x） 在count中对应的下标应为 x-min，而 count[x-min]-1 就
        // 是 x 在ret中的 index，于是将 x 放在 ret[count[x-min]]-1 之后，还要将 count[x-min] 也减一
        for (int i = arr.length - 1; i >= 0; --i) {
            ret[--count[arr[i] - min]] = arr[i];
        }
        return ret;
    }


// Selection sort **************************************************
    // left part is sorted, find min of the rest part in each iteration and swap
    // with the left most element of the right part.
    public static void testSelectionSort(int[] arr) {
        int[] result = selectionSort(arr);
        System.out.print("selectionSort output: { ");
        for (int n : result) {
            System.out.print(n + " ");
        }
        System.out.print(" }\n");
    }
    public static int[] selectionSort(int[] arr) {
        int[] ret = arr.clone();
        for (int i = 0; i < ret.length - 1; ++i) {
            int minIndex = i;
            for (int j = i + 1; j < ret.length; ++j) {
                if (ret[minIndex] > ret[j]) minIndex = j;
            }
            swap(ret, minIndex, i);
        }
        return ret;
    }

// Bubble sort **********************************************
    // Iterate n times, swap larger neighbors to right in each iteration.
    public static void testBubbleSort(int[] arr) {
        int[] result = bubbleSort(arr);
        System.out.print("bubbleSort output: { ");
        for (int n : result) {
            System.out.print(n + " ");
        }
        System.out.print(" }\n");
    }
    public static int[] bubbleSort(int[] arr) {
        int[] ret = arr.clone();
        for (int i = 0; i < ret.length; ++i) {
            for (int j = 1; j < ret.length; ++j) {
                if (ret[j - 1] > ret[j]) swap(ret, j, j - 1);
            }
        }
        return ret;
    }


// Insertion sort ******************************************
    // maintain a sorted sequence at left, insert one element at one time
    public static void testInsertionSort(int[] arr) {
        int[] result = insertionSort(arr);
        System.out.print("insertionSort output: { ");
        for (int n : result) {
            System.out.print(n + " ");
        }
        System.out.print(" }\n");
    }

    public static int[] insertionSort(int[] arr) {
        int[] ret = arr.clone();
        for (int i = 1; i < ret.length; ++i) {
            int key = ret[i];
            int j = 0;
            for (j = i - 1; j >= 0 && ret[j] > key; --j) {
                ret[j + 1] = ret[j];
            }
            ret[j + 1] = key;
        }
        return ret;
    }


// Merge sort ************************************************
/*
    MergeSort 为什么需要 O(n) space：
    因为每次我们需要把两个sorted array merge 到原来的 array 中的时候，我们必须新建辅助数组
    储存那两个array，这个过程在recursion的过程中，至多消耗O(n)的space；
*/
    public static void testMergeSort(int[] arr) {
        int[] result = mergeSort(arr);
        System.out.print("mergeSort output: { ");
        for (int n : result) {
            System.out.print(n + " ");
        }
        System.out.print(" }\n");
    }

// // merge sort, 手动建一个aux array的解法，这种方法也可以用来说明mergesort O(n) 的空间复杂度
    public static int[] mergeSort(int[] arr) {
        int[] ret = Arrays.copyOf(arr, arr.length);
        // 这里建一个长度为n的aux数组，用来作为每次merge的缓存数组，也是除了递归栈之外的所有额外空间，
        // 递归栈高度为logn，所以整个的空间复杂度为 O(n + logn) == O(n);
        int[] aux = new int[arr.length];
        // mergeSort(ret, aux, 0, arr.length - 1);
        mergeSort(ret, 0, arr.length - 1);
        return ret;
    }

    private static void mergeSort(int[] arr, int left, int right) {
        if (left >= right) return;
        int mid = left + (right - left) / 2;
        mergeSort(arr, left, mid);
        mergeSort(arr, mid + 1, right);
        merge(arr, left, mid, right);
    }

    private static void merge(int[] arr, int left, int mid, int right) {
        int[] aux = Arrays.copyOfRange(arr, left, right + 1);
        int i = left, j = mid + 1;
        for (int k = left; k <= right; ++k) {
            if (i > mid) arr[k] = aux[j++ - left];
            else if (j > right) arr[k] = aux[i++ - left];
            else if (aux[i - left] < aux[j - left]) arr[k] = aux[i++ - left];
            else arr[k] = aux[j++ - left];
        }
    }

    // private static void mergeSort(int[] arr, int[] aux, int p, int r) {
    //     if (p >= r) return;
    //     int q = p + (r - p) / 2;
    //     mergeSort(arr, aux, p, q);
    //     mergeSort(arr, aux, q + 1, r);
    //     merge(arr, aux, p, q, r);
    // }

    // private static void merge(int[] arr, int[] aux, int p, int q, int r) {
    //     // 先把arr中需要merge部分复制到aux中，然后merge后的结果存回arr原位
    //     for (int i = p; i <= r; ++i) {
    //         aux[i] = arr[i];
    //     }
    //     int i = p, j = q + 1;
    //     for (int k = p; k <= r; ++k) {
    //         if (i > q) arr[k] = aux[j++];
    //         else if (j > r) arr[k] = aux[i++];
    //         else if (aux[i] < aux[j]) arr[k] = aux[i++];
    //         else arr[k] = aux[j++];
    //     }
    // }


// // MergeSort 简洁的写法
    // public static int[] mergeSort(int[] arr) {
    //     int[] ret = Arrays.copyOf(arr, arr.length);
    //     mergeSort(ret, 0, ret.length - 1);
    //     return ret;
    // }
    // private static void mergeSort(int[] arr, int p, int r) {
    //     if (p >= r) return;
    //     int q = (p + r) / 2;
    //     mergeSort(arr, p, q);
    //     mergeSort(arr, q + 1, r);
    //     merge(arr, p, q, r);
    // }
    //
    // // 写这个函数之前先画图：
    // //  input arr: __|p|__________| q |______| r |_____
    // //  aux:         |0|__________|q-p|______|r-p|
    // private static void merge(int[] arr, int p, int q, int r) {
    //     int[] aux = Arrays.copyOfRange(arr, p, r + 1);
    //     int i = 0, j = q - p + 1;
    //     for (int k = p; k < r + 1; ++k) {
    //         if (i > q - p)            arr[k] = aux[j++];
    //         else if (j > r - p)       arr[k] = aux[i++];
    //         else if (aux[i] < aux[j]) arr[k] = aux[i++];
    //         else                      arr[k] = aux[j++];
    //     }
    // }



// Quick sort ************************************************
    public static void testQuickSort(int[] arr) {
        int[] result = quickSort(arr);
        System.out.print("quickSort output: { ");
        for (int n : result) {
            System.out.print(n + " ");
        }
        System.out.print(" }\n");
    }

    public static int[] quickSort(int[] arr) {
        int[] ret = Arrays.copyOf(arr, arr.length);
        quickSort(ret, 0, ret.length - 1);
        return ret;
    }

    private static void quickSort(int[] arr, int left, int right) {
        if (left >= right) return;
        int mid = partition(arr, left, right);
        quickSort(arr, left, mid - 1);
        quickSort(arr, mid + 1, right);
    }
    // |----------------i-------------j->             pivot|
    // left  <= pivot   |   > pivot   |  unexplored       right
    private static int partition(int[] arr, int left, int right) {
        int pivot = arr[right];
        int i = left - 1;
        for (int j = left; j <= right; ++j) {
            if (arr[j] <= pivot) swap(arr, ++i, j);
        }
        return i;
    }


    private static void swap(int[] arr, int i, int j) {
        int t = arr[i];
        arr[i] = arr[j];
        arr[j] = t;
    }
}
```
