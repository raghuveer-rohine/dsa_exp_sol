Question -
2498. Frog Jump II

You are given a 0-indexed integer array stones sorted in strictly increasing order representing the positions of stones in a river.

A frog, initially on the first stone, wants to travel to the last stone and then return to the first stone. However, it can jump to any stone at most once.

The length of a jump is the absolute difference between the position of the stone the frog is currently on and the position of the stone to which the frog jumps.

More formally, if the frog is at stones[i] and is jumping to stones[j], the length of the jump is |stones[i] - stones[j]|.
The cost of a path is the maximum length of a jump among all jumps in the path.

Return the minimum cost of a path for the frog.

```java
public int maxJump(int[] stones) {

        int max = stones[1] - stones[0];

        for (int i = 2; i < stones.length; i++) {
            max = Math.max(max, stones[i] - stones[i - 2]);
        }

        return max;
    }
```

### 1. The Core Concept
When you need to travel across a path and return, but you can only step on each stepping stone *once* during the entire round trip, what is the best strategy to keep your jumps as small as possible? 

The secret strategy is to **alternate**. You skip every other stone on your forward journey, saving those skipped stones for your return journey. Because you are consistently skipping exactly one stone in both directions, the longest jump you will ever make is simply the distance between a stone and the stone *two spots* behind it. This algorithm simply measures the distance of all these "skip-one" jumps across the entire path and finds the widest one.

### 2. A Physical Analogy
Imagine it's July in Mumbai, the street is heavily waterlogged from the monsoon, and you need to cross the road to buy milk from the local *Amul dairy* and come back home. There are several loose paver blocks (bricks) in the muddy water forming a line across the street.

Here's the catch: the bricks are loose and sink into the mud once you step on them, meaning **you cannot use the same brick twice**. 

If you step on every single brick on your way to the dairy, you'll have zero bricks left for the return journey, forcing you to make one impossible, superhuman jump all the way back across the flooded street! To avoid slipping or tearing your trousers, you play it smart: you use the 1st, 3rd, 5th, and 7th bricks on the way to the dairy. You purposely leave the 2nd, 4th, and 6th bricks untouched so you can use them on your way back home.

Because you are perfectly alternating and skipping exactly one brick (whether going forward or backward), your jumps always span across two bricks. The code calculates the distance of every single "skip-one-brick" jump to tell you exactly how wide your scariest, most difficult puddle jump is going to be.

### 3. Variable Translation
*   `int[] stones`: The physical tape-measure distance of each paver block in the flooded street, measured from your doorstep.
*   `max`: Your mental notebook tracking the "scariest, widest jump" you've encountered so far. You want to know the absolute toughest jump you will face on this grocery run.
*   `i`: Your eyes scanning the paver blocks ahead of you, one by one.

### 4. Translating the Math
*   `stones[1] - stones[0]`: Your very first step out of the house. You *have* to get from your doorstep (stone 0) to the very first paver block (stone 1). You calculate this small gap first because if the dairy is right across the street and there are only two stones total, this is your only jump.
*   `for (int i = 2; ...)`: You start looking from the 3rd brick (`i = 2`, since counting starts at 0). Why? Because to measure a "skip-one-brick" jump, you need at least three bricks to look at: the one you want to land on, the one you are skipping, and the one you jump from.
*   `stones[i] - stones[i - 2]`: The physical distance of your "skip-one-brick" jump. It calculates the tape-measure gap between your target brick (`i`) and the brick you are jumping from two positions back (`i - 2`).
*   `Math.max(max, ...)`: Your brain constantly comparing the puddle jump you just measured against the scariest jump you've remembered so far. If this new jump requires you to stretch your legs further, it overrides the old number and becomes the new "scariest jump" (`max`).

### 5. Deep Example Trace
Let's say the paver blocks are placed at these distances (in feet) from your doorstep:
`stones = [0, 2, 5, 6, 9, 10]`
*(0 ft is your doorstep. 10 ft is the dairy).*

**Initial Setup:**
*   You measure the step from your house to the first brick: `stones[1] - stones[0]` = 2 - 0 = **2 feet**.
*   `max = 2`. The scariest jump so far is 2 feet.

**Iteration 1 (`i = 2`):**
*   You look at the 3rd brick (at 5 ft).
*   What is the distance if you skip the 2nd brick and jump directly from the 1st brick?
*   `stones[2] - stones[0]` = 5 - 0 = **5 feet**.
*   Compare with your mental note: `Math.max(2, 5)`. The new scariest jump is now **5 feet**. (`max = 5`)

**Iteration 2 (`i = 3`):**
*   You look at the 4th brick (at 6 ft).
*   What is the jump distance coming from the 2nd brick (at 2 ft)?
*   `stones[3] - stones[1]` = 6 - 2 = **4 feet**.
*   Compare with your mental note: `Math.max(5, 4)`. 
*   5 feet is still a wider puddle. The `max` stays **5**.

**Iteration 3 (`i = 4`):**
*   You look at the 5th brick (at 9 ft).
*   What is the jump distance coming from the 3rd brick (at 5 ft)?
*   `stones[4] - stones[2]` = 9 - 5 = **4 feet**.
*   Compare: `Math.max(5, 4)`. 
*   The `max` stays **5**.

**Iteration 4 (`i = 5`, the end of the array):**
*   You look at the final brick, the Amul dairy (at 10 ft).
*   What is the jump distance coming from the 4th brick (at 6 ft)?
*   `stones[5] - stones[3]` = 10 - 6 = **4 feet**.
*   Compare: `Math.max(5, 4)`. 
*   The `max` stays **5**.

**Final Result:**
The loop ends. The algorithm returns `max = 5`. 
This tells you that on your entire round trip—going forward to the dairy (`0 ft ➔ 5 ft ➔ 9 ft ➔ 10 ft`) and coming backward home using the leftover bricks (`10 ft ➔ 6 ft ➔ 2 ft ➔ 0 ft`)—the absolute widest puddle you will *ever* have to jump across is exactly **5 feet**. Your trousers are safe!