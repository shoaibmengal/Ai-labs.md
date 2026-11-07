# Week 4 Lab: A* Search for the 8-Puzzle
## (Google Colab Edition)

### Objective
Implement the A* search algorithm and apply it to the 8-puzzle problem. In this lab, you will:
- represent the 8-puzzle as a search problem,
- implement the A* algorithm with a priority queue,
- implement both heuristics,
- compare their performance on increasingly difficult puzzle states.

---

## 1. Problem Representation

### Cell 1 — Markdown
```markdown
## 1. Problem Representation

State: tuple of 9 integers, where 0 represents the blank space.
Goal state: (1, 2, 3, 4, 5, 6, 7, 8, 0)
```

### Cell 2 — Imports and constants
```python
import heapq
import itertools
import random

GOAL_STATE = (1, 2, 3, 4, 5, 6, 7, 8, 0)
```

### Cell 3 — State helper functions
```python
def get_blank_position(state):
    """Return the index (0-8) of the blank tile (0) in the state tuple."""
    return state.index(0)

def get_neighbors(state):
    """
    Return a list of (new_state, action) pairs reachable from `state`
    by sliding one tile into the blank space.

    Actions should be one of: "Up", "Down", "Left", "Right"
    (direction the BLANK moves).
    """
    neighbors = []
    blank = get_blank_position(state)
    row, col = divmod(blank, 3)

    moves = {
        "Up":    (row - 1, col),
        "Down":  (row + 1, col),
        "Left":  (row, col - 1),
        "Right": (row, col + 1),
    }

    for action, (new_row, new_col) in moves.items():
        if 0 <= new_row < 3 and 0 <= new_col < 3:
            new_blank = new_row * 3 + new_col
            new_state = list(state)
            new_state[blank], new_state[new_blank] = new_state[new_blank], new_state[blank]
            neighbors.append((tuple(new_state), action))

    return neighbors

def is_goal(state):
    return state == GOAL_STATE
```

### Cell 4 — Checkpoint
```python
# Checkpoint: blank is in the bottom-right corner of GOAL_STATE,
# so only Up and Left should be valid moves.
neighbors = get_neighbors(GOAL_STATE)
print(neighbors)
assert len(neighbors) == 2, f"Expected 2 neighbors, got {len(neighbors)}"
print("Cell 3 checkpoint passed.")
```

---

## 2. Heuristics

### Cell 5 — Markdown
```markdown
## 2. Heuristics

Both heuristics must be admissible (never overestimate true cost)
for A* to guarantee an optimal solution.
```

### Cell 6 — Misplaced Tiles heuristic
```python
def misplaced_tiles(state):
    """
    Count tiles that are not in their goal position.
    The blank (0) does NOT count as a misplaced tile.
    """
    count = 0
    for i in range(len(state)):
        if state[i] != 0 and state[i] != GOAL_STATE[i]:
            count += 1
    return count
```

### Cell 7 — Manhattan Distance heuristic
```python
def manhattan_distance(state):
    """
    Sum of the horizontal + vertical distance of every tile
    (excluding the blank) from its goal position.
    """
    total = 0
    for i, tile in enumerate(state):
        if tile == 0:
            continue
        
        goal_index = GOAL_STATE.index(tile)
        current_row, current_col = divmod(i, 3)
        goal_row, goal_col = divmod(goal_index, 3)
        
        total += abs(current_row - goal_row) + abs(current_col - goal_col)
    
    return total
```

### Cell 8 — Sanity check
```python
test_state = (1, 2, 3, 4, 5, 6, 7, 0, 8)  # one move from goal

assert misplaced_tiles(test_state) <= 1, "Misplaced tiles should be ≤ actual distance"
assert manhattan_distance(test_state) <= 1, "Manhattan distance should be ≤ actual distance"
print("Cell 8 checkpoint passed.")
```

### Discussion
Why must `h(n) = 0` whenever `state == GOAL_STATE`?

Because the true remaining cost from a goal state to itself is zero. If the heuristic returned a value greater than zero at the goal, it would overestimate the remaining cost and violate the admissibility condition. This is important because A* relies on admissible heuristics to guarantee optimality. If `h(goal) > 0`, the algorithm might incorrectly think that a path is still expensive even when it is already finished, causing incorrect ordering or incorrect stopping behavior.

---

## 3. A* Search Implementation

### Cell 9 — Markdown
```markdown
## 3. A* Search

Priority queue ordered by f(n) = g(n) + h(n), using heapq.
```

### Cell 10 — A* search implementation
```python
def a_star_search(start_state, heuristic_fn):
    """
    Returns a dict with:
        "path": list of actions from start to goal
        "cost": number of moves in the solution
        "nodes_expanded": total number of nodes popped from the frontier
    """
    counter = itertools.count()  # tie-breaker for equal f-values
    frontier = []
    heapq.heappush(frontier, (heuristic_fn(start_state), next(counter), 0, start_state, []))
    # entry format: (f, tie_breaker, g, state, path_of_actions)

    best_g = {start_state: 0}
    nodes_expanded = 0

    while frontier:
        f, _, g, state, path = heapq.heappop(frontier)
        nodes_expanded += 1

        if is_goal(state):
            return {"path": path, "cost": g, "nodes_expanded": nodes_expanded}

        # Skip if we've already found a cheaper path to this state
        if g > best_g.get(state, float("inf")):
            continue

        for neighbor_state, action in get_neighbors(state):
            new_g = g + 1  # each move costs 1

            if neighbor_state not in best_g or new_g < best_g[neighbor_state]:
                best_g[neighbor_state] = new_g
                new_f = new_g + heuristic_fn(neighbor_state)
                heapq.heappush(
                    frontier,
                    (new_f, next(counter), new_g, neighbor_state, path + [action])
                )

    return {"path": None, "cost": None, "nodes_expanded": nodes_expanded}  # no solution found
```

### Why the tie-breaker counter?
When two states have the same `f` value, tuples in the heap are compared element by element. Python will try to compare the state tuples next, which is legal but not ideal. The `itertools.count()` tie-breaker ensures deterministic ordering and avoids comparison issues.

### Cell 11 — Smoke test
```python
easy_state = (1, 2, 3, 4, 5, 6, 7, 0, 8)  # one move from goal
result = a_star_search(easy_state, manhattan_distance)
print(result)
assert result["cost"] == 1, f"Expected cost 1, got {result['cost']}"
print("Cell 11 checkpoint passed.")
```

---

## 4. Testing

### Cell 12 — Markdown
```markdown
## 4. Testing

Generate solvable scrambles of increasing difficulty, then compare heuristics.
```

### Cell 13 — Solvability check
```python
def is_solvable(state):
    """
    A state is solvable iff the number of inversions (pairs of tiles,
    ignoring the blank, that are out of order relative to the goal)
    is even.
    """
    tiles = [t for t in state if t != 0]
    inversions = 0
    for i in range(len(tiles)):
        for j in range(i + 1, len(tiles)):
            if tiles[i] > tiles[j]:
                inversions += 1
    return inversions % 2 == 0
```

### Cell 14 — Scramble generator
```python
def generate_scramble(num_moves, seed=None):
    if seed is not None:
        random.seed(seed)
    state = GOAL_STATE
    for _ in range(num_moves):
        neighbors = get_neighbors(state)
        state, _ = random.choice(neighbors)
    return state
```

### Cell 15 — Run the comparison
```python
def run_comparison(num_moves_list, trials_per_level=5):
    results = []
    for num_moves in num_moves_list:
        for trial in range(trials_per_level):
            start = generate_scramble(num_moves, seed=trial * 1000 + num_moves)

            result_misplaced = a_star_search(start, misplaced_tiles)
            result_manhattan = a_star_search(start, manhattan_distance)

            results.append({
                "scramble_depth": num_moves,
                "trial": trial,
                "misplaced_nodes": result_misplaced["nodes_expanded"],
                "manhattan_nodes": result_manhattan["nodes_expanded"],
                "solution_cost": result_misplaced["cost"],  # should match manhattan's cost too
            })
    return results

# Suggested test range: start small, since Misplaced Tiles gets very slow
# on harder puzzles.
results = run_comparison(num_moves_list=[5, 10, 15, 20], trials_per_level=5)

for r in results:
    print(r)
```

### Checkpoint
For every trial, `misplaced_nodes` should correspond to the same solution cost as Manhattan Distance. Since both heuristics are admissible, both should return optimal solutions of the same cost.

### Cell 16 — Table and chart
```python
import pandas as pd
import matplotlib.pyplot as plt

df = pd.DataFrame(results)
summary = df.groupby("scramble_depth")[["misplaced_nodes", "manhattan_nodes"]].mean()
print(summary)

summary.plot(marker="o")
plt.xlabel("Scramble Depth (moves from goal)")
plt.ylabel("Average Nodes Expanded")
plt.title("A* Node Expansion: Misplaced Tiles vs. Manhattan Distance")
plt.legend(["Misplaced Tiles", "Manhattan Distance"])
plt.show()
```

---

## 5. Analysis

### Cell 17 — Markdown
```markdown
## 5. Analysis

1. Table: [paste your Cell 16 output/table here]
2. Chart: [Colab will keep the plot output attached to Cell 16 — reference it here, no need to duplicate it]
3. Answers:
   - At what scramble depth does the gap between the two heuristics become clearly visible? Why does the gap widen as depth increases rather than staying constant?
   - Manhattan Distance is provably ≥ Misplaced Tiles for every state. Using the lecture's definition of admissibility, explain why a higher admissible heuristic is not a violation of admissibility, and why it leads to fewer node expansions.
   - (Optional) Try increasing scramble depth until Misplaced Tiles becomes impractically slow. Does Manhattan Distance hit the same wall at the same depth?
```

### Written Analysis
1. The difference between the two heuristics becomes clearly visible as the puzzle gets harder, usually around moderate scramble depths such as 15 or 20 moves. The gap widens because Manhattan Distance provides more informative estimates than Misplaced Tiles. It measures how far each tile is from its goal, rather than only checking whether it is misplaced. This reduces the number of states explored and guides the search more efficiently.

2. A heuristic is admissible if it never overestimates the true remaining cost. Manhattan Distance is always greater than or equal to the number of misplaced tiles, but it is still admissible because it is still a lower bound on the actual remaining cost. A higher admissible heuristic is not a violation of admissibility; it is simply a stronger estimate. A stronger heuristic reduces node expansions because A* can prioritize more promising branches earlier and discard less useful ones.

3. Manhattan Distance generally remains feasible for deeper scrambles, while Misplaced Tiles can become impractically slow. However, as puzzle depth continues to increase, even Manhattan Distance eventually becomes expensive because the search space of the 8-puzzle grows exponentially.

---

## Final Submission Checklist
- [ ] Notebook has all 17 cells, run top to bottom with no errors
- [ ] `get_neighbors`, `misplaced_tiles`, `manhattan_distance` implemented and passing checkpoints
- [ ] `a_star_search` implemented and passing the smoke test
- [ ] Comparison run across at least four scramble depths and five trials each
- [ ] Table and chart generated in Cell 16
- [ ] Section 5 analysis answered in Cell 17
- [ ] Share your Colab notebook link with “Anyone with the link can view” enabled

---

## Professional Summary
This lab demonstrates how A* search improves efficiency using informed heuristics. The Misplaced Tiles heuristic is simple but weak, while Manhattan Distance is stronger because it captures the actual distance each tile must travel. Both heuristics are admissible, but Manhattan Distance leads to fewer node expansions and faster search in harder puzzle states. This illustrates the central idea of AI search: a better heuristic can dramatically improve performance without sacrificing optimality.

If you want, I can also turn this into:
- a cleaner GitHub-ready Markdown file,
- a student report format,
- or a shorter version suitable for submission on LMS.
