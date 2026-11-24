# Employee Management System — Login, OOP and Sorting

[![.NET](https://img.shields.io/badge/.NET-512BD4?style=for-the-badge&logo=dotnet&logoColor=white)](https://dotnet.microsoft.com/)
[![C#](https://img.shields.io/badge/C%23-239120?style=for-the-badge&logo=csharp&logoColor=white)](https://learn.microsoft.com/dotnet/csharp/)
[![Microsoft Learn](https://img.shields.io/badge/Microsoft%20Learn-258FFA?style=for-the-badge&logo=microsoft&logoColor=white)](https://learn.microsoft.com/)

A C# console application combining login validation, employee management through object-oriented design, and manually implemented sorting algorithms. The supplied assignment brief defines the required login rules, employee hierarchy, menu operations, sorting constraints, and complexity-analysis topics. This README distinguishes those written requirements from the behaviour present in the current source code.

## Contents

- [Overview](#overview)
- [Assignment requirements](#assignment-requirements)
- [Implemented features](#implemented-features)
- [Authentication flow](#authentication-flow)
- [Employee object model](#employee-object-model)
- [Employee operations](#employee-operations)
- [Sorting algorithms](#sorting-algorithms)
- [Complexity analysis](#complexity-analysis)
- [Architecture](#architecture)
- [Project structure](#project-structure)
- [Build and run](#build-and-run)
- [Requirement-to-code alignment](#requirement-to-code-alignment)
- [Source files](#source-files)

## Overview

The application begins with an authentication step. When authentication succeeds, `Program` creates an `EmployeeService` and starts the employee menu. Employee objects are stored in a `List<Employee>`, allowing both full-time and part-time employee objects to be managed through a common base-class reference.

The assignment combines:

1. Login with a maximum of three attempts.
2. An employee class hierarchy.
3. Employee creation, display, sorting, and analysis.
4. Bubble Sort, Selection Sort, and Quick Sort.
5. Complexity explanations for sorting and selected employee operations.

## Assignment requirements

The supplied `C# Assignment_240426 (1).docx` specifies the following.

### Login system

- Username and password input.
- Hardcoded users `admin / 1234` and `user / pass`.
- Maximum three attempts.
- Username comparison case-insensitive.
- Password comparison case-sensitive.
- A validation method and a `while` or `do-while` loop.

### Employee hierarchy

The brief requires a base `Employee` class with ID, name, salary, a virtual `CalculateBonus()` method, and `DisplayDetails()`.

| Type | Required bonus |
|---|---:|
| Full-time employee | 20% |
| Part-time employee | 5% |

It also calls for inheritance, method overriding, polymorphism through a base-class reference, method overloading, and a static company member.

### Employee menu

- Add employee and select employment type.
- View employee ID, name, salary, and type.
- Sort by name or salary.
- Find the Nth-highest salary.
- Find duplicate names.
- Reverse employee-name word order.
- Calculate bonuses through polymorphism.
- Exit.

### Assignment constraints

The brief states that built-in sorting and LINQ should not be used, that the code should be separated into reusable methods, and that complexity explanations should cover best and worst cases.

## Implemented features

### Authentication

`AuthService.Authenticate()` uses a loop and an attempt counter initialized to three. Empty credentials and invalid credentials consume an attempt. The current implementation checks the `admin` / `1234` pair and returns a Boolean result.

### Employee creation

The service collects employee ID, name, salary, and employment type. It creates either a `FullTimeEmployee` or `PartTimeEmployee` and adds that object to `List<Employee>`.

### Employee listing

The view operation displays:

```text
Id | Name | Salary | Type
```

The type is determined by checking whether the current object is a `FullTimeEmployee`.

### Employee sorting

The user selects a sort field (name or salary), then chooses one of the three sorting algorithms. The sorting methods accept a comparison delegate, allowing the same algorithm to be reused for different employee fields.

### Nth-highest salary

The current implementation sorts the employee list by salary using Bubble Sort, reverses the returned list, and selects the employee at position `n - 1`.

### Duplicate-name detection

The service compares employee names against other entries and avoids printing a duplicate name more than once.

### Reverse employee names

The service splits a name into words, reverses the order of those words, joins them, and displays the resulting employee list.

Example:

```text
John Doe  ->  Doe John
```

### Bonus calculation

The service iterates through `List<Employee>` and calls `CalculateBonus()`. Runtime polymorphism selects the implementation from the actual employee type.

## Authentication flow

```text
Program.Main()
     |
     v
AuthService.Authenticate()
     |
     +-- Valid credentials --> EmployeeService.PerformService()
     |
     +-- Attempts exhausted --> Account Locked
```

## Employee object model

```text
                  Employee
                 /        \
                /          \
       FullTimeEmployee  PartTimeEmployee
          20% bonus          5% bonus
```

### Employee

The base class contains:

- `id`
- `name`
- `salary`
- Virtual `CalculateBonus()`
- `DisplayDetails()`
- A static company-name field

### FullTimeEmployee

Inherits from `Employee` and overrides `CalculateBonus()` to calculate 20% of salary.

```text
Bonus = Salary × 20 / 100
```

### PartTimeEmployee

Inherits from `Employee` and overrides `CalculateBonus()` to calculate 5% of salary.

```text
Bonus = Salary × 5 / 100
```

## Employee operations

| Operation | Implementation |
|---|---|
| Add | Creates a derived employee object and stores it in `List<Employee>` |
| View | Displays ID, name, salary, and type |
| Sort | Sorts a copied list by name or salary |
| Nth-highest salary | Bubble-sorts by salary, reverses the result, and selects the requested position |
| Duplicate names | Uses nested comparisons and prints each duplicate name once |
| Reverse name | Reverses the word order in each employee name |
| Calculate bonus | Calls the virtual bonus method through base-class references |

## Sorting algorithms

`SortingService` contains three sorting implementations.

| Algorithm | How it works in this project |
|---|---|
| Bubble Sort | Compares adjacent employee records and swaps them according to the supplied comparison delegate |
| Selection Sort | Finds the next element according to the comparison delegate and places it at the current position |
| Quick Sort | Uses partitioning with the last element as the pivot, then recursively sorts the two partitions |

The sort methods create a new list using `ToList()` before performing the algorithm, so the source list is not directly reordered by the sort operation.

## Complexity analysis

The table describes the algorithmic complexity of the current implementations.

| Operation | Best case | Average case | Worst case |
|---|---:|---:|---:|
| Bubble Sort in this code | O(n²) | O(n²) | O(n²) |
| Selection Sort | O(n²) | O(n²) | O(n²) |
| Quick Sort | O(n log n) | O(n log n) | O(n²) |
| Nth-highest salary | O(n²) | O(n²) | O(n²) |
| Duplicate-name detection | O(n²) | O(n²) | O(n²) |
| Reverse words in one name | O(w) | O(w) | O(w) |

`n` is the number of employees and `w` is the number of words in a name. The Bubble Sort implementation does not include an early-exit flag, so its best-case runtime remains O(n²). Quick Sort can degrade to O(n²) when partitions are repeatedly unbalanced.

## Architecture

| Component | Responsibility |
|---|---|
| `Program` | Runs authentication and starts the employee service after successful login |
| `AuthService` | Reads credentials and controls the attempt counter |
| `EmployeeService` | Maintains employee records and performs menu operations |
| `SortingService` | Implements Bubble Sort, Selection Sort, and Quick Sort |
| `Employee` | Base employee data and virtual bonus method |
| `FullTimeEmployee` | Full-time bonus calculation |
| `PartTimeEmployee` | Part-time bonus calculation |

## Project structure

```text
02_Employee_Management_System_Using_OOP/
├── Models/
│   ├── Employee.cs
│   ├── FullTimeEmployee.cs
│   └── PartTimeEmployee.cs
├── Properties/
│   └── AssemblyInfo.cs
├── Services/
│   ├── AuthService.cs
│   ├── EmployeeService.cs
│   └── SortingService.cs
├── App.config
├── Program.cs
├── Employee_Management_System.csproj
└── Employee_Management_System.slnx
```

The assignment brief is supplied in `C# Assignment_240426 (1).docx`.

## Build and run

The project targets **.NET Framework 4.7.2**.

1. Open `Employee_Management_System.slnx` in Visual Studio with the .NET Framework 4.7.2 targeting pack.
2. Build the solution.
3. Run the console application.
4. Authenticate using the credential pair checked by the current `AuthService`.
5. Use the employee menu to add, view, sort, and analyse employee records.

## Requirement-to-code alignment

| Written assignment requirement | Current source |
|---|---|
| `admin / 1234` and `user / pass` | Only `admin / 1234` is checked |
| Username case-insensitive | Current comparison uses `==`, which is case-sensitive |
| Maximum three attempts | Attempt counter is initialized to 3 |
| Full-time bonus 20% | Implemented |
| Part-time bonus 5% | Implemented |
| Bubble, Selection, Quick Sort | Implemented |
| No built-in sorting | Sorting algorithms are written manually |
| No LINQ | `SortingService` uses `ToList()` to copy the list |
| Method overloading | No overloaded employee method appears in the inspected source |
| Static company member | Static company-name field exists in `Employee` |
| Separate complexity explanation file | No separate complexity file is included; complexity is documented above |

## Source files

- [Assignment solution](Employee_Management_System.slnx)
- [Project file](Employee_Management_System.csproj)
- [Program entry point](Program.cs)
- [Employee base class](Models/Employee.cs)
- [Full-time employee](Models/FullTimeEmployee.cs)
- [Part-time employee](Models/PartTimeEmployee.cs)
- [Authentication service](Services/AuthService.cs)
- [Employee service](Services/EmployeeService.cs)
- [Sorting service](Services/SortingService.cs)

## Official documentation

- [Microsoft .NET](https://dotnet.microsoft.com/)
- [C# documentation](https://learn.microsoft.com/dotnet/csharp/)
- [Inheritance](https://learn.microsoft.com/dotnet/csharp/fundamentals/object-oriented/inheritance)
- [Polymorphism](https://learn.microsoft.com/dotnet/csharp/fundamentals/object-oriented/polymorphism)
- [Generic collections](https://learn.microsoft.com/dotnet/standard/collections/)
