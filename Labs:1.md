Exercise 1: Degree Honors Rule
Add a new rule to UNIVERSITY_KB["rules"] for students completing their degree program:

Condition:
- cgpa >= 3.70
- disciplinary_warnings == 0

Then:
- Status: "Graduated with Magna Cum Laude Honors"

Exercise 2: Tracing Forward Chaining
Modify evaluate_student() so that it prints each evaluated rule condition as the loop runs, showing step-by-step reasoning.

Below is a complete and professional Colab-ready solution.

Code (Google Colab)

```python
# ===========================================
# University Student Evaluation System
# Part 2: Hands-On Student Exercises
# ===========================================

# Knowledge Base
UNIVERSITY_KB = {
    "rules": []
}

# Existing rule example: Dean's List
deans_list_rule = {
    "id": "R-02",
    "name": "Dean's List",
    "checks": [
        ("cgpa >= 3.50", lambda student: student.get("cgpa", 0) >= 3.50),
        ("disciplinary_warnings == 0", lambda student: student.get("disciplinary_warnings", 0) == 0),
    ],
    "status": "Dean's List"
}

# Exercise 1: Add new rule for Magna Cum Laude Honors
magna_cum_laude_rule = {
    "id": "R-01",
    "name": "Magna Cum Laude Honors",
    "checks": [
        ("cgpa >= 3.70", lambda student: student.get("cgpa", 0) >= 3.70),
        ("disciplinary_warnings == 0", lambda student: student.get("disciplinary_warnings", 0) == 0),
    ],
    "status": "Graduated with Magna Cum Laude Honors"
}

# Add rules to the knowledge base
if not any(rule.get("id") == "R-02" for rule in UNIVERSITY_KB["rules"]):
    UNIVERSITY_KB["rules"].append(deans_list_rule)

if not any(rule.get("id") == "R-01" for rule in UNIVERSITY_KB["rules"]):
    UNIVERSITY_KB["rules"].insert(0, magna_cum_laude_rule)

print("Knowledge Base Ready")
print("Rules in order:", [rule["name"] for rule in UNIVERSITY_KB["rules"]])
```

Forward chaining with trace:

```python
def evaluate_student(student, knowledge_base=UNIVERSITY_KB):
    """
    Evaluate student against all rules.
    Prints each rule condition while checking,
    so the logical deduction process is visible.
    """
    student_name = student.get("name", "Unknown Student")
    print("\n" + "=" * 80)
    print(f"Student: {student_name}")
    print(f"CGPA: {student.get('cgpa', 0)}")
    print(f"Disciplinary Warnings: {student.get('disciplinary_warnings', 0)}")
    print("=" * 80)

    for rule_number, rule in enumerate(knowledge_base["rules"], start=1):
        print(f"\nChecking Rule {rule_number}: {rule['name']} (ID: {rule['id']})")
        condition_results = []

        for condition, checker in rule["checks"]:
            passed = bool(checker(student))
            condition_results.append(passed)

            if passed:
                print(f"  [PASS] {condition}")
            else:
                print(f"  [FAIL] {condition}")

        if all(condition_results):
            print(f"\nRule matched successfully!")
            print(f"Status: {rule['status']}")
            return rule["status"]

        print("Rule did not match. Moving to next rule.")

    print("\nNo rule matched the student profile.")
    return "No matching rule"
```

Test data:

```python
students = [
    {
        "name": "Ayesha Khan",
        "cgpa": 3.86,
        "disciplinary_warnings": 0
    },
    {
        "name": "Bilal Ahmed",
        "cgpa": 3.60,
        "disciplinary_warnings": 0
    },
    {
        "name": "Sara Malik",
        "cgpa": 3.95,
        "disciplinary_warnings": 1
    }
]

for student in students:
    status = evaluate_student(student)
    print("\nFinal Result:", status)
    print("-" * 80)
```

Sample Output

```text
Knowledge Base Ready
Rules in order: ['Magna Cum Laude Honors', "Dean's List"]

================================================================================
Student: Ayesha Khan
CGPA: 3.86
Disciplinary Warnings: 0
================================================================================

Checking Rule 1: Magna Cum Laude Honors (ID: R-01)
  [PASS] cgpa >= 3.70
  [PASS] disciplinary_warnings == 0

Rule matched successfully!
Status: Graduated with Magna Cum Laude Honors

Final Result: Graduated with Magna Cum Laude Honors
--------------------------------------------------------------------------------

================================================================================
Student: Bilal Ahmed
CGPA: 3.6
Disciplinary Warnings: 0
================================================================================

Checking Rule 1: Magna Cum Laude Honors (ID: R-01)
  [FAIL] cgpa >= 3.70
  [PASS] disciplinary_warnings == 0
Rule did not match. Moving to next rule.

Checking Rule 2: Dean's List (ID: R-02)
  [PASS] cgpa >= 3.50
  [PASS] disciplinary_warnings == 0

Rule matched successfully!
Status: Dean's List

Final Result: Dean's List
--------------------------------------------------------------------------------

================================================================================
Student: Sara Malik
CGPA: 3.95
Disciplinary Warnings: 1
================================================================================

Checking Rule 1: Magna Cum Laude Honors (ID: R-01)
  [PASS] cgpa >= 3.70
  [FAIL] disciplinary_warnings == 0
Rule did not match. Moving to next rule.

Checking Rule 2: Dean's List (ID: R-02)
  [PASS] cgpa >= 3.50
  [FAIL] disciplinary_warnings == 0
Rule did not match. Moving to next rule.

No rule matched the student profile.
Final Result: No matching rule
--------------------------------------------------------------------------------
```


# University Student Evaluation System

## Objective
This project demonstrates rule-based reasoning for evaluating students.

## Rules
1. Magna Cum Laude Honors:
   - cgpa >= 3.70
   - disciplinary_warnings == 0

2. Dean's List:
   - cgpa >= 3.50
   - disciplinary_warnings == 0

## How it works
The application checks ea
- a presentation-ready slide deck in text format,
- or a cleaner notebook version with exact formatting for Colab.
