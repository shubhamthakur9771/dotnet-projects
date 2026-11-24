# Banking Management System — Object-Oriented C# Application

[![.NET](https://img.shields.io/badge/.NET-512BD4?style=for-the-badge&logo=dotnet&logoColor=white)](https://dotnet.microsoft.com/)
[![C#](https://img.shields.io/badge/C%23-239120?style=for-the-badge&logo=csharp&logoColor=white)](https://learn.microsoft.com/dotnet/csharp/)
[![Microsoft Learn](https://img.shields.io/badge/Microsoft%20Learn-258FFA?style=for-the-badge&logo=microsoft&logoColor=white)](https://learn.microsoft.com/)

A C# console application that models customers, savings accounts, current accounts, deposits, withdrawals, transfers, balances, and loans. The project demonstrates inheritance, method overriding, interfaces, static members, generic collections, and account-specific withdrawal rules. A separate SQL Server script provides a relational banking schema and stored procedures.

## Contents

- [Overview](#overview)
- [Application capabilities](#application-capabilities)
- [Customer management](#customer-management)
- [Account types](#account-types)
- [Transactions and transfers](#transactions-and-transfers)
- [Loan management](#loan-management)
- [Object-oriented design](#object-oriented-design)
- [SQL Server database script](#sql-server-database-script)
- [Architecture](#architecture)
- [Project structure](#project-structure)
- [Technology stack](#technology-stack)
- [Build and run](#build-and-run)
- [Implementation scope](#implementation-scope)
- [Source files](#source-files)

## Overview

The folder contains two separate banking artefacts:

1. A console application using `BankService` and in-memory collections.
2. `BankDB.sql`, a SQL Server script that defines database tables and stored procedures for banking operations.

The C# console application does not call the SQL stored procedures. The SQL script is independently available for database setup and inspection.

## Application capabilities

The console application supports:

- Adding and viewing customers.
- Creating savings accounts.
- Creating current accounts.
- Depositing funds.
- Withdrawing funds.
- Checking account balances.
- Transferring funds between accounts.
- Applying for loans.
- Viewing loans and calculated interest.

## Customer management

The `Customer` model contains:

- `CustomerId`
- `Name`
- `City`

`BankService.AddCustomer()` reads these values and checks the current customer collection for a duplicate ID. `Customer.TotalCustomers` is a static field incremented by the customer constructor. The view operation displays customer details and the static count.

## Account types

### Account base class

`Account` implements `IAccount` and contains:

- `AccountNumber`
- `Balance`
- `Transactions`

It provides `Deposit()`, `Withdraw()`, `ShowBalance()`, and `ShowTransactions()` methods.

The base withdrawal operation checks that the requested amount does not exceed the current balance before reducing the balance.

### Savings account

`SavingsAccount` inherits from `Account` and adds `InterestRate`.

- The current `BankService` creates savings accounts with a 5% interest rate.
- `Withdraw()` is overridden to maintain a minimum balance of 500.
- `AddInterest()` calculates interest from the current balance and adds it to the balance.
- Interest additions are recorded in the account transaction list.

```text
Interest = Balance × Interest Rate / 100
```

### Current account

`CurrentAccount` inherits from `Account` and adds `OverdraftLimit`.

- The current `BankService` creates current accounts with an overdraft limit of 1,000.
- `Withdraw()` is overridden to allow withdrawals up to the balance plus overdraft limit.
- A withdrawal that exceeds the allowed amount is rejected.

```text
Maximum withdrawal = Balance + Overdraft Limit
```

## Transactions and transfers

The account model keeps transaction descriptions in a `List<string>`. Successful deposits and withdrawals append transaction entries. Savings-account interest additions also append an entry.

The `Transfer()` operation:

1. Reads the source account number.
2. Reads the destination account number.
3. Reads the transfer amount.
4. Looks up both accounts.
5. Calls `Withdraw()` on the source account and `Deposit()` on the destination account when both accounts are found.

The source account's runtime type determines which withdrawal rule is applied.

## Loan management

The `Loan` model contains:

- `LoanId`
- `CustomerId`
- `LoanAmount`
- `InterestRate`

Its `CalculateInterest()` method calculates a percentage of the loan amount.

```text
Interest = Loan Amount × Interest Rate / 100
```

`BankService.ApplyLoan()` reads the loan ID, customer ID, and amount, then creates the loan using a 10% interest rate. `ViewLoans()` displays the loan ID, customer ID, amount, and calculated interest.

## Object-oriented design

| Concept | Implementation |
|---|---|
| Classes and objects | `Customer`, `Account`, `SavingsAccount`, `CurrentAccount`, `Loan` |
| Encapsulation | Properties and account methods |
| Inheritance | Savings and current accounts derive from `Account` |
| Method overriding | Both derived account types override `Withdraw()` |
| Runtime polymorphism | Base `Account` references can refer to derived account objects |
| Interface | `IAccount` defines common account operations |
| Static member | `Customer.TotalCustomers` |
| Generic collections | `List<Customer>`, `List<Account>`, `List<Loan>`, and transaction lists |

## Console menu

```text
1.  Add Customer
2.  View Customers
3.  Create Account
4.  Deposit
5.  Withdraw
6.  Check Balance
7.  Transfer
8.  Apply Loan
9.  View Loans
10. Exit
```

## SQL Server database script

`BankDB.sql` is a separate SQL Server implementation. It creates three tables.

| Table | Purpose |
|---|---|
| `Customers` | Customer profile and account-related information |
| `Transactions` | Transaction records |
| `Admins` | Administrator credentials |

### Authentication and registration procedures

- `sp_GetAdminCredentials`
- `sp_GetCustomerCredentials`
- `sp_register`

### Customer maintenance and lookup procedures

- `sp_AddCustomer`
- `sp_GetMaxId`
- `sp_UpdateCustomerMobileNumber`
- `sp_UpdateCustomerEmail`
- `sp_UpdateCustomerAddress`
- `sp_UpdateCustomerAccountType`
- `sp_UpdateCustomerAccountStatus`
- `sp_DeleteCustomer`
- `sp_GetAllCustomers`
- `sp_GetCustomerByAccountNumber`
- `sp_GetCustomerByMobileNumber`
- `sp_GetCustomerByAccountType`

### Transaction and account procedures

- `sp_GetAllTransactions`
- `sp_Deposit`
- `sp_Withdraw`
- `sp_FundTransfer`
- `sp_GetBalance`
- `sp_GetMiniStatement`
- `sp_UpdateCustomerPassword`

These procedures are defined in the SQL script; the console `BankService` does not invoke them.

## Architecture

### Console application

```text
Program
  |
  v
BankService
  |
  +-- List<Customer>
  +-- List<Account>
  +-- List<Loan>
  |
  +-- Account
       |
       +-- SavingsAccount
       |
       +-- CurrentAccount
```

### Separate SQL Server artefact

```text
SQL Server
  |
  +-- Customers
  +-- Transactions
  +-- Admins
  |
  +-- Stored procedures
       +-- Authentication and registration
       +-- Customer maintenance and lookup
       +-- Deposits and withdrawals
       +-- Fund transfer and balance lookup
       +-- Transaction history
```

## Project structure

```text
04_Banking_Management_System_Using_OOP/
├── Interfaces/
│   └── IAccount.cs
├── Models/
│   ├── Account.cs
│   ├── CurrentAccount.cs
│   ├── Customer.cs
│   ├── Loan.cs
│   └── SavingsAccount.cs
├── Properties/
│   └── AssemblyInfo.cs
├── Services/
│   └── BankService.cs
├── App.config
├── BankDB.sql
├── Banking_Management_System_Using_OOP.csproj
└── Banking_Management_System_Using_OOP.slnx
```

## Technology stack

| Technology / concept | Usage |
|---|---|
| C# | Console application |
| .NET Framework 4.7.2 | Project target |
| OOP | Account and customer model |
| Inheritance and overriding | Savings and current account behaviour |
| Interfaces | Common account contract |
| `List<T>` | In-memory application records |
| SQL Server | Separate database script |
| T-SQL | Table and stored procedure definitions |

## Build and run

### Console application

1. Open `Banking_Management_System_Using_OOP.slnx` in Visual Studio with the .NET Framework 4.7.2 targeting pack.
2. Build the solution.
3. Run the console application.
4. Create customers and accounts before performing account operations.

### Database script

Open `BankDB.sql` in SQL Server Management Studio or another SQL Server query tool and execute it against a SQL Server instance.

## Implementation scope

- The console application's customers, accounts, loans, and transaction descriptions are stored in memory.
- The console application does not connect to the SQL Server script.
- `AddInterest()` exists on `SavingsAccount`, but the current console menu does not expose a separate interest operation.
- Accounts are created using account number and initial balance; the current account model does not store a customer ID.
- The SQL script provides a separate relational implementation with stored procedures.

## Source files

- [Solution](Banking_Management_System_Using_OOP.slnx)
- [Project file](Banking_Management_System_Using_OOP.csproj)
- [Program entry point](Program.cs)
- [Account interface](Interfaces/IAccount.cs)
- [Account base class](Models/Account.cs)
- [Savings account](Models/SavingsAccount.cs)
- [Current account](Models/CurrentAccount.cs)
- [Customer model](Models/Customer.cs)
- [Loan model](Models/Loan.cs)
- [Bank service](Services/BankService.cs)
- [Database script](BankDB.sql)

## Official documentation

- [Microsoft .NET](https://dotnet.microsoft.com/)
- [C# documentation](https://learn.microsoft.com/dotnet/csharp/)
- [Object-oriented programming in C#](https://learn.microsoft.com/dotnet/csharp/fundamentals/object-oriented/)
- [Interfaces](https://learn.microsoft.com/dotnet/csharp/fundamentals/types/interfaces)
- [SQL Server](https://learn.microsoft.com/sql/sql-server/)
- [T-SQL language reference](https://learn.microsoft.com/sql/t-sql/language-reference)
