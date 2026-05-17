```java
    public int coinChange(int[] coins, int amount) {
// Base case: if amount is 0, we need 0 coins
        if (amount == 0) return 0;
        
        // Create DP array up to the target amount
        // We initialize with amount + 1 because that's a value 
        // impossible to reach (the max coins possible is 'amount' using 1-cent coins)
        int max = amount + 1;
        int[] dp = new int[amount + 1];
        Arrays.fill(dp, max);
        
        // Base case: 0 coins needed for amount 0
        dp[0] = 0;
        
        // Outer loop: iterate through every amount from 1 to the target
        for (int i = 1; i <= amount; i++) {
            // Inner loop: try every coin denomination
            for (int coin : coins) {
                if (coin <= i) {
                    // Check if taking this coin results in a smaller number of coins
                    dp[i] = Math.min(dp[i], dp[i - coin] + 1);
                }
            }
        }
        
        // If dp[amount] is still 'max', it means the amount is unreachable
        return dp[amount] > amount ? -1 : dp[amount];
    }
```

Here is a breakdown of the Coin Change logic, specifically designed to help you build an intuitive mental model using an everyday Indian analogy.

### 1. The Core Concept
The algorithm uses a pattern called **Dynamic Programming (Bottom-Up)**. 
Instead of trying to be overly smart and greedily picking the biggest coin first (which sometimes fails to give the *minimum* total coins), the algorithm admits: "I don't know the answer for a large amount yet. Let me first figure out the absolute best way to make ₹1, then ₹2, then ₹3, and build my way up to the target amount." 

By solving for the smaller amounts first and writing down the answers, you can reuse those exact answers to easily solve the bigger amounts later without recalculating them.

### 2. A Physical Analogy
Imagine **Ramu Kaka**, a busy *Kirana* (grocery) store owner in your neighborhood. There is a severe shortage of loose change (chillar) in the market, so he absolutely must give customers their exact change using the **fewest number of coins possible** from his *galla* (cash box).

To do this quickly, Ramu Kaka keeps a small **rough notebook**. 
- Page 1 of the notebook answers: "What is the minimum coins I need for ₹1?"
- Page 2 answers: "What is the minimum coins for ₹2?"
- And so on, all the way up to the target change amount.

Whenever a customer asks for ₹11 change, Ramu Kaka doesn't guess. He opens his notebook, looks at his available coins, and uses the answers he already figured out on the earlier pages to quickly calculate the best combination for ₹11.

### 3. Variable Translation
Let's translate the code's syntax into Ramu Kaka's physical reality:

*   **`coins`**: The physical types of coins currently sitting in Ramu Kaka's *galla* (e.g., ₹1, ₹2, ₹5 coins).
*   **`amount`**: The final change the customer requested (e.g., ₹11).
*   **`dp` array**: Ramu Kaka’s **rough notebook**. `dp[i]` literally means "the number written on Page `i`".
*   **`max = amount + 1`**: A ridiculously high, impossible number. Think of it as Ramu Kaka writing **"INFINITY"** (or drawing a cross 'X') on a blank page before he starts calculating, meaning *"I haven't figured this out yet."*
*   **`i` (Outer loop)**: The current page number Ramu Kaka is filling out in his notebook, starting from Page 1 up to the target amount.
*   **`coin` (Inner loop)**: Ramu Kaka picking up one specific coin from his *galla* to see if handing it to the customer is a good idea.

### 4. Translating the Math
Here is exactly what the code is doing mechanically inside the loops:

*   **`Arrays.fill(dp, max)`**: Every morning, Ramu Kaka writes "INFINITY" on every page of his notebook.
*   **`dp[0] = 0`**: On Page 0, he writes `0`. Logic: "If the customer wants ₹0 change, I hand them exactly 0 coins."
*   **`if (coin <= i)`**: A reality check. "If the customer wants ₹3 change (`i`), I cannot give them a ₹5 coin (`coin`). I can only use coins that are smaller than or equal to the total change needed."
*   **`dp[i - coin]`**: This is the genius part. Ramu Kaka thinks: *"If I hand the customer this ₹5 `coin` right now, how much change is left to give? Ah, it's `i - 5`. Instead of calculating the rest in my head, let me just flip to Page `i - 5` in my notebook and see what the minimum coins were for that amount!"*
*   **`+ 1`**: He adds `1` because he literally just handed 1 physical coin (the ₹5 coin) to the customer, plus whatever number he read from the past page.
*   **`Math.min(...)`**: Ramu Kaka tries *every* available coin in his galla for the current amount. For each one, he compares the new result with whatever he previously scribbled on the current page (`dp[i]`). He crosses out the larger number and always keeps the smaller one, because his goal is to save chillar.

### 5. Deep Example Trace
Let's trace it far enough to see the magic happen. 
*   **Ramu Kaka's galla (`coins`)**: ₹1, ₹2, ₹5
*   **Customer wants (`amount`)**: ₹6
*   **Initial Notebook (`dp`)**: `[0, 7, 7, 7, 7, 7, 7]` *(7 is our "Infinity" since `amount + 1 = 7`)*

**Page 1 (`i = 1`)**:
*   Try ₹1 coin: 1 left (1 - 1 = 0). Look at Page 0 -> `0` coins. Total: 0 + 1 = 1 coin. `Math.min(7, 1)` -> Notebook updates to `1`.
*   *(₹2 and ₹5 are too big, skip)*.
*   **Notebook is now**: `[0, 1, 7, 7, 7, 7, 7]`

**Page 2 (`i = 2`)**:
*   Try ₹1 coin: 1 left (2 - 1 = 1). Look at Page 1 -> `1` coin. Total: 1 + 1 = 2 coins.
*   Try ₹2 coin: 0 left (2 - 2 = 0). Look at Page 0 -> `0` coins. Total: 0 + 1 = 1 coin. `Math.min(2, 1)` -> Notebook updates to `1`.
*   **Notebook is now**: `[0, 1, 1, 7, 7, 7, 7]`

**Page 3 (`i = 3`)**:
*   Try ₹1 coin: 2 left (3 - 1 = 2). Look at Page 2 -> `1` coin. Total: 1 + 1 = 2 coins. 
*   Try ₹2 coin: 1 left (3 - 2 = 1). Look at Page 1 -> `1` coin. Total: 1 + 1 = 2 coins. `Math.min(2, 2)` -> stays `2`.
*   **Notebook is now**: `[0, 1, 1, 2, 7, 7, 7]`

*(Skipping Page 4 to keep it brief, Page 4 will become `2` because of ₹2 + ₹2)*
*   **Notebook is now**: `[0, 1, 1, 2, 2, 7, 7]`

**Page 5 (`i = 5`)**:
*   Try ₹1 coin: 4 left. Page 4 says `2`. Total: 2 + 1 = 3 coins.
*   Try ₹2 coin: 3 left. Page 3 says `2`. Total: 2 + 1 = 3 coins.
*   **Try ₹5 coin**: 0 left (5 - 5 = 0). Page 0 says `0`. Total: 0 + 1 = 1 coin! `Math.min(3, 1)` -> Notebook updates to `1`.
*   **Notebook is now**: `[0, 1, 1, 2, 2, 1, 7]`

**Page 6 (`i = 6`)**: *The final amount!*
*   Try ₹1 coin: 5 left (6 - 1 = 5). Look at Page 5 -> `1` coin. Total: 1 + 1 = 2 coins (A ₹1 coin + a ₹5 coin).
*   Try ₹2 coin: 4 left (6 - 2 = 4). Look at Page 4 -> `2` coins. Total: 2 + 1 = 3 coins. `Math.min(2, 3)` -> keeps `2`.
*   Try ₹5 coin: 1 left (6 - 5 = 1). Look at Page 1 -> `1` coin. Total: 1 + 1 = 2 coins. `Math.min(2, 2)` -> keeps `2`.

**Final Answer:** Ramu Kaka looks at Page 6 of his notebook, sees the number **2**, and knows instantly that 2 coins is the absolute minimum way to give ₹6 change!