Below is a professional, Markdown-formatted submission for the Lab 4 student tasks, lab questions, and extension. Exact results from randomized experiments may vary between runs.

# Lab 4 — Hill-Climbing Search with the N-Queens Problem

## Task 1: State Representation

In the state `[1, 3, 0, 2]`, the list index represents the column, and the value represents the queen's row.

```python
state = [1, 3, 0, 2]

print("State:", state)
```

**Output:**

```text
State: [1, 3, 0, 2]
```

There is one queen in each column in this state. No queens attack one another in this arrangement, so it is a valid solution to the 4-Queens problem.

---

## Task 2: Experiments with Different Board Sizes

Because the initial states are random, results may vary across different runs for the same board size. The cell below performs the required runs and prints the results as a Markdown table.

> `random.seed(42)` helps make results reproducible. Even so, run the cell in your own Python environment and report your actual results.

```python
import random

random.seed(42)

experiments = {
    4: 2,
    8: 3,
    10: 3,
}

results = []

for n, number_of_runs in experiments.items():
    for run in range(1, number_of_runs + 1):
        solution, scores = hill_climbing(n)
        conflicts = count_conflicts(solution)

        results.append({
            "N": n,
            "Run": run,
            "Final Conflicts": conflicts,
            "Iterations": len(scores),
            "Solved?": conflicts == 0,
        })

print("| N | Run | Final Conflicts | Iterations | Solved? |")
print("|---:|---:|---:|---:|:---:|")

for result in results:
    print(
        f"| {result['N']} "
        f"| {result['Run']} "
        f"| {result['Final Conflicts']} "
        f"| {result['Iterations']} "
        f"| {'Yes' if result['Solved?'] else 'No'} |"
    )
```

### Observations

- Different runs for the same board size may produce different final conflict counts.
- Hill-Climbing moves only when it finds a neighbor that is better than the current state.
- If no better neighbor is found, the algorithm stops—even if conflicts remain.
- In this lab's code, `Iterations` is the length of `scores`. It includes the evaluations of both the initial and final states, so it is not necessarily the exact number of accepted moves.

---

## Task 3: Comparing Basic and Random-Restart Hill-Climbing

The cell below runs both algorithms five times and prints the results for each run.

```python
random.seed(42)

print("Basic Hill-Climbing")
print("-------------------")

for run in range(1, 6):
    state, scores = hill_climbing(8)
    conflicts = count_conflicts(state)

    print(
        f"Run {run}: "
        f"Conflicts = {conflicts}, "
        f"Iterations = {len(scores)}, "
        f"Solved = {conflicts == 0}"
    )


print("\nRandom-Restart Hill-Climbing")
print("----------------------------")

for run in range(1, 6):
    state, restarts, best_scores = random_restart_hill_climbing(
        8,
        max_restarts=100
    )
    conflicts = count_conflicts(state)

    print(
        f"Run {run}: "
        f"Conflicts = {conflicts}, "
        f"Restarts = {restarts}, "
        f"Solved = {conflicts == 0}"
    )
```

### Comparison

Basic Hill-Climbing starts its search from one random initial state. If it gets stuck at a non-solution local maximum, it does not find a solution in that run.

Random-Restart Hill-Climbing tries again from a new random starting state after each unsuccessful run. This increases the chance of finding a solution, but does not guarantee success within a fixed restart limit, such as 100. The actual number of restarts and whether each run is solved can be reported after running the code above.

---

## Lab Questions

### Question 1: What is the purpose of the evaluation function in Hill-Climbing?

The evaluation function measures the quality of the current state. In this lab, it counts the conflicting pairs of queens and returns the negative of that count. This allows the algorithm to compare states and select a better neighbor.

### Question 2: Why is an evaluation score of zero the best result?

The score is `-count_conflicts(state)`. A state with zero conflicts has a score of `0`. States with conflicts have negative scores, so zero is the highest and best possible score.

### Question 3: What is the difference between a local maximum and a global maximum?

A **local maximum** is a state whose score is better than or equal to the scores of its immediate neighbors. A **global maximum** is the state with the best score in the entire search space. A local maximum may also be the global maximum, but not necessarily.

### Question 4: Why might basic Hill-Climbing stop even though a solution exists?

Basic Hill-Climbing only examines the neighbors of the current state. If none of them improves the score, the algorithm stops. A solution may exist elsewhere in the search space, but reaching it might require first making a move that does not improve the current score. This implementation does not accept such a move.

### Question 5: Is reaching a local maximum always a failure?

No. If the local maximum has a score of zero, it is a valid N-Queens solution and the algorithm has succeeded. It is a failure only when the algorithm stops at a state with nonzero conflicts.

### Question 6: How many neighbors does each state have for N = 8?

In each column, the queen can be moved to any of the other `N - 1` rows. Therefore, the number of neighbors is:

`N × (N - 1) = 8 × 7 = 56`

### Question 7: What does the evaluation-score graph show?

The graph shows how the evaluation score changes during the search. In Basic Hill-Climbing, accepted moves improve the score, so it is non-decreasing. If the final score is zero, a solution has been found. If the final score is negative, the algorithm stopped while conflicts remained.

### Question 8: Explain probabilistic completeness in your own words.

Probabilistic completeness means that if each independent run has a nonzero chance of finding a solution, then as the number of runs increases, the chance of finding at least one solution can approach 1. It does not guarantee success within any fixed number of runs.

### Question 9: Why does Random-Restart Hill-Climbing increase the chance of finding a solution?

Each restart begins the search from a new random initial state. This gives the algorithm multiple opportunities to search different parts of the search space. One run may get stuck at a local maximum, while another may reach a solution.

### Question 10: Does probabilistic completeness guarantee a solution within 100 restarts?

No. It does not guarantee success within a fixed 100 restarts. If each independent run has a success probability of `p`, then the probability of success in `k` runs is:

`P(success) = 1 - (1 - p)^k`

This probability increases with `k`, but the possibility of failure remains for any finite `k`.

---

## Extension Task: N = 20

The cell below performs one Basic Hill-Climbing run and one Random-Restart run for N = 20.

```python
random.seed(42)

n = 20

# Basic Hill-Climbing
basic_state, basic_scores = hill_climbing(n)
basic_conflicts = count_conflicts(basic_state)

print("Basic Hill-Climbing")
print("-------------------")
print("Final conflicts:", basic_conflicts)
print("Recorded iterations:", len(basic_scores))
print("Solved:", basic_conflicts == 0)


# Random-Restart Hill-Climbing
restart_state, restarts, best_scores = (
    random_restart_hill_climbing(n, max_restarts=100)
)
restart_conflicts = count_conflicts(restart_state)

print("\nRandom-Restart Hill-Climbing")
print("----------------------------")
print("Final conflicts:", restart_conflicts)
print("Restarts performed:", restarts)
print("Solved:", restart_conflicts == 0)
```

### Extension Discussion

The result of the basic run depends on the initial random state. If the final number of conflicts is zero, a valid solution has been found; otherwise, the algorithm stopped at a non-solution state. Random restarts provide additional chances to find a solution, but success is not guaranteed within the restart limit.

As N increases, each state has `N × (N - 1)` neighbors. Therefore, more neighbors must be evaluated during each iteration for a larger board. The random initial state also affects the search's progress and the number of iterations required.

### Verifying a Valid State for N = 20

The state below is a known valid arrangement for N = 20. It is not the result of the algorithm's run, but an example used for verification.

```python
valid_state_20 = [
    1, 3, 5, 7, 9, 11, 13, 15, 17, 19,
    2, 0, 6, 8, 10, 12, 14, 16, 18, 4
]

print("Conflicts:", count_conflicts(valid_state_20))
print("Valid solution:", count_conflicts(valid_state_20) == 0)
```

**Expected output:**

```text
Conflicts: 0
Valid solution: True
```

This state confirms that a solution exists for N = 20. However, it does not by itself prove that the random-restart experiment found this state; record the experiment's own result after running the code.
