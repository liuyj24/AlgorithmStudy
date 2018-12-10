### 题目
> 给定一个整数数组 nums 和一个目标值 target，请你在该数组中找出和为目标值的那 两个 整数，并返回他们的数组下标。  
你可以假设每种输入只会对应一个答案。但是，你不能重复利用这个数组中同样的元素。

```
示例:
给定 nums = [2, 7, 11, 15], target = 9

因为 nums[0] + nums[1] = 2 + 7 = 9
所以返回 [0, 1]
```
> 坑 : 不能重复利用数组种同样的元素. 

- 我一开始的思路, 两个for循环嵌套找两个数, 时间复杂度O(n^2)
```
public static int[] twoSum(int[] nums, int target){
    for(int i = 0; i < nums.length; i++){
        for(int j = i; j < nums.length; j++){
            if(nums[i] + nums[j] == target && i != j){
                return new int[]{i, j};
            }
        }
    }
    return null;
}
```
- 学习答案之后的解法, 使用哈希表, 用空间换时间, 时间复杂度O(n), 额外空间复杂度O(n), 快多了.
```
public static int[] twoSum2(int[] nums, int target){
    Map<Integer, Integer> map = new HashMap<>();
    for(int i = 0; i < nums.length; i++){
        map.put(nums[i], i);
    }
    for(int i = 0; i < nums.length; i++){
        int complement = target - nums[i];
        if(map.containsKey(complement) && i != map.get(complement)){
            return new int[]{i, map.get(complement)};
        }
    }
    throw new IllegalArgumentException("No two sum solution!");
}
```