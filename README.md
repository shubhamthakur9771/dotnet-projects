# .NET Projects Portfolio

[![.NET](https://img.shields.io/badge/.NET-512BD4?style=for-the-badge&logo=dotnet&logoColor=white)](https://dotnet.microsoft.com/)
[![C#](https://img.shields.io/badge/C%23-239120?style=for-the-badge&logo=csharp&logoColor=white)](https://learn.microsoft.com/dotnet/csharp/)
[![Microsoft Learn](https://img.shields.io/badge/Microsoft%20Learn-258FFA?style=for-the-badge&logo=microsoft&logoColor=white)](https://learn.microsoft.com/)

A collection of C# and .NET projects covering console application development, object-oriented programming, collections and LINQ, ADO.NET, SQL Server stored procedures, ASP.NET Core Web APIs, HTTP clients, browser interfaces, and reporting workflows.

The repository is organized into independent project folders. Each project README documents its purpose, implemented features, architecture, data models, technology stack, setup instructions, and links to relevant source files.

## Project directory

| # | Project | Application type | Main areas |
|---:|---|---|---|
| 01 | [Super Market Billing System](01_Super_Market_Billing_System/README.md) | C# console application | Product catalogue, customers, cart, billing, arrays |
| 02 | [Employee Management System — OOP and Sorting](02_Employee_Management_System_Using_OOP/README.md) | C# console application | Login, inheritance, polymorphism, sorting algorithms |
| 03 | [Student Course and Fee Management System](03_Student_Course_And_Fee_Management_System/README.md) | C# console application | Courses, students, fee tracking, LINQ, reports |
| 04 | [Banking Management System](04_Banking_Management_System_Using_OOP/README.md) | C# console application + SQL script | Savings/current accounts, transactions, loans, OOP |
| 05 | [Employee Management — ADO.NET CRUD and Stored Procedures](05_Employee_Management_ADO_CRUD_SP/README.md) | C# console database client | ADO.NET, SQL Server, stored procedures |
| 06 | [Flight Booking — Console API Client](06_Flight_Booking_Management_System_ClientApp/README.md) | C# console API client | `HttpClient`, REST, JSON |
| 07 | [Flight Booking — Web API](07_Flight_Booking_Management_System_WebApp/README.md) | ASP.NET Core Web API + browser UI | .NET 8, ADO.NET, SQL Server, static frontend |
| 08 | [Hospital Appointment and Patient Management System](08_Hospital_Management_System/README.md) | ASP.NET Core Web API | .NET 8, patients, doctors, appointments, reports |
| 09 | [Shipment Intelligence Platform (DSIP)](09_DSIP/README.md) | Multi-project .NET solution | API, console app, data library, dashboard, Python report |

## Technology coverage

### C# and .NET

- C# console applications and menu-driven workflows
- Classes, objects, properties, constructors, and interfaces
- Inheritance, method overriding, and runtime polymorphism
- Arrays and generic collections
- LINQ filtering, grouping, ordering, and aggregation
- Exception handling and input validation
- .NET Framework 4.7.2 projects
- .NET 8 and ASP.NET Core projects

### Data access and databases

- ADO.NET connections, commands, parameters, and data readers
- SQL Server database schemas
- Stored procedure execution
- CRUD operations
- Relational tables, constraints, and foreign keys
- Reporting queries, views, and indexes

### Web APIs and HTTP clients

- ASP.NET Core Web API controllers
- REST endpoint design
- Dependency injection
- Service and repository abstractions
- HTTP GET, POST, PUT, PATCH, and DELETE
- `HttpClient`
- JSON serialization and deserialization
- Swagger / OpenAPI integration

### Browser interfaces and reporting

- HTML, CSS, and JavaScript
- Browser-to-API communication
- Dashboard metrics and tabular data
- Python API consumption
- Daily operational summaries
- CSV export

## Repository structure

```text
11_Dotnet-Projects/
├── 01_Super_Market_Billing_System/
├── 02_Employee_Management_System_Using_OOP/
├── 03_Student_Course_And_Fee_Management_System/
├── 04_Banking_Management_System_Using_OOP/
├── 05_Employee_Management_ADO_CRUD_SP/
├── 06_Flight_Booking_Management_System_ClientApp/
├── 07_Flight_Booking_Management_System_WebApp/
├── 08_Hospital_Management_System/
└── 09_DSIP/
```

## Framework versions

| Target framework | Projects |
|---|---|
| .NET Framework 4.7.2 | Projects 01, 02, 03, 04, 05, and 06 |
| .NET 8 | Projects 07, 08, and all three .NET projects in 09 |

The classic .NET Framework projects use MSBuild project files and a compatible Visual Studio/.NET Framework targeting pack. The .NET 8 projects use SDK-style project files and the .NET SDK.

## Working with the projects

Each project has its own solution or project file. Open the relevant project folder and follow the setup instructions in its README.

- For a console application, open its `.slnx` solution in Visual Studio or build it with a compatible toolchain.
- For a Web API, restore packages, configure its database connection, prepare the SQL database, and run the API.
- For an API client, start the corresponding API and ensure that the client base address matches the API's listening address.
- For projects with SQL scripts, execute the database script in SQL Server before starting database-backed operations.

## Project documentation

- [01 — Super Market Billing System](01_Super_Market_Billing_System/README.md)
- [02 — Employee Management System](02_Employee_Management_System_Using_OOP/README.md)
- [03 — Student Course and Fee Management](03_Student_Course_And_Fee_Management_System/README.md)
- [04 — Banking Management System](04_Banking_Management_System_Using_OOP/README.md)
- [05 — Employee ADO.NET CRUD](05_Employee_Management_ADO_CRUD_SP/README.md)
- [06 — Flight Booking Console Client](06_Flight_Booking_Management_System_ClientApp/README.md)
- [07 — Flight Booking Web API](07_Flight_Booking_Management_System_WebApp/README.md)
- [08 — Hospital Management System](08_Hospital_Management_System/README.md)
- [09 — Shipment Intelligence Platform](09_DSIP/README.md)

## Official technology documentation

- [Microsoft .NET](https://dotnet.microsoft.com/)
- [C# documentation](https://learn.microsoft.com/dotnet/csharp/)
- [ASP.NET Core documentation](https://learn.microsoft.com/aspnet/core/)
- [SQL Server documentation](https://learn.microsoft.com/sql/sql-server/)
- [ADO.NET documentation](https://learn.microsoft.com/dotnet/framework/data/adonet/)
- [Visual Studio](https://visualstudio.microsoft.com/)
- [Microsoft Learn](https://learn.microsoft.com/)
