### BFPRT算法
> 解决问题 :   
a) 求数组中最大的第k个, 或数组中最小的第k个元素.  
b) 求数组中最小的k个数.   
对于一个无序数组, 排序之后再取出第k个, 时间复杂度为O(N * logN), 有没有时间复杂度为O(N)的算法?

> 先介绍足够优良的解. 在普通场合直接使用最优解即可, 因为BFPRT算法实现十分复杂, 它的常数项也非常大, 只有在数据样本足够大的情况下它才跑得比最优解快. 

#### 优良解
- 引入快排中的partition过程, partition过程随机选中一个数, 然后在数组中分出小于区, 等于区, 大于区. 
```
假设现在求的是第100小的数. 
_______|_________|_________
  <x       =x       >x
          80~90
```
- 得到等于区的范围是80~90, 那么下一次partition过程将在大于区展开.
- 在普通的快排中需要在小于区和大于区同时继续展开partition过程, 而对应这类型的题目只要在其中一个区域进行展开. 
- 在大于区进行展开后, 假如等于区的下标命中了100, 那么就返回结果. 

> 时间复杂度分析 

- 最好情况, 每次等于区都能划分在中间, 第一次要partition遍历的是O(N), 第二次是O(N/2), 然后O(N/4)...最终是O(N).(等比数列求和)
- 最坏情况下, 每次选的随机数都打在数组的头和尾, partition一次只在原数据规模的基础上减1, 整体的时间复杂度为O(N^2). 
- 可以看出整个查找过程是基于一个概率事件的, 对整体概率长期期望最终收敛于O(N).
- 所以这个优良解的算法已经是O(N)了. 

> 有没有不基于概率, 严格做到O(N)的解? 
#### BFPRT
> BFPRT算法由经典算法而来. 

```
对经典算法的回顾 : 
1) 随机选出一个数. 
---------------------
2) 进行partition过程
3) 选择一边return
```
> BFPRT算法只是对选数这个过程进行了优化, 它给出了一个策略, 后面的过程2和3完全一样. 

```
BFPRT过程:
int bfprt(arr, k){}
求arr数组中, 第k小的数. 

第一步 : 
把数组每五个元素分为一组, 
[ 5个 ][ 5个 ][ 5个 ][ 5个 ]...... 最后基本是5/N组. 

第二步 : 
每一组内进行排序, 5个元素排序的代价为O(1)
[有序][有序][有序][有序]...... 
由于一共由5/N组, 也就是进行5/N次排序, 时间复杂度O(N).

第三步 : 
把每一个小组的中位数拿出来, 如果某个小组是偶数个, 就拿上中位数. 

第四步 : 
把拿出来的中位数组成一个新的数组new_arr, 数组的长度为N/5.

最后 : 
求new_arr数组的中位数. 
递归调用bfprt(new_arr, N/10);
对于bfprt算法的参数, 给出new_arr, 和数组长度的一半N/10, 也就是中位数的位置, 如果数组的长度为奇数, 返回的就是中位数, 偶数则返回上中位数. 

最终把得到的中位数作为划分的位置. 后面的过程照常进行. 
```
> 为什么这么做?

- 经典算法中随机选一个划分位置, 左右两边的数据规模是未知的. 如果使用BFPRT算法, 可以确定每次每次划分都至少能淘汰掉3/10N的数据. 
![image](EDD9587DF83247DDBBF3F7D7AAE9A623)
- 保证每次至少得到7/10N的数据大于x. 

> 时间复杂度

```
1) 划分组 O(1)
2) 给每个组排好序, 得到中位数数组 O(N)
3) 递归调用bfprt()得到new_arr的中位数, 这时的数据规模是5/N B(5/N), 最终得到x
4) partition过程 O(N)
5) 继续递归调用partition, 但是最多只要7/10N的量. B(7/10N)

最终时间复杂度的表达式为 : 
T(N) = T(N/5) + t(7/10N) + O(N)
最终可以证明上面的式子时间复杂度为O(N), 算法导论第九章整章. 
```
---
#### 求数组中最小的k个数. 
> 上面的求第k个最小的数解决了, 这里就简单了, 我们遍历一遍数组, 只要把比x小的数取出来就行了, 如果结果不够k个就用x补上. 

> 代码实现

##### 最菜的方法, 时间复杂度O(N * logN) 
```
    private static int[] getMinKNumBySort(int[] arr, int k) {
        if (k < 1 || k > arr.length){
            return arr;
        }
        Arrays.sort(arr);
        int res[] = new int[k];
        for (int i = 0; i < k; i++){
            res[i] = arr[i];
        }
        return res;
    }
```

##### 优良解的实现, 时间复杂度O(N)
```
public class goodSolution {
    public static int[] getMinKNumBySort2(int[] arr, int k){
        if (arr == null || k < 1 || k > arr.length){
            return arr;
        }
        int kNum = getNum(arr, 0, arr.length - 1, k);
        int resArray[] = new int[k];
        int index = 0;
        for (int i = 0; i < arr.length; i++){
            if (arr[i] < kNum){
                resArray[index++] = arr[i];
            }
        }
        while(index < k){
            resArray[index++] = kNum;
        }
        return resArray;
    }

    private static int getNum(int[] arr, int l, int r, int k) {
        if (l == r){
            return arr[l];
        }
        swap(arr, l + (int)(Math.random() * (r - l + 1)), r);
        int res[] = partition(arr, l, r);
        if (k < res[0] + 1){
            return getNum(arr,0, res[0] - 1, k);
        }else if (k > res[1] + 1){
            return getNum(arr, res[1] + 1, r, k);
        }
        return arr[res[0]];
    }

    private static int[] partition(int[] arr, int l, int r) {
        int less = l - 1;
        int more = r;
        while(l < more){
            if (arr[l] < arr[r]){
                swap(arr, ++less, l++);
            }else if (arr[l] > arr[r]){
                swap(arr, --more, l);
            }else{
                l++;
            }
        }
        swap(arr, more, r);
        return new int[]{less + 1, more};
    }

    public static void printArray(int[] arr) {
        for (int i = 0; i != arr.length; i++) {
            System.out.print(arr[i] + " ");
        }
        System.out.println();
    }

    public static void swap(int[] arr, int i, int j){
        int temp = arr[i];
        arr[i] = arr[j];
        arr[j] = temp;
    }

    public static void main(String[] args) {
        int[] arr = {5, 4, 3, 8, 9, 2, 1, 5, 5, 5, 5};
        printArray(getMinKNumBySort2(arr, 8));
    }
}
```

##### 最优解, 牛逼的BFPRT
```
public class BFPRT {

    // O(N)
    public static int[] getMinKNumsByBFPRT(int[] arr, int k) {
        if (k < 1 || k > arr.length) {
            return arr;
        }
        int minKth = getMinKthByBFPRT(arr, k);//找到第k大/小的数.
        int[] res = new int[k];
        int index = 0;
        for (int i = 0; i != arr.length; i++) {
            if (arr[i] < minKth) {
                res[index++] = arr[i];
            }
        }
        for (; index != res.length; index++) {
            res[index] = minKth;
        }
        return res;
    }

    public static int getMinKthByBFPRT(int[] arr, int K) {
        int[] copyArr = copyArray(arr);//为了数据的稳定性, 复制一个新数组.
        return select(copyArr, 0, copyArr.length - 1, K - 1);//相当于进入快排过程, 查找那个中位数
    }

    public static int select(int[] arr, int begin, int end, int i) {
        if (begin == end) {
            return arr[begin];
        }
        int pivot = medianOfMedians(arr, begin, end);//BFPRT算法的精华, 找到每次partition数组的中位数
        int[] pivotRange = partition(arr, begin, end, pivot);
        if (i >= pivotRange[0] && i <= pivotRange[1]) {
            return arr[i];
        } else if (i < pivotRange[0]) {
            return select(arr, begin, pivotRange[0] - 1, i);
        } else {
            return select(arr, pivotRange[1] + 1, end, i);
        }
    }

    public static int medianOfMedians(int[] arr, int begin, int end) {
        int num = end - begin + 1;//得到数组元素的总数
        int offset = num % 5 == 0 ? 0 : 1;//每5个分成一组, 判断是否有余数, 有余数最后要增加一组
        int[] mArr = new int[num / 5 + offset];//每五个元素一组, 分为(num/5 + offset)组
        for (int i = 0; i < mArr.length; i++) {//一次循环则找到一个组的中位数
            int beginI = begin + i * 5;//这么做保证每次拿到原数组中的一组5个数
            int endI = beginI + 4;
            mArr[i] = getMedian(arr, beginI, Math.min(end, endI));
        }
        return select(mArr, 0, mArr.length - 1, mArr.length / 2);
    }

    public static int[] partition(int[] arr, int begin, int end, int pivotValue) {
        int small = begin - 1;
        int cur = begin;
        int big = end + 1;
        while (cur != big) {
            if (arr[cur] < pivotValue) {
                swap(arr, ++small, cur++);
            } else if (arr[cur] > pivotValue) {
                swap(arr, cur, --big);
            } else {
                cur++;
            }
        }
        int[] range = new int[2];
        range[0] = small + 1;
        range[1] = big - 1;
        return range;
    }

    public static int getMedian(int[] arr, int begin, int end) {
        insertionSort(arr, begin, end);//5个元素先排序, 时间复杂度O(1)
        int sum = end + begin;//这两行代码求的是中位数的下标, 防止最后一组出现不是5个数的情况. 
        int mid = (sum / 2) + (sum % 2);
        return arr[mid];
    }

    public static void insertionSort(int[] arr, int begin, int end) {
        for (int i = begin + 1; i != end + 1; i++) {
            for (int j = i; j != begin; j--) {
                if (arr[j - 1] > arr[j]) {
                    swap(arr, j - 1, j);
                } else {
                    break;
                }
            }
        }
    }

    public static void swap(int[] arr, int index1, int index2) {
        int tmp = arr[index1];
        arr[index1] = arr[index2];
        arr[index2] = tmp;
    }


    public static int[] copyArray(int[] arr) {
        int[] res = new int[arr.length];
        for (int i = 0; i != res.length; i++) {
            res[i] = arr[i];
        }
        return res;
    }

    public static void printArray(int[] arr) {
        for (int i = 0; i != arr.length; i++) {
            System.out.print(arr[i] + " ");
        }
        System.out.println();
    }

    public static void main(String[] args) {
        int[] arr = { 6, 9, 1, 3, 1, 2, 2, 5, 6, 1, 3, 5, 9, 7, 2, 5, 6, 1, 9 };
        // sorted : { 1, 1, 1, 1, 2, 2, 2, 3, 3, 5, 5, 5, 6, 6, 6, 7, 9, 9, 9 }
        printArray(getMinKNumsByBFPRT(arr, 10));
    }
}
```
