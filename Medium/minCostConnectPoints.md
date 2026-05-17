```java
 public int minCostConnectPoints(int[][] points) {

int n = points.length;
        int minCost = 0;
        int edgesUsed = 0;
        
        // mstDist[i] stores the minimum distance from any node in the MST to node i
        int[] mstDist = new int[n];
        for (int i = 1; i < n; i++) {
            mstDist[i] = Integer.MAX_VALUE;
        }
        
        // track which nodes are already included in our MST
        boolean[] inMST = new boolean[n];
        
        // We start with node 0
        int currNode = 0;
        
        while (edgesUsed < n - 1) {
            inMST[currNode] = true;
            edgesUsed++;
            
            int nextNode = -1;
            int nextMinDist = Integer.MAX_VALUE;
            
            for (int i = 0; i < n; i++) {
                if (!inMST[i]) {
                    // Calculate Manhattan distance between currNode and i
                    int dist = Math.abs(points[currNode][0] - points[i][0]) 
                             + Math.abs(points[currNode][1] - points[i][1]);
                    
                    // Update the minimum distance to node i if this new edge is shorter
                    if (dist < mstDist[i]) {
                        mstDist[i] = dist;
                    }
                    
                    // Find the next closest node to add to the MST
                    if (mstDist[i] < nextMinDist) {
                        nextMinDist = mstDist[i];
                        nextNode = i;
                    }
                }
            }
            
            minCost += nextMinDist;
            currNode = nextNode;
        }
        
        return minCost;
    }
```

### 1. The Core Concept

The algorithm used here is a famous pattern called **Prim's Algorithm**. Its goal is to find the "Minimum Spanning Tree" of a graph. In plain English: imagine you have a bunch of scattered dots and you want to connect all of them together into one single network, but you want to use the absolute minimum amount of ink (or wire/cost) possible. 

The strategy is "Greedy". You start at one dot. You find the closest unconnected dot and connect it. Now you have a connected "blob" of two dots. You look at all the dots in your blob, find the absolute closest unconnected dot to *any* of them, and pull it into your blob. You keep expanding this blob, one closest dot at a time, until everything is connected. 

### 2. A Physical Analogy

Imagine you are a contractor working for Jio, and your job is to lay optical fiber cables to connect a cluster of rural villages in Punjab to the internet. 

The government rule is strict: **Every village must be connected to the main network**, but to maximize profit, you must spend the **absolute minimum on cables**. 

You start your base camp at the Sarpanch's village. First, you look for the village nearest to the Sarpanch's house and lay a cable there. Now, your active Jio network covers *two* villages. 

For the next village, you don't just measure distances from the Sarpanch's house anymore. You tell your surveyor: *"Look at both villages in our network. Find me the closest unconnected village to EITHER of them."* You lay a cable to that third village. You keep repeating this—growing your connected "Jio network blob"—and always grabbing the easiest, cheapest next village until every single village has internet.

### 3. Variable Translation

Let's peek into the contractor's toolkit:

*   **`points`**: The GPS map showing the exact coordinates of every village.
*   **`minCost`**: Your total cable bill (in ₹). We keep adding to this every time we lay a new wire.
*   **`edgesUsed`**: The number of cables laid. Mathematically, to connect `N` villages without making useless loops, you need exactly `N - 1` cables. 
*   **`mstDist` array**: This is your Munshi's (accountant's) **Rough Notebook**. For every unconnected village, the Munshi writes down the *cheapest known quote* to connect it to our existing network. Initially, because we haven't surveyed anything, he writes `Integer.MAX_VALUE` (an infinitely high price).
*   **`inMST` array**: The **Checklist**. If `inMST[i]` is true, it means "Jio Fiber is active in Village `i`". We don't need to connect it again.
*   **`currNode`**: The village you *just* connected. You are standing here right now, checking if being in this new village gives you a cheaper shortcut to any of the remaining unconnected villages.
*   **`nextMinDist` & `nextNode`**: After the Munshi checks all quotes for the day, these variables store the winning bid—the absolute cheapest cable cost, and the specific village it goes to.

### 4. Translating the Math

*   **`edgesUsed < n - 1`**: The stopping condition. If you have 5 villages, you need exactly 4 cables to string them all together. Once you've laid 4 cables, the job is done. The loop stops.
*   **`Math.abs(x1 - x2) + Math.abs(y1 - y2)`**: Calculating the "Manhattan Distance". When laying cables in India, you usually dig along farm borders and grid-like village roads. You can't lay a cable diagonally across someone's house (like a flying crow). You must go strictly horizontal (East/West) and then vertical (North/South). The `Math.abs` ensures distances are always positive (you can't buy negative kilometers of cable).
*   **`if (dist < mstDist[i]) { mstDist[i] = dist; }`**: This is the core magic! You are standing in your newly connected village (`currNode`). You measure the distance to an unconnected village `i`. You look at the Munshi's notebook (`mstDist[i]`). If your *new* measurement is CHEAPER than the old quote written in the notebook, you scratch out the old quote and write down the new, cheaper one. (This is a manual way of writing `Math.min`).
*   **`if (mstDist[i] < nextMinDist)`**: After the notebook is updated, you scan the entire list. You are searching for the absolute lowest number. That village becomes your target for tomorrow's digging.

### 5. Deep Example Trace

Let's assume cable costs ₹1 per unit of distance. We have 4 villages:
*   **Village 0 (Rampur):** coordinates (0, 0)
*   **Village 1 (Sitapur):** coordinates (2, 2)
*   **Village 2 (Laxmanpur):** coordinates (3, 10)
*   **Village 3 (Bharatpur):** coordinates (5, 2)

**Initial State:**
Budget (`minCost`) = ₹0.
Checklist (`inMST`) = [No, No, No, No].
Munshi's Notebook (`mstDist`) = [₹∞, ₹∞, ₹∞, ₹∞].
Current Location (`currNode`) = 0 (Rampur).

---
**Iteration 1:**
We connect Rampur. Checklist: `[Yes, No, No, No]`.
We stand at Rampur and measure distances to everyone else to update the notebook:
*   To Sitapur: |0-2| + |0-2| = 4. *₹4 is cheaper than ₹∞. Notebook updated to ₹4.*
*   To Laxmanpur: |0-3| + |0-10| = 13. *Notebook updated to ₹13.*
*   To Bharatpur: |0-5| + |0-2| = 7. *Notebook updated to ₹7.*

*Notebook reads:* Sitapur: ₹4, Laxmanpur: ₹13, Bharatpur: ₹7.
*Decision:* Sitapur is the cheapest (₹4). 
*Action:* Lay cable to Sitapur. Budget = ₹4. Move to Sitapur.

---
**Iteration 2 (The Crucial Step):**
We connect Sitapur. Checklist: `[Yes, Yes, No, No]`.
We are now standing at Sitapur. Can we find a shortcut from here to the remaining villages? Let's measure:
*   To Laxmanpur: |2-3| + |2-10| = 9. The notebook says Laxmanpur costs ₹13 (from Rampur). But from Sitapur, it only costs ₹9! *Scratch out ₹13. Update notebook to ₹9.*
*   To Bharatpur: |2-5| + |2-2| = 3. The notebook says Bharatpur costs ₹7 (from Rampur). But from Sitapur, it only costs ₹3! *Scratch out ₹7. Update notebook to ₹3.*

*Notebook reads:* Laxmanpur: ₹9, Bharatpur: ₹3.
*Decision:* Bharatpur is the cheapest (₹3).
*Action:* Lay cable to Bharatpur. Budget = ₹4 + ₹3 = ₹7. Move to Bharatpur.

---
**Iteration 3:**
We connect Bharatpur. Checklist: `[Yes, Yes, No, Yes]`.
We stand at Bharatpur. Let's check the only unconnected village left: Laxmanpur.
*   To Laxmanpur: |5-3| + |2-10| = 10. The notebook says we can connect Laxmanpur for ₹9 (via Sitapur). From Bharatpur, it would cost ₹10. 
*   *Action:* ₹10 is NOT cheaper than ₹9. The Munshi does NOT update the notebook. We keep the ₹9 route.

*Notebook reads:* Laxmanpur: ₹9.
*Decision:* Laxmanpur is the cheapest (and only) option left.
*Action:* Lay cable to Laxmanpur. Budget = ₹7 + ₹9 = ₹16. Move to Laxmanpur.

---
**End:**
We have laid 3 cables for 4 villages. `edgesUsed` is now 3, which is not `< 4 - 1`. The loop ends. 
The total minimum cost to wire all the villages is **₹16**.