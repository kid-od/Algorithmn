# Divide-and-Conquer

问题2：链表归并排序优化

对单链表实现归并排序，要求空间复杂度优化至O(logn)（仅允许递归栈开销，不使用额外辅助链表存储所有节点），并处理链表为空、仅有一个节点及存在重复元素的边界情况。

### （1）链表拆分

- 使用 快慢指针：

  - slow 每次走 1 步
  - fast 每次走 2 步
  - 当 fast 到尾时，slow 刚好在中点
  - 断开链表，将中点前节点的 next 置为 NULL。

  

### （2）合并两个有序链表

- 使用 双指针：

  - 比较两个链表头节点值，小的接到结果链表
  - 遍历直到其中一个链表为空
  - 将剩余链表直接接到结果尾部

  

### （3）边界情况

- 空链表：head == None → 直接返回 None
- 仅一个节点：head.next == None → 直接返回 head
- 存在重复元素：合并时仍然按顺序即可，无需特殊处理



### （4）代码

```c
#include <stdio.h>
#include <stdlib.h>

// 定义链表节点
typedef struct ListNode {
    int val;
    struct ListNode *next;
} ListNode;

// 合并两个有序链表
ListNode* merge(ListNode* l1, ListNode* l2) {
    ListNode dummy;
    ListNode *tail = &dummy;
    dummy.next = NULL;

    while (l1 && l2) {
        if (l1->val <= l2->val) {
            tail->next = l1;
            l1 = l1->next;
        } else {
            tail->next = l2;
            l2 = l2->next;
        }
        tail = tail->next;
    }

    tail->next = (l1) ? l1 : l2;
    return dummy.next;
}

// 归并排序
ListNode* merge_sort(ListNode* head) {
    // 边界情况：空链表或单节点
    if (!head || !head->next)
        return head;

    // 使用快慢指针找中点
    ListNode *prev = NULL, *slow = head, *fast = head;
    while (fast && fast->next) {
        prev = slow;
        slow = slow->next;
        fast = fast->next->next;
    }
    prev->next = NULL; // 断开链表

    // 递归排序左右两半
    ListNode *l1 = merge_sort(head);
    ListNode *l2 = merge_sort(slow);

    // 合并有序链表
    return merge(l1, l2);
}

// 打印链表
void print_list(ListNode* head) {
    while (head) {
        printf("%d ", head->val);
        head = head->next;
    }
    printf("\n");
}

// 测试
int main() {
    // 创建链表 4->2->1->3->2
    int vals[] = {4, 2, 1, 3, 2};
    int n = sizeof(vals)/sizeof(vals[0]);
    ListNode *head = NULL, *tail = NULL;

    for (int i = 0; i < n; i++) {
        ListNode *node = (ListNode*)malloc(sizeof(ListNode));
        node->val = vals[i];
        node->next = NULL;
        if (!head) {
            head = tail = node;
        } else {
            tail->next = node;
            tail = node;
        }
    }

    printf("原链表: ");
    print_list(head);

    head = merge_sort(head);

    printf("排序后链表: ");
    print_list(head);

    return 0;
}
```







问题3：请查阅[visualgo.net](https://visualgo.net/)网站；实现QuickSort算法。请严格按其算法描述实现。



 

![img](https://p.cldisk.com/star3/418_183Q50/c56d8b1b252f2a67b27ee4126b955159.png?rw=418&rh=183&_fileSize=306737&_orientation=1)



```c
#include <stdio.h>
#include <stdlib.h>
#include <time.h>

// 交换数组中两个元素的值
void swap(int *a, int *b) {
    int temp = *a;
    *a = *b;
    *b = temp;
}

int partition(int arr[], int low, int high) {
    // 设置第一个元素为 pivot
    int pivot = arr[low];
    int pivotIndex = low;
    int storeIndex = pivotIndex + 1;

    // 初始化随机数生成器
    srand((unsigned int)time(NULL));

    for (int i = pivotIndex + 1; i <= high; i++) {
        // 处理小于 pivot 的情况，或者相等但 50% 概率的情况
        if (arr[i] < pivot || (arr[i] == pivot && rand() % 2 == 0)) {
            swap(&arr[i], &arr[storeIndex]);
            storeIndex++;
        }
    }

    // 将 pivot 放到正确的位置
    swap(&arr[pivotIndex], &arr[storeIndex - 1]);

    // 返回 pivot 的最终位置
    return storeIndex - 1;
}

// 快速排序函数
void quickSort(int arr[], int low, int high) {
    if (low < high) {
        // 分区，得到 pivot 的位置
        int pivotPos = partition(arr, low, high);

        // 对 pivot 左边的子数组进行排序
        quickSort(arr, low, pivotPos - 1);
        // 对 pivot 右边的子数组进行排序
        quickSort(arr, pivotPos + 1, high);
    }
}

int main() {
    int arr[] = {5, 2, 9, 1, 5, 6};
    int n = sizeof(arr) / sizeof(arr[0]);

    printf("原始数组：");
    for (int i = 0; i < n; i++) {
        printf("%d ", arr[i]);
    }
    printf("\n");

    quickSort(arr, 0, n - 1);

    printf("排序后数组：");
    for (int i = 0; i < n; i++) {
        printf("%d ", arr[i]);
    }
    printf("\n");

    return 0;
}
```





请阐述不少于两种划分的方法。



### **划分方法一：Lomuto 划分方案**

在 Lomuto 划分方案中，<u>**选择一个元素作为枢轴（pivot），通常选择数组的最后一个元素。**</u>然后，遍历数组，将小于枢轴的元素移到数组的左侧，大于等于枢轴的元素移到右侧。最后，将枢轴放置到其正确位置。

**算法步骤：**

1. 选择枢轴元素。

2. 初始化一个指针 i，指向数组的起始位置。

3. 遍历数组，对于每个元素：如果该元素小于枢轴，则将其与指针 i 指向的元素交换，并将 i 向右移动一位。

4. 最后，将枢轴元素与指针 i 指向的元素交换，使枢轴元素位于其正确位置。

5. 返回枢轴元素的索引。

   

### **划分方法二：Hoare 划分方案**

Hoare 划分方案**<u>选择一个元素作为枢轴，通常选择数组的第一个元素。</u>**然后，使用两个指针从数组的两端向中间移动，找到比枢轴大的元素和比枢轴小的元素，交换它们的位置，直到两个指针相遇。

**算法步骤：**

1. 选择枢轴元素。
2. 初始化两个指针 i 和 j，分别指向数组的两端。
3. 重复以下步骤，直到两个指针相遇：
   - 从左侧开始，找到一个大于等于枢轴的元素。
   - 从右侧开始，找到一个小于等于枢轴的元素。
   - 如果 i 小于 j，则交换这两个元素。
4. 返回 i 或 j 作为枢轴元素的新位置。

 

问题4：请查阅[visualgo.net](https://visualgo.net/)网站；实现Random QuickSort算法。请严格按算法描述实现。

**代码**

```c 
#include <stdio.h>
#include <stdlib.h>
#include <time.h>

// 交换两个元素
void swap(int *a, int *b) {
    int tmp = *a;
    *a = *b;
    *b = tmp;
}

// Lomuto 划分
int partition(int arr[], int left, int right) {
    int pivot = arr[right]; // 枢轴在右侧
    int i = left;
    for (int j = left; j < right; j++) {
        if (arr[j] <= pivot) {
            swap(&arr[i], &arr[j]);
            i++;
        }
    }
    swap(&arr[i], &arr[right]);
    return i;
}

// 随机选择枢轴并划分
int randomPartition(int arr[], int left, int right) {
    int pivotIndex = left + rand() % (right - left + 1); // 随机选枢轴
    swap(&arr[pivotIndex], &arr[right]); // 交换到最后
    return partition(arr, left, right);
}

// Random QuickSort
void randomQuickSort(int arr[], int left, int right) {
    if (left < right) {
        int pi = randomPartition(arr, left, right);
        randomQuickSort(arr, left, pi - 1);
        randomQuickSort(arr, pi + 1, right);
    }
}

// 测试
int main() {
    srand(time(NULL)); // 初始化随机数种子

    int arr[] = {10, 7, 8, 9, 1, 5};
    int n = sizeof(arr)/sizeof(arr[0]);

    printf("排序前: ");
    for(int i=0;i<n;i++) printf("%d ", arr[i]);
    printf("\n");

    randomQuickSort(arr, 0, n-1);

    printf("排序后: ");
    for(int i=0;i<n;i++) printf("%d ", arr[i]);
    printf("\n");

    return 0;
}
```



 

![img](https://p.cldisk.com/star3/428_194Q50/0f6ab7690df0ee7d5b0264296767460f.png?rw=428&rh=194&_fileSize=332936&_orientation=1)



请阐述这种方法解决了通用算法的哪些不足，并说明理由。



**Random QuickSort 算法与传统的 QuickSort 算法类似，不同之处在于每次递归时，随机选择一个元素作为枢轴，而不是固定选择某个位置的元素。这样可以有效避免最坏情况的发生，提高算法的平均性能。**

 

问题5：

输入: 平面点集 P 中有n 个点, n >1

输出: P中的两个点，其距离最小

请用分治法表述出解决该问题的算法；还有除算法外的解决方案么？

### **分治法解决方案**

1. **分（Divide）**
   - 将点集按 x 坐标排序（预处理，便于划分）。
   - 以中点为界，将点集分为左半部分`L`和右半部分`R`。
2. **治（Conquer）**
   - 递归求解左半部分`L`的最近点对距离`d1`。
   - 递归求解右半部分`R`的最近点对距离`d2`。
   - 取`d = min(d1, d2)`作为当前已知的最小距离。
3. **合（Combine）**
   - 关键步骤：检查「跨左右两部分」的点对是否存在距离小于`d`的情况（因为最近点对可能分别在`L`和`R`中）。
   - 筛选出 x 坐标与中点 x 坐标之差小于`d`的点（这些点才可能形成跨区域的更近点对），记为集合`S`。
   - 将`S`按 y 坐标排序，对每个点只与后续最多 6 个点比较（几何性质保证：在`d×2d`的矩形内最多有 6 个点），更新最小距离`d`。



尝试回答 100万个空间三维点； 当给出一个位置点后，如何找到离该位置点最近的点？

**对于三维点集，可以将点集按照 z 坐标排序，类似地应用分治法求解。**



当查阅解决方案后，你有什么启发。

**对于小规模点集，可以直接使用分治法或暴力法，而在大规模点集中，则必须依靠空间索引结构，如构建 kd 树（一种多维空间索引结构），通过树结构高效检索最近邻点，以提高效率。此外，空间划分与剪枝是分治法和 kd-tree 的核心思想，通过对局部区域的判断，能够有效减少不必要的比较，从而显著降低时间复杂度。**



代码

```c
#include <stdio.h>
#include <stdlib.h>
#include <math.h>
#include <limits.h>

// 点结构定义
typedef struct {
    double x, y;
} Point;

// 存储最近点对及距离
typedef struct {
    Point p1, p2;
    double dist;
} Result;

// 计算两点距离的平方（避免开方，提高效率）
double dist_sq(Point a, Point b) {
    double dx = a.x - b.x;
    double dy = a.y - b.y;
    return dx*dx + dy*dy;
}

// 计算两点距离
double distance(Point a, Point b) {
    return sqrt(dist_sq(a, b));
}

// 比较函数：按x坐标排序
int compare_x(const void* a, const void* b) {
    Point* p1 = (Point*)a;
    Point* p2 = (Point*)b;
    return (p1->x - p2->x) > 0 ? 1 : -1;
}

// 比较函数：按y坐标排序
int compare_y(const void* a, const void* b) {
    Point* p1 = (Point*)a;
    Point* p2 = (Point*)b;
    return (p1->y - p2->y) > 0 ? 1 : -1;
}

// 暴力法：求解小规模点集的最近点对
Result brute_force(Point P[], int n) {
    Result res;
    res.dist = INFINITY;
    for (int i = 0; i < n; i++) {
        for (int j = i + 1; j < n; j++) {
            double d = dist_sq(P[i], P[j]);
            if (d < res.dist) {
                res.dist = d;
                res.p1 = P[i];
                res.p2 = P[j];
            }
        }
    }
    res.dist = sqrt(res.dist);  // 最终转换为实际距离
    return res;
}

// 合并步骤：检查跨区域的最近点对
Result merge(Point strip[], int size, double d, Result best) {
    qsort(strip, size, sizeof(Point), compare_y);  // 按y排序

    // 每个点只与后续最多6个点比较
    for (int i = 0; i < size; i++) {
        for (int j = i + 1; j < size && (strip[j].y - strip[i].y) < d; j++) {
            double dist = distance(strip[i], strip[j]);
            if (dist < best.dist) {
                best.dist = dist;
                best.p1 = strip[i];
                best.p2 = strip[j];
            }
        }
    }
    return best;
}

// 分治法递归函数
Result closest_recursive(Point P[], int n) {
    // 小规模点集直接用暴力法
    if (n <= 3) {
        return brute_force(P, n);
    }

    // 找中点
    int mid = n / 2;
    Point mid_point = P[mid];

    // 递归求解左右两部分
    Result left = closest_recursive(P, mid);
    Result right = closest_recursive(P + mid, n - mid);

    // 取左右部分的最小距离
    Result best = (left.dist < right.dist) ? left : right;

    // 筛选x在中点附近[mid.x - d, mid.x + d]的点
    Point strip[n];
    int strip_size = 0;
    for (int i = 0; i < n; i++) {
        if (fabs(P[i].x - mid_point.x) < best.dist) {
            strip[strip_size++] = P[i];
        }
    }

    // 检查跨区域点对，更新最优解
    return merge(strip, strip_size, best.dist, best);
}

// 主函数：求解最近点对
Result closest_pair(Point P[], int n) {
    qsort(P, n, sizeof(Point), compare_x);  // 预处理：按x排序
    return closest_recursive(P, n);
}

// 示例
int main() {
    Point P[] = {{2, 3}, {12, 30}, {40, 50}, {5, 1}, {12, 10}, {3, 4}};
    int n = sizeof(P) / sizeof(P[0]);

    Result res = closest_pair(P, n);
    printf("最近点对：\n");
    printf("点1: (%.2f, %.2f)\n", res.p1.x, res.p1.y);
    printf("点2: (%.2f, %.2f)\n", res.p2.x, res.p2.y);
    printf("距离: %.2f\n", res.dist);

    return 0;
}
```



 

问题6：求数组中第k大问题；

给出算法描述，并实现。

**算法步骤：**

1. 选择一个元素作为枢轴。
2. 使用划分方法将数组分成两部分。
3. 如果枢轴的位置正好是 k，则返回该元素。
4. 如果枢轴的位置大于 k，则在左半部分递归查找。
5. 如果枢轴的位置小于 k，则在右半部分递归查找。



代码

```c
#include <stdio.h>
#include <stdlib.h>
#include <time.h>

// 交换两个元素
void swap(int *a, int *b) {
    int tmp = *a;
    *a = *b;
    *b = tmp;
}

// Lomuto 划分
int partition(int arr[], int left, int right) {
    int pivot = arr[right]; // 选择最后一个元素作为枢轴
    int i = left;
    for (int j = left; j < right; j++) {
        if (arr[j] <= pivot) { // 小于等于枢轴放左边
            swap(&arr[i], &arr[j]);
            i++;
        }
    }
    swap(&arr[i], &arr[right]);
    return i;
}

// QuickSelect 查找第 k 大元素
int quickSelect(int arr[], int left, int right, int k) {
    if (left == right) // 仅有一个元素
        return arr[left];

    int pivotIndex = partition(arr, left, right);

    int count = pivotIndex - left + 1; // 枢轴左侧数量+1
    if (count == k) {
        return arr[pivotIndex]; // 找到第 k 大
    } else if (k < count) {
        return quickSelect(arr, left, pivotIndex - 1, k);
    } else {
        return quickSelect(arr, pivotIndex + 1, right, k - count);
    }
}

// 测试
int main() {
    int arr[] = {3, 2, 1, 5, 6, 4};
    int n = sizeof(arr)/sizeof(arr[0]);
    int k = 2; // 查找第 2 大元素

    // QuickSelect 查找第 k 大
    int result = quickSelect(arr, 0, n-1, n-k+1); // 第 k 大 -> 第 n-k+1 小
    printf("数组中第 %d 大的元素是 %d\n", k, result);

    return 0;
}
```



谈谈有什么启发。

**QuickSelect 算法通过减少不必要的比较和交换操作，提高了寻找第 k 大元素的效率。**