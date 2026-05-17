```java
public int largestRectangleArea(int[] heights) {
        int n = heights.length;
        int [] leftMin = new int[heights.length];
        int [] rightMin = new int[heights.length];

        Stack<Integer> stack = new Stack<>();

        for (int i = 0; i < leftMin.length; i++) {
            while (!stack.isEmpty() && heights[i] <= heights[stack.peek()]) {
                stack.pop();
            }
            leftMin[i] = stack.isEmpty() ? -1 : stack.peek();
            stack.add(i);
        }

        stack.clear();

        for (int i = rightMin.length - 1; i >=0; i--) {
            while (!stack.isEmpty() && heights[i] <= heights[stack.peek()]) {
                stack.pop();
            }
            rightMin[i] = stack.isEmpty() ? n : stack.peek();
            stack.add(i);
        }

        int maxArea = 0;

        for (int i = 0; i < heights.length; i++) {
            int height = heights[i];
            int width = rightMin[i] - leftMin[i] - 1;
            
            maxArea = Math.max(maxArea, (height * width));
        }
        return maxArea;
    }
```
    Here is a breakdown of the code logic, heavily grounded in an everyday Indian context to build your intuition. 

### 1. The Core Concept
The code solves the "Largest Rectangle in Histogram" problem. In plain English, the algorithm’s core logic is based on identifying **"bottlenecks."** 

Any large rectangle you draw inside a bar chart will eventually be restricted by the height of at least one specific bar. That bar is the "bottleneck." Therefore, the algorithm says: *Instead of randomly guessing rectangles, let's treat every single bar as the bottleneck one by one. If I force the rectangle to be exactly as tall as my current bar, how far to the left and right can I stretch it before I hit a bar that is strictly shorter than me?* 

Once it calculates this maximum width for every single bar, it calculates the area (Height × Width) and simply picks the biggest one.

### 2. A Physical Analogy
Imagine you are a Pandal contractor in Mumbai during Ganesh Chaturthi. You have been hired to hang a massive, perfectly rectangular advertising banner (say, for Jio or Parle-G) across a row of tightly packed Chawl buildings. 

Each building has a different number of floors.
*   **The Rule:** The banner must be a perfect rectangle and its top edge must be pinned to the buildings.
*   **The Constraint:** If you decide your banner will be exactly the height of a 4-story building, you can stretch it left and right across neighboring buildings **only if** they are 4 stories or taller. The moment you stretch it over a 3-story building, the banner will sag in the air with nothing to pin it to. 

So, your job is to walk down the street, look at every single building, and ask: *"If I make the banner exactly THIS building's height, what is the first building to my left that is shorter? And what is the first building to my right that is shorter?"* Those two shorter buildings are your **blockers**. Your banner hangs perfectly between them.

### 3. Variable Translation
*   `heights` array: The row of Chawl buildings, and the exact number of floors each building has.
*   `leftMin` array: The index of the **"Left Blocker"** for each building. (The first building to the left that is too short to support the banner).
*   `rightMin` array: The index of the **"Right Blocker"** for each building. 
*   `stack`: The contractor’s **"Rough Notebook."** As you walk down the street left-to-right, you write down building numbers. However, if you write down a tall building, and the very next building is short, you scratch the tall building out of your notebook. Why? Because that tall building is now useless. It can never act as a "shorter left boundary" for any future buildings because the new short building is already blocking the way!
*   `maxArea`: The biggest banner area you've successfully measured so far to report to the society secretary.

### 4. Translating the Math
*   `while (!stack.isEmpty() && heights[i] <= heights[stack.peek()]) { stack.pop(); }`
    **The Scratch-Out:** This is you (the contractor) looking at the building right in front of you (`heights[i]`), comparing it to the last building you wrote in your notebook (`stack.peek()`). If the notebook's building is taller than or equal to the one in front of you, you scratch it out (`stack.pop()`). It is useless as a "shorter boundary." You keep scratching out tall buildings until you find one in your notebook that is strictly shorter.
*   `stack.isEmpty() ? -1 : stack.peek()`
    **The Empty Street:** If you scratch out so many buildings that your notebook is empty, it means there is *no* shorter building to your left. Your left boundary is the start of the street itself! We mathematically represent the start of the street as an imaginary building at index `-1`.
*   `rightMin[i] - leftMin[i] - 1`
    **Calculating Width:** If your Left Blocker is Building #1 and your Right Blocker is Building #4, the buildings that can actually support your banner are buildings #2 and #3. The math is `4 - 1 - 1 = 2` buildings wide. We subtract the extra `1` because the blockers themselves cannot support the banner; the banner hangs *between* them.

### 5. Deep Example Trace
Let's walk down a street with 6 Chawl buildings. 
Building heights (in floors): `[2, 1, 5, 6, 2, 3]`
Building indices: `0, 1, 2, 3, 4, 5`

Let's trace the first `for` loop, building the `leftMin` array (finding the Left Blockers). We start with an empty notebook (`stack = []`).

**Step 0: Index 0 (Height 2)**
*   Notebook is empty. No buildings to the left. Left Blocker is the street edge (`-1`).
*   `leftMin[0] = -1`.
*   Write index 0 in Notebook. Notebook is now `[0]`.

**Step 1: Index 1 (Height 1)**
*   Notebook's last entry: Index 0 (Height 2). 
*   Is 2 taller than our current height 1? Yes. *Scratch it out!* (`pop`). Notebook is empty.
*   No more buildings in notebook. Left Blocker is `-1`.
*   `leftMin[1] = -1`.
*   Write index 1 in Notebook. Notebook is now `[1]`.

**Step 2: Index 2 (Height 5)**
*   Notebook's last entry: Index 1 (Height 1).
*   Is 1 taller than 5? No, it's shorter! We found our Left Blocker.
*   `leftMin[2] = 1`.
*   Write index 2 in Notebook. Notebook is now `[1, 2]`.

**Step 3: Index 3 (Height 6)**
*   Notebook's last entry: Index 2 (Height 5).
*   Is 5 taller than 6? No. Left Blocker found.
*   `leftMin[3] = 2`.
*   Write index 3 in Notebook. Notebook is now `[1, 2, 3]`.

**Step 4: Index 4 (Height 2) <— The Crucial "Middle" Stage**
*   Our current height is 2. Let's look at the notebook.
*   Last entry: Index 3 (Height 6). Taller than 2? YES. *Scratch it out!* Notebook: `[1, 2]`.
*   Last entry: Index 2 (Height 5). Taller than 2? YES. *Scratch it out!* Notebook: `[1]`.
*   Last entry: Index 1 (Height 1). Taller than 2? NO! It's shorter. Left Blocker found!
*   `leftMin[4] = 1`.
*   Write index 4 in Notebook. Notebook is now `[1, 4]`.
> *Physical Intuition here:* Standing at the 2-floor building, you looked left. The 6-floor and 5-floor buildings were completely useless as "shorter boundaries." Your banner stretches right past them until it hits the 1-floor building. The notebook brilliantly skipped having to physically re-measure those tall buildings because we just threw them in the trash!

**Step 5: Index 5 (Height 3)**
*   Last entry: Index 4 (Height 2). Shorter than 3? Yes. 
*   `leftMin[5] = 4`.
*   Notebook is now `[1, 4, 5]`.

*Note: The second loop for `rightMin` does the exact same thing but walks the street backward (right to left).* 

Once both arrays are filled, the final loop just calculates the width `(Right Blocker - Left Blocker - 1)` and multiplies it by the height for each building, remembering the absolute maximum `maxArea` found along the way.