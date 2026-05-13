```java
public int longestValidParentheses(String s) {
        int left = 0, right = 0, maxLength = 0;

        // Forward pass: Left to Right
        for (int i = 0; i < s.length(); i++) {
            if (s.charAt(i) == '(') {
                left++;
            } else {
                right++;
            }
            
            if (left == right) {
                maxLength = Math.max(maxLength, 2 * right);
            } else if (right > left) {
                left = right = 0;
            }
        }

        left = right = 0;
        // Backward pass: Right to Left
        for (int i = s.length() - 1; i >= 0; i--) {
            if (s.charAt(i) == '(') {
                left++;
            } else {
                right++;
            }
            
            if (left == right) {
                maxLength = Math.max(maxLength, 2 * left);
            } else if (left > right) {
                left = right = 0;
            }
        }

        return maxLength;
    }
```

Viewed solution.java:1-21

Here is a breakdown of the Longest Valid Parentheses algorithm, explained step-by-step using a relatable Indian scenario to build your intuition for *why* the code is written this way.

### 1. The Core Concept
This algorithm tries to find the longest unbroken sequence of perfectly matched brackets without using extra computer memory. It uses a clever "two-way sweep" technique. It sweeps from left-to-right, counting open and close brackets, and records a match when the counts are perfectly equal. It resets the counts if a closing bracket appears without an opening one (an invalid break in the chain). 

But there's a trap: what if there are extra *opening* brackets that never close? The left-to-right sweep will get confused and miss the valid matches hidden inside them. To fix this, the algorithm does a second sweep from right-to-left. This backward pass acts as a safety net, catching the valid groups that the forward pass missed.

### 2. A Physical Analogy
Let's go to Siddhivinayak Temple in Mumbai. Meet Ramesh and his night-shift partner, Suresh. They manage the Free Chappal (Shoe) Counter.

They have a daily logbook (the string `s`). 
*   `(` means **"A devotee deposited their chappals"** (Token generated).
*   `)` means **"A devotee returned the token and took their chappals"**.

A "Perfect Rush" (a valid sequence) is a continuous period where every deposited chappal gets perfectly returned, leaving the counter completely balanced at zero pending shoes.

**Ramesh's Shift (Left to Right pass):**
Ramesh reads the logbook forward, from morning to night. He keeps a tally of `Deposits` and `Returns`.
*   If `Deposits == Returns`: Perfect balance! He calculates the length of this rush and writes it down as a record.
*   If `Returns > Deposits`: **"Chor! Fraud!"** Someone is trying to take chappals when the rack is empty. This breaks the sequence permanently. Ramesh violently crosses out the log, resets his tallies to 0, and starts tracking the next group fresh.

**The Problem:** What if the logbook says: `Deposit, Deposit, Return` `( ( )`?
Ramesh ends the day with 2 Deposits and 1 Return. He never hits that perfect `Deposits == Returns` moment, so he records a maximum length of 0. But wait! The last two actions `( )` were a perfectly valid transaction of 2! Ramesh missed it because the first extra `Deposit` threw his count off for the rest of the day.

**Suresh's Shift (Right to Left pass):**
To fix this, Suresh takes the exact same logbook and reads it *backwards*, from night to morning. He counts exactly the same way. But reading backward, that extra `Deposit` is caught immediately because `Deposits > Returns`. Suresh yells "Abandoned chappals!", resets his tallies to 0, and starts fresh. Between the two brothers, they are guaranteed to catch the true Longest Perfect Rush.

### 3. Variable Translation
*   **`s`**: The temple's daily logbook.
*   **`left`**: The "Chappals Deposited" tally counter.
*   **`right`**: The "Chappals Returned" tally counter.
*   **`maxLength`**: The record book. The highest number of perfectly matched chappal transactions handled in one continuous streak.
*   **`First for loop (i = 0 to length)`**: Ramesh reading the logbook forward (Morning to Night).
*   **`Second for loop (i = length - 1 down to 0)`**: Suresh reading the exact same logbook backward (Night to Morning).
*   **`if (left == right)`**: The "Perfect Balance" moment! Every token given out was returned.
*   **`left = right = 0`**: The Reset button. Clearing the tally counters to zero because a chain was permanently broken (either by a fraud or an abandoned shoe).

### 4. Translating the Math
Let's break down the math operations inside the loops:
*   **`left++` and `right++`**: Clicking the mechanical tally counter up by 1.
*   **`2 * right` (or `2 * left`)**: Think about it physically. If you have 3 Deposits and 3 Returns (`left == 3, right == 3`), the total length of that valid sequence is `3 + 3 = 6`. Instead of doing `left + right`, the code just does `2 * right` because they are perfectly equal at that exact moment.
*   **`Math.max(maxLength, ...)`**: The brothers checking the record book. *"Is this new Perfect Rush (say, 6) bigger than our previous recorded best (say, 4)?"* If yes, overwrite the old record.
*   **`right > left` (Ramesh's condition)**: The `Returns` tally is higher than `Deposits`. Someone wants shoes that aren't there. Sequence broken.
*   **`left > right` (Suresh's condition)**: The `Deposits` tally is higher than `Returns` (while reading backward). Someone abandoned their shoes and went home barefoot. Sequence broken.

### 5. Deep Example Trace
Let's trace **`s = "( ( )"`** to see exactly why Ramesh fails and why Suresh is the hero.

String: `(`  `(`  `)`
Logbook: `Deposit`, `Deposit`, `Return`.

**Pass 1: Ramesh reads Forward (Morning to Night)**
*   **i = 0 `(`**: "Deposit." `left` becomes 1. `right` is 0.
*   **i = 1 `(`**: "Deposit." `left` becomes 2. `right` is 0.
*   **i = 2 `)`**: "Return." `left` is 2. `right` becomes 1.

*Ramesh finishes the day. He never saw `left == right` (2 never equaled 1). He never saw `right > left`. His `maxLength` remains **0**. He missed the valid pair entirely!*

**Ramesh resets the counters to 0 before handing the book to Suresh.** `left = 0, right = 0`.

**Pass 2: Suresh reads Backward (Night to Morning)**
Suresh looks at the string backwards: `)`  `(`  `(`
*   **i = 2 `)`**: "Return." (Reading backward, he sees the end of the day first). `right` becomes 1. `left` is 0.
*   **i = 1 `(`**: "Deposit." `left` becomes 1. `right` is 1.
    -> **WAIT!** `left == right` (1 == 1)! Perfect balance found! 
    -> `maxLength = Math.max(0, 2 * 1) = 2`. Suresh happily records a length of 2!
*   **i = 0 `(`**: "Deposit." `left` becomes 2. `right` is 1.
    -> `left > right` (2 > 1). *"Ah, an abandoned chappal from the morning!"* Suresh hits the reset button: `left = 0, right = 0`.

End of the shift. The final answer in the record book is **2**. The algorithm works beautifully because the brothers covered each other's blind spots!