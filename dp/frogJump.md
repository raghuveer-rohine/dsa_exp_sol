### 1. The Core Concept

At its heart, this algorithm is about **"Stateful Exploration"** (often called Dynamic Programming or Breadth-First Search with memorization). 

In simpler problems, to know if you can reach a destination, you only need to know *where* you are currently standing. But in this problem, simply knowing your location isn't enough. You also need to know *how fast you were moving* when you got there, because your current speed limits what you can do next. Therefore, the algorithm works by exploring the path forward while obsessively keeping track of the "momentum" or "speed" at every single pitstop.

### 2. A Physical Analogy

Imagine you are trying to cross a badly waterlogged street in Mumbai during the peak monsoon season. The muddy water is deep, but some kind shopkeepers have placed heavy **cement bricks (stones)** at random distances across the water. You need to jump from brick to brick to cross the street.

Here is the catch: Human momentum. 
When you are standing on the very first brick, you are starting from a standstill. Your first jump can only be a careful, short step of exactly 1 meter. 

Once you land on the next brick, your body has built up a certain "momentum" based on your last jump. If your last jump was, say, 2 meters wide, your muscles are primed. For your *next* jump from this new brick, you have exactly three choices:
1. Lose a little momentum and jump **1 meter** (jump - 1).
2. Maintain your current momentum and jump **2 meters** (jump).
3. Push a little harder and jump **3 meters** (jump + 1).

Because there are multiple ways to reach a single brick (maybe you reached brick #5 with a 2-meter jump, but your friend reached it with a 3-meter jump), you decide to carry a **small rough notebook**. Every time you look at a brick, you write down a list of *all the different jump sizes* that successfully landed you there. If you can reach the final brick on the other side of the street using any of your recorded jump sizes, you win!

### 3. Variable Translation

*   `int[] stones`: **The Street Map**. This tells you the exact meter marks where the cement bricks are placed in the water (e.g., there is a brick at 0m, 1m, 3m, 5m).
*   `Map<Integer, Set<Integer>> map`: **The "Momentum Notebook"**. The notebook has a separate page for every single brick. On a brick's page, you write down a list (`Set`) of all the successful jump sizes you used to land on that specific brick. 
*   `int stone`: The **current brick** you are physically standing on right now.
*   `Set<Integer> jumps`: Looking at your notebook's page for your current brick to see all the **available jump speeds** your body currently has stored up.
*   `int jump`: **One specific jump size** you are trying out from your list of available speeds.
*   `int nextStone = stone + jump`: **Your landing target**. You calculate exactly which meter mark your feet will land on if you make this jump.

### 4. Translating the Math

*   `map.put(stone, new HashSet<>())`: Opening a fresh, blank page in your notebook for every single brick on the street before you even start jumping.
*   `map.get(0).add(1)`: The absolute rule of starting from a standstill. On the notebook page for the very first brick (at 0m), you write down "1" because your first jump *must* be exactly 1 meter.
*   `nextStone == stones[stones.length - 1]`: You just calculated your landing spot, and you realize, "Wait, this jump will land me on the absolute last brick!" You've crossed the flooded street! (`return true`).
*   `if (map.containsKey(nextStone))`: Before you actually take the leap, you look ahead. Is there actually a cement brick placed at the meter mark you are about to land on? Or will you fall straight into the muddy pothole? You only proceed if a brick exists there.
*   **The Momentum Math** (If you land successfully on a new brick, you write down your future possibilities on *that* brick's notebook page):
    *   `if (jump - 1 > 0) -> map.get(nextStone).add(jump - 1)`: You write down that you can make your *next* jump slightly smaller (losing momentum). We check `> 0` because you can't jump 0 meters (you'd just jump in place) or jump backwards into the water!
    *   `map.get(nextStone).add(jump)`: You write down that you can make your *next* jump with the exact same momentum.
    *   `map.get(nextStone).add(jump + 1)`: You write down that you can make your *next* jump slightly larger (gaining momentum).

### 5. Deep Example Trace

Let's trace a street where bricks are placed at: **`[0, 1, 3, 5, 6, 8]`**

**Setup:** 
We open our Notebook. Pages exist for bricks at 0, 1, 3, 5, 6, and 8.
Rule #1: Notebook page for Brick `0` gets `[1]`.

---

**Iteration 1: Standing on Brick `0`**
*   Look at Notebook: I have a jump of `1` available.
*   Calculate landing: `0 (current) + 1 (jump) = target 1`.
*   Is there a brick at `1`? **Yes!**
*   Update Notebook for Brick `1` with my future possibilities based on this jump of `1`:
    *   Next jump can be `1 - 1 = 0` (Ignore, must be > 0)
    *   Next jump can be `1` (Write it down on page 1)
    *   Next jump can be `1 + 1 = 2` (Write it down on page 1)
*   *Notebook for Brick `1` now has: `[1, 2]`*

---

**Iteration 2: Standing on Brick `1`**
*   Look at Notebook: I have two jumps available: `[1, 2]`. I will try both.
*   **Try Jump 1:** Calculate `1 + 1 = target 2`. Is there a brick at 2? **No! Splash.**
*   **Try Jump 2:** Calculate `1 + 2 = target 3`. Is there a brick at 3? **Yes!**
*   Update Notebook for Brick `3` with future possibilities based on this jump of `2`:
    *   Next jump can be `2 - 1 = 1`
    *   Next jump can be `2`
    *   Next jump can be `2 + 1 = 3`
*   *Notebook for Brick `3` now has: `[1, 2, 3]`*

---

**Iteration 3: Standing on Brick `3`**
*   Look at Notebook: I have three jumps available: `[1, 2, 3]`.
*   **Try Jump 1:** Calculate `3 + 1 = target 4`. Brick at 4? **No. Splash.**
*   **Try Jump 2:** Calculate `3 + 2 = target 5`. Brick at 5? **Yes!**
    *   Update Brick `5`'s Notebook based on jump `2` -> gets `[1, 2, 3]`.
*   **Try Jump 3:** Calculate `3 + 3 = target 6`. Brick at 6? **Yes!**
    *   Update Brick `6`'s Notebook based on jump `3` -> gets `[2, 3, 4]`.

---

**Iteration 4: Standing on Brick `5`** 
*(This is where the magic of the notebook shines)*
*   Look at Notebook: I arrived here earlier, and the notebook says my available jumps are `[1, 2, 3]`.
*   **Try Jump 1:** Calculate `5 + 1 = target 6`. Brick at 6? **Yes!**
    *   Update Brick `6`'s Notebook based on jump `1` -> gets `[1, 2]`. 
    *   *(Note: Brick 6's page previously had `[2, 3, 4]`. It now has `[1, 2, 3, 4]`. The algorithm brilliantly remembers that we can reach Brick 6 with many different momentum states!)*
*   **Try Jump 2:** Calculate `5 + 2 = target 7`. Brick at 7? **No. Splash.**
*   **Try Jump 3:** Calculate `5 + 3 = target 8`. target 8 is the absolute last brick! 
*   **BINGO! `return true`**. We successfully crossed the flooded street! 

Notice how in the later stages, the frog (or the Mumbai commuter) relies heavily on the "momentum options" passed forward from previous successful jumps. If we hadn't written down all the possible speeds on Brick 3's page, we wouldn't have had the necessary "Jump 3" available when standing on Brick 5 to reach Brick 8!