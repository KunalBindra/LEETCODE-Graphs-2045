# LEETCODE-Graphs-2045
This solution aims to find the second minimum time to reach the nth node in a graph with given edges, travel time, and traffic signal change intervals. Let's break down the code step by step:

1. **Graph Initialization**:
   ```java
   List<Integer>[] graph = new List[n + 1];
   ```
   This initializes an array of lists to represent the graph. Each list will store the neighboring nodes for each node.

2. **Queue Initialization**:
   ```java
   Queue<int[]> q = new ArrayDeque<>(Arrays.asList(new int[] {1, 0}));
   ```
   This initializes a queue to store nodes along with the time taken to reach them. It starts with node 1 at time 0.

3. **Minimum Time Array Initialization**:
   ```java
   int[][] minTime = new int[n + 1][2];
   Arrays.stream(minTime).forEach(A -> Arrays.fill(A, Integer.MAX_VALUE));
   minTime[1][0] = 0;
   ```
   `minTime[i][0]` keeps track of the first minimum time to reach node `i`, and `minTime[i][1]` keeps track of the second minimum time. Initially, all values are set to `Integer.MAX_VALUE`, except the first node's first minimum time, which is set to 0.

4. **Building the Graph**:
   ```java
   for (int i = 1; i <= n; ++i)
     graph[i] = new ArrayList<>();
   
   for (int[] edge : edges) {
     final int u = edge[0];
     final int v = edge[1];
     graph[u].add(v);
     graph[v].add(u);
   }
   ```
   This builds the graph using the given edges, where `u` and `v` are connected nodes.

5. **Breadth-First Search (BFS)**:
   ```java
   while (!q.isEmpty()) {
     final int i = q.peek()[0];
     final int prevTime = q.poll()[1];
     final int numChangeSignal = prevTime / change;
     final int waitTime = numChangeSignal % 2 == 0 ? 0 : change - prevTime % change;
     final int newTime = prevTime + waitTime + time;
     for (final int j : graph[i])
       if (newTime < minTime[j][0]) {
         minTime[j][0] = newTime;
         q.offer(new int[] {j, newTime});
       } else if (minTime[j][0] < newTime && newTime < minTime[j][1]) {
         if (j == n)
           return newTime;
         minTime[j][1] = newTime;
         q.offer(new int[] {j, newTime});
       }
   }
   ```

   The BFS algorithm processes each node and calculates the time to reach its neighbors. It considers the waiting time if the signal is red when reaching the node. It updates the `minTime` array with the new times and adds the neighboring nodes to the queue if the new time is either the first or second minimum time to reach that node.

6. **Exception Handling**:
   ```java
   throw new IllegalArgumentException();
   ```
   If no valid second minimum time is found, an exception is thrown.

### Dry Run Example

Let's do a dry run for a simple example:

- **Input**:
  - `n = 3`
  - `edges = [[1, 2], [2, 3]]`
  - `time = 2`
  - `change = 3`

- **Graph Representation**:
  ```
  1 -- 2 -- 3
  ```

- **Execution**:
  - Start at node 1 with `prevTime = 0`.
  - From node 1 to node 2:
    - `numChangeSignal = 0 / 3 = 0` (green light)
    - `waitTime = 0`
    - `newTime = 0 + 0 + 2 = 2`
    - Update `minTime[2][0] = 2`
    - Add `[2, 2]` to the queue.
  - From node 2 to node 3:
    - `prevTime = 2`
    - `numChangeSignal = 2 / 3 = 0` (green light)
    - `waitTime = 0`
    - `newTime = 2 + 0 + 2 = 4`
    - Update `minTime[3][0] = 4`
    - Add `[3, 4]` to the queue.
  - Back to node 1 from node 2:
    - `newTime = 2 + 0 + 2 = 4`
    - Update `minTime[1][1] = 4`
    - Add `[1, 4]` to the queue.
  - From node 2 to node 3 again:
    - `prevTime = 4`
    - `numChangeSignal = 4 / 3 = 1` (red light)
    - `waitTime = 3 - 4 % 3 = 2`
    - `newTime = 4 + 2 + 2 = 8`
    - Update `minTime[3][1] = 8`
    - Add `[3, 8]` to the queue.

- **Result**:
  - The second minimum time to reach node 3 is `8`.

This solution ensures that we consider both the first and second minimum times to reach each node and handles the traffic signal change intervals correctly.
