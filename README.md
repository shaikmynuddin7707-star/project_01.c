Abstract                                                                                                                                                                        The Grades Management Program is a C-based application designed to efficiently handle student examination records. It allows the user to input the number of students and subjects, and then collects each student's ID, name, and marks dynamically using memory allocation. The program calculates total marks, percentage, and assigns a letter grade based on a predefined grading scale. It generates a structured report showing individual student performance and also computes class-level analytics such as average marks, highest score, and lowest score. Dynamic memory management ensures flexibility for varying numbers of students and subjects. Overall, the program provides a simple yet effective system for managing and evaluating academic results.  



Functional Requirements

Below are precise, testable functional requirements derived from your grades.c program. Each item states what the system must do, with input, output, validation, and any important constraints.

1. Start-up / Welcome

R1.1: On launch, display a title banner: "Exam / Grades Management System" and a short separator line.

Acceptance: Banner printed to stdout before any prompts.

2. Student & Subject Count Input

R2.1: Prompt the user to enter the number of students (n).

Input: integer

Validation: must be an integer > 0

Error Handling: If invalid, print "Invalid student count." and exit with non-zero status.

R2.2: Prompt the user to enter the number of subjects (subjects).

Input: integer

Validation: must be an integer > 0

Error Handling: If invalid, print "Invalid subject count." and exit with non-zero status.

3. Memory Allocation for Students

R3.1: Allocate a contiguous array of n Student structures.

R3.2: For each student, allocate a dynamic integer array of length subjects for marks.

Error Handling: If any allocation fails, print "Memory allocation failed." and terminate with error.

4. Student Data Entry

R4.1: For each student i (1..n), prompt:

Student ID (integer)

Validation: must be integer

Behavior: re-prompt until valid integer entered

Student name (string)

Behavior: read full line (up to NAME_LEN) and strip trailing newline

Marks for each subject s (integer 0–100)

Validation: must be integer within [0,100]

Behavior: re-prompt until valid

R4.2: Consume/clear trailing newline characters appropriately between inputs so subsequent fgets/scanf works correctly.

5. Result Computation

R5.1: Compute each student's total marks as the sum of their subject marks.

R5.2: Compute percentage as (total / (subjects * max_mark_per_subject)) * 100.0, where max_mark_per_subject is 100 by default.

R5.3: Assign a letter grade using the grading scale:

>= 90.0 → A

>= 80.0 → B

>= 70.0 → C

>= 60.0 → D

< 60.0 → F

6. Report Generation

R6.1: Print a formatted table header: ID, Name, Total, Percent(%), Grade.

R6.2: For each student print a row with ID, name, total, percent (two decimal places), and grade aligned to columns.

7. Class Statistics

R7.1: Compute and display:

Average total marks (two decimal places)

Highest total marks

Lowest total marks

Precondition: n > 0; if n == 0, skip statistics.

8. Memory Deallocation

R8.1: Free each student's marks array and then free the students array before program exit to avoid memory leaks.

9. Input Robustness & Flow

R9.1: Where scanf is used, flush invalid input from stdin and re-prompt.

R9.2: Use fgets for names to allow spaces; strip newline.

10. Configurable Parameters (implicit)

R10.1: The maximum marks per subject is assumed to be 100 but should be used in percentage calculation (currently hard-coded as max_mark_per_subject = 100).

11. Error & Exit Codes

R11.1: The program must return a non-zero exit code for fatal errors (invalid counts, memory allocation failure).

R11.2: On normal completion, the program exits with 0.

12. Usability

R12.1: Prompts and output must be human-readable and follow the order shown in the code (prompt → input → compute → report → statistics).

Mapping to Code Components (for traceability)

Input & validation: main() prompts + scanf checks, input_data()

Allocation / deallocation: create_students(), free_students()

Computation: compute_results(), calculate_grade()

Output: print_report(), print_statistics()

Example Acceptance Test (quick)

Start program.

Enter 2 students, 3 subjects.

Student1: ID 101, Name Alice, marks 80 90 100.

Student2: ID 102, Name Bob, marks 50 60 70.

Program prints table with correct totals (270, 180), percentages, grades (A and F/D depending on scale), and class stats (avg, highest, lowest).
