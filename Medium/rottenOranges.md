You are given an m x n grid where each cell can have one of three values:

0 representing an empty cell,
1 representing a fresh orange, or
2 representing a rotten orange.
Every minute, any fresh orange that is 4-directionally adjacent to a rotten orange becomes rotten.

Return the minimum number of minutes that must elapse until no cell has a fresh orange. If this is impossible, return -1.

```java
public int orangesRotting(int[][] grid) {
        if (grid == null || grid.length == 0) return 0;
        
        int rows = grid.length;
        int cols = grid[0].length;
        Queue<int[]> queue = new LinkedList<>();
        int freshCount = 0;
        
        // Step 1: Initialize the queue with all initially rotten oranges
        // and count the total number of fresh oranges.
        for (int r = 0; r < rows; r++) {
            for (int c = 0; c < cols; c++) {
                if (grid[r][c] == 2) {
                    queue.offer(new int[]{r, c});
                } else if (grid[r][c] == 1) {
                    freshCount++;
                }
            }
        }
        
        // If there are no fresh oranges to begin with, 0 minutes have passed.
        if (freshCount == 0) return 0;
        
        int minutes = 0;
        // Direction vectors for moving Up, Down, Left, Right
        int[][] directions = {{-1, 0}, {1, 0}, {0, -1}, {0, 1}};
        
        // Step 2: Begin Multi-Source BFS
        while (!queue.isEmpty()) {
            int size = queue.size();
            boolean rottedInThisLayer = false;
            
            // Process all oranges that became rotten at the current minute
            for (int i = 0; i < size; i++) {
                int[] current = queue.poll();
                int r = current[0];
                int c = current[1];
                
                for (int[] dir : directions) {
                    int nextR = r + dir[0];
                    int nextC = c + dir[1];
                    
                    // Check boundaries and if the adjacent orange is fresh
                    if (nextR >= 0 && nextR < rows && nextC >= 0 && nextC < cols && grid[nextR][nextC] == 1) {
                        grid[nextR][nextC] = 2; // Infect the fresh orange
                        queue.offer(new int[]{nextR, nextC}); // Add it to queue for the next minute
                        freshCount--; // Decrement fresh count
                        rottedInThisLayer = true;
                    }
                }
            }
            
            // Only increment time if we actually spread the rot to new oranges
            if (rottedInThisLayer) {
                minutes++;
            }
        }
        
        // Step 3: If fresh oranges remain, return -1; otherwise return total minutes
        return freshCount == 0 ? minutes : -1;
    }
```

### 1. The Core Concept

The algorithm being used here is called a **Multi-Source Breadth-First Search (BFS)**. 

In plain, non-technical English, this means "starting from multiple places at once, and spreading outward in expanding circles, one step at a time." It doesn't trace one single long, winding path all the way to the end. Instead, it takes one step in *all* directions simultaneously, pauses, and then takes the next step in *all* directions simultaneously. It's an algorithm built for measuring the "ripple effect."

### 2. A Physical Analogy

Imagine a typical Indian Housing Society (like Gokuldham Society) laid out in a square grid of flats. 

- A `2` represents a flat where the Aunty has just received a juicy, viral WhatsApp forward (e.g., "UNESCO declares our National Anthem as the best!"). 
- A `1` represents a flat with an innocent, peaceful Aunty who hasn't checked her phone yet.
- A `0` represents a locked flat or a bachelor who isn't on the society WhatsApp group (immune to the gossip).

Because Aunties can only gossip over the balcony or by knocking on adjacent doors, the rumour only spreads to immediate neighbours (Up, Down, Left, Right). 

Every evening (which represents `1 minute` in the code), every Aunty who *just* learned the rumour that day spreads it to her adjacent innocent neighbours. The neighbours who learn it today will wait until *tomorrow* evening to spread it to their neighbours. The code calculates how many evenings it takes for the entire society to know the rumour. If even one innocent Aunty is completely isolated (maybe surrounded by locked bachelor flats) and never gets the rumour, the algorithm fails and returns `-1`.

### 3. Variable Translation

Let's translate the code's variables into our Indian housing society analogy:

- `grid`: The society's layout plan/map, showing which flat is where.
- `queue`: The **"Active Gossipers List"**. Think of this as a rough notebook where we write down the flat numbers of the Aunties who *just* learned the gossip today and are extremely eager to spread it. 
- `freshCount`: The **"Innocent Count"**. The total number of Aunties who are currently living in peace, unaware of the rumour.
- `minutes`: The number of evenings (or days) that have passed.
- `size` (inside the `while` loop): The batch of Aunties working on spreading the gossip on *this specific evening*. We must process them as a single batch before moving to the next day.
- `directions`: The four physical paths from a balcony—North, South, East, West.

### 4. Translating the Math

Let's break down what the math and logic inside the loops physically represent:

- `for (int[] dir : directions)` and `int nextR = r + dir[0]`: This is the mathematical way of walking to the neighbour's door. Grids work on X and Y coordinates (rows and columns). Adding `1` to the row (`r + 1`) physically means walking one flat South. Subtracting `1` (`r - 1`) means walking one flat North.
- `if (nextR >= 0 && nextR < rows...)`: This is the **society's boundary wall check**. It stops an Aunty from trying to gossip with a brick wall at the edge of the society. We only check flats that actually exist.
- `grid[nextR][nextC] == 1`: The neighbour's door opens! Is the person inside innocent (`1`)? If they are a bachelor (`0`) or already know the rumour (`2`), we don't waste our breath and we move on.
- `grid[nextR][nextC] = 2`: The gossip is delivered! The innocent Aunty is now infected with the rumour. Her status permanently changes to `2`.
- `freshCount--`: We cross out one person from our overall "Innocent Count".
- `minutes++`: The sun sets. One full batch of evening gossiping has finished. We increment the day counter.

### 5. Deep Example Trace

Let's trace a small society map with actual numbers to see how the "middle" stages work.

**Initial Society Map (3x3 grid):**
Row 0: `[ 2, 1, 1 ]`
Row 1: `[ 1, 1, 0 ]`
Row 2: `[ 0, 1, 1 ]`

**Initial Setup (Before any time passes):**
We survey the society (Step 1 in the code).
- `freshCount` = 6 innocent Aunties.
- `queue` (Active Gossipers) = Flat (0,0). Only one Aunty has the rumour to start.
- `minutes` = 0.

**Evening 1 (Minute = 1):**
- We look at our `queue`. There is 1 Aunty in today's batch: (0,0).
- Aunty at (0,0) knocks on her neighbours' doors. She has two valid neighbours: Right (0,1) and Down (1,0).
- Both are `1` (innocent). She tells them the rumour! We change (0,1) and (1,0) to `2`.
- `freshCount` drops from 6 to 4.
- We add the newly infected (0,1) and (1,0) to our `queue` for *tomorrow's* batch.
- *End of Evening 1*. `minutes` becomes 1.
*Society status:*
`[ 2, 2, 1 ]`
`[ 2, 1, 0 ]`
`[ 0, 1, 1 ]`

**Evening 2 (Minute = 2):**
- We look at our `queue`. Today's batch has 2 Aunties: (0,1) and (1,0). They are eager to spread it!
- **Aunty (0,1) goes first:** Her Left neighbour (0,0) already knows (`2`). But her Right neighbour (0,2) and Down neighbour (1,1) are innocent (`1`)! She infects them. They become `2`.
- **Aunty (1,0) goes next:** Her Up neighbour (0,0) already knows. Her Right neighbour (1,1) *just* learned it from the previous Aunty a second ago (so it's a `2` now). Her Down neighbour (2,0) is a bachelor (`0`). She infects nobody new.
- `freshCount` drops from 4 to 2.
- The newly infected (0,2) and (1,1) are added to the `queue` for tomorrow.
- *End of Evening 2*. `minutes` becomes 2.
*Society status:*
`[ 2, 2, 2 ]`
`[ 2, 2, 0 ]`
`[ 0, 1, 1 ]`

**Evening 3 (Minute = 3):**
- `queue` has today's batch: (0,2) and (1,1).
- **Aunty (0,2):** Down is a bachelor (`0`). Left already knows (`2`). No new infections.
- **Aunty (1,1):** Her Down neighbour (2,1) is innocent! She infects them.
- `freshCount` drops from 2 to 1.
- We add (2,1) to the `queue` for tomorrow.
- *End of Evening 3*. `minutes` becomes 3.
*Society status:*
`[ 2, 2, 2 ]`
`[ 2, 2, 0 ]`
`[ 0, 2, 1 ]`

**Evening 4 (Minute = 4):**
- `queue` has 1 Aunty: (2,1).
- She knocks on her Right neighbour (2,2)'s door. It's the last innocent Aunty! She infects her.
- `freshCount` drops from 1 to 0.
- We add (2,2) to the `queue`.
- *End of Evening 4*. `minutes` becomes 4.
*Society status:*
`[ 2, 2, 2 ]`
`[ 2, 2, 0 ]`
`[ 0, 2, 2 ]`

**Evening 5 (Minute = 5 - The code's final check):**
- `queue` has 1 Aunty: (2,2).
- She knocks on her neighbours' doors. Everyone around her either already knows (`2`) or is a bachelor (`0`).
- **NO new Aunties were infected today!** (`rottedInThisLayer = false`).
- Because nobody new was infected, we DO NOT increment the minutes. It was just a wasted evening.
- The `queue` is now empty. The `while` loop finishes.

**Final Result (Step 3 in code):**
- We check `freshCount`. Is it 0? Yes! Every single Aunty knows the rumour.
- We return `minutes`, which is **4**. It took exactly 4 evenings for the entire society to get the gossip!