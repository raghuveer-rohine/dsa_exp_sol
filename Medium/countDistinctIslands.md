Given a boolean 2D matrix grid of size n * m. You have to find the number of distinct islands where a group of connected 1s (horizontally or vertically) forms an island. Two islands are considered to be distinct if and only if one island is not equal to another (not rotated or reflected).

```java
public int countDistinctIslands(int[][] grid) {
        if (grid == null || grid.length == 0) return 0;
        
        int n = grid.length;
        int m = grid[0].length;
        
        // HashSet to store the unique shapes of islands
        Set<List<String>> distinctIslands = new HashSet<>();
        
        // Traverse the entire grid
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < m; j++) {
                // When an unvisited land cell is found, it's a new island
                if (grid[i][j] == 1) {
                    List<String> islandShape = new ArrayList<>();
                    // i and j act as the base coordinates for this specific island
                    dfs(grid, i, j, i, j, islandShape);
                    distinctIslands.add(islandShape);
                }
            }
        }
        
        return distinctIslands.size();
    }
    
    private void dfs(int[][] grid, int r, int c, int baseR, int baseC, List<String> islandShape) {
        int n = grid.length;
        int m = grid[0].length;
        
        // Base boundary conditions and check if it's land
        if (r < 0 || r >= n || c < 0 || c >= m || grid[r][c] == 0) {
            return;
        }
        
        // Mark as visited by flipping to 0 (In-place optimization)
        grid[r][c] = 0;
        
        // Calculate relative coordinate from the island's base and add to shape list
        islandShape.add((r - baseR) + "," + (c - baseC));
        
        // Explore all 4 standard directions systematically
        dfs(grid, r - 1, c, baseR, baseC, islandShape); // Up
        dfs(grid, r + 1, c, baseR, baseC, islandShape); // Down
        dfs(grid, r, c - 1, baseR, baseC, islandShape); // Left
        dfs(grid, r, c + 1, baseR, baseC, islandShape); // Right
    }
```

Edited rottenOranges.md
Edited prompt.md
Viewed prompt.md:1-11

### 1. The Core Concept

The algorithm uses a pattern called **Depth-First Search (DFS) for Connected Components**. 

In plain English, the code is trying to find separated clusters of land (islands) and then compare their physical shapes. Because the same shape can exist anywhere on the map (top-left, bottom-right), we cannot use their absolute map coordinates to compare them. Instead, we translate every island into a list of "relative steps" from its starting point. We then throw these translated shapes into a mathematical `Set` (which automatically deletes any duplicates) to count how many unique shapes exist.

### 2. A Physical Analogy

Imagine the morning of Diwali. Your large courtyard is represented by the grid.
- `1` represents a dot of white Rangoli powder on the floor.
- `0` represents the empty floor.

A continuous group of connected dots (touching Up, Down, Left, or Right) forms a single, complete Rangoli design. 
Your task is to find out: **How many unique stencils (shapes) were used to draw all these Rangolis?**

If someone drew the exact same L-shaped Rangoli near the Tulsi plant and another identical one near the main gate, they look the same, so they should only count as 1 unique stencil. 

To figure this out, whenever you spot a new Rangoli, you stand at its very first dot (the "Base Dot"). Then, you trace the rest of the Rangoli by writing down directions *from that Base Dot* (e.g., "This connected dot is 1 step down and 1 step right from where I started"). You save this list of directions in a master catalogue. If you trace another Rangoli later and it generates the exact same list of directions, you know it's a duplicate stencil!

### 3. Variable Translation

Let's translate the variables to our Diwali courtyard:

- `grid`: The entire courtyard floor.
- `distinctIslands` (HashSet): The **"Master Stencil Catalogue"**. A HashSet is a special list that automatically rejects duplicate entries, so it naturally keeps only unique designs.
- `islandShape` (List): The **"Direction List"** for the *current* Rangoli we are tracing. We build this list step-by-step.
- `i, j` (in the main loop): Our eyes scanning the courtyard row by row, looking for the first dot of a new, unvisited Rangoli.
- `r, c` (in `dfs`): Our physical hand moving across the connected dots to trace the shape of the current Rangoli.
- `baseR, baseC`: The **"Starting Dot"** of the current Rangoli. Every other dot's location will be measured relative to this anchor point.

### 4. Translating the Math

Let's break down the mathematical formulas inside the `dfs` function:

- `grid[r][c] = 0`: Once we trace a Rangoli dot, we literally **smudge it off the floor** (change it to `0`). This is a brilliant trick to prevent us from accidentally walking backward in circles and counting the same dot twice.
- `(r - baseR)` and `(c - baseC)`: This is the core magic! It translates absolute map coordinates into **relative distance**.
  - Suppose Rangoli A's Starting Dot is at row 2, col 2 (`baseR=2, baseC=2`). You trace a connected dot at row 3, col 2 (`r=3, c=2`). The math does: `(3 - 2)` and `(2 - 2)` = `1` and `0`. Physically, this means "This dot is 1 step Down and 0 steps Right from the start."
  - Suppose Rangoli B's Starting Dot is far away at row 8, col 8. You trace a connected dot at row 9, col 8. The math does: `(9 - 8)` and `(8 - 8)` = `1` and `0`.
  - Because both calculations resulted in `"1,0"`, the computer realizes these two distant dots form the exact same pattern!
- `dfs(grid, r - 1, c...)`, `dfs(grid, r + 1, c...)`: This is us physically walking one step Up, Down, Left, and Right from the current dot to look for more connected Rangoli powder.

### 5. Deep Example Trace

Let's trace a courtyard with 2 identical L-shaped Rangolis drawn in different corners.

**Courtyard Map:**
Row 0: `[ 1, 1, 0, 0 ]`
Row 1: `[ 1, 0, 0, 0 ]`
Row 2: `[ 0, 0, 1, 1 ]`
Row 3: `[ 0, 0, 1, 0 ]`

**Scanning starts...**
We scan row 0 and find our first `1` at `(0,0)`. This is our first Rangoli!
`baseR = 0, baseC = 0`.
We create an empty `islandShape` list.

**Tracing Rangoli 1 (Top-Left):**
1. Hand is at `(0,0)`. We smudge it (`grid[0][0] = 0`).
   Math: `(0-0), (0-0)` -> `"0,0"`. We add `"0,0"` to the list.
2. We walk **Down** to `(1,0)`. It's a `1`! We smudge it.
   Math: `(1-0), (0-0)` -> `"1,0"`. We add `"1,0"` to the list.
   *(Walking around (1,0) finds nothing else, so we step back).*
3. Back at `(0,0)`, we walk **Right** to `(0,1)`. It's a `1`! We smudge it.
   Math: `(0-0), (1-0)` -> `"0,1"`. We add `"0,1"` to the list.
- *Rangoli 1 finished.*
- `islandShape` is exactly: `["0,0", "1,0", "0,1"]`.
- We add this shape to `distinctIslands` (our Catalogue). Catalogue size is now **1**.

**Current Courtyard map (smudged):**
Row 0: `[ 0, 0, 0, 0 ]` *(smudged)*
Row 1: `[ 0, 0, 0, 0 ]` *(smudged)*
Row 2: `[ 0, 0, 1, 1 ]`
Row 3: `[ 0, 0, 1, 0 ]`

**Scanning continues...**
We ignore all the `0`s until we hit row 2, col 2. We found a new Rangoli!
`baseR = 2, baseC = 2`.
We create a fresh `islandShape` list.

**Tracing Rangoli 2 (Bottom-Right):**
1. Hand is at `(2,2)`. We smudge it.
   Math: `(2-2), (2-2)` -> `"0,0"`. Add to list.
2. We walk **Down** to `(3,2)`. It's a `1`! We smudge it.
   Math: `(3-2), (2-2)` -> `"1,0"`. Add to list.
3. Back at `(2,2)`, we walk **Right** to `(2,3)`. It's a `1`! We smudge it.
   Math: `(2-2), (3-2)` -> `"0,1"`. Add to list.
- *Rangoli 2 finished.*
- `islandShape` is exactly: `["0,0", "1,0", "0,1"]`.

**The Magic Moment:**
We try to add Rangoli 2's shape `["0,0", "1,0", "0,1"]` to the `distinctIslands` Catalogue. 
But wait! The Catalogue (HashSet) says, "I already have this exact string of directions! This is a duplicate stencil!" 
Because it's a HashSet, it simply ignores the duplicate. The Catalogue size remains **1**.

**Final Result:**
The scanning finishes. We return `distinctIslands.size()`, which is **1**. Even though there were two physical Rangolis in the courtyard, the algorithm successfully figured out that they were made from 1 distinct stencil!