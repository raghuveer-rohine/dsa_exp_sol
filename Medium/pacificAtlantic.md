```java
 public List<List<Integer>> pacificAtlantic(int[][] heights) {
        List<List<Integer>> result = new ArrayList<>();
        if (heights == null || heights.length == 0 || heights[0].length == 0) {
            return result;
        }

        int rows = heights.length;
        int cols = heights[0].length;

        boolean[][] pacific = new boolean[rows][cols];
        boolean[][] atlantic = new boolean[rows][cols];

        Queue<int[]> pacificQueue = new LinkedList<>();
        Queue<int[]> atlanticQueue = new LinkedList<>();

        // Step 1: Initialize queues with the ocean border cells
        for (int r = 0; r < rows; r++) {
            // Left edge (Pacific)
            pacificQueue.offer(new int[]{r, 0});
            pacific[r][0] = true;
            
            // Right edge (Atlantic)
            atlanticQueue.offer(new int[]{r, cols - 1});
            atlantic[r][cols - 1] = true;
        }

        for (int c = 0; c < cols; c++) {
            // Top edge (Pacific)
            pacificQueue.offer(new int[]{0, c});
            pacific[0][c] = true;
            
            // Bottom edge (Atlantic)
            atlanticQueue.offer(new int[]{rows - 1, c});
            atlantic[rows - 1][c] = true;
        }

        // Step 2: Run BFS for both oceans
        bfs(heights, pacificQueue, pacific);
        bfs(heights, atlanticQueue, atlantic);

        // Step 3: Find cells visited by both oceans
        for (int r = 0; r < rows; r++) {
            for (int c = 0; c < cols; c++) {
                if (pacific[r][c] && atlantic[r][c]) {
                    result.add(Arrays.asList(r, c));
                }
            }
        }

        return result;
    }

    private void bfs(int[][] heights, Queue<int[]> queue, boolean[][] visited) {
        int rows = heights.length;
        int cols = heights[0].length;
        int[][] dirs = {{0, 1}, {0, -1}, {1, 0}, {-1, 0}};

        while (!queue.isEmpty()) {
            int[] curr = queue.poll();
            int r = curr[0];
            int c = curr[1];

            for (int[] d : dirs) {
                int nr = r + d[0];
                int nc = c + d[1];

                // Check bounds and if it's already visited
                if (nr >= 0 && nr < rows && nc >= 0 && nc < cols && !visited[nr][nc]) {
                    // Water can flow from (nr, nc) to (r, c) if heights[nr][nc] >= heights[r][c]
                    if (heights[nr][nc] >= heights[r][c]) {
                        visited[nr][nc] = true;
                        queue.offer(new int[]{nr, nc});
                    }
                }
            }
        }
    }
```

Here is a breakdown of the Pacific Atlantic Water Flow algorithm, designed to build your intuition using a relatable, everyday Indian scenario.

### 1. The Core Concept
The naive way to solve this problem is **Top-Down (Forward Tracking)**: Drop a paper boat at every single cell, let it flow downhill, and see if it eventually hits both oceans. This is incredibly slow because you are repeating the same paths over and over again.

The genius of this algorithm is **Bottom-Up (Reverse Tracking)**. Instead of asking, *"Where does water from this cell flow down to?"* we start at the oceans and ask, *"Which cells can flow down to me?"* 

By starting at the finish line and walking **backwards and upwards**, we only need to sweep the grid twice (once for each ocean). Any cell that gets successfully reached by the upward sweep of *both* oceans is our answer.

---

### 2. A Physical Analogy: The BMC Monsoon Drainage
Imagine a hilly neighborhood in Mumbai (like Ghatkopar or Malabar Hill) during the heavy July monsoons. 
* There are two massive storm-water gutters. 
* The **Western & Northern Gutter** represents the Pacific Ocean.
* The **Eastern & Southern Gutter** represents the Atlantic Ocean.
* Water naturally flows from higher elevation to lower elevation (or stays flat). 

The BMC (Brihanmumbai Municipal Corporation) wants to know: *Which specific houses are situated on peaks or ridges such that their rainwater eventually splits and floods into BOTH gutters?*

Instead of dropping colored water from every single house to see where it goes, the smart BMC Inspector hires two teams of workers: **Team Blue** and **Team Red**.
* **Team Blue** stands inside the Western/Northern Gutter (Pacific).
* **Team Red** stands inside the Eastern/Southern Gutter (Atlantic).

Their instruction is simple: **Walk UP the slope.** 
A worker can step from their current spot to a neighboring house **ONLY IF** the neighbor's house is at the *same height or higher*. Why? Because if the neighbor is higher up, it means rainwater would naturally flow *down* from that neighbor into the worker's current spot!

Team Blue marks every house they can reach with a **Blue Tick** (✓). Team Red marks every house they can reach with a **Red Tick** (✓). At the end of the day, any house with *both* ticks is added to the final report.

---

### 3. Variable Translation
Let's translate the Java syntax into the BMC Inspector's tools:

* **`heights` (int[][])**: The topographical map of the neighborhood showing the elevation (in meters) of every house.
* **`pacific` (boolean[][])**: The **"Blue Tick" Register**. If `pacific[r][c]` is `true`, it means Team Blue successfully hiked up to this house from the Pacific gutter.
* **`atlantic` (boolean[][])**: The **"Red Tick" Register**.
* **`pacificQueue` / `atlanticQueue`**: The actual lineup of BMC workers. When a worker discovers a valid higher neighbor, they radio basecamp to add a new worker to the back of the queue. This new worker will go stand at that newly discovered house to continue the uphill search.
* **`dirs`**: The 4 directions a worker can look: checking the *Padosi* (neighbor) to the North, South, East, and West.

---

### 4. Translating the Math
Let's look inside the `bfs` (Breadth-First Search) method. This is what one single worker is doing when it's their turn:

* **`int nr = r + d[0]; int nc = c + d[1];`**
  * *English:* The worker standing at house `(r, c)` takes one step to look at the Padosi's house `(nr, nc)`.
* **`nr >= 0 && nr < rows && nc >= 0 && nc < cols`**
  * *English:* The boundary check. The worker is making sure they don't accidentally walk out of the neighborhood grid into a different suburb.
* **`!visited[nr][nc]`**
  * *English:* Has my team already put a tick mark on this Padosi's house? If yes, skip it. No need to do *double mehnat* (waste time doing the same work twice).
* **`heights[nr][nc] >= heights[r][c]`**
  * *English:* **The Golden Rule of Reverse Flow.** The worker is at `(r, c)` and looks at the Padosi at `(nr, nc)`. The worker asks: *"Bhaiyya, is your house higher than or equal to mine?"* 
  * If the Padosi's height is `>=` the worker's height, it guarantees that during the rains, water will flow from the Padosi down to the worker. 
  * *Math.min/Math.max aren't used here, but this `>=` operator is the entire engine of the algorithm.* It mechanically reverses gravity.

---

### 5. Deep Example Trace
Let’s take a tiny 3x3 neighborhood grid.
```text
[1, 2, 2]  <- Row 0
[3, 2, 3]  <- Row 1
[2, 4, 1]  <- Row 2
```
* Pacific Gutter is the Top & Left edge.
* Atlantic Gutter is the Bottom & Right edge.

Let's focus just on **Team Blue (Pacific)** doing their upward sweep.

**Step 1: The Initial Lineup**
Team Blue workers position themselves at the borders. The `pacificQueue` starts with coordinates: `(0,0)=1`, `(0,1)=2`, `(0,2)=2`, `(1,0)=3`, `(2,0)=2`.
*All these border houses immediately get a Blue Tick in the register.*

**Step 2: A Flat Walk (Early Stage)**
* The worker at **(0,1) [height 2]** gets their turn. 
* They look South to the Padosi at **(1,1) [height 2]**.
* *Math check:* `heights[1][1] (2) >= heights[0][1] (2)`. This is `true` (2 is equal to 2, flat ground, water flows).
* *Action:* The worker steps to (1,1), gives it a Blue Tick, and adds (1,1) to the queue to be explored later.

**Step 3: A Blocked Path (Checking a ditch)**
* The worker at **(1,0) [height 3]** gets their turn.
* They look East to the Padosi at **(1,1) [height 2]**.
* *Math check:* `heights[1][1] (2) >= heights[1,0] (3)`. This is **FALSE**! 
* *Physical meaning:* The Padosi is in a ditch compared to the worker. Water in a ditch cannot flow *up* to height 3. The worker ignores this Padosi.

**Step 4: Climbing the Mountain (Later Stage)**
* Remember the worker we added to the queue in Step 2? The one at **(1,1) [height 2]**? They finally get their turn.
* They look South to the Padosi at **(2,1) [height 4]**.
* *Math check:* `heights[2][1] (4) >= heights[1][1] (2)`. This is **TRUE**! (4 is much higher than 2).
* *Physical meaning:* Because house (2,1) is a tall hill, its rainwater will beautifully wash down to our worker at (1,1) (and from there, eventually to the Pacific gutter).
* *Action:* The worker gives (2,1) a Blue Tick and adds it to the queue. 

By the end of the day, Team Blue has marked a specific set of houses. Team Red does the exact same process from their side. Finally, the code loops through the entire register: `if (pacific[r][c] && atlantic[r][c])` — if a house has both a Blue and Red tick, it goes into our final `result` list!