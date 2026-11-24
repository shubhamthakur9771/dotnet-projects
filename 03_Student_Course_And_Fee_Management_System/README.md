# Student Course and Fee Management System

[![.NET](https://img.shields.io/badge/.NET-512BD4?style=for-the-badge&logo=dotnet&logoColor=white)](https://dotnet.microsoft.com/)
[![C#](https://img.shields.io/badge/C%23-239120?style=for-the-badge&logo=csharp&logoColor=white)](https://learn.microsoft.com/dotnet/csharp/)
[![Microsoft Learn](https://img.shields.io/badge/Microsoft%20Learn-258FFA?style=for-the-badge&logo=microsoft&logoColor=white)](https://learn.microsoft.com/)

A C# console application for managing course and student records, recording fee payments, tracking outstanding balances, and producing course-wise and monthly summaries. The implementation uses generic collections (`List<T>`) and LINQ for searching, filtering, grouping, and aggregation.

## Contents

- [Overview](#overview)
- [Course management](#course-management)
- [Student management](#student-management)
- [Admission workflow](#admission-workflow)
- [Fee tracking and calculations](#fee-tracking-and-calculations)
- [Search and filtering](#search-and-filtering)
- [Reports](#reports)
- [Architecture and components](#architecture-and-components)
- [Data models](#data-models)
- [Project structure](#project-structure)
- [Technology stack](#technology-stack)
- [Build and run](#build-and-run)
- [Data storage](#data-storage)
- [Source files](#source-files)

## Overview

The application manages two related record types:

- **Course:** course ID, course name, and fee.
- **Student:** student ID, name, selected course name, total fee, fee paid, and admission date.

`CourseService` maintains the course collection. `StudentService` receives a `CourseService` instance through its constructor so that student admission can verify that a course exists and obtain its fee.

The application is operated through `MainMenu`, which invokes the corresponding service methods.

## Course management

The course module supports:

- Adding a course with ID, name, and fee.
- Checking course ID uniqueness.
- Viewing all courses.
- Finding a course by name using a case-insensitive comparison.
- Deleting a course by name.
- Preventing deletion when students are enrolled in that course.

Course records are maintained in `List<Course>`.

## Student management

The student module supports:

- Adding a student with a unique student ID.
- Selecting an existing course by course name.
- Assigning the course's fee as the student's total fee.
- Capturing the initial amount paid.
- Setting the admission date to the current date and time.
- Viewing student records.
- Updating a student's name and recording an additional payment.
- Deleting a student by ID.

Student records are maintained in `List<Student>`.

## Admission workflow

```text
Enter student ID
      |
      v
Check ID uniqueness
      |
      v
Enter name and course name
      |
      v
Find course through CourseService
      |
      +-- Course not found --> Stop admission
      |
      v
Read initial fee payment
      |
      v
Validate payment <= course fee
      |
      v
Create Student object
      |
      v
Set admission date
      |
      v
Add to List<Student>
```

The selected course must already exist before a student can be added.

## Fee tracking and calculations

The `Student` model stores the total course fee and cumulative fee paid. Its fee-due value is derived from those values.

```text
Fee Due = Total Fee - Fee Paid
```

The application enforces these payment checks:

- Initial fee paid cannot exceed the course fee.
- An additional payment cannot make cumulative `FeePaid` greater than `TotalFee`.
- Students with `FeeDue > 0` can be listed through the pending-fee search.

The update operation asks for a new student name and an additional amount paid. The amount is added to the existing payment after validation.

## Search and filtering

`StudentService` implements the following operations:

| Method | Behaviour |
|---|---|
| `SearchByName()` | Partial name match using case-insensitive string comparison |
| `SearchByCourse()` | Exact course-name match, ignoring case |
| `FeeDueStudents()` | Filters students whose fee due is greater than zero |
| `SearchByMonth()` | Filters students by the month component of admission date |

These queries operate on the current in-memory student collection.

## Reports

The current Reports menu option invokes two report methods.

### Course-wise student count

`CourseWiseCount()` groups students by course name and calculates the number of students in each group.

### Monthly revenue

`MonthlyRevenue()` groups students by the month component of `AdmissionDate` and sums the fee-paid amounts for each month.

The report implementation uses LINQ operations including `GroupBy()`, `Select()`, `Count()`, and `Sum()`.

## Architecture and components

```text
Program
  |
  +-- CourseService
  |      +-- List<Course>
  |      +-- Add / View / Find / Delete
  |
  +-- StudentService(CourseService)
         +-- List<Student>
         +-- Add / View / Update / Delete
         +-- Search / Filter
         +-- Course-wise count
         +-- Monthly revenue
                |
                v
             MainMenu
```

| Component | Responsibility |
|---|---|
| `Program` | Creates service instances and starts the menu |
| `MainMenu` | Presents course and student operations |
| `Course` | Represents course ID, name, and fee |
| `Student` | Represents student, course, payment, and admission information |
| `CourseService` | Maintains and operates on the course collection |
| `StudentService` | Maintains student records and performs fee, search, and reporting operations |

## Data models

### Course

| Property | Description |
|---|---|
| `CourseId` | Course identifier |
| `CourseName` | Course name |
| `Fee` | Course fee |

### Student

| Property | Description |
|---|---|
| `StudentId` | Student identifier |
| `Name` | Student name |
| `CourseName` | Selected course |
| `TotalFee` | Course fee assigned at admission |
| `FeePaid` | Cumulative amount paid |
| `AdmissionDate` | Student admission timestamp |
| `FeeDue` | Calculated outstanding fee |

## LINQ usage

| LINQ operation | Use |
|---|---|
| `Any()` | Duplicate ID checks and course-enrolment checks |
| `FirstOrDefault()` | Find course or student records |
| `Where()` | Search and filter student records |
| `GroupBy()` | Group by course or admission month |
| `Select()` | Project grouped report results |
| `Count()` | Calculate course-wise student counts |
| `Sum()` | Calculate monthly fee revenue |

## Project structure

```text
03_Student_Course_And_Fee_Management_System/
├── Menus/
│   └── MainMenu.cs
├── Models/
│   ├── Course.cs
│   └── Student.cs
├── Properties/
│   └── AssemblyInfo.cs
├── Services/
│   ├── CourseService.cs
│   └── StudentService.cs
├── App.config
├── Program.cs
├── Student_Course_And_Fee_Management_System.csproj
└── Student_Course_And_Fee_Management_System.slnx
```

## Technology stack

| Technology / concept | Usage |
|---|---|
| C# | Application language |
| .NET Framework 4.7.2 | Project target |
| Console application | Menu-based interface |
| `List<T>` | Dynamic course and student collections |
| LINQ | Filtering, grouping, projection, and aggregation |
| `decimal` | Course fees and payments |
| `DateTime` | Admission date and month filtering |
| Service classes | Separate course and student operations |

## Build and run

The project targets **.NET Framework 4.7.2**.

1. Open `Student_Course_And_Fee_Management_System.slnx` in Visual Studio with the .NET Framework 4.7.2 targeting pack.
2. Build the solution.
3. Run the console application.
4. Add courses before adding students because admission requires an existing course.

## Data storage

- Course and student records are stored in in-memory `List<T>` collections.
- No database connection or SQL script is included in this project.
- Records are not persisted after the process exits.
- The student record stores the course name rather than a course ID foreign-key reference.
- The implemented reports are course-wise count and monthly revenue.

## Source files

- [Solution](Student_Course_And_Fee_Management_System.slnx)
- [Project file](Student_Course_And_Fee_Management_System.csproj)
- [Program entry point](Program.cs)
- [Main menu](Menus/MainMenu.cs)
- [Course model](Models/Course.cs)
- [Student model](Models/Student.cs)
- [Course service](Services/CourseService.cs)
- [Student service](Services/StudentService.cs)

## Official documentation

- [Microsoft .NET](https://dotnet.microsoft.com/)
- [C# documentation](https://learn.microsoft.com/dotnet/csharp/)
- [Collections in .NET](https://learn.microsoft.com/dotnet/standard/collections/)
- [LINQ in C#](https://learn.microsoft.com/dotnet/csharp/linq/)
- [Date and time types](https://learn.microsoft.com/dotnet/standard/datetime/)
