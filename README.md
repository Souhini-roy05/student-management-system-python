# student-management-system-python
A simple Python-based Student Management System that stores and manages student records such as name, roll number, marks, and attendance. It allows adding, updating, searching, deleting records, and provides basic class analysis like average marks and highest scorer.
"""
Student Management System


students = []   # List to store all student dictionaries


# ----------------------- ADD STUDENT -----------------------
def add_student():
    print("\n--- Add New Student ---")
    try:
        roll = int(input("Enter Roll Number: "))
        name = input("Enter Student Name: ")
        marks = float(input("Enter Marks (0-100): "))

        student = {"roll": roll, "name": name, "marks": marks}
        students.append(student)

        print("Student added successfully!\n")

    except ValueError:
        print("Invalid input! Please enter correct values.\n")


# ----------------------- VIEW ALL -----------------------
def view_students():
    print("\n--- Student Records ---")
    if not students:
        print("No records found.\n")
        return

    for s in students:
        print(f"Roll: {s['roll']} | Name: {s['name']} | Marks: {s['marks']}")
    print()


# ----------------------- SEARCH -----------------------
def search_student():
    print("\n--- Search Student ---")
    roll = int(input("Enter roll number to search: "))

    for s in students:
        if s["roll"] == roll:
            print(f"Found: {s['roll']} | {s['name']} | Marks: {s['marks']}\n")
            return

    print("Student not found!\n")


# ----------------------- UPDATE -----------------------
def update_student():
    print("\n--- Update Student ---")
    roll = int(input("Enter roll to update: "))

    for s in students:
        if s["roll"] == roll:
            new_name = input("Enter new name: ")
            new_marks = float(input("Enter new marks: "))
            s["name"], s["marks"] = new_name, new_marks
            print("Record updated!\n")
            return

    print("Record not found.\n")


# ----------------------- DELETE -----------------------
def delete_student():
    print("\n--- Delete Student ---")
    roll = int(input("Enter roll to delete: "))

    for s in students:
        if s["roll"] == roll:
            students.remove(s)
            print("Student deleted.\n")
            return

    print("Student not found.\n")


# ----------------------- ANALYSIS -----------------------
def class_analysis():
    print("\n--- Class Analysis ---")

    if not students:
        print("No records to analyze.\n")
        return

    marks_list = [s["marks"] for s in students]

    avg = sum(marks_list) / len(marks_list)
    highest = max(marks_list)
    lowest = min(marks_list)

    print(f"Average Marks : {avg:.2f}")
    print(f"Highest Marks : {highest}")
    print(f"Lowest Marks  : {lowest}\n")


# ----------------------- SAVE TO FILE -----------------------
def save_data():
    with open("students_data.txt", "w") as f:
        for s in students:
            f.write(f"{s['roll']},{s['name']},{s['marks']}\n")
    print("Data saved to file.\n")


# ----------------------- LOAD FROM FILE -----------------------
def load_data():
    try:
        with open("students_data.txt", "r") as f:
            for line in f:
                roll, name, marks = line.strip().split(",")
                students.append({"roll": int(roll), "name": name, "marks": float(marks)})
        print("Data loaded successfully.\n")

    except FileNotFoundError:
        print("No saved data found. Starting fresh.\n")


# ----------------------- MAIN MENU -----------------------
def main():
    load_data()

    while True:
        print("===== Student Management System =====")
        print("1. Add Student")
        print("2. View All Students")
        print("3. Search Student")
        print("4. Update Student")
        print("5. Delete Student")
        print("6. Class Analysis")
        print("7. Save Data")
        print("8. Exit")

        choice = input("Enter choice: ")

        if choice == "1": add_student()
        elif choice == "2": view_students()
        elif choice == "3": search_student()
        elif choice == "4": update_student()
        elif choice == "5": delete_student()
        elif choice == "6": class_analysis()
        elif choice == "7": save_data()
        elif choice == "8":
            print("Exiting... Goodbye!")
            break
        else:
            print("Invalid choice!\n")


# Run the program
main()

# student-management-system-python/screenshots/
[MAIN MENU]-
<img width="436" height="203" alt="Screenshot 2025-11-28 010923" src="https://github.com/user-attachments/assets/0ea5746c-c24f-4338-82ba-024601205404" />
[ADD STUDENT]- 
<img width="508" height="402" alt="Screenshot 2025-11-28 004852" src="https://github.com/user-attachments/assets/be3b0114-321a-4bc1-b740-768a731fccf6" />
<img width="480" height="372" alt="Screenshot 2025-11-28 004904" src="https://github.com/user-attachments/assets/3cc7bee9-5f67-427d-9781-232576a43a25" />
[VIEW ALL STUDENT]-
<img width="528" height="331" alt="Screenshot 2025-11-28 004914" src="https://github.com/user-attachments/assets/3eb4eec3-cafc-4f84-9232-efc29efcdb7e" />
[SEARCH STUDENT]-
<img width="579" height="323" alt="Screenshot 2025-11-28 004929" src="https://github.com/user-attachments/assets/6a4368bd-6cc5-4924-9aab-4e5938068e87" />
[UPDATE STUDENT]-
<img width="510" height="372" alt="Screenshot 2025-11-28 004942" src="https://github.com/user-attachments/assets/e6953e78-3051-4674-9ac5-70804216fae0" />
[DELETE STUDENT]-
<img width="579" height="333" alt="Screenshot 2025-11-28 004953" src="https://github.com/user-attachments/assets/d6c0a088-8713-44e1-b2d6-cf3e34c708c8" />
[CLASS ANALYSIS]-
<img width="508" height="365" alt="Screenshot 2025-11-28 005003" src="https://github.com/user-attachments/assets/4f1ea0b1-0742-4583-af76-f02d28ff85c2" />
[SAVE DATA AND EXIT]-
<img width="453" height="517" alt="Screenshot 2025-11-28 005020" src="https://github.com/user-attachments/assets/5b5abace-8854-4804-b59c-04dd097406c5" />










     
   
