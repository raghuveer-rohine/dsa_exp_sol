```java
    public int rob(int[] nums) {

        int prev2 = 0; // dp[i-2]
        int prev1 = 0; // dp[i-1]

        for (int num : nums) {

            int take = prev2 + num;
            int skip = prev1;

            int curr = Math.max(take, skip);

            prev2 = prev1;
            prev1 = curr;
        }

        return prev1;
    }
```

Here is a breakdown of the dynamic programming code using another relatable, everyday Indian scenario.

### 1. The Core Concept
**The Algorithm:** Dynamic Programming with Space Optimization (often called the "Moving Window" pattern).
**In Plain English:** Instead of calculating every single possible combination of choices from scratch (which would take forever), you walk through the problem step-by-step. At each step, you make the best possible decision based *only* on the best outcomes of your last two steps. Because you only ever need to look two steps back, you don't need a massive notebook to remember your entire history—you just need to remember two numbers at any given time.

### 2. A Physical Analogy
Imagine you are a **BMC (Brihanmumbai Municipal Corporation) Officer** walking down a busy, narrow Mumbai market street lined with illegal street hawkers. 
*   **The Problem:** You know exactly how much ₹ fine you can collect from each hawker's stall.
*   **The Catch:** If you stop to issue a fine to Hawker 1, it takes a few minutes. The hawker immediately next to them (Hawker 2) sees this, quickly packs up their stall, and runs away! By the time you finish with Hawker 1, Hawker 2 is gone. But Hawker 3 is still there because they were too far to notice in time.
*   **The Rule:** You can only collect fines from strictly **non-adjacent** hawkers.
*   **The Goal:** Walk down the street just once, never looking back, and maximize the total ₹ fine you collect for the day.

### 3. Variable Translation
*   `num`: The ₹ fine you can collect from the *current* hawker you are standing in front of.
*   `prev1` (Your Left Pocket): The maximum total fine you could have legally collected up to the *previous* hawker.
*   `prev2` (Your Right Pocket): The maximum total fine you could have legally collected up to the *hawker before the previous one* (two hawkers ago).
*   `take`: The scenario where you decide, *"I will catch this current hawker."*
*   `skip`: The scenario where you decide, *"I will ignore this current hawker and let them be."*
*   `curr`: Your final, calculated decision for the current hawker (whichever scenario makes you more ₹).

### 4. Translating the Math
Let's break down what happens inside the loop for every hawker:

*   `int take = prev2 + num;`
    *   If you decide to `take` (catch) the current hawker, it means you *must* have skipped the previous hawker (otherwise this one would have run away). So, your total money for this scenario is the fine from the current hawker (`num`) PLUS your best total earnings from *two hawkers ago* (`prev2`).
*   `int skip = prev1;`
    *   If you decide to `skip` the current hawker, you don't collect any new money right now. Your total money remains exactly the same as your best total earnings up to the *previous* hawker (`prev1`).
*   `int curr = Math.max(take, skip);`
    *   You calculate both scenarios in your head. You ask yourself: *"Do I get more money by catching this guy (relying on my earnings from 2 hawkers ago), or by skipping him (keeping my great earnings from the last hawker)?"* You pick the maximum.
*   `prev2 = prev1;` and `prev1 = curr;`
    *   You take a physical step forward to the next hawker. Your "previous hawker" now becomes "two hawkers ago" (`prev2 = prev1`). Your "current hawker" becomes your "previous hawker" (`prev1 = curr`). You shift your memory forward. You don't need an array or a heavy notebook; your two pockets are enough!

### 5. Deep Example Trace
Let's trace a street with 5 hawkers: **[₹20, ₹70, ₹90, ₹30, ₹100]**

Before you start walking:
`prev2` = ₹0 (Earnings from an imaginary hawker -2)
`prev1` = ₹0 (Earnings from an imaginary hawker -1)

**Iter 1: Hawker 1 (₹20)**
*   `take` = prev2 (₹0) + ₹20 = ₹20.
*   `skip` = prev1 (₹0).
*   `curr` = max(20, 0) = ₹20. *(You catch him).*
*   Step forward: `prev2` becomes ₹0, `prev1` becomes ₹20.

**Iter 2: Hawker 2 (₹70)**
*   `take` = prev2 (₹0) + ₹70 = ₹70.
*   `skip` = prev1 (₹20).
*   `curr` = max(70, 20) = ₹70. *(You catch him, meaning you skipped the ₹20 guy).*
*   Step forward: `prev2` becomes ₹20, `prev1` becomes ₹70.

**Iter 3: Hawker 3 (₹90)**
*   `take` = prev2 (₹20) + ₹90 = **₹110**. *(Ah! Because you caught the ₹20 guy two steps ago, you skipped the ₹70 guy, which means the ₹90 guy didn't run away!)*
*   `skip` = prev1 (₹70). *(If you skipped this guy to preserve the ₹70 you made earlier).*
*   `curr` = max(110, 70) = ₹110.
*   Step forward: `prev2` becomes ₹70, `prev1` becomes ₹110.

**Iter 4: Hawker 4 (₹30) - *The Crucial "Middle" Stage***
*   `take` = prev2 (₹70) + ₹30 = ₹100. *(This means you caught the ₹70 guy, skipped ₹90, and caught ₹30).*
*   `skip` = prev1 (₹110). *(This means ignoring the ₹30 guy so you don't ruin the amazing ₹110 you secured from catching ₹20 + ₹90).*
*   `curr` = max(100, 110) = **₹110**. *(You smartly choose to skip the ₹30 guy).*
*   Step forward: `prev2` becomes ₹110, `prev1` becomes ₹110. *(Notice how the 110 carries forward!)*

**Iter 5: Hawker 5 (₹100) - *The Final Stage***
*   `take` = prev2 (₹110) + ₹100 = **₹210**. *(Because you smartly skipped the ₹30 guy in the last step, this ₹100 guy is still here! You add his fine to your ₹110 from two steps ago).*
*   `skip` = prev1 (₹110).
*   `curr` = max(210, 110) = ₹210.
*   Step forward: `prev2` becomes ₹110, `prev1` becomes ₹210.

**End of street!** You return `prev1`, which is **₹210**. 
The optimal sequence was catching Hawker 1 (₹20), Hawker 3 (₹90), and Hawker 5 (₹100). The algorithm found this perfectly in a single pass without ever looking back more than two steps!