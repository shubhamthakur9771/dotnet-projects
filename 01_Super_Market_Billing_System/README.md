# Super Market Billing System

[![.NET](https://img.shields.io/badge/.NET-512BD4?style=for-the-badge&logo=dotnet&logoColor=white)](https://dotnet.microsoft.com/)
[![C#](https://img.shields.io/badge/C%23-239120?style=for-the-badge&logo=csharp&logoColor=white)](https://learn.microsoft.com/dotnet/csharp/)
[![Microsoft Learn](https://img.shields.io/badge/Microsoft%20Learn-258FFA?style=for-the-badge&logo=microsoft&logoColor=white)](https://learn.microsoft.com/)

A menu-driven C# console application for supermarket product management, customer records, shopping-cart operations, and bill generation. The application separates console navigation, data models, and business operations into menu, model, and service classes.

## Contents

- [Overview](#overview)
- [Features](#features)
- [Application workflow](#application-workflow)
- [Architecture and components](#architecture-and-components)
- [Data models](#data-models)
- [Business rules and calculations](#business-rules-and-calculations)
- [Technology stack](#technology-stack)
- [Project structure](#project-structure)
- [Build and run](#build-and-run)
- [Implementation scope](#implementation-scope)
- [Source files](#source-files)

## Overview

The application provides two functional areas:

- **Admin Menu:** manages the product catalogue, including product creation, listing, updates, deletion, searching, and low-stock identification.
- **User Menu:** manages customer records and the shopping cart, and generates bills.

`Program.Main()` creates one `ProductService`, one `CustomerService`, and one `CartService`. The cart service receives the product service so that it can look up products and update stock after a bill is generated. The menu classes receive their required service instances through constructors.

## Features

### Product catalogue

The `ProductService` stores products in a fixed-size array with a capacity of 100.

| Operation | Description |
|---|---|
| Add product | Captures product code, name, price, and quantity |
| View products | Displays code, name, unit price, and quantity |
| Find by code | Returns the matching product object |
| Update product | Changes the selected product's price and quantity |
| Delete product | Removes the product and shifts later array elements |
| Search by name | Performs a case-insensitive partial-name search |
| Search by price | Lists products within a minimum and maximum price |
| Low-stock listing | Lists products with quantity below 5 |

The add operation checks the product array capacity and rejects duplicate product codes.

### Customer management

The `CustomerService` stores up to 50 customers in a fixed-size array.

- Add a customer using an ID and name.
- View all stored customers.
- Update a customer's name using the customer ID.
- Delete a customer using the customer ID.
- Compact the active array entries after deletion.

### Cart management

The `CartService` stores up to 50 cart lines.

- Add a product by product code.
- Resolve product details through `ProductService`.
- Reject a product code that cannot be found.
- Validate requested quantity against available stock.
- Remove a cart line by product code.
- Display each cart line and its calculated amount.

### Bill generation

The bill displays product name, quantity, unit price, and line amount. It calculates a total across all cart lines, reduces the corresponding product quantities, and resets the cart count after the bill is printed.

## Application workflow

```text
Program
  |
  +-- ProductService --------------------+
  |       |                              |
  |       +-- AdminMenu                  |
  |             +-- Add / View           |
  |             +-- Update / Delete      |
  |             +-- Search / Low stock   |
  |                                      |
  +-- CustomerService                    |
  |       +-- Customer CRUD              |
  |                                      |
  +-- CartService <----------------------+
          +-- Add / Remove cart item
          +-- View cart
          +-- Generate bill
          +-- Reduce product stock
```

## Architecture and components

| Component | Responsibility |
|---|---|
| `Program` | Creates services and menu objects and starts the main menu |
| `MainMenu` | Routes to the admin or user menu |
| `AdminMenu` | Presents product-management operations |
| `UserMenu` | Presents customer and cart operations |
| `Product` | Stores product code, name, price, and quantity |
| `Customer` | Stores customer ID and name |
| `CartItem` | Stores product details and cart quantity |
| `ProductService` | Product CRUD, lookup, search, and stock queries |
| `CustomerService` | Customer CRUD operations |
| `CartService` | Cart operations, billing, and stock reduction |
| `Helper` | Utility class in the `Utilities` folder |

## Data models

### Product

| Property | Meaning |
|---|---|
| `ProductCode` | Product identifier used for lookup |
| `Name` | Product name |
| `Price` | Unit price |
| `Quantity` | Available stock quantity |

### Customer

| Property | Meaning |
|---|---|
| `CustomerId` | Customer identifier |
| `Name` | Customer name |

### CartItem

| Property | Meaning |
|---|---|
| `ProductCode` | Reference to the selected product |
| `Name` | Product name captured in the cart |
| `Price` | Unit price captured in the cart |
| `Quantity` | Quantity requested |
| `Amount` | Calculated line amount |

## Business rules and calculations

```text
Line Amount = Unit Price × Quantity
Bill Total  = Sum of all cart line amounts
```

- Product storage is limited to 100 entries.
- Customer storage is limited to 50 entries.
- Cart storage is limited to 50 entries.
- Product codes are checked for duplicates during product creation.
- Cart quantity is checked against the product's current quantity.
- Products with stock below 5 appear in the low-stock listing.
- Stock is reduced when the bill is generated.
- The cart is cleared after bill generation.

## Technology stack

| Technology / concept | Usage |
|---|---|
| C# | Application language |
| .NET Framework 4.7.2 | Target framework in the project file |
| Console application | Menu-driven interface |
| Classes and objects | Product, customer, and cart models |
| Arrays | Fixed-capacity in-memory storage |
| CRUD | Product and customer operations |
| Constructor injection | Cart service receives ProductService |
| Loops and conditionals | Menu navigation and array operations |

## Project structure

```text
01_Super_Market_Billing_System/
├── Menus/
│   ├── AdminMenu.cs
│   ├── MainMenu.cs
│   └── UserMenu.cs
├── Models/
│   ├── CartItem.cs
│   ├── Customer.cs
│   └── Product.cs
├── Properties/
│   └── AssemblyInfo.cs
├── Services/
│   ├── CartService.cs
│   ├── CustomerService.cs
│   └── ProductService.cs
├── Utilities/
│   └── Helper.cs.cs
├── App.config
├── Program.cs
├── Super_Market_Billing_System.csproj
└── Super_Market_Billing_System.slnx
```

## Build and run

The project targets **.NET Framework 4.7.2** and uses the classic MSBuild project format.

1. Open `Super_Market_Billing_System.slnx` in Visual Studio with the .NET Framework 4.7.2 targeting pack.
2. Build the solution.
3. Start the console application.
4. Use the main menu to enter the admin or user workflow.

## Implementation scope

- Product, customer, and cart records are held in memory.
- No SQL Server database or external persistence layer is included in this project.
- Records are not retained after the application process exits.
- The same `ProductService` instance is shared between the admin menu and cart service.
- Product stock is adjusted during bill generation.

## Source files

- [Solution](Super_Market_Billing_System.slnx)
- [Project file](Super_Market_Billing_System.csproj)
- [Application entry point](Program.cs)
- [Main menu](Menus/MainMenu.cs)
- [Admin menu](Menus/AdminMenu.cs)
- [User menu](Menus/UserMenu.cs)
- [Product model](Models/Product.cs)
- [Customer model](Models/Customer.cs)
- [Cart item model](Models/CartItem.cs)
- [Product service](Services/ProductService.cs)
- [Customer service](Services/CustomerService.cs)
- [Cart service](Services/CartService.cs)

## Official documentation

- [Microsoft .NET](https://dotnet.microsoft.com/)
- [C# documentation](https://learn.microsoft.com/dotnet/csharp/)
- [Classes and objects](https://learn.microsoft.com/dotnet/csharp/fundamentals/types/classes)
- [Arrays in C#](https://learn.microsoft.com/dotnet/csharp/language-reference/builtin-types/arrays)
