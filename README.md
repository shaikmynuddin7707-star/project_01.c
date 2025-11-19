/* grades.c
   Simple Exam / Grades Management Program in C (C99)
   Features:
   - Input number of students and subjects
   - Input student ID, name and marks per subject
   - Compute total, percentage, letter grade
   - Print report and class statistics (average, highest, lowest)
*/

#include <stdio.h>
#include <stdlib.h>
#include <string.h>

#define NAME_LEN 100

typedef struct {
    int id;
    char name[NAME_LEN];
    int *marks;     // dynamic array of marks per subject
    int total;
    double percent;
    char grade;     // letter grade
} Student;

/* function prototypes */
Student *create_students(int n, int subjects);
void input_data(Student *students, int n, int subjects);
void compute_results(Student *students, int n, int subjects, int max_mark_per_subject);
char calculate_grade(double percent);
void print_report(Student *students, int n, int subjects);
void print_statistics(Student *students, int n);
void free_students(Student *students, int n);

int main(void) {
    int n, subjects;
    int max_mark_per_subject = 100; // assumed max marks per subject

    printf("Exam / Grades Management System\n");
    printf("--------------------------------\n");
    printf("Enter number of students: ");
    if (scanf("%d", &n) != 1 || n <= 0) {
        printf("Invalid student count.\n");
        return 1;
    }

    printf("Enter number of subjects: ");
    if (scanf("%d", &subjects) != 1 || subjects <= 0) {
        printf("Invalid subject count.\n");
        return 1;
    }

    Student *students = create_students(n, subjects);
    if (!students) {
        printf("Memory allocation failed.\n");
        return 1;
    }

    /* consume newline before fgets */
    getchar();

    input_data(students, n, subjects);
    compute_results(students, n, subjects, max_mark_per_subject);
    print_report(students, n, subjects);
    print_statistics(students, n);

    free_students(students, n);
    return 0;
}

/* allocate array of students and allocate marks arrays for each student */
Student *create_students(int n, int subjects) {
    Student *arr = malloc(sizeof(Student) * n);
    if (!arr) return NULL;
    for (int i = 0; i < n; ++i) {
        arr[i].marks = malloc(sizeof(int) * subjects);
        if (!arr[i].marks) {
            /* free already allocated */
            for (int j = 0; j < i; ++j) free(arr[j].marks);
            free(arr);
            return NULL;
        }
        arr[i].total = 0;
        arr[i].percent = 0.0;
        arr[i].grade = 'F';
    }
    return arr;
}

/* read student details */
void input_data(Student *students, int n, int subjects) {
    for (int i = 0; i < n; ++i) {
        printf("\n--- Student %d ---\n", i + 1);
        printf("Enter student ID (integer): ");
        while (scanf("%d", &students[i].id) != 1) {
            printf("Invalid input. Enter integer ID: ");
            while (getchar() != '\n'); // flush
        }
        while (getchar() != '\n'); // consume trailing newline

        printf("Enter student name: ");
        if (!fgets(students[i].name, NAME_LEN, stdin)) {
            students[i].name[0] = '\0';
        } else {
            /* strip newline */
            size_t len = strlen(students[i].name);
            if (len && students[i].name[len - 1] == '\n') students[i].name[len - 1] = '\0';
        }

        for (int s = 0; s < subjects; ++s) {
            int m;
            printf("Enter marks for subject %d (0 - 100): ", s + 1);
            while (scanf("%d", &m) != 1 || m < 0 || m > 100) {
                printf("Invalid marks. Enter 0 - 100: ");
                while (getchar() != '\n'); // flush
            }
            students[i].marks[s] = m;
        }
        while (getchar() != '\n'); // clear remainder before next fgets
    }
}

/* compute totals, percentages and letter grades */
void compute_results(Student *students, int n, int subjects, int max_mark_per_subject) {
    int full_marks = subjects * max_mark_per_subject;
    for (int i = 0; i < n; ++i) {
        int sum = 0;
        for (int s = 0; s < subjects; ++s) {
            sum += students[i].marks[s];
        }
        students[i].total = sum;
        students[i].percent = (double)sum * 100.0 / full_marks;
        students[i].grade = calculate_grade(students[i].percent);
    }
}

/* simple grade scale */
char calculate_grade(double percent) {
    if (percent >= 90.0) return 'A';
    if (percent >= 80.0) return 'B';
    if (percent >= 70.0) return 'C';
    if (percent >= 60.0) return 'D';
    return 'F';
}

/* print neat table with student results */
void print_report(Student *students, int n, int subjects) {
    printf("\n\n=== Students Report ===\n");
    printf("%-6s %-20s %-8s %-10s %-6s\n", "ID", "Name", "Total", "Percent(%)", "Grade");
    printf("-----------------------------------------------------------------\n");
    for (int i = 0; i < n; ++i) {
        printf("%-6d %-20s %-8d %-10.2f %-6c\n",
               students[i].id,
               students[i].name,
               students[i].total,
               students[i].percent,
               students[i].grade);
    }
}

/* class-level statistics */
void print_statistics(Student *students, int n) {
    if (n == 0) return;
    int highest = students[0].total;
    int lowest = students[0].total;
    double sum = 0.0;
    for (int i = 0; i < n; ++i) {
        if (students[i].total > highest) highest = students[i].total;
        if (students[i].total < lowest) lowest = students[i].total;
        sum += students[i].total;
    }
    double avg = sum / n;
    printf("\n=== Class Statistics ===\n");
    printf("Average total marks : %.2f\n", avg);
    printf("Highest total marks : %d\n", highest);
    printf("Lowest total marks  : %d\n", lowest);
}

/* free dynamic memory */
void free_students(Student *students, int n) {
    for (int i = 0; i < n; ++i) free(students[i].marks);
    free(students);
}
