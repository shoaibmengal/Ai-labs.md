
## Labs-1: Student Exercises

### Exercise 1: Add a Degree Honors Rule

Add a new rule in the `UNIVERSITY_KB["rules"]` list for graduation honors:

- Condition: `cgpa >= 3.70` and `disciplinary_warnings == 0`
- Result: Status = "Graduated with Magna Cum Laude Honors"

### Example Solution

```python
UNIVERSITY_KB["rules"].append({
    "id": "RULE_07",
    "conditions": {
        "cgpa_min": 3.70,
        "disciplinary_warnings": 0
    },
    "condition_type": "degree_honors",
    "grade": "A+",
    "status": "Graduated with Magna Cum Laude Honors",
    "action": "Eligible for graduation honors recognition."
})
```

---

### Exercise 2: Trace Forward Chaining

The student should modify the `evaluate_student()` function to print each rule as it is tested, demonstrating step-by-step logical deduction.

### Example Improved Version

```python
def evaluate_student(student_record, kb):
    print(f"\n--- EVALUATING STUDENT: {student_record['student_name']} (ID: {student_record['student_id']}) ---")
    
    # Attendance rule
    if student_record["attendance"] < 75:
        print("Checking attendance rule...")
        attendance_rule = next(r for r in kb["rules"] if r["condition_type"] == "attendance_below")
        print(f"Attendance {student_record['attendance']} < 75: true")
        return {
            "rule_id": attendance_rule["id"],
            "grade": attendance_rule["grade"],
            "status": attendance_rule["status"],
            "action": attendance_rule["action"]
        }
    
    print("Attendance is sufficient. Checking marks rules...")
    marks = student_record["total_marks"]
    
    for rule in kb["rules"]:
        if rule["condition_type"] == "marks_range":
            lower = rule["conditions"]["marks_min"]
            upper = rule["conditions"]["marks_max"]
            print(f"Checking {rule['id']}: marks {marks} in range {lower}-{upper}")
            if lower <= marks <= upper:
                print(f"Rule {rule['id']} matched.")
                return {
                    "rule_id": rule["id"],
                    "grade": rule["grade"],
                    "status": rule["status"],
                    "action": rule["action"]
                }
    
    return {
        "rule_id": "UNKNOWN",
        "grade": "N/A",
        "status": "Invalid Score Entry",
        "action": "Verify submitted marks with the exam department."
    }
```

This trace helps students understand that the system is performing forward chaining:
- It checks the facts
- It evaluates conditions
- It fires the matched rule
- It produces a conclusion

---

## Part 5: Final Lab Discussion

This lab demonstrates the main idea behind a rule-based expert system:
- A knowledge base stores rules and facts
- An inference engine processes information
- The system reaches decisions based on logical conditions

This is a simplified version of how real expert systems work in fields such as:
- Medical diagnosis
- Banking risk assessment
- Industrial fault detection
- Academic advising
- Legal decision support

---

