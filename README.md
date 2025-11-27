# student-management-system-python
A simple Python-based Student Management System that stores and manages student records such as name, roll number, marks, and attendance. It allows adding, updating, searching, deleting records, and provides basic class analysis like average marks and highest scorer.
"""
STUDENT PROGRESS TRACKER
Author: SOUHINI ROY
"""

import csv
import os
import sys
from typing import Dict, List

# --------------------------- Utility Functions ---------------------------

def validate_marks(m: str) -> bool:
    """Validate marks as integer between 0 and 100"""
    try:
        iv = int(m)
        return 0 <= iv <= 100
    except:
        return False

def grade_from_average(avg: float) -> str:
    """Return grade letter based on average"""
    if avg >= 85: return "A"
    elif avg >= 70: return "B"
    elif avg >= 55: return "C"
    elif avg >= 40: return "D"
    else: return "F"

def safe_input(prompt: str) -> str:
    """Wrapper for input to allow easier testing / redirection"""
    try:
        return input(prompt)
    except EOFError:
        return ""

# --------------------------- OOP: Student Class ---------------------------

class Student:
    def __init__(self, roll: str, name: str, marks: Dict[str,int]):
        self.roll = roll          # string
        self.name = name          # string
        self.marks = marks.copy() # dict subject->int (uses dict copying)
        self.total = sum(self.marks.values())
        self.average = round(self.total / len(self.marks), 2) if self.marks else 0.0
        self.grade = grade_from_average(self.average)

    def update_marks(self, new_marks: Dict[str,int]):
        """Update marks (demonstrates methods, passing dicts)"""
        self.marks.update(new_marks)
        self.total = sum(self.marks.values())
        self.average = round(self.total / len(self.marks), 2)
        self.grade = grade_from_average(self.average)

    def to_csv_row(self, subjects: List[str]) -> List:
        """Convert to CSV row (list)"""
        return [self.roll, self.name] + [self.marks.get(s,0) for s in subjects] + [self.total, self.average, self.grade]

# --------------------------- Manager (CRUD + File I/O) ---------------------------

class StudentManager:
    def __init__(self, filename: str = "data/students.csv", subjects: List[str] = None):
        self.filename = filename
        self.subjects = subjects or ["Math", "Physics", "Chemistry"]
        self.students: Dict[str, Student] = {}  # roll -> Student object
        # ensure directory exists
        os.makedirs(os.path.dirname(self.filename), exist_ok=True)
        # create file header if not present
        if not os.path.exists(self.filename):
            with open(self.filename, "w", newline="", encoding="utf-8") as f:
                writer = csv.writer(f)
                writer.writerow(["roll", "name"] + self.subjects + ["total", "average", "grade"])
        self.load()

    # ---------- Load / Save (File handling + CSV) ----------
    def load(self):
        try:
            with open(self.filename, newline="", encoding="utf-8") as f:
                reader = csv.DictReader(f)
                for row in reader:
                    roll = row["roll"]
                    marks = {s: int(row.get(s,0)) for s in self.subjects}
                    self.students[roll] = Student(roll, row["name"], marks)
        except Exception as e:
            # Exception handling demonstrated here
            print("Warning: could not load data:", e)

    def save(self):
        try:
            with open(self.filename, "w", newline="", encoding="utf-8") as f:
                writer = csv.writer(f)
                writer.writerow(["roll","name"] + self.subjects + ["total","average","grade"])
                for s in self.students.values():
                    writer.writerow(s.to_csv_row(self.subjects))
        except Exception as e:
            print("Error saving:", e)

    # ---------- CRUD ----------
    def add_student(self):
        roll = safe_input("Enter roll number: ").strip()
        if not roll:
            print("Empty roll not allowed.")
            return
        if roll in self.students:
            print("Roll exists. Use update.")
            return
        name = safe_input("Enter student name: ").strip()
        marks = {}
        for sub in self.subjects:
            while True:
                m = safe_input(f"Marks for {sub}: ").strip()
                if validate_marks(m):
                    marks[sub] = int(m); break
                else:
                    print("Invalid marks. Enter 0-100.")
        stu = Student(roll, name, marks)
        self.students[roll] = stu
        self.save()
        print(f"Added: {name} (Roll: {roll}) Total:{stu.total} Avg:{stu.average} Grade:{stu.grade}")

    def view_student(self):
        roll = safe_input("Enter roll to view: ").strip()
        stu = self.students.get(roll)
        if not stu:
            print("Not found."); return
        print(f"\nReport for {stu.name} (Roll {stu.roll})")
        for k,v in stu.marks.items():
            print(f"  {k}: {v}")
        print(f"Total: {stu.total} Average: {stu.average} Grade: {stu.grade}\n")

    def update_student(self):
        roll = safe_input("Enter roll to update: ").strip()
        stu = self.students.get(roll)
        if not stu:
            print("Not found."); return
        new_name = safe_input(f"New name (Enter to keep '{stu.name}'): ").strip()
        if new_name:
            stu.name = new_name
        for s in self.subjects:
            nv = safe_input(f"{s} (current {stu.marks.get(s)}): ").strip()
            if nv != "":
                if validate_marks(nv):
                    stu.marks[s] = int(nv)
                else:
                    print("Invalid; skipping")
        stu.update_marks({})
        self.save()
        print("Updated and saved.")

    def delete_student(self):
        roll = safe_input("Enter roll to delete: ").strip()
        if roll in self.students:
            del self.students[roll]
            self.save()
            print("Deleted.")
        else:
            print("Not found.")

    def list_students(self):
        if not self.students:
            print("No students.\n"); return
        sorted_list = sorted(self.students.values(), key=lambda x: x.roll)
        print("\nAll students:")
        for s in sorted_list:
            print(f"{s.roll} - {s.name} | Total={s.total} Avg={s.average} Grade={s.grade}")
        print()

    def search_by_name(self):
        q = safe_input("Enter name substring to search: ").strip().lower()
        results = [s for s in self.students.values() if q in s.name.lower()]
        if not results:
            print("No matches.\n"); return
        for s in results:
            print(f"{s.roll} - {s.name} | Avg={s.average}")

    def class_summary(self):
        if not self.students:
            print("No data.\n"); return
        students = list(self.students.values())
        class_avg = round(sum(s.average for s in students)/len(students),2)
        topper = max(students, key=lambda x: x.total)
        bottom = min(students, key=lambda x: x.total)
        print(f"Class Avg: {class_avg} Topper: {topper.name} ({topper.total}) Bottom: {bottom.name} ({bottom.total})")
        for sub in self.subjects:
            top = max(students, key=lambda x: x.marks.get(sub,0))
            print(f"{sub} Topper: {top.name} ({top.marks.get(sub)})")
        print()

    def export_reports(self):
        os.makedirs("exports", exist_ok=True)
        ind_file = os.path.join("exports","individual_reports.csv")
        with open(ind_file,"w",newline="",encoding="utf-8") as f:
            writer = csv.writer(f)
            writer.writerow(["roll","name"]+self.subjects+["total","average","grade"])
            for s in self.students.values():
                writer.writerow(s.to_csv_row(self.subjects))
        print("Exported individual reports to", ind_file)
        summary_file = os.path.join("exports","class_summary.txt")
        with open(summary_file,"w",encoding="utf-8") as f:
            f.write("Class Summary\n")
            students=list(self.students.values())
            if students:
                class_avg = round(sum(s.average for s in students)/len(students),2)
                f.write(f"Class average: {class_avg}\n")
        print("Exported class summary to", summary_file)

# --------------------------- Main Menu ---------------------------

def main_menu():
    mgr = StudentManager()
    while True:
        print("""
1. Add Student
2. View Student
3. Update Student
4. Delete Student
5. List Students
6. Search by Name
7. Class Summary
8. Export Reports
9. Exit
""")
        ch = safe_input("Choice: ").strip()
        if ch == "1": mgr.add_student()
        elif ch == "2": mgr.view_student()
        elif ch == "3": mgr.update_student()
        elif ch == "4": mgr.delete_student()
        elif ch == "5": mgr.list_students()
        elif ch == "6": mgr.search_by_name()
        elif ch == "7": mgr.class_summary()
        elif ch == "8": mgr.export_reports()
        elif ch == "9": print("Goodbye!"); break
        else: print("Invalid choice.")

if __name__ == '__main__':
    main_menu()
