# Shipment Intelligence Platform (DSIP)

[![.NET](https://img.shields.io/badge/.NET-512BD4?style=for-the-badge&logo=dotnet&logoColor=white)](https://dotnet.microsoft.com/)
[![C#](https://img.shields.io/badge/C%23-239120?style=for-the-badge&logo=csharp&logoColor=white)](https://learn.microsoft.com/dotnet/csharp/)
[![Microsoft Learn](https://img.shields.io/badge/Microsoft%20Learn-258FFA?style=for-the-badge&logo=microsoft&logoColor=white)](https://learn.microsoft.com/)

A multi-component shipment management and reporting solution built with .NET 8, ASP.NET Core Web API, ADO.NET, SQL Server, a browser dashboard, a console application, and a Python reporting utility. The solution separates the HTTP API, data-access library, console workflow, frontend dashboard, and end-of-day report.

## Contents

- [Overview](#overview)
- [Functional modules](#functional-modules)
- [Shipment statuses](#shipment-statuses)
- [REST API reference](#rest-api-reference)
- [Database design](#database-design)
- [API and data architecture](#api-and-data-architecture)
- [Console application](#console-application)
- [Browser dashboard](#browser-dashboard)
- [Python end-of-day report](#python-end-of-day-report)
- [GenAI development artefacts](#genai-development-artefacts)
- [Solution structure](#solution-structure)
- [Technology stack](#technology-stack)
- [Configuration](#configuration)
- [Build and run](#build-and-run)
- [Implementation boundaries](#implementation-boundaries)
- [Source files](#source-files)

## Overview

The Shipment Intelligence Platform manages shipment records identified by an AWB number. The `DSIP.slnx` solution contains three .NET projects:

- **Delhivery.API** — ASP.NET Core Web API exposing shipment operations.
- **Delhivery.Data** — shipment models, repository interfaces, SQL Server repository implementation, and a typed not-found exception.
- **Delhivery.Console** — menu-driven shipment workflow with its own in-memory repository implementation.

The folder also includes a SQL Server database script, a browser dashboard, a Python reporting utility, and GenAI development notes.

## Functional modules

### Shipment booking

A shipment record contains:

- AWB number
- Sender name
- Receiver name
- Origin
- Destination
- Weight in kilograms
- Status
- Booking timestamp
- Delivery timestamp, when available

The API service validates required text fields, requires a positive weight, trims input strings, checks whether the AWB already exists, and then inserts the shipment through the data repository.

### Shipment listing and tracking

- Retrieve all shipment records.
- Retrieve one shipment by AWB number.
- Display route, sender, receiver, weight, status, and booking information.
- Return a not-found response when the AWB does not exist.

### Status updates

The API accepts a new status and validates it against the supported values before calling the repository.

The SQL procedure updates the status and sets `DeliveredAt` to the current database time when the new status is `Delivered`. For other statuses, the procedure sets `DeliveredAt` to `NULL`.

### Shipment cancellation

The API accepts a shipment ID and deletes the corresponding row from `Shipments`. It returns a no-content response after successful deletion and a not-found response when no row is affected.

### Dashboard statistics

The API returns counts for:

- Total shipments
- Booked
- In Transit
- Out for Delivery
- Delivered
- RTO

## Shipment statuses

The database constrains the status field to these values:

| Status | Meaning |
|---|---|
| `Booked` | Shipment has been booked |
| `In Transit` | Shipment is moving through the network |
| `Out for Delivery` | Shipment is in the delivery stage |
| `Delivered` | Shipment has been delivered |
| `RTO` | Shipment is marked return-to-origin |

These values are used by the API statistics response and dashboard KPI cards.

## REST API reference

The controller route prefix is `/api/Shipments`.

| Method | Endpoint | Controller action | Description |
|---|---|---|---|
| GET | `/api/Shipments` | `GetAll()` | Returns all shipments |
| GET | `/api/Shipments/{awb}` | `GetByAWB()` | Retrieves a shipment by AWB |
| POST | `/api/Shipments` | `Book()` | Books a shipment |
| PUT | `/api/Shipments/{awb}/status` | `UpdateStatus()` | Updates shipment status |
| DELETE | `/api/Shipments/{shipmentId}` | `Cancel()` | Deletes a shipment by ID |
| GET | `/api/Shipments/stats` | `GetStats()` | Returns status counts |

### Booking request

`BookShipmentRequest` contains the AWB number, sender name, receiver name, origin, destination, and weight.

### Status update request

`UpdateStatusRequest` contains the requested `Status`. The service checks that it belongs to the supported status set.

### Shipment response

`ShipmentResponse` includes shipment ID, AWB number, sender, receiver, origin, destination, weight, status, booking timestamp, and delivery timestamp.

## Database design

`Database/DelhiveryDB.sql` creates the `DelhiveryDB` database when it does not exist and defines the `dbo.Shipments` table.

| Column | SQL definition / rule | Purpose |
|---|---|---|
| `ShipmentId` | `INT IDENTITY(101,1)` primary key | Internal shipment identifier |
| `AWBNumber` | `NVARCHAR(20)`, unique, not null | Tracking number |
| `SenderName` | `NVARCHAR(100)`, not null | Sender |
| `ReceiverName` | `NVARCHAR(100)`, not null | Receiver |
| `Origin` | `NVARCHAR(100)`, not null | Origin location |
| `Destination` | `NVARCHAR(100)`, not null | Destination location |
| `WeightKg` | `DECIMAL(8,2)`, positive check | Shipment weight |
| `Status` | `NVARCHAR(30)`, constrained values | Current status |
| `BookedAt` | `DATETIME`, default `GETDATE()` | Booking timestamp |
| `DeliveredAt` | Nullable `DATETIME` | Delivery timestamp |

### Stored procedures

| Procedure | Purpose |
|---|---|
| `usp_GetAllShipments` | Retrieves shipments ordered by booking time |
| `usp_GetShipmentByAWB` | Retrieves one shipment by AWB |
| `usp_UpdateShipmentStatus` | Validates and updates status and delivery timestamp |

The SQL script also defines the `vw_ShipmentDashboard` view and a nonclustered index named `IX_Shipments_Status` on the status column, including selected shipment fields.

### Seed data

The SQL script inserts example shipment rows when the table is empty. The sample rows cover the supported shipment statuses and provide initial records for local dashboard and reporting use.

## API and data architecture

```text
Browser dashboard
       |
       | HTTP
       v
Delhivery.API
       |
       +-- ShipmentsController
       |
       +-- IShipmentService / ShipmentService
       |
       v
Delhivery.Data
       |
       +-- IShipmentRepository
       |
       +-- ShipmentRepository
       |
       v
ADO.NET / Microsoft.Data.SqlClient
       |
       v
SQL Server: DelhiveryDB
```

The API registers its service and repository dependencies in `Program.cs`. The data project contains SQL Server data access and `ShipmentNotFoundException`.

## Console application

The console project provides a separate shipment workflow.

### Menu options

- Book Shipment
- List Shipments
- Update Status
- Cancel Shipment
- Exit

### Booking input validation

The console workflow validates:

- AWB number is required, alphanumeric, and no longer than 15 characters.
- Sender and receiver names are required, contain letters and spaces, and meet configured length limits.
- Origin and destination are required and accept letters, spaces, and hyphens.
- Origin and destination cannot be the same.
- Weight must be numeric, greater than zero, and no more than 5,000 kg.

The status menu presents the five supported shipment status values.

### Console architecture

```text
Program
  |
  v
App
  |
  +-- GUIMenu
  +-- ConsoleHelper
  +-- AsciiTitle
  |
  v
IShipmentService / ShipmentService
  |
  v
IShipmentRepository / ShipmentRepository
  |
  v
In-memory List<Shipment>
```

The console repository maintains its own `List<Shipment>`, generates in-memory IDs, and sets booking timestamps when a shipment is added.

## Browser dashboard

The `UI` directory contains the dashboard page, JavaScript, and stylesheet.

### Dashboard interface elements

- Shipment overview.
- KPI cards for total shipments and each status.
- Shipment booking form.
- AWB tracking input and result area.
- Shipment table.
- Status filter.
- Text search.
- Refresh controls.
- Loading overlay.
- Toast notifications.
- Current date/time and last-updated display.

### Browser/API communication

`UI/app.js` defines helper methods for GET, POST, PUT, and DELETE requests. It uses the Fetch API, JSON request bodies, and response-status checks to communicate with the shipment API.

The API base URL is declared near the beginning of `app.js` and must match the running API address.

## Python end-of-day report

`Python/reports.py` consumes the shipment API through the Python `requests` library.

### Report workflow

1. Send a GET request to the shipments endpoint.
2. Stop with an error message if the API request fails.
3. Filter shipments to records whose `bookedAt` date matches the current date.
4. Count records by shipment status.
5. Calculate average shipment weight.
6. Identify the heaviest shipment and its AWB number.
7. Print a formatted end-of-day report.
8. Optionally export the filtered records to CSV.

### Report metrics

| Metric | Calculation |
|---|---|
| Total shipments | Number of today's shipment records |
| Booked | Count with status `Booked` |
| In Transit | Count with status `In Transit` |
| Out for Delivery | Count with status `Out for Delivery` |
| Delivered | Count with status `Delivered` |
| RTO | Count with status `RTO` |
| Average weight | Total weight divided by today's shipment count |
| Heaviest shipment | Record with the maximum weight |

### Run the report

```bash
cd Python
python -m pip install requests
python reports.py
```

To export the filtered records to CSV:

```bash
python reports.py --export
```

The output filename includes the current report date.

## GenAI development artefacts

The `GenAI` directory contains three development records.

| File | Contents |
|---|---|
| `COPILOT_LOG.md` | Selected Copilot suggestions, implementation decisions, and explanations |
| `PROMPT_LOG.md` | Prompt iterations for finding shipments in transit for more than three days |
| `REFLECTION.md` | Notes on reviewing and validating AI-generated code against requirements |

## Solution structure

```text
09_DSIP/
├── Database/
│   └── DelhiveryDB.sql
├── Delhivery.API/
│   ├── Controllers/
│   │   └── ShipmentController.cs
│   ├── DTOs/
│   │   ├── BookShipmentRequest.cs
│   │   ├── ShipmentResponse.cs
│   │   ├── ShipmentStatsDto.cs
│   │   └── UpdateStatusRequest.cs
│   ├── Services/
│   │   ├── IShipmentService.cs
│   │   └── ShipmentService.cs
│   ├── Properties/
│   │   └── launchSettings.json
│   ├── appsettings.json
│   ├── Delhivery.API.csproj
│   └── Program.cs
├── Delhivery.Console/
│   ├── Models/
│   │   └── Shipment.cs
│   ├── Presentation/
│   │   ├── App.cs
│   │   └── Templates/
│   │       ├── AsciiTitle.cs
│   │       ├── ConsoleHelper.cs
│   │       └── GUIMenu.cs
│   ├── Repository/
│   │   ├── IShipmentRepository.cs
│   │   └── ShipmentRepository.cs
│   ├── Services/
│   │   ├── IShipmentService.cs
│   │   └── ShipmentService.cs
│   ├── Delhivery.Console.csproj
│   └── Program.cs
├── Delhivery.Data/
│   ├── Exceptions/
│   │   └── ShipmentNotFoundException.cs
│   ├── Models/
│   │   └── Shipment.cs
│   ├── Repository/
│   │   ├── IShipmentRepository.cs
│   │   └── ShipmentRepository.cs
│   └── Delhivery.Data.csproj
├── GenAI/
│   ├── COPILOT_LOG.md
│   ├── PROMPT_LOG.md
│   └── REFLECTION.md
├── Python/
│   └── reports.py
├── UI/
│   ├── index.html
│   ├── app.js
│   └── styles.css
└── DSIP.slnx
```

## Technology stack

| Technology | Usage |
|---|---|
| C# | API, console, and data library |
| .NET 8 | Target framework |
| ASP.NET Core Web API | Shipment HTTP API |
| ADO.NET | SQL Server data access |
| `Microsoft.Data.SqlClient` | SQL Server provider |
| SQL Server | Shipment persistence |
| Stored procedures | Shipment retrieval and status updates |
| HTML / CSS / JavaScript | Browser dashboard |
| Fetch API | Browser-to-API requests |
| Python | End-of-day reporting |
| `requests` | Python HTTP client |
| CSV | Optional report export |

## Configuration

### API database connection

The API reads `DefaultConnection` from its application configuration. Set it to the SQL Server instance hosting `DelhiveryDB`.

### Browser API base URL

The dashboard API base URL is defined in `UI/app.js`. It must point to the running API host.

### Python API base URL

The Python report defines `BASE_URL` near the top of `Python/reports.py`. It must match the running API host and shipments route.

## Build and run

All three .NET projects target **.NET 8**.

### 1. Prepare the database

Execute `Database/DelhiveryDB.sql` in SQL Server.

### 2. Run the API

```bash
cd Delhivery.API
dotnet restore
dotnet build
dotnet run
```

### 3. Run the browser dashboard

Start the API, set the API base URL in `UI/app.js`, and open `UI/index.html` in a browser.

### 4. Run the console application

```bash
cd Delhivery.Console
dotnet restore
dotnet run
```

### 5. Run the Python report

With the API running:

```bash
cd Python
python -m pip install requests
python reports.py
```

Use `python reports.py --export` to create a CSV report.

## Implementation boundaries

- The API uses the SQL Server repository in `Delhivery.Data`.
- The console application contains a separate in-memory repository implementation. Its console data operations do not use the SQL Server repository.
- The SQL script defines three stored procedures. Shipment booking and cancellation in the SQL Server repository are implemented with parameterized SQL commands.
- The dashboard and Python report communicate with the API through their configured base URLs.
- The Python report filters records using the `bookedAt` date returned by the API.

## Source files

- [Solution](DSIP.slnx)
- [Database script](Database/DelhiveryDB.sql)
- [API project](Delhivery.API/Delhivery.API.csproj)
- [API startup](Delhivery.API/Program.cs)
- [Shipment controller](Delhivery.API/Controllers/ShipmentController.cs)
- [Shipment service](Delhivery.API/Services/ShipmentService.cs)
- [Booking request DTO](Delhivery.API/DTOs/BookShipmentRequest.cs)
- [Shipment response DTO](Delhivery.API/DTOs/ShipmentResponse.cs)
- [Statistics DTO](Delhivery.API/DTOs/ShipmentStatsDto.cs)
- [Status request DTO](Delhivery.API/DTOs/UpdateStatusRequest.cs)
- [Data project](Delhivery.Data/Delhivery.Data.csproj)
- [SQL repository](Delhivery.Data/Repository/ShipmentRepository.cs)
- [Console project](Delhivery.Console/Delhivery.Console.csproj)
- [Console workflow](Delhivery.Console/Presentation/App.cs)
- [Dashboard page](UI/index.html)
- [Dashboard JavaScript](UI/app.js)
- [Dashboard stylesheet](UI/styles.css)
- [Python report](Python/reports.py)
- [Copilot usage log](GenAI/COPILOT_LOG.md)
- [Prompt log](GenAI/PROMPT_LOG.md)
- [Development reflection](GenAI/REFLECTION.md)

## Official documentation

- [Microsoft .NET](https://dotnet.microsoft.com/)
- [ASP.NET Core](https://learn.microsoft.com/aspnet/core/)
- [C# documentation](https://learn.microsoft.com/dotnet/csharp/)
- [ADO.NET](https://learn.microsoft.com/dotnet/framework/data/adonet/)
- [Microsoft.Data.SqlClient](https://learn.microsoft.com/sql/connect/ado-net/introduction-microsoft-data-sqlclient-namespace)
- [SQL Server](https://learn.microsoft.com/sql/sql-server/)
- [HTML](https://developer.mozilla.org/docs/Web/HTML)
- [CSS](https://developer.mozilla.org/docs/Web/CSS)
- [JavaScript](https://developer.mozilla.org/docs/Web/JavaScript)
- [Python](https://docs.python.org/3/)
- [Requests library](https://requests.readthedocs.io/)
