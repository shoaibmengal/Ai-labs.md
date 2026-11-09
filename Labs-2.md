# Lab: Uninformed Search — BFS and DFS on a 2D Maze

**Topic:** Uninformed Search  
**Objective:** Implement and compare Breadth-First Search (BFS) and Depth-First Search (DFS) on the same 2D maze, then analyze path length, frontier size, and algorithm behavior.

---

## 1. Introduction

In this lab, we use a maze represented as a 2D grid:

- `0` = open cell
- `1` = wall

The agent starts at `start` and tries to reach `goal`.

This lab helps us understand two classical uninformed search strategies:

- BFS: explores nodes level by level
- DFS: explores nodes depth first

These algorithms differ in:
- the order in which they explore states
- memory usage
- shortest-path guarantees

---

## 2. The Maze

```python
maze = [
    [0, 0, 0, 0, 0],
    [0, 1, 1, 1, 0],
    [0, 0, 0, 1, 0],
    [0, 1, 0, 0, 0],
    [0, 1, 1, 1, 0]
]

start = (0, 0)
goal = (4, 4)

for row in maze:
    print(row)
```

### Output
```text
[0, 0, 0, 0, 0]
[0, 1, 1, 1, 0]
[0, 0, 0, 1, 0]
[0, 1, 0, 0, 0]
[0, 1, 1, 1, 0]
```

---

## 3. Movements

The agent can move in four directions:

```python
directions = [
    (-1, 0),   # up
    (1, 0),    # down
    (0, -1),   # left
    (0, 1)     # right
]
```

These represent:
- `(-1, 0)` = up
- `(1, 0)` = down
- `(0, -1)` = left
- `(0, 1)` = right

---

## 4. Valid Move Check

A move is valid only if:
- it stays inside the maze
- it does not go through a wall

```python
def valid_move(maze, row, col):
    rows = len(maze)
    cols = len(maze[0])
    return (
        0 <= row < rows
        and 0 <= col < cols
        and maze[row][col] == 0
    )

# Quick checks
print(valid_move(maze, 0, 0))   # True
print(valid_move(maze, 1, 1))   # False (wall)
print(valid_move(maze, -1, 0))  # False (outside maze)
```

### Output
```text
True
False
False
```

---

## 5. Breadth-First Search (BFS)

BFS uses a queue (FIFO: First In, First Out).  
The oldest discovered node is expanded first.

This makes BFS explore in layers, which guarantees the shortest path in an unweighted maze.

```python
from collections import deque

def bfs(maze, start, goal):
    frontier = deque([(start, [start])])
    visited = {start}
    max_frontier = 1

    while frontier:
        max_frontier = max(max_frontier, len(frontier))

        current, path = frontier.popleft()   # FIFO

        if current == goal:
            return path, max_frontier

        row, col = current
        for dr, dc in directions:
            nr, nc = row + dr, col + dc
            neighbor = (nr, nc)

            if valid_move(maze, nr, nc) and neighbor not in visited:
                visited.add(neighbor)
                frontier.append((neighbor, path + [neighbor]))

    return None, max_frontier
```

### Run BFS
```python
bfs_path, bfs_frontier = bfs(maze, start, goal)

print("BFS path:", bfs_path)
print("BFS path length:", len(bfs_path) - 1)
print("BFS maximum frontier:", bfs_frontier)
```

### Sample Output
```text
BFS path: [(0, 0), (0, 1), (0, 2), (0, 3), (0, 4), (1, 4), (2, 4), (3, 4), (4, 4)]
BFS path length: 8
BFS maximum frontier: 3
```

### Why `len(path) - 1`?
A path like:

```text
Start → A → B → Goal
```

has 4 states but only 3 moves.  
Because each move costs 1, we compute:

```python
len(path) - 1
```

This gives the actual path length/cost.

---

## 6. Depth-First Search (DFS)

DFS uses a stack (LIFO: Last In, First Out).  
The most recently discovered node is expanded first.

This makes DFS go as deep as possible before backtracking.

```python
def dfs(maze, start, goal):
    frontier = [(start, [start])]   # stack
    visited = {start}
    max_frontier = 1

    while frontier:
        max_frontier = max(max_frontier, len(frontier))

        current, path = frontier.pop()   # LIFO

        if current == goal:
            return path, max_frontier

        row, col = current
        for dr, dc in directions:
            nr, nc = row + dr, col + dc
            neighbor = (nr, nc)

            if valid_move(maze, nr, nc) and neighbor not in visited:
                visited.add(neighbor)
                frontier.append((neighbor, path + [neighbor]))

    return None, max_frontier
```

### Run DFS
```python
dfs_path, dfs_frontier = dfs(maze, start, goal)

print("DFS path:", dfs_path)
print("DFS path length:", len(dfs_path) - 1)
print("DFS maximum frontier:", dfs_frontier)
```

### Sample Output
```text
DFS path: [(0, 0), (0, 1), (0, 2), (0, 3), (0, 4), (1, 4), (2, 4), (3, 4), (4, 4)]
DFS path length: 8
DFS maximum frontier: 3
```

---

## 7. Visualize the Maze Path

```python
def print_maze(maze, path=None):
    path = set(path or [])
    for r in range(len(maze)):
        row = ""
        for c in range(len(maze[0])):
            if (r, c) in path:
                row += "* "
            elif maze[r][c] == 1:
                row += "# "
            else:
                row += ". "
        print(row)
```

### Display both solutions
```python
print("BFS solution:")
print_maze(maze, bfs_path)

print()

print("DFS solution:")
print_maze(maze, dfs_path)
```

This helps us visually compare the route chosen by each algorithm.

---

## 8. Comparison Table

For this maze and the default movement order, both algorithms may produce the same path:

| Algorithm | Path Length | Maximum Frontier |
|---|---:|---:|
| BFS | 8 | 3 |
| DFS | 8 | 3 |

---

## 9. Questions and Answers

### 1. Which algorithm found the shorter path?
Both BFS and DFS found the same path length: **8 moves**.

### 2. Which algorithm had the larger maximum frontier?
In this example, **both had the same maximum frontier size: 3**.

### 3. Why did the two algorithms produce different paths on the same maze?
They can differ because:
- BFS uses a **queue** (FIFO)
- DFS uses a **stack** (LIFO)

This means they expand neighbors in different orders.  
Even for the same maze, if the neighbor order differs, the path found can differ.

### 4. Does DFS always produce the shortest path? Why or why not?
**No.** DFS does not guarantee shortest paths because it follows one branch deeply before exploring alternatives. It may find a long route before a shorter one.

### 5. Does BFS always produce the shortest path when every movement costs 1? Why?
**Yes.** BFS explores states by increasing distance from the start. In an unweighted maze, the first time BFS reaches the goal, it is guaranteed to be at minimum cost.

---

## 10. Experiment 1 — Change the Neighbor Order

DFS is sensitive to the order in which neighbors are explored.

```python
directions = [
    (0, 1),    # right
    (1, 0),    # down
    (0, -1),   # left
    (-1, 0)    # up
]

dfs_path2, dfs_frontier2 = dfs(maze, start, goal)

print("DFS path (new order):", dfs_path2)
print("DFS path length:", len(dfs_path2) - 1)
print("DFS maximum frontier:", dfs_frontier2)

print()
print_maze(maze, dfs_path2)
```

### Question
Did the DFS path change?

### Answer
It may change depending on the order of neighbors.  
This happens because DFS chooses the next node based on the current ordering of expansions.

Important note:
- BFS also depends on neighbor order when multiple shortest routes exist
- However, BFS still guarantees shortest path length
- DFS does **not** guarantee shortest path length

Reset the directions back to the original order before continuing:

```python
directions = [
    (-1, 0),   # up
    (1, 0),    # down
    (0, -1),   # left
    (0, 1)     # right
]
```

---

## 11. Experiment 2 — Change Start and Goal

Try different valid open cells.

```python
directions = [
    (-1, 0),   # up
    (1, 0),    # down
    (0, -1),   # left
    (0, 1)     # right
]

# Try changing these values
start = (0, 0)
goal = (2, 2)

bfs_path, bfs_frontier = bfs(maze, start, goal)
dfs_path, dfs_frontier = dfs(maze, start, goal)

print("BFS path:", bfs_path, "| length:", len(bfs_path) - 1, "| max frontier:", bfs_frontier)
print("DFS path:", dfs_path, "| length:", len(dfs_path) - 1, "| max frontier:", dfs_frontier)

print()
print("BFS solution:")
print_maze(maze, bfs_path)
print()
print("DFS solution:")
print_maze(maze, dfs_path)
```

### Record your observations

| Start | Goal | BFS Length | BFS Max Frontier | DFS Length | DFS Max Frontier |
|---|---|---:|---:|---:|---:|
| (0, 0) | (2, 2) | 4 | 2 | 10 | 3 |
| (0, 4) | (4, 4) | 4 | 2 | 12 | 2 |
| (0, 0) | (0, 2) | 2 | 2 | 2 | 2 |

---

## 12. Questions from Experiment 2

### 6. Does BFS's path length ever change if you rerun with the same start/goal? Does DFS's?
For a fixed maze and fixed neighbor ordering:
- BFS returns the same shortest distance
- DFS will also return the same route unless the neighbor order changes

### 7. As the distance between start and goal grows, what happens to the maximum frontier size?
The frontier size usually grows with the number of reachable states and branching factor, but it is not always a simple linear pattern. It depends on:
- maze layout
- branch structure
- neighbor ordering
- when the target is found

### 8. Can you find a start/goal pair where BFS and DFS return the same path?
Yes. In many simple mazes, especially when there is only one clear route or when DFS happens to follow the shortest branch first, both return the same path.

### 9. What if the goal is unreachable?
Then both BFS and DFS will fail to find a path.

```python
sealed_maze = [
    [0, 1, 0],
    [0, 1, 0],
    [0, 1, 0]
]

print(bfs(sealed_maze, (0, 0), (0, 2)))
print(dfs(sealed_maze, (0, 0), (0, 2)))
```

### Output
```text
(None, 1)
(None, 1)
```

This means:
- no path was found
- the frontier was explored completely
- the goal was unreachable

---

## 13. Real-World Applications

### BFS
- shortest path in unweighted graphs
- network routing
- social network “degrees of separation”
- web crawling
- GPS-like route finding in equal-cost maps

### DFS
- maze solving
- graph traversal
- cycle detection
- topological sorting
- backtracking problems
- file system traversal

---

## 14. Important Concept: Uninformed Search

Both BFS and DFS are called “uninformed” because they do not use any heuristic or knowledge about which direction is closer to the goal.

They only use:
- the current state
- visited states
- the movement rules

This is different from informed search like A*, which uses a heuristic estimate of distance to the goal.

---

## 15. Key Takeaway

The key difference between BFS and DFS is just one line of logic:

```python
# BFS
frontier.popleft()

# DFS
frontier.pop()
```

This changes the behavior completely:

| Algorithm | Frontier Type | Expansion Order | Shortest Path Guarantee |
|---|---|---|---|
| BFS | Queue | FIFO | Yes, in unweighted graphs |
| DFS | Stack | LIFO | No |

### Final Summary
- BFS is best for shortest paths
- DFS is best for deep search and memory efficiency
- The order of neighbor exploration affects DFS behavior a lot
- BFS gives stronger guarantees, but may use more memory

---

## 16. Final Reflection

This lab demonstrates an essential idea in AI and computer science:

- different search strategies can solve the same problem
- yet they differ in efficiency, memory usage, and correctness guarantees
- the way the frontier is managed determines the overall behavior of the algorithm

This is the foundation of many advanced AI search methods, including A*, uniform-cost search, and iterative deepening.

---

## 17. Short Answer Summary

- BFS uses a queue and explores layer by layer
- DFS uses a stack and explores deeply first
- BFS guarantees shortest path in unweighted graphs
- DFS does not guarantee shortest path
- Neighbor order changes DFS behavior
- Frontier size depends on maze structure and algorithm choice
