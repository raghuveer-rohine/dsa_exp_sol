```java
 public int change(int amount, int[] coins) {
        // dp[i] will store the number of ways to make amount i
        int[] dp = new int[amount + 1];
        
        // Base case: There is 1 way to make amount 0 (using no coins)
        dp[0] = 1;
        
        // Iterate through each coin denomination
        for (int coin : coins) {
            // Update the dp array for all amounts that can include this coin
            for (int i = coin; i <= amount; i++) {
                dp[i] += dp[i - coin];
            }
        }
        
        return dp[amount];
    }
```


Here is a breakdown of the Coin Change algorithm, explained from the ground up using a relatable, everyday Indian scenario.

### 1. The Core Concept
Instead of trying to calculate all the complicated combinations to make a large amount of money all at once, this algorithm builds the answer from the bottom up. It first figures out how to make ₹1, then ₹2, then ₹3, all the way up to your target amount. 

It relies on one simple truth: if you already know all the ways to make ₹3, finding out how to make ₹5 using a ₹2 coin is just a matter of adding a ₹2 coin to your existing ₹3 combinations. By keeping a written record of these smaller, previous answers, the algorithm avoids calculating the same things over and over again.

### 2. A Physical Analogy
Imagine Raju Bhai, a Kirana store owner in your neighborhood. He has to give exact change (*chillar*) to customers all day. He's tired of doing mental gymnastics every time someone asks for ₹50 in change.

So, Raju Bhai buys a long, thick rough notebook (a *Khata*). He decides to make a permanent "Cheat Sheet". He numbers the pages from Page 0 up to Page 50. Page 5 will eventually hold the final answer for: *"Total number of ways I can give exactly ₹5 in change."*

Raju Bhai doesn't dump all his coins on the table at once—that's too confusing. 
1. First, he opens his cash drawer and takes out **only his ₹1 coins**. He goes through his *Khata* from start to finish and fills out every page using *only* ₹1 coins.
2. Then, he brings out **only his ₹2 coins**. He goes through the *Khata* again. This time, he updates the pages. For example, when he gets to Page 5, he thinks: *"I already wrote down some ways to make ₹5 using just ₹1 coins. Now, if I hand the customer a ₹2 coin, I still owe them ₹3. Let me just flip back to Page 3, see how many ways I already figured out to make ₹3, and add that number to Page 5's total!"*

He repeats this process for every type of coin he has (₹5, ₹10, etc.), updating his *Khata* round by round.

### 3. Variable Translation
*   **`int amount`**: The final total change Raju Bhai needs to give a customer (e.g., ₹5).
*   **`int[] coins`**: The different compartments in Raju Bhai's cash box (e.g., he has ₹1, ₹2, and ₹5 coins).
*   **`int[] dp`**: Raju Bhai's *Khata* (rough notebook). `dp[5]` is literally "Page 5", which stores the total number of ways to make exactly ₹5.
*   **`dp = new int[amount + 1]`**: Raju Bhai buying a notebook with enough pages. If the target is ₹5, he needs 6 pages (Page 0, 1, 2, 3, 4, and 5).
*   **`dp[0] = 1`**: The first entry on Page 0. If a customer asks for ₹0 in change, Raju Bhai just stares at them and hands over nothing. There is exactly 1 way to give zero rupees: do nothing. (It sounds weird, but mathematically, this is the solid foundation the rest of the book relies on).
*   **`for (int coin : coins)`**: Raju Bhai picking up one type of coin at a time from his drawer. *"Okay, let's look at what I can do with ₹1 coins first... done. Now let's see how ₹2 coins change things..."*
*   **`for (int i = coin; i <= amount; i++)`**: Raju Bhai flipping through his *Khata* from left to right. Notice he starts at page `i = coin`. If he's currently holding a ₹5 coin, he doesn't bother looking at Page 1, 2, 3, or 4, because a ₹5 coin is too big to be used for those small amounts! He starts flipping directly from Page 5.

### 4. Translating the Math
The core magic of the algorithm happens on this single line: 
`dp[i] += dp[i - coin];`

Let's break down exactly what Raju Bhai is doing here physically:
*   **`i`**: The page Raju Bhai is currently looking at (let's say Page 5).
*   **`coin`**: The coin he is currently holding in his hand (let's say a ₹2 coin).
*   **`i - coin`**: This is Raju Bhai doing quick mental math. *"If the customer wants ₹5, and I drop this ₹2 coin in their hand... I still owe them ₹3."* (`5 - 2 = 3`).
*   **`dp[i - coin]`**: Raju Bhai physically flipping back to Page 3 of his *Khata* to see how many combinations he already calculated for ₹3.
*   **`+=`**: Raju Bhai taking his pen and adding the number from Page 3 to his current running tally on Page 5.

**In plain English:** "The new total ways to make ₹5 (`dp[5]`) equals the ways I already knew (`+=`), **PLUS** the number of ways I can make ₹3 (`dp[5 - 2]`). Why? Because for every single combination that makes ₹3, I just slap this ₹2 coin on top of it, and boom, I have a brand new way to make ₹5!"

### 5. Deep Example Trace
Let's trace finding change for ₹5 (`amount = 5`).
Raju Bhai only has two types of coins: `coins = [1, 2]`.

**Setup:**
He buys a *Khata* with 6 pages (0 to 5).
He writes a '1' on Page 0 (`dp[0] = 1`). All other pages are blank (0).
*Khata pages (0 to 5):* `[1, 0, 0, 0, 0, 0]`

**Round 1: Raju Bhai only uses ₹1 coins (`coin = 1`)**
He starts flipping pages from Page 1 to Page 5.
*   **Page 1 (i=1):** `dp[1] += dp[1 - 1]`. Flip back to Page 0. Page 0 says '1'. So, Page 1 gets `0 + 1 = 1`.
*   **Page 2 (i=2):** `dp[2] += dp[2 - 1]`. Flip back to Page 1. Page 1 says '1'. So, Page 2 gets `0 + 1 = 1`.
*   ...He does this all the way up to Page 5.
*   **End of Round 1 Khata:** `[1, 1, 1, 1, 1, 1]`
*(This makes perfect sense: if you only have ₹1 coins, there is exactly 1 way to make any amount—just hand over a fistful of ₹1 coins).*

**Round 2: Raju Bhai brings out the ₹2 coins (`coin = 2`)**
He starts flipping pages, but he starts at Page 2 (because a ₹2 coin is useless for Page 1).
*   **Page 2 (i=2):** *"I need to make ₹2. I use a ₹2 coin. I owe ₹0."*
    `dp[2] += dp[2 - 2]` -> Add Page 0's value (which is 1) to Page 2.
    New Page 2 value = 1 (old ways) + 1 (new way from Page 0) = **2**.
    *(The 2 ways are: `1+1` or a single `2`)*.

*   **Page 3 (i=3):** *"I need to make ₹3. I use a ₹2 coin. I owe ₹1."*
    `dp[3] += dp[3 - 2]` -> Add Page 1's value (which is 1) to Page 3.
    New Page 3 value = 1 (old ways) + 1 (new way from Page 1) = **2**.
    *Khata so far:* `[1, 1, 2, 2, 1, 1]`

*   **Page 4 (i=4):** *"I need to make ₹4. I use a ₹2 coin. I owe ₹2."*
    `dp[4] += dp[4 - 2]` -> Add Page 2's value (which is **now 2!**) to Page 4.
    New Page 4 value = 1 (old ways) + 2 (new ways from Page 2) = **3**.
    *Khata so far:* `[1, 1, 2, 2, 3, 1]`

*   **Page 5 (i=5):** *"I need to make ₹5. I use a ₹2 coin. I owe ₹3."*
    `dp[5] += dp[5 - 2]` -> Add Page 3's value (which is now 2) to Page 5.
    New Page 5 value = 1 (old ways) + 2 (new ways from Page 3) = **3**.
    *Final Khata:* `[1, 1, 2, 2, 3, 3]`

Raju Bhai closes the book. If the customer asks for ₹5 change, he doesn't need to calculate anything. He just turns to `dp[amount]` (Page 5), sees the number 3, and confidently knows there are exactly 3 ways to do it. 
*(For your reference, those 3 ways are: `1+1+1+1+1`, `1+1+1+2`, and `1+2+2`).*