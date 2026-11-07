# Lab 2 — BFS and DFS: Completed Answer Key

## Results and assumptions

These results use the maze and search functions from the lab. Unless noted otherwise, the neighbor order is **up, down, left, right**. Path length counts moves, so it is `len(path) - 1`.

The maximum frontier values below follow the lab’s implementation: the frontier is measured before removing the next state.

## 1. Original maze: start `(0, 0)`, goal `(4, 4)`

BFS returns the lower route; DFS returns the upper route:

**BFS path**

```text
(0, 0) → (1, 0) → (2, 0) → (2, 1) → (2, 2)
       → (3, 2) → (3, 3) → (3, 4) → (4, 4)
```

**DFS path**

```text
(0, 0) → (0, 1) → (0, 2) → (0, 3) → (0, 4)
       → (1, 4) → (2, 4) → (3, 4) → (4, 4)
```

| Algorithm | Path length (moves) | Maximum frontier |
|---|---:|---:|
| BFS | 8 | 2 |
| DFS | 8 | 3 |

### Answers to Section 7

1. **Which algorithm found the shorter path?** Neither. Both paths are 8 moves long.
2. **Which algorithm had the larger maximum frontier?** DFS, with a maximum frontier of 3 compared with BFS’s 2.
3. **Why did they produce different paths?** BFS expands the oldest discovered state first using a FIFO queue. DFS expands the newest state first using a LIFO stack. They explore the maze in different orders, so they can find different routes.
4. **Does DFS always produce the shortest path?** No. DFS returns the first goal it reaches, which may be on a longer route.
5. **Does BFS always produce the shortest path when each movement costs 1?** Yes. BFS explores states in increasing order of distance from the start, so the first path it finds to the goal is a shortest path.

## 2. Experiment 1: change the neighbor order

With directions changed to **right, down, left, up**, DFS still returns the upper route in this maze. Its exploration order changes, but the first path it finds to the goal remains 8 moves long.

| Algorithm | Path length (moves) | Maximum frontier |
|---|---:|---:|
| DFS, new order | 8 | 2 |

**Did the DFS path change?** No—not for this maze and these two direction orders. In other mazes, changing neighbor order can change DFS’s returned path.

**Does BFS depend on neighbor order in the same way?** Neighbor order can change which path BFS selects when there are multiple shortest paths, but it does not change the shortest-path guarantee. With the new order, rerunning BFS returns the upper 8-move route instead of the lower one.

Reassigning `directions` does not change a result already computed. Run `bfs()` or `dfs()` again to see the effect of the new order.

## 3. Experiment 2: different start and goal pairs

Restore the original direction order (**up, down, left, right**) before running these examples.

| Start | Goal | BFS length | BFS max frontier | DFS length | DFS max frontier |
|---|---|---:|---:|---:|---:|
| `(0, 0)` | `(2, 2)` | 4 | 2 | 10 | 3 |
| `(0, 4)` | `(4, 4)` | 4 | 2 | 12 | 2 |
| `(0, 0)` | `(0, 2)` | 2 | 2 | 2 | 2 |

For `(0, 0)` to `(2, 2)`, BFS finds the short route:

```text
(0, 0) → (1, 0) → (2, 0) → (2, 1) → (2, 2)
```

DFS first follows the upper branch and reaches the goal by a longer route:

```text
(0, 0) → (0, 1) → (0, 2) → (0, 3) → (0, 4)
       → (1, 4) → (2, 4) → (3, 4) → (3, 3) → (3, 2) → (2, 2)
```

### Answers to Section 9

6. **Does path length change when rerunning with the same start and goal?** Not if the maze, direction order, and implementation stay the same. BFS returns a shortest path; DFS follows a deterministic exploration order and returns the same first-found path. Changing the direction order can change the route selected by either algorithm, though BFS still returns a shortest route.
7. **What happens to maximum frontier size as the distance between start and goal grows?** It does not necessarily grow in a predictable or monotonic way. Frontier size depends on the number and arrangement of reachable branches, the neighbor order, and how soon the goal is found.
8. **Can BFS and DFS return the same path?** Yes. For `(0, 0)` to `(0, 2)`, both return the same 2-move path. This can happen when DFS follows a shortest route or when the maze structure leaves only one practical route. It does not mean DFS always finds a shortest path.
9. **What if the goal is unreachable?** Both functions exhaust the cells reachable from the start and return `(None, max_frontier)`. The original maze has no separated open region, so use a separate maze to test this:

```python
sealed_maze = [
    [0, 1, 0],
    [0, 1, 0],
    [0, 1, 0]
]

print(bfs(sealed_maze, (0, 0), (0, 2)))  # (None, 1)
print(dfs(sealed_maze, (0, 0), (0, 2)))  # (None, 1)
```

The wall column separates the start from the goal, so neither search can reach the goal.

## Key conclusions

- **BFS** returns a shortest path when every move has equal cost.
- **DFS** does not guarantee a shortest path; its result depends on exploration order.
- Neighbor order can affect which path is returned when multiple routes are available.
- Frontier size depends on the maze and the particular search run; neither algorithm always has the smaller frontier.
