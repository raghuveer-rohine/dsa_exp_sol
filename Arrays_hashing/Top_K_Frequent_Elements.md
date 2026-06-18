Given an integer array nums and an integer k, return the k most frequent elements. You may return the answer in any order.

 

Example 1:

Input: nums = [1,1,1,2,2,3], k = 2

Output: [1,2]

Example 2:

Input: nums = [1], k = 1

Output: [1]

Example 3:

Input: nums = [1,2,1,2,1,2,3,1,3,2], k = 2

Output: [1,2]

```java
    public int[] topKFrequent(int[] nums, int k) {
        Map<Integer, Integer> map = new HashMap<>();

        for (int i = 0; i < nums.length; i++) {
            if (map.containsKey(nums[i])) {
                int x = map.get(nums[i]) + 1;
                map.put(nums[i], x);
            } else {
                map.put(nums[i], 1);
            }
        }

        List<Integer> list = map.entrySet().stream().sorted((a, b) -> b.getValue() - a.getValue()).limit(k).map(Map.Entry::getKey).toList();

        return list.stream().mapToInt(Integer::intValue).toArray();

    }
```