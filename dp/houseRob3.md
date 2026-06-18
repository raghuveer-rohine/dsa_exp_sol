```java
class Solution {

    public int rob(TreeNode root) {

        int[] ans = dfs(root);

        return Math.max(ans[0], ans[1]);
    }

    // ans[0] -> skip current node
    // ans[1] -> rob current node

    private int[] dfs(TreeNode node) {

        if (node == null) {
            return new int[]{0, 0};
        }

        int[] left = dfs(node.left);
        int[] right = dfs(node.right);

        int rob = node.val + left[0] + right[0];

        int skip =
                Math.max(left[0], left[1]) +
                Math.max(right[0], right[1]);

        return new int[]{skip, rob};
    }
}
```

### 1. The Core Concept

The algorithm uses a pattern called **Dynamic Programming on a Tree** (specifically, a "bottom-up, choose-or-skip decision maker"). 

Instead of starting from the top and guessing what to do, it starts from the absolute bottom (the leaves of the tree) and works its way up. For every single person in the tree, it calculates and remembers exactly two scenarios:
1. "What is the maximum total money we can get from this person's sub-group if this person **is** included?"
2. "What is the maximum total money we can get from this person's sub-group if this person **is NOT** included?"

By carrying these two answers upward, the parent nodes don't have to recalculate anything. They just look at their children's two answers and make a simple, localized decision.

---

### 2. A Physical Analogy

Imagine a massive Indian IT consulting company right before Diwali. The company is structured like a strict corporate hierarchy (a tree): there is one CEO at the top, who has VPs reporting to them, who have Managers, down to the freshers at the bottom. 

The company wants to distribute ₹ Diwali bonuses (the `node.val`). However, the HR department has instituted a strict "No Jealousy" policy: **You cannot give a bonus to a manager and their direct reportees at the same time.** 

The HR Head wants to calculate the absolute maximum total bonus amount they can distribute across the company without breaking this rule. 

To do this, HR doesn't start at the CEO. They start at the bottom with the freshers. For each employee, HR creates a **"Bonus Slip"** (an array with two numbers) that asks two questions:
- **Scenario A (Skip):** If we give this employee ₹0, what is the maximum bonus we can distribute to their entire downline?
- **Scenario B (Give):** If we give this employee their ₹ bonus, what is the maximum bonus we can distribute to their downline? (Keeping in mind their direct juniors get nothing).

Each employee hands their "Bonus Slip" up to their manager, who uses those numbers to calculate their own slip, passing it all the way up to the CEO.

---

### 3. Variable Translation

- `dfs(TreeNode node)`: The HR manager evaluating an employee's specific department. It returns a "Bonus Slip" with exactly two numbers.
- `int[] ans`: The final Bonus Slip handed to the CEO, containing the two grand totals for the entire company.
- `int[] left` & `int[] right`: The completed Bonus Slips handed *up* by the employee's two direct reportees.
- `node.val`: The specific ₹ Diwali bonus amount assigned to this individual employee.
- `ans[0]` or `skip`: The rough notebook entry for "Total bonus distributed in this department if the current manager gets **₹0**".
- `ans[1]` or `rob`: The rough notebook entry for "Total bonus distributed in this department if the current manager **GETS** their ₹ bonus".

---

### 4. Translating the Math

Let's look at the mathematical formulas inside the loop and see what they mean for our HR manager:

**`int rob = node.val + left[0] + right[0];`**
- **The Physical Meaning:** If the HR manager decides to **GIVE** the bonus to the current employee (`rob`), the total is that employee's bonus (`node.val`). But, because of the strict HR policy, their direct reportees cannot get a bonus! Therefore, we are *forced* to use the `[0]` (skip) values from both the left and right reportees' Bonus Slips. We have no choice but to add `left[0]` and `right[0]`.

**`int skip = Math.max(left[0], left[1]) + Math.max(right[0], right[1]);`**
- **The Physical Meaning:** If the HR manager decides to **SKIP** this employee (they get ₹0), the policy says their direct reportees *are allowed* to get bonuses. But they don't *have* to get it. HR wants the maximum total money distributed. So, HR looks at the left reportee's Bonus Slip and says, "Which is bigger: giving them the bonus (`left[1]`) or skipping them (`left[0]`)? I'll take the max!" (`Math.max(left[0], left[1])`). They do the exact same greedy check for the right reportee and add the two best-case scenarios together. 

---

### 5. Deep Example Trace

Let's build a small corporate hierarchy with ₹ amounts. 
Notice how VP2 has a small bonus (₹50) but manages a star performer M2 who has a massive bonus (₹100).

```text
       CEO (₹30)
      /         \
   VP1 (₹40)   VP2 (₹50)
    /           \
 M1 (₹10)      M2 (₹100)
```

If a node is `null` (doesn't exist), the slip is `[0, 0]`.

**Stage 1: The Freshers (M1 and M2)**
- **Evaluate M1 (₹10):**
  - No juniors. `left = [0, 0]`, `right = [0, 0]`.
  - `skip` = max(0,0) + max(0,0) = **0**.
  - `rob` = 10 + 0 + 0 = **10**.
  - M1 hands slip `[0, 10]` to VP1.
- **Evaluate M2 (₹100):**
  - No juniors. `left = [0, 0]`, `right = [0, 0]`.
  - `skip` = max(0,0) + max(0,0) = **0**.
  - `rob` = 100 + 0 + 0 = **100**.
  - M2 hands slip `[0, 100]` to VP2.

**Stage 2: The Middle Management (VP1 and VP2) - *The Middle Trace***
- **Evaluate VP1 (₹40):**
  - Receives left slip `[0, 10]` from M1. Right is empty `[0, 0]`.
  - `skip`: VP1 gets nothing. We pick the best from M1. `Math.max(0, 10)` is 10. Total = **10**.
  - `rob`: VP1 gets ₹40. M1 *must* be skipped (`left[0]`). 40 + 0 + 0 = **40**.
  - VP1 hands slip `[10, 40]` to CEO.
- **Evaluate VP2 (₹50):**
  - Left is empty `[0, 0]`. Receives right slip `[0, 100]` from M2.
  - `skip`: VP2 gets nothing. We pick the best from M2. `Math.max(0, 100)` is 100. Total = **100**.
  - `rob`: VP2 gets ₹50. M2 *must* be skipped (`right[0]`). 50 + 0 + 0 = **50**.
  - VP2 hands slip `[100, 50]` to CEO.
  - *(Key Intuition Point: Look closely at VP2's slip. If VP2 is robbed, we only get ₹50. But if VP2 is skipped, we get ₹100! Skipping a manager to unlock a massive payout in their downline is the core magic of this algorithm).*

**Stage 3: The Boss (CEO) - *The Late Trace***
- **Evaluate CEO (₹30):**
  - Receives left slip `[10, 40]` from VP1.
  - Receives right slip `[100, 50]` from VP2.
  - `skip`: CEO gets nothing. HR takes the absolute best from VP1's department (`Math.max(10, 40)` = 40) AND the best from VP2's department (`Math.max(100, 50)` = 100). Total = 40 + 100 = **140**.
  - `rob`: CEO gets ₹30. VP1 and VP2 *must* be skipped. We are forced to use `left[0]` (which is 10) and `right[0]` (which is 100). Total = 30 + 10 + 100 = **140**.
  - The CEO's final slip is `[140, 140]`.

**The Final Return**
The main function runs `return Math.max(ans[0], ans[1]);`. The CEO looks at their final slip `[140, 140]` and picks the highest number. The maximum Diwali bonus distributed without violating the HR policy is **₹140**. (In this specific example, giving the CEO a bonus or skipping them happens to result in the exact same total payout).