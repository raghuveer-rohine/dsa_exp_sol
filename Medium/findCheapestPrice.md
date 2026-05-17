```java
 public int findCheapestPrice(int n, int[][] flights, int src, int dst, int k) {
        // Step 1: Build the adjacency list for the graph
        List<List<int[]>> adj = new ArrayList<>();
        for (int i = 0; i < n; i++) {
            adj.add(new ArrayList<>());
        }
        for (int[] flight : flights) {
            adj.get(flight[0]).add(new int[]{flight[1], flight[2]}); // {to, price}
        }

        // Step 2: Array to store the minimum cost to reach each city
        int[] minCost = new int[n];
        Arrays.fill(minCost, Integer.MAX_VALUE);
        minCost[src] = 0;

        // Queue stores elements as {current_city, cost_to_reach_this_city}
        Queue<int[]> queue = new LinkedList<>();
        queue.offer(new int[]{src, 0});

        int stops = 0;

        // Step 3: Standard BFS limited to K + 1 levels (edges)
        while (!queue.isEmpty() && stops <= k) {
            int size = queue.size();
            // Create a snapshot clone of the costs array for this level 
            // to avoid using updated costs within the same level iteration
            int[] nextCosts = Arrays.copyOf(minCost, n);

            for (int i = 0; i < size; i++) {
                int[] curr = queue.poll();
                int u = curr[0];
                int costSoFar = curr[1];

                // If the recorded global cost is already better than this path, skip it
                if (costSoFar > minCost[u]) continue;

                // Explore neighbors
                for (int[] neighbor : adj.get(u)) {
                    int v = neighbor[0];
                    int price = neighbor[1];

                    // Relaxation step
                    if (costSoFar + price < nextCosts[v]) {
                        nextCosts[v] = costSoFar + price;
                        queue.offer(new int[]{v, nextCosts[v]});
                    }
                }
            }
            // Update the main cost tracker with the progress made at this level
            minCost = nextCosts;
            stops++;
        }

        return minCost[dst] == Integer.MAX_VALUE ? -1 : minCost[dst];
    }
```

### 1. The Core Concept

At a high level, this code uses an algorithm called **Breadth-First Search (BFS) with Level Constraints**. Think of it as exploring a map in expanding "waves" or "rounds." 

Instead of randomly trying to find a path from the source to the destination, we explore systematically: first, we find everywhere we can reach directly (0 stops). Then, from those places, we find everywhere we can reach with 1 stop, and so on. At each step, we rigorously track the absolute cheapest cost to reach every city *so far*. We stop expanding our waves the moment we hit our maximum allowed stops ($K$). 

### 2. A Physical Analogy

Imagine you are standing at the New Delhi Railway Station and you need to reach Chennai. Direct trains are too expensive, so you are willing to change trains at junction stations like Bhopal or Nagpur. However, your strict Indian mother gives you an ultimatum: *"Beta, journey lamba hai, you can change trains a maximum of $K$ times, not more than that!"*

You go to a smart **IRCTC Enquiry Clerk**. Instead of looking at a confusing web of million train routes, the clerk works in "Rounds" (levels):
- **Round 0 (No changes):** Which cities can you reach directly from Delhi, and for how many ₹?
- **Round 1 (1 train change):** From those cities, where can you go next, and what is the *new total ₹*?

The clerk maintains a **Master Whiteboard** showing the cheapest known ticket to any city. But here is the genius part: during an active round, the clerk does *not* write directly on the Whiteboard. Instead, they make a **Photocopy (rough sheet)** of the Whiteboard. 

Why? Because if a passenger finds a super-cheap ticket to Nagpur that requires *3 changes*, the clerk doesn’t want that cheap price to accidentally mix with the current calculations for people who are strictly looking at *1-change* routes. The Master Whiteboard is only updated *after* everyone in the current round finishes their enquiry. 

### 3. Variable Translation

Let's look at the clerk's desk and map the variables:

- **`adj` (Adjacency List):** The **IRCTC Master Timetable**. It tells you: "From station A, you can go to station B for ₹X".
- **`minCost` Array:** The **Master Whiteboard**. It stores the absolute lowest ₹ found to reach each station *so far*, locked in at the end of every round.
- **`Integer.MAX_VALUE`:** **Infinity!** Before the clerk starts calculating, they assume every city costs ₹99,99,999 to reach (like hiring a private helicopter) because no train routes have been found yet. 
- **`queue`:** The **Enquiry Waiting Line**. People standing in this line hold a token that says: *"I am currently at Station $U$, and I have spent ₹$Y$ to reach here."*
- **`stops`:** The **Round Counter**. It tracks how many times you have changed trains so far.
- **`nextCosts` Array:** The **Photocopy (Rough Sheet)**. Used during the current round to scribble down new "Sasta Jugaad" (cheap hack) prices without ruining the Master Whiteboard.

### 4. Translating the Math

Let's break down the logic the clerk uses inside the loop:

- **`minCost[src] = 0;`** 
  Standing at your home station (Delhi) costs ₹0. You haven't bought a ticket yet!

- **`if (costSoFar > minCost[u]) continue;`** *(The "Arey Bhai" Check)*
  A passenger steps up to the counter and says, *"I reached Bhopal and I've spent ₹5000!"* The clerk looks at the Whiteboard and sees someone else already found a way to Bhopal for just ₹2000. The clerk gets annoyed: *"Arey bhai, your route is a scam. You already overpaid. I won't even look at where you want to go next. Get out of the line!"*

- **`costSoFar + price < nextCosts[v]`** *(Finding a "Sasta Jugaad")*
  Let's translate the math physically:
  - `costSoFar` = ₹2000 (Money you spent to reach Bhopal).
  - `price` = ₹1500 (The timetable says Bhopal to Nagpur costs ₹1500).
  - `costSoFar + price` = ₹3500 (Your total journey cost to Nagpur).
  - `nextCosts[v]` = The cheapest known fare to Nagpur on the rough photocopy (let's say a direct train was ₹5000).
  
  Because **3500 < 5000**, you just found a cheaper Jugaad! The clerk scratches out 5000 on the photocopy, writes down **3500**, and gives you a token to stand in the line for the next round (`queue.offer`).

- **`stops++`** 
  The current round is over. The clerk stamps the papers, copies the rough sheet back to the Master Whiteboard (`minCost = nextCosts`), and announces: *"All 1-change routes are processed. Now let's look at 2-change routes!"*

### 5. Deep Example Trace

Let's trace a real scenario with actual ₹ numbers.
- **Cities:** 0 (Delhi), 1 (Bhopal), 2 (Nagpur), 3 (Chennai)
- **$K$ = 1** (Mother's rule: Maximum 1 train change allowed)

**IRCTC Timetable (`adj`):**
- Delhi (0) $\rightarrow$ Bhopal (1): **₹1000**
- Bhopal (1) $\rightarrow$ Nagpur (2): **₹1000**
- Nagpur (2) $\rightarrow$ Chennai (3): **₹1000**
- Delhi (0) $\rightarrow$ Nagpur (2): **₹5000**
- Bhopal (1) $\rightarrow$ Chennai (3): **₹4000**

**Initial State:** 
`minCost` (Whiteboard): `[0, INF, INF, INF]`
`queue` (Line): `[{Delhi, ₹0}]`

---

#### `stops = 0` (Direct Trains - 0 Changes)
- **Photocopy (`nextCosts`)**: `[0, INF, INF, INF]`
- **Clerk talks to Passenger 1:** At Delhi (0), spent ₹0.
  - Checks timetable: Direct train to Bhopal (1) is ₹1000. Total = ₹1000. Is 1000 < INF? Yes! Update photocopy for Bhopal: `1000`. Add to queue.
  - Checks timetable: Direct train to Nagpur (2) is ₹5000. Total = ₹5000. Is 5000 < INF? Yes! Update photocopy for Nagpur: `5000`. Add to queue.
- **Round Ends:** Whiteboard becomes `[0, 1000, 5000, INF]`. 
  - `queue` is now: `[{Bhopal, 1000}, {Nagpur, 5000}]`. 
  - `stops` becomes **1**.

---

#### `stops = 1` (1 Train Change allowed)
- **Photocopy (`nextCosts`)**: `[0, 1000, 5000, INF]`
- **Clerk talks to Passenger 1:** At Bhopal (1), spent ₹1000.
  - Timetable: Train to Nagpur (2) is ₹1000. Total = ₹2000. 
    *Is ₹2000 < Photocopy's Nagpur price (₹5000)?* **YES!** We found a Sasta Jugaad! Update photocopy for Nagpur: `2000`. Add `{Nagpur, 2000}` to next round's queue.
  - Timetable: Train to Chennai (3) is ₹4000. Total = ₹5000. 
    *Is ₹5000 < Photocopy's Chennai price (INF)?* **YES!** Update photocopy for Chennai: `5000`. Add `{Chennai, 5000}` to queue.
    
- **Clerk talks to Passenger 2:** At Nagpur (2), spent ₹5000. *(Remember, this guy took the expensive direct train in Round 0).*
  - Timetable: Train to Chennai (3) is ₹1000. Total = ₹6000.
    *Is ₹6000 < Photocopy's Chennai price (₹5000, which Passenger 1 just updated)?* **NO!** ₹6000 is too expensive. The clerk ignores this route. *(This perfectly shows why we check `nextCosts[v]`!)*
    
- **Round Ends:** Whiteboard becomes `[0, 1000, 2000, 5000]`. 
  - `stops` becomes **2**.

---

#### `stops = 2` (End of the line)
The `while` loop checks: `stops <= k`. Since **2 is NOT $\le$ 1**, the loop breaks. The IRCTC rule strictly kicks in—no more train changes allowed!

**Final Answer:** We look at the Master Whiteboard for Chennai (`minCost[3]`). The value is **5000**. The absolute cheapest way to reach Chennai with a maximum of 1 change is ₹5000. You buy the ticket and pack your bags!