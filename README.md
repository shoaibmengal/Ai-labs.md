# Ai-labs.md
My Python and AI lab experiments and assignments.
# My AI Labs & Course Work


## Week 1: Practical Lab 1: Building a Knowledge-Based System (KBS)
- **Core Concepts:** Definition of AI, Turing Test, rational agents.
- **Practical Lab:** Setting up Python environment and building a basic rule-based expert system.
- Code 1: Exercise 1 (Degree Honors Rule Added to Knowledge Base)
Python Code
# ===================================================================== 
# EXERCISE 1: KNOWLEDGE BASE WITH NEW DEGREE HONORS RULE
# ===================================================================== 

UNIVERSITY_KB = {
    "system_name": "BSCS Academic Performance & Grading Expert System",
    "passing_marks": 50,
    "rules": [
        # Exercise 1: Newly Added Degree Honors Rule
        {
            "id": "RULE_HONORS",
            "conditions": {
                "cgpa_min": 3.70,
                "disciplinary_warnings": 0
            },
            "condition_type": "degree_honors",
            "grade": "A+",
            "status": "Graduated with Magna Cum Laude Honors",
            "action": "Eligible for valedictorian and top honors distinction."
        },
        {
            "id": "RULE_01",
            "conditions": {"attendance_percentage": 74},
            "condition_type": "attendance_below",
            "grade": "F",
            "status": "Debarred",
            "action": "Ineligible to sit for final exam due to short attendance."
        },
        {
            "id": "RULE_02",
            "conditions": {"marks_min": 85, "marks_max": 100},
            "condition_type": "marks_range",
            "grade": "A",
            "status": "Passed with Distinction",
            "action": "Recommended for Merit Scholarship."
        }
    ]
}

def evaluate_student_exercise_1(student_record, kb):
    """
    Evaluates student record including the new Degree Honors rule.
    """
    print(f"\n--- EVALUATING EXERCISE 1 FOR: {student_record.get('student_name', 'Student')} ---")
    
    # 1. Check Degree Honors Rule
    if "cgpa" in student_record and "disciplinary_warnings" in student_record:
        for rule in kb["rules"]:
            if rule["condition_type"] == "degree_honors":
                if (student_record["cgpa"] >= rule["conditions"]["cgpa_min"] and 
                    student_record["disciplinary_warnings"] == rule["conditions"]["disciplinary_warnings"]):
                    return {
                        "rule_id": rule["id"],
                        "grade": rule["grade"],
                        "status": rule["status"],
                        "action": rule["action"]
                    }
                    
    return {"rule_id": "N/A", "grade": "N/A", "status": "Standard Progress", "action": "Continue coursework."}

# Test Case for Exercise 1
student_honors = {
    "student_id": "BSCS-2024-045",
    "student_name": "Hacker",
    "cgpa": 3.85,
    "disciplinary_warnings": 0
}

result_ex1 = evaluate_student_exercise_1(student_honors, UNIVERSITY_KB)
print(f"Matched Rule ID: {result_ex1['rule_id']}")
print(f"Status: {result_ex1['status']}")
print(f"Action: {result_ex1['action']}")
Code 2: Exercise 2 (Tracing Forward Chaining)
Python Code
# ===================================================================== 
# EXERCISE 2: TRACING FORWARD CHAINING IN INFERENCE ENGINE
# ===================================================================== 

UNIVERSITY_KB_EX2 = {
    "system_name": "BSCS Tracing Expert System",
    "rules": [
        {
            "id": "RULE_01", 
            "conditions": {"attendance_percentage": 74}, 
            "condition_type": "attendance_below", 
            "grade": "F", 
            "status": "Debarred", 
            "action": "Ineligible to sit for final exam."
        },
        {
            "id": "RULE_02", 
            "conditions": {"marks_min": 85, "marks_max": 100}, 
            "condition_type": "marks_range", 
            "grade": "A", 
            "status": "Passed with Distinction", 
            "action": "Recommended for Merit Scholarship."
        }
    ]
}

def evaluate_student_with_trace(student_record, kb):
    """
    Modified function to print each evaluated rule condition step-by-step.
    """
    print(f"\n--- TRACING FORWARD CHAINING FOR: {student_record['student_name']} ---")
    
    # Trace Step 1: Check Attendance rule
    print(f"[Trace] Evaluating RULE_01: Is attendance ({student_record['attendance']}%) < 75%?")
    if student_record["attendance"] < 75:
        attendance_rule = next(r for r in kb["rules"] if r["condition_type"] == "attendance_below")
        print(f"[Deduction] Match found! Attendance is below threshold.")
        return {
            "rule_id": attendance_rule["id"],
            "grade": attendance_rule["grade"],
            "status": attendance_rule["status"],
            "action": attendance_rule["action"]
        }
    else:
        print(f"[Trace] Condition failed. Attendance is safe.")

    # Trace Step 2: Loop through marks rules
    marks = student_record["total_marks"]
    for rule in kb["rules"]:
        if rule["condition_type"] == "marks_range":
            print(f"[Trace] Evaluating {rule['id']}: Is marks ({marks}) between {rule['conditions']['marks_min']} and {rule['conditions']['marks_max']}?")
            if rule["conditions"]["marks_min"] <= marks <= rule["conditions"]["marks_max"]:
                print(f"[Deduction] Match found! Rule {rule['id']} triggered.")
                return {
                    "rule_id": rule["id"],
                    "grade": rule["grade"],
                    "status": rule["status"],
                    "action": rule["action"]
                }
            else:
                print(f"[Trace] Condition failed for {rule['id']}.")

    return {"rule_id": "UNKNOWN", "grade": "N/A", "status": "No matching rule found", "action": "Verify marks."}

# Test Case for Exercise 2
student_normal = {
    "student_id": "BSCS-2024-012",
    "student_name": "Hacker",
    "total_marks": 88,
    "attendance": 90
}

result_ex2 = evaluate_student_with_trace(student_normal, UNIVERSITY_KB_EX2)
print(f"Final Result -> Grade: {result_ex2['grade']} | Status: {result_ex2['status']}")


## Week 2: Uninformed Search Strategies
- **Core Concepts:** State-space graphs, BFS, and DFS.
- **Practical Lab:** Implementing BFS and DFS in Python for a 2D maze.
