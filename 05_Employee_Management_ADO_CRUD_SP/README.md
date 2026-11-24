# Employee Management System — ADO.NET CRUD with Stored Procedures

[![.NET](https://img.shields.io/badge/.NET-512BD4?style=for-the-badge&logo=dotnet&logoColor=white)](https://dotnet.microsoft.com/)
[![C#](https://img.shields.io/badge/C%23-239120?style=for-the-badge&logo=csharp&logoColor=white)](https://learn.microsoft.com/dotnet/csharp/)
[![Microsoft Learn](https://img.shields.io/badge/Microsoft%20Learn-258FFA?style=for-the-badge&logo=microsoft&logoColor=white)](https://learn.microsoft.com/)

A C# console application for managing employee records in SQL Server through ADO.NET and stored procedures. It provides employee listing, lookup by ID, insertion, salary updates, and deletion through a menu-driven interface.

## Contents

- [Overview](#overview)
- [Features](#features)
- [Console menu](#console-menu)
- [Application workflow](#application-workflow)
- [ADO.NET implementation](#adonet-implementation)
- [Stored procedure contract](#stored-procedure-contract)
- [Expected database structure](#expected-database-structure)
- [Project structure](#project-structure)
- [Technology stack](#technology-stack)
- [Database configuration](#database-configuration)
- [Build and run](#build-and-run)
- [Implementation scope](#implementation-scope)
- [Source files](#source-files)

## Overview

The application is a database client. `Program.cs` displays a repeating menu and dispatches each operation to a dedicated method. The methods connect to SQL Server using `System.Data.SqlClient`, execute stored procedures, read result sets, and handle SQL exceptions.

The project folder contains the application source, solution and project files, and application configuration. It does not include a database creation script.

## Features

### Display all employees

Calls `sp_GetAllEmployees`, reads the result through `SqlDataReader`, and displays employee ID, name, department, and salary.

### Display employee by ID

Accepts an employee ID and calls `sp_GetEmployeeById` with the `@EmpId` parameter. Any returned employee row is displayed.

### Add employee

Accepts employee name, department, and salary, then calls `sp_AddEmployee` with the corresponding parameters.

### Update employee salary

Accepts an employee ID and new salary, then calls `sp_UpdateEmployeeSalary` with `@EmpId` and `@Salary`.

### Delete employee

Accepts an employee ID and calls `sp_DeleteEmployee`.

## Console menu

```text
1. Display All Employees
2. Display Employee Info By Id
3. Add new Employee
4. Update Employee salary
5. Delete an Employee
6. Exit
```

The menu repeats until the user selects Exit.

## Application workflow

```text
Program.Main()
     |
     v
Console menu
     |
     +-- Display all ----> sp_GetAllEmployees
     |
     +-- Get by ID ------> sp_GetEmployeeById
     |
     +-- Add ------------> sp_AddEmployee
     |
     +-- Update salary --> sp_UpdateEmployeeSalary
     |
     +-- Delete ---------> sp_DeleteEmployee
     |
     v
ADO.NET SqlConnection / SqlCommand
     |
     v
SQL Server
```

## ADO.NET implementation

| ADO.NET type | Use in the application |
|---|---|
| `SqlConnection` | Connects to SQL Server |
| `SqlCommand` | Represents a stored procedure call |
| `CommandType.StoredProcedure` | Marks the command as a stored procedure |
| Parameters | Supplies employee IDs, names, departments, and salary values |
| `SqlDataReader` | Reads employee query results |
| `ExecuteReader()` | Executes retrieval operations |
| `ExecuteNonQuery()` | Executes insert, update, and delete operations |
| `SqlException` | Handles SQL Server exceptions |

Each CRUD method uses `try` / `catch` / `finally`. The connection is closed in the `finally` block after the operation.

## Stored procedure contract

The source expects the following stored procedures to exist in the configured database.

| Stored procedure | Parameters passed by the client | Operation |
|---|---|---|
| `sp_GetAllEmployees` | None | Retrieve all employees |
| `sp_GetEmployeeById` | `@EmpId` | Retrieve one employee |
| `sp_AddEmployee` | `@EmpName`, `@Department`, `@Salary` | Insert an employee |
| `sp_UpdateEmployeeSalary` | `@EmpId`, `@Salary` | Update salary |
| `sp_DeleteEmployee` | `@EmpId` | Delete an employee |

The client reads these result columns:

```text
EmpId
EmpName
Department
Salary
```

## Expected database structure

The source expects an employee table with the following columns:

| Column | Purpose |
|---|---|
| `EmpId` | Employee identifier |
| `EmpName` | Employee name |
| `Department` | Department name |
| `Salary` | Employee salary |

The current source connection string references a database named `DemoDB`.

## Project structure

```text
05_Employee_Management_ADO_CRUD_SP/
├── Properties/
│   └── AssemblyInfo.cs
├── App.config
├── Program.cs
├── Employee_Management_ADO_CRUD_SP.csproj
└── Employee_Management_ADO_CRUD_SP.slnx
```

## Technology stack

| Technology | Usage |
|---|---|
| C# | Console application |
| .NET Framework 4.7.2 | Target framework |
| ADO.NET | SQL Server data access |
| `System.Data.SqlClient` | SQL Server provider |
| SQL Server | Employee record storage |
| Stored procedures | Database operations |
| `SqlDataReader` | Read result sets |

## Database configuration

Before running the application, create the SQL Server database, employee table, and five stored procedures listed above.

The connection string is currently declared in `Program.cs`. It uses a local SQL Server data source, the `DemoDB` database, and integrated security. The connection string must point to the SQL Server instance and database used for the run.

## Build and run

This project targets **.NET Framework 4.7.2** and uses the classic MSBuild project format.

1. Start the SQL Server instance.
2. Create the database, employee table, and required stored procedures.
3. Configure the connection string in `Program.cs`.
4. Open `Employee_Management_ADO_CRUD_SP.slnx` in Visual Studio with the .NET Framework 4.7.2 targeting pack.
5. Build the solution.
6. Run the console application and select an operation.

## Implementation scope

- Employee records are stored in SQL Server, not in an in-memory list.
- All five database operations are called as stored procedures.
- The project folder does not include a `.sql` database setup script.
- The application uses one shared `SqlConnection` object and opens/closes it for each operation.
- SQL exceptions are caught and their messages are written to the console.

## Source files

- [Solution](Employee_Management_ADO_CRUD_SP.slnx)
- [Project file](Employee_Management_ADO_CRUD_SP.csproj)
- [Program and ADO.NET operations](Program.cs)
- [Application configuration](App.config)

## Official documentation

- [Microsoft .NET](https://dotnet.microsoft.com/)
- [C# documentation](https://learn.microsoft.com/dotnet/csharp/)
- [ADO.NET overview](https://learn.microsoft.com/dotnet/framework/data/adonet/)
- [SqlConnection](https://learn.microsoft.com/dotnet/api/system.data.sqlclient.sqlconnection)
- [SqlCommand](https://learn.microsoft.com/dotnet/api/system.data.sqlclient.sqlcommand)
- [SqlDataReader](https://learn.microsoft.com/dotnet/api/system.data.sqlclient.sqldatareader)
- [SQL Server stored procedures](https://learn.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine)
