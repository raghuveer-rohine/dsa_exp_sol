You are climbing a staircase. It takes n steps to reach the top.

Each time you can either climb 1 or 2 steps. In how many distinct ways can you climb to the top?

```java
public int climbStairs(int n) {
        // Base cases
        if (n <= 0) return 0;
        if (n == 1) return 1;
        if (n == 2) return 2;
        
        // pointers representing the number of ways to reach (i - 2) and (i - 1)
        int stepMinusTwo = 1; // Ways to reach step 1
        int stepMinusOne = 2; // Ways to reach step 2
        int currentStep = 0;
        
        // Iteratively compute ways for step 3 up to n
        for (int i = 3; i <= n; i++) {
            currentStep = stepMinusOne + stepMinusTwo;
            
            // Move pointers forward for the next iteration
            stepMinusTwo = stepMinusOne;
            stepMinusOne = currentStep;
        }
        
        return currentStep;
    }
```

Here is an explanation of the logic, broken down into your requested sections and tailored with an Indian context to help build your intuition.

### 1. The Core Concept
The algorithm uses a pattern called "Dynamic Programming" (specifically, a bottom-up approach with space optimization). In plain English, it solves a big problem by breaking it down into smaller, stepping-stone problems. Instead of trying to calculate the final answer from scratch, it starts from the smallest possible cases, remembers their answers, and uses them to build up to the final goal. 

The clever part of this specific code? It realizes it doesn't need to remember *everything* it has ever calculated—just the last two steps. It throws away old information to save memory, keeping only what's immediately necessary to take the next step forward.

### 2. A Physical Analogy
Although the code is named `climbStairs`, let's translate this into something deeply relatable: **Paying a Chaiwala**.

Imagine you drink cutting chai daily, and at the end of the week, your total bill is **₹N**. You have a specific habit: you only ever pay the Chaiwala using **₹1 coins** and **₹2 coins**. You want to know: *In how many different combinations/sequences can I hand over these coins to pay exactly ₹N?*

Think about the very *last* coin you hand over to settle the bill. It can only be one of two things:
1. A **₹1 coin**: This means *before* handing this coin over, you must have already paid exactly **₹(N-1)**.
2. A **₹2 coin**: This means *before* handing this coin over, you must have already paid exactly **₹(N-2)**.

Therefore, the total number of ways to pay your ₹N bill is simply: 
**(All the ways you could have paid a ₹(N-1) bill) + (All the ways you could have paid a ₹(N-2) bill).**

The Chaiwala doesn't need to memorize a massive list of coin combinations. To figure out the total combinations for today's bill, he just looks at his notebook where he wrote down the answers for yesterday's bill and the day before yesterday's bill, and simply adds those two numbers together!

### 3. Variable Translation
Here is what the code elements represent in our Chaiwala's world:
*   `n`: Your total chai bill amount (e.g., ₹5).
*   `stepMinusTwo` (initially 1): The Chaiwala's memory of the ways to pay a **₹1 bill**. Think of this as his record for the *second-to-last* bill amount.
*   `stepMinusOne` (initially 2): The Chaiwala's memory of the ways to pay a **₹2 bill**. Think of this as his record for the *previous* bill amount.
*   `currentStep`: The blank space in the Chaiwala's notebook where he will write down the total ways to pay the **current bill** he is calculating.
*   `i` (in the loop): The specific bill amount the Chaiwala is calculating *right now*, starting from ₹3 and going all the way up to your final bill, ₹N.

### 4. Translating the Math
*   `for (int i = 3; i <= n; i++)`: The Chaiwala can't magically jump to calculating the combinations for a ₹50 bill. He has to sequentially calculate the answers for ₹3, then ₹4, then ₹5... all the way up to `n`. This loop represents him turning the pages of his notebook, day by day.
*   `currentStep = stepMinusOne + stepMinusTwo`: The core logic! To find the answer for today's bill (`i`), he just takes yesterday's answer (`stepMinusOne`) and adds the day before yesterday's answer (`stepMinusTwo`).
*   `stepMinusTwo = stepMinusOne;` and `stepMinusOne = currentStep;`: This is the Chaiwala **updating his memory for the next day**. Once he has the answer for ₹3, he no longer cares about how to pay ₹1. For calculating ₹4 tomorrow, the ₹2 answer becomes his new "day before yesterday", and the newly calculated ₹3 answer becomes his new "yesterday". He shifts his focus down the notebook to only look at the two most recent numbers.

### 5. Deep Example Trace
Let's trace the execution for a bill of **₹5** (`n = 5`).

**Setup (The Base Cases):**
The Chaiwala knows from basic experience:
*   Ways to pay ₹1 = 1 way (just hand over one ₹1 coin). So, `stepMinusTwo = 1`.
*   Ways to pay ₹2 = 2 ways (two ₹1 coins, or one ₹2 coin). So, `stepMinusOne = 2`.

**Iteration 1 (Calculating for a ₹3 bill, so `i = 3`):**
*   `currentStep = 2 + 1 = 3`. (He calculates there are 3 ways to pay ₹3).
*   *Shift memory for the next round:*
    *   He forgets about ₹1. `stepMinusTwo` becomes **2** (the old ₹2 answer).
    *   `stepMinusOne` becomes **3** (the new ₹3 answer).

**Iteration 2 (Calculating for a ₹4 bill, so `i = 4`):**
*   `currentStep = 3 + 2 = 5`. (He adds yesterday's ₹3 answer and the day before's ₹2 answer. There are 5 ways to pay ₹4).
*   *Shift memory for the next round:*
    *   He forgets about ₹2. `stepMinusTwo` becomes **3** (the old ₹3 answer).
    *   `stepMinusOne` becomes **5** (the new ₹4 answer).

**Iteration 3 (Calculating for a ₹5 bill, so `i = 5`):**
*   `currentStep = 5 + 3 = 8`. (He adds yesterday's ₹4 answer and the day before's ₹3 answer. There are 8 ways to pay ₹5).
*   *Shift memory for the next round:*
    *   He forgets about ₹3. `stepMinusTwo` becomes **5** (the old ₹4 answer).
    *   `stepMinusOne` becomes **8** (the new ₹5 answer).

The loop finishes because `i` has reached your actual bill `n` (5).
The code returns `currentStep`, which is **8**. 

You now know there are exactly 8 different sequences of handing over ₹1 and ₹2 coins to pay your ₹5 chai bill, and the code calculated it without ever having to save the answers for ₹1, ₹2, and ₹3 in the long term!