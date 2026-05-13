```java
public int lengthOfLongestSubstring(String s) {
        int n = s.length();
        int maxLength = 0;
        
        // Map to store the last seen index of a character
        // Key: Character, Value: Index + 1 (to move the pointer past the duplicate)
        HashMap<Character, Integer> map = new HashMap<>();
        
        // i is the left pointer, j is the right pointer
        for (int j = 0, i = 0; j < n; j++) {
            char currentChar = s.charAt(j);
            
            if (map.containsKey(currentChar)) {
                // Move the left pointer to the right of the last seen duplicate
                // We use Math.max to ensure 'i' never moves backward
                i = Math.max(map.get(currentChar), i);
            }
            
            // Calculate length of current window (j - i + 1)
            maxLength = Math.max(maxLength, j - i + 1);
            
            // Update the last seen index of the character
            map.put(currentChar, j + 1);
        }
        
        return maxLength;
    }
```

Here is a breakdown of the algorithm, structured exactly as you requested, using a heavily "Indianised" analogy to build your intuition.

### 1. The Core Concept
This algorithm uses a pattern called the **"Sliding Window"**, specifically a dynamically resizing window. Instead of checking every single possible combination of characters from scratch (which takes too much time), we maintain a valid "window" (a substring with no duplicates). We expand the window from the right to add new characters. If we accidentally include a duplicate, we don't start completely over; we simply shrink the window from the left just enough to kick out the old duplicate. 

### 2. A Physical Analogy
Imagine a grand Indian wedding (*Shaadi*). Guests are seated in a long, continuous row of chairs, eating dinner. 

You are the wedding videographer. The strict auntie who hired you gave you a weird rule: **"Take a continuous panning shot of the longest line of guests, but absolutely NO TWO PEOPLE in the frame can be wearing the exact same colored clothes."**

You are holding a physical, rectangular camera frame. You start at the first chair and slide the **right edge** of your frame forward to include more guests. Your assistant, "Chotu", stands next to you with a rough notebook. Every time a guest enters the frame, Chotu notes down their clothes color and their chair number.

Suddenly, your right edge includes a guest wearing a "Red Saree". Chotu checks his notebook and screams, *"Boss! We already have a Red Saree in the frame back at Chair 2!"*

To fix the shot, you **do not** stop recording and start all over. You simply drag the **left edge** of your camera frame past Chair 2. The old Red Saree is now kicked out of the frame, the new Red Saree is in, and you peacefully continue sliding the right edge forward.

### 3. Variable Translation
*   `s`: The long row of chairs with guests sitting on them.
*   `j`: The **right edge** of your camera frame (sliding forward to add people).
*   `i`: The **left edge** of your camera frame (getting dragged forward to kick out duplicates).
*   `map`: **Chotu’s Rough Notebook**. He writes down `Key: Color, Value: Chair Number + 1`. Why `+ 1`? Because he is writing down the *exact safe chair* the left edge should jump to if we see this color again (the chair immediately *after* the duplicate).
*   `maxLength`: Your personal high score. The widest your camera frame ever opened during the entire wedding.
*   `currentChar`: The clothing color of the guest currently entering the right side of the frame.

### 4. Translating the Math
*   `j++` (in the loop): Sliding the right edge of the frame one chair forward.
*   `map.put(currentChar, j + 1)`: Chotu updating his notebook. *"Boss, if we see this color again, just jump the left edge straight to chair `j + 1` to kick this person out."*
*   `j - i + 1`: The physical width of your camera frame right now. If your frame stretches from chair 2 to chair 5, doing `5 - 2 = 3`. But there are actually 4 people in the frame (Chairs 2, 3, 4, 5). The `+ 1` accounts for the inclusive counting.
*   `maxLength = Math.max(maxLength, j - i + 1)`: Comparing your current frame width with your all-time high score. If today's frame is wider, it becomes the new high score.
*   `i = Math.max(map.get(currentChar), i)`: **The "Don't Go Backwards" Rule.** Imagine you find a duplicate Blue Kurta, and Chotu's notebook says, *"Blue Kurta was at chair 2, jump the left edge to chair 3"*. But your left edge `i` is ALREADY at chair 5 because of some other duplicate you fixed earlier. You cannot drag the left edge backward to chair 3—that would ruin the photo by bringing old, kicked-out people back! `Math.max` ensures the left edge ONLY moves forward. It tells Chotu: *"Ignore the old notebook entry if my left edge has already passed it."*

### 5. Deep Example Trace
Let's use the guest colors: **R B G B Y R** (Red, Blue, Green, Blue, Yellow, Red)  
Indices (Chair Numbers): `0 1 2 3 4 5`

Initial State:
*   `maxLength = 0`
*   `i = 0`, `j = 0`
*   Chotu's Notebook (`map`) is empty.

**Step 1: j = 0 (Guest R)**
*   Right edge sees Red (R). Not in notebook.
*   `maxLength = Math.max(0, 0 - 0 + 1) = 1`
*   Chotu writes: `R -> 1` (Safe chair to jump to is 1).
*   *Current Frame: [R] (Chairs 0 to 0)*

**Step 2: j = 1 (Guest B)**
*   Right edge sees Blue (B). Not in notebook.
*   `maxLength = Math.max(1, 1 - 0 + 1) = 2`
*   Chotu writes: `B -> 2`.
*   *Current Frame: [R, B] (Chairs 0 to 1)*

**Step 3: j = 2 (Guest G)**
*   Right edge sees Green (G). Not in notebook.
*   `maxLength = Math.max(2, 2 - 0 + 1) = 3`
*   Chotu writes: `G -> 3`.
*   *Current Frame: [R, B, G] (Chairs 0 to 2)*

**Step 4: j = 3 (Guest B) — The "Middle Stage" Duplicate!**
*   Right edge sees Blue (B). Duplicate! 
*   Chotu screams: *"Boss, B is in the notebook! The safe chair is 2!"* (`map.get('B')` is 2).
*   You drag the left edge: `i = Math.max(2, i) = Math.max(2, 0) = 2`. 
*   The old R and B are kicked out. The new left edge is chair 2.
*   `maxLength = Math.max(3, 3 - 2 + 1) = 2` (High score remains 3).
*   Chotu updates notebook: `B -> 4` (New safe chair for B is 4).
*   *Current Frame: [G, B] (Chairs 2 to 3)*

**Step 5: j = 4 (Guest Y)**
*   Right edge sees Yellow (Y). Not in notebook.
*   `maxLength = Math.max(3, 4 - 2 + 1) = 3`
*   Chotu writes: `Y -> 5`.
*   *Current Frame: [G, B, Y] (Chairs 2 to 4)*

**Step 6: j = 5 (Guest R) — The "Don't Go Backwards" Magic!**
*   Right edge sees Red (R) at chair 5. 
*   Chotu checks the notebook: *"Boss! We saw R before! The notebook says the safe chair is 1!"* (`map.get('R')` is 1).
*   We calculate the left edge jump: `i = Math.max(map.get('R'), i) = Math.max(1, 2)`.
*   **CRITICAL MOMENT:** Why didn't `i` jump to 1? Because your left edge is *already* at chair 2 (you moved it there to fix the Blue duplicate in Step 4). If you dragged the camera backward to chair 1, you would pull the old kicked-out Blue guest back into the frame and ruin the shot!
*   Because of `Math.max(1, 2)`, `i` stays safely at `2`. The old Red at chair 0 is already out of the frame anyway!
*   `maxLength = Math.max(3, 5 - 2 + 1) = 4` (New high score!).
*   Chotu updates notebook: `R -> 6`.
*   *Current Frame: [G, B, Y, R] (Chairs 2 to 5)*

**Final answer returned: 4.**