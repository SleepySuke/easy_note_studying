# LeetCode-Hot-100

## 两数之和

```
package leetcodehot100;

/**
 * @author 自然醒
 * @version 1.0
 */

/**
 * 两数之和
 * 给定一个整数数组 nums 和一个整数目标值 target，请你在该数组中找出 和为目标值 target  的那 两个 整数，并返回它们的数组下标。
 * 你可以假设每种输入只会对应一个答案。但是，数组中同一个元素在答案里不能重复出现。
 * 你可以按任意顺序返回答案。
 * 示例 1：
 * 输入：nums = [2,7,11,15], target = 9
 * 输出：[0,1]
 * 解释：因为 nums[0] + nums[1] == 9 ，返回 [0, 1] 。
 * 示例 2：
 * 输入：nums = [3,2,4], target = 6
 * 输出：[1,2]
 * 示例 3：
 * 输入：nums = [3,3], target = 6
 * 输出：[0,1]
 */
public class hot1 {
    public static void main(String[] args) {
        int[] nums = {2, 7, 11, 15};
        int target = 9;
        int[] ints = twoSum(nums, target);
        for (int anInt : ints) {
            System.out.println(anInt);
        }
    }
    public static int[] twoSum(int[] nums, int target) {
        int[] res = new int[2];
        for(int i = 0; i < nums.length; i++){
            for(int j = i + 1; j < nums.length; j++){
                if(nums[i] + nums[j] == target){
                    res[0] = i;
                    res[1] = j;
                    return res;
                }
            }
        }
        res[0] = -1;
        res[1] = -1;
        return res;
    }
}
```

暴力枚举遍历数组，时间复杂为O(n^2)

```
public static int[] twoSum(int[] nums, int target) {
    HashMap<Integer, Integer> map = new HashMap<>();
    for (int i = 0; i < nums.length; i++) {
        int isExit = target - nums[i];
        if(map.containsKey(isExit)){
            return new int[]{map.get(isExit), i};
        }
        map.put(nums[i], i);
    }
  return new int[]{-1, -1};
}
```

以上是hash优化，主要使用Hash表特性，存储数字以及对应的索引，k-v键值对存储（此时数字为key值，索引为value值），遍历数组，然后求其对应的target-nums[i]补数，使用api判断当前是否已经存在该值，存在的话则直接返回对应的索引，不存在则添加至hashmap中，进行下一次判断

## 字母异或词分组

```
package leetcodehot100;

/**
 * @author 自然醒
 * @version 1.0
 */

import java.util.*;

/**
 * 字母异或词分组
 * 字母异位词：通过重新排列不同单词或短语的字母而形成的单词或短语，并使用所有原字母一次
 * 给定一个字符串数组，将字母异位词组合在一起。可以按任意顺序返回结果。
 * 示例1：
 * 输入: strs = ["eat", "tea", "tan", "ate", "nat", "bat"]
 * 输出: [["bat"],["nat","tan"],["ate","eat","tea"]]
 * 解释：
 * 数组中没有任何字符串可以通过重新排列形成"bat"
 * 字符串"eat"、"tea"和"ate"可以重新排列形成彼此
 * "tan"、"nat"可以重新排列形成彼此
 * 示例2：
 * 输入: strs = [""]
 * 输出: [[""]]
 * 示例3：
 * 输入: strs = ["a"]
 * 输出: [["a"]]
 */
public class hot2 {
    public static List<List<String>> groupAnagrams(String[] strs) {
        Map<String, List<String>> map = new HashMap<>();
        for (String str : strs) {
            char[] chars = str.toCharArray();
            Arrays.sort(chars);
            String key = new String(chars);
            if(!map.containsKey( key)){
                map.put(key, new ArrayList<>());
            }
            map.get(key).add(str);
        }
        return new ArrayList<>(map.values());
    }
}
```

该题解法主要思想为**特征提取+哈希分组**

**特征提取的核心思想**：将复杂数据转换为能够代表其本质特征的简化表示

- 将不同形式的相同内容映射到相同的特征表示
- 此题则是将"eat"、"tea"、"ate"映射到特征"aet"
- 消除排列顺序的影响，只保留本质的字母组成信息

**哈希分组的核心思想**：使用特征作为键，将具有相同特征的数据聚合在一起。

主要利用哈希表的O(1)查找特性

"特征提取+哈希分组"模式可以应用于很多类似问题：

1. **分组回文串**：提取特征为排序后的字符串
2. **按字符串长度分组**：提取特征为字符串长度
3. **按数字各位数之和分组**：提取特征为各位数字之和
4. **按自定义规则分组**：任何可以提取出分组依据特征的问题

上述时间复杂为O(n*k * logk )，主要的时间复杂浪费在排序

可以采用计数+哈希，减去了排序的时间

```
public List<List<String>> groupAnagrams(String[] strs) {
    Map<String, List<String>> map = new HashMap<>();
    
    for (String str : strs) {
        // 创建字符计数数组
        int[] count = new int[26];
        for (char c : str.toCharArray()) {
            count[c - 'a']++;
        }
        
        // 将计数数组转换为字符串作为键
        StringBuilder keyBuilder = new StringBuilder();
        for (int i = 0; i < 26; i++) {
            if (count[i] > 0) {
                keyBuilder.append((char)('a' + i)).append(count[i]);
            }
        }
        String key = keyBuilder.toString();
        
        if (!map.containsKey(key)) {
            map.put(key, new ArrayList<>());
        }
        map.get(key).add(str);
    }
    
    return new ArrayList<>(map.values());
}
```

一种可以用来学习的操作，按需使用，**懒加载（Lazy Initialization）**

```
 public List<List<String>> groupAnagrams(String[] strs) {
     return new AbstractList<>() {
     // 只需要实现get()和size()两个抽象方法
    // 其他方法（如iterator(), contains()等）有默认实现
            List<List<String>> list;
            private void init() {
                if (list != null)
                    return;
                Map<String, List<String>> strMap = new HashMap<>();
                for (String str : strs) {
                    char[] charArr = str.toCharArray();
                    Arrays.sort(charArr);
                    String strSorted = new String(charArr);
                    if (!strMap.containsKey(strSorted)) {
                        strMap.put(strSorted, new ArrayList<>());
                    }
                    strMap.get(strSorted).add(str);
                }
                list = new ArrayList<>(strMap.values());
            }
            @Override
            public List<String> get(int index) {
                init();
                return list.get(index);
            }
            @Override
            public int size() {
                init();
                return list.size();
            }
        };
    }
```

此种方法，如果不去调用结果，它永远不会执行计算，如果是需要部分结果，它也不会直接去计算全部，而是只计算需要的内容

- **不立即计算**：创建对象时并不执行实际计算
- **按需计算**：只有在真正需要数据时才执行计算
- **结果缓存**：计算一次后缓存结果，后续调用直接使用缓存


## 最长连续序列

```
package leetcodehot100;

/**
 * @author 自然醒
 * @version 1.0
 */

/**
 * 最长连续序列
 * 给定一个未排序的整数数组 nums ，找出数字连续的最长序列（不要求序列元素在原数组中连续）的长度。
 * 设计一个算法的时间复杂度为 O(n) 。
 * 示例 1：
 * 输入：nums = [100,4,200,1,3,2]
 * 输出：4
 * 解释：最长数字连续序列是 [1, 2, 3, 4]。它的长度为 4。
 * 示例 2：
 * 输入：nums = [0,3,7,2,5,8,4,6,0,1]
 * 输出：9
 * 解释：最长数字连续序列是 [0, 1, 2, 3, 4, 5, 6, 7, 8]。它的长度为 9。
 * 示例3 :
 * 输入：nums = [1,0,1,2]
 * 输出：3
 */
public class hot3 {
    public static int longestConsecutive(int[] nums) {
        if(nums == null){
            return 0;
        }
        if(nums.length == 0){
            return 0;
        }
        insertSort(nums);
        int current_length = 1;
        int max_length = 1;
        for(int i = 1; i < nums.length; i++){
            if(nums[i] == nums[i - 1] + 1){
                current_length++;
            } else if (nums[i] == nums[i-1]) {
                continue;
            } else{
                current_length = 1;
            }
            max_length = Math.max(current_length, max_length);
        }
        return max_length;
    }

    public static void insertSort(int[] nums){
        for(int i = 1; i < nums.length; i++){
            int base = nums[i];
            int idx = i - 1;
            while(idx >= 0 && nums[idx] > base){
                nums[idx + 1] = nums[idx];
                idx--;
            }
            nums[idx + 1] = base;
        }
    }
}
```

时间超出限制，我真笑了。。。

```
public static int longestConsecutive(int[] nums) {
    Set<Integer> set = new HashSet<>();
    for (int num : nums) {
        set.add(num);
    }
    int max_length = 0;
    for (int num : set) {
        int current = num;
        // 只有当num-1不存在时，才开始向后遍历num+1，num+2，num+3......
        if(!set.contains(current - 1)){
            while (set.contains(current + 1)) {
                current++;
            }
        }
        max_length = Math.max(max_length, current - num + 1);
    }
    return max_length;
}
```

直接使用Set集合即可，第一种方法主要浪费在排序的时间，虽然插入排序最好可以达到O(n)，但是如果是超大数或者逆序的排序数组，此时它的时间会为O(n^2)

Set集合的思路

1. 将所有数字存入Set（O(n)）
2. 遍历Set中的每个数字
3. 只有当当前数字是某个连续序列的起点（即num-1不存在）时，才开始向后寻找连续数字
4. 记录最长的序列长度

