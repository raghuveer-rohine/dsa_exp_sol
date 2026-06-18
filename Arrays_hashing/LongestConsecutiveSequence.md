Given an unsorted array of integers nums, return the length of the longest consecutive elements sequence.

You must write an algorithm that runs in O(n) time.

```java
    public int longestConsecutive(int[] nums) {
        if (nums.length == 0) return 0;
        if (nums.length == 1) return 1;
        int left = 0; int right = 0;
        int max = 0;
        Arrays.sort(nums);
        List<Integer> list = new ArrayList<>();
        list.add(nums[0]);

        for (int i = 1; i < nums.length; i++) {
             if (nums[i - 1] == (nums[i]) ) {
                continue;
             } else {
                list.add(nums[i]);
             }
        }


        int nums2 [] = list.stream().mapToInt(Integer::intValue).toArray();

        if (nums2.length == 0) return 0;
        if (nums2.length == 1) return 1;

        for (int i = 1; i < nums2.length; i++) {
            if (nums2[i - 1] + 1 == (nums2[i]) ) {
                right++;
                max = Math.max(max, right - left + 1);
            } else {
                 max = Math.max(max, right - left + 1);
                 left = i;
                 right++;
            }
        }

        return max;
    }
```

