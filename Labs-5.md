Neeche Lab 4 ke student tasks, lab questions aur extension ke liye ek professional, Markdown-format submission di gayi hai. Randomized experiments ke exact results har run mein badal sakte hain, is liye unke numbers ghair-haqiqi taur par fill nahi kiye; diye gaye code ko notebook mein run karne par tables aur results khud generate honge.

# Lab 4 — Hill-Climbing Search with the N-Queens Problem

## Task 1: State Representation

State `[1, 3, 0, 2]` mein list ka index column ko aur value queen ki row ko show karti hai.

```python
state = [1, 3, 0, 2]

print("State:", state)
```

**Output:**

```text
State: [1, 3, 0, 2]
```

Is state mein har column mein ek queen hai. Is arrangement mein queens ek doosre ko attack nahi kartin, is liye yeh 4-Queens ka valid solution hai.

---

## Task 2: Different Board Sizes ke Experiments

Random initial states ki wajah se ek hi board size par different runs ke results mukhtalif ho sakte hain. Neeche diya gaya cell required runs karta hai aur results ko Markdown table mein print karta hai.

> `random.seed(42)` results ko reproducible banane mein madad karta hai. Phir bhi, apne Python environment mein cell run karke apne actual results report karein.

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

### Observation

- Ek hi board size ke different runs mukhtalif final conflict counts de sakte hain.
- Hill-Climbing sirf us waqt move karta hai jab koi neighbor current state se behtar ho.
- Agar koi behtar neighbor na mile, algorithm ruk jata hai—even agar conflicts abhi bhi baqi hon.
- `Iterations` is lab code mein `scores` ki length hai. Is mein initial state aur final state dono ki evaluation shamil hoti hai; yeh zaroori nahi ke yeh accepted moves ki exact tadaad ho.

---

## Task 3: Basic aur Random-Restart Hill-Climbing ka Muqabla

Neeche diya gaya cell dono algorithms ko paanch martaba chalata hai aur har run ke results print karta hai.

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

Basic Hill-Climbing ek random initial state se search shuru karta hai. Agar woh non-solution local maximum par ruk jaye, to us run mein solution nahi milta.

Random-Restart Hill-Climbing har unsuccessful run ke baad nayi random starting state se koshish karta hai. Is se solution milne ka chance barhta hai, lekin 100 jaise fixed restart limit ke andar solution milne ki guarantee nahi hoti. Actual restarts aur solved/unsolved results upar wala code run karne ke baad report kiye ja sakte hain.

---

## Lab Questions

### Question 1: Hill-Climbing mein evaluation function ka kya maqsad hai?

Evaluation function current state ki quality measure karta hai. Is lab mein yeh queen-pairs ke conflicts ko count karke uska negative return karta hai. Is se algorithm states ko compare kar sakta hai aur behtar neighbor select kar sakta hai.

### Question 2: Evaluation score zero sab se behtar result kyun hai?

Score `-count_conflicts(state)` hai. Zero conflicts ka score `0` hota hai. Conflicts ki tadaad negative score deti hai, is liye zero sab se bara aur behtareen mumkin score hai.

### Question 3: Local maximum aur global maximum mein kya farq hai?

**Local maximum** aisi state hai jiska score uske immediate neighbors se behtar ya unke barabar ho. **Global maximum** poore search space mein sab se behtar score wali state hai. Local maximum global maximum bhi ho sakta hai, lekin zaroori nahi.

### Question 4: Solution maujood hone ke bawajood basic Hill-Climbing kyun ruk sakta hai?

Basic Hill-Climbing sirf current state ke neighbors dekhta hai. Agar un mein se koi neighbor score improve na kare, algorithm ruk jata hai. Solution kisi aur jagah search space mein ho sakta hai, lekin us tak jaane ke liye pehle aisa move karna par sakta hai jo current score ko improve na kare. Yeh implementation aisa move accept nahi karti.

### Question 5: Kya local maximum tak pahunchna hamesha failure hota hai?

Nahi. Agar local maximum ka score zero ho, to woh valid N-Queens solution hai aur algorithm kamyab hai. Failure sirf tab hoti hai jab algorithm nonzero conflicts wali state par ruk jaye.

### Question 6: N = 8 ke liye har state ke kitne neighbors hain?

Har column mein queen ko baqi `N - 1` rows mein move kiya ja sakta hai. Is liye neighbors ki tadaad:

`N × (N - 1) = 8 × 7 = 56`

### Question 7: Evaluation-score graph kya batata hai?

Graph dikhata hai ke search ke dauran evaluation score kaise badalta hai. Basic Hill-Climbing mein accepted moves score ko improve karte hain, is liye score non-decreasing hota hai. Agar final score zero ho, solution mil gaya. Agar final score negative ho, algorithm conflicts baqi hone ke bawajood ruk gaya.

### Question 8: Probabilistic completeness ko apne alfaaz mein samjhayen.

Probabilistic completeness ka matlab hai ke agar har independent run ke paas solution tak pahunchne ka nonzero chance ho, to zyada runs ke saath kam az kam ek solution milne ka chance 1 ke qareeb ja sakta hai. Yeh kisi fixed number of runs ke andar success ki guarantee nahi deta.

### Question 9: Random-Restart Hill-Climbing solution ka chance kyun barhata hai?

Har restart nayi random initial state se search shuru karta hai. Is se algorithm ko search space ke mukhtalif hisson se solution dhoondhne ke multiple chances milte hain. Ek run local maximum par ruk sakta hai, jabke doosra run solution tak pahunch sakta hai.

### Question 10: Kya probabilistic completeness 100 restarts ke andar solution ki guarantee deta hai?

Nahi. Yeh fixed 100 restarts ke andar success guarantee nahi karta. Agar har independent run ka success chance `p` ho, to `k` runs mein success ka chance:

`P(success) = 1 - (1 - p)^k`

hota hai. Yeh chance `k` ke saath barhta hai, magar finite `k` par failure ka chance baqi reh sakta hai.

---

## Extension Task: N = 20

Neeche diya gaya cell N = 20 ke liye ek basic run aur Random-Restart run karta hai.

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

Basic run ka result initial random state par depend karta hai. Agar final conflicts zero hon, valid solution mil gaya; warna algorithm non-solution state par ruk gaya. Random restarts solution dhoondhne ke additional chances dete hain, lekin restart limit ke andar success guaranteed nahi hoti.

N barhne par har state ke neighbors ki tadaad `N × (N - 1)` hoti hai. Is liye bade board ke liye har iteration mein zyada neighbors evaluate karne padte hain. Random initial state bhi search ki progress aur required iterations par asar انداز hoti hai.

### N = 20 ke liye Valid State ki Verification

Neeche diya gaya state ek known valid N = 20 arrangement hai. Yeh algorithm ke run ka result nahi, balki verification ke liye ek valid example hai.

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

Yeh state confirm karta hai ke N = 20 ke liye solution maujood hai. Lekin yeh apne aap mein sabit nahi karta ke random-restart experiment ne isi state ko find kiya; experiment ka apna result code run karne ke baad record karein.
