# Flight Booking Management System — ASP.NET Core Web API

[![.NET](https://img.shields.io/badge/.NET-512BD4?style=for-the-badge&logo=dotnet&logoColor=white)](https://dotnet.microsoft.com/)
[![C#](https://img.shields.io/badge/C%23-239120?style=for-the-badge&logo=csharp&logoColor=white)](https://learn.microsoft.com/dotnet/csharp/)
[![Microsoft Learn](https://img.shields.io/badge/Microsoft%20Learn-258FFA?style=for-the-badge&logo=microsoft&logoColor=white)](https://learn.microsoft.com/)

An ASP.NET Core Web API for managing flight records in SQL Server. The application uses a controller, service abstraction, repository abstraction, ADO.NET, and stored procedures. It also includes a browser interface served from the application's `wwwroot` directory.

## Contents

- [Overview](#overview)
- [Features](#features)
- [Architecture](#architecture)
- [API reference](#api-reference)
- [Flight data model](#flight-data-model)
- [Database design](#database-design)
- [Stored procedures](#stored-procedures)
- [Browser interface](#browser-interface)
- [Project structure](#project-structure)
- [Technology stack](#technology-stack)
- [Configuration](#configuration)
- [Build and run](#build-and-run)
- [Related project](#related-project)
- [Source files](#source-files)

## Overview

The application exposes create, read, update, and delete operations for flight records. HTTP requests are handled by `FlightController`, forwarded to the service layer, and executed by the repository through ADO.NET.

The repository invokes SQL Server stored procedures. The same ASP.NET Core application also serves the static browser files under `wwwroot`.

## Features

### Flight management API

- Retrieve all flight records.
- Retrieve a flight by ID.
- Add a flight.
- Update a flight.
- Delete a flight.
- Return HTTP responses for successful operations, empty collections, missing records, and invalid requests.

### Database integration

- SQL Server database: `FlightDB`.
- ADO.NET repository implementation.
- Stored procedure calls using `SqlConnection` and `SqlCommand`.
- Flight data mapping to and from the `Flight` model.
- Service and repository interfaces registered through dependency injection.

### Browser interface

The `wwwroot` folder contains the HTML, JavaScript, and CSS assets used by the browser UI.

## Architecture

```text
Browser / Console Client / API tool
                |
                v
         FlightController
                |
                v
          IFlightService
                |
                v
          FlightServices
                |
                v
             IFlight
                |
                v
         Flightrepositery
                |
                v
              ADO.NET
                |
                v
        SQL Server: FlightDB
                |
                v
        Stored procedures
```

| Layer | Main component | Responsibility |
|---|---|---|
| API/controller | `FlightController` | HTTP routes and response handling |
| Service contract | `IFlightService` | Defines service operations |
| Service implementation | `FlightServices` | Coordinates operations with repository |
| Repository contract | `IFlight` | Defines data operations |
| Repository implementation | `Flightrepositery` | Executes SQL Server commands |
| Model | `Flight` | Represents flight information |
| Database | `FlightDB` | Stores flight records |
| Browser UI | `wwwroot` | Static user interface assets |

## API reference

The controller is named `FlightController` and uses `[Route("api/[controller]")]`. The resulting route prefix is `/api/Flight`.

| Method | Endpoint | Action | Description |
|---|---|---|---|
| GET | `/api/Flight` | `GetAll()` | Returns all flights; returns 404 if the list is empty |
| GET | `/api/Flight/{id}` | `GetByID(int id)` | Returns a flight by ID or 404 |
| POST | `/api/Flight` | `Add(Flight flight)` | Adds a flight and returns 201 with the created record |
| PUT | `/api/Flight/{id}` | `Update(Flight flight)` | Updates a flight and returns 200 or 404 |
| DELETE | `/api/Flight/{id}` | `Delete(int id)` | Deletes a flight and returns 200 or 404 |

The current update route includes an `{id}` route segment, while the action method accepts the `Flight` model as its parameter and uses the ID carried by the model through the service/repository flow.

## Flight data model

| Property | Description |
|---|---|
| `FlightId` | Flight record identifier |
| `FlightNumber` | Flight number |
| `SourceCity` | Departure city |
| `DestinationCity` | Arrival city |
| `DepartureTime` | Scheduled departure |
| `Price` | Flight price |
| `AvailableSeats` | Available seat count |

## Database design

The supplied `FlightDB.sql` script creates the `FlightDB` database and the `Flights` table.

| Column | SQL definition | Purpose |
|---|---|---|
| `FlightId` | `INT IDENTITY(1001,1)` primary key | Flight identifier |
| `FlightNumber` | `VARCHAR(100)` | Flight number |
| `SourceCity` | `VARCHAR(100)` | Origin |
| `DestinationCity` | `VARCHAR(100)` | Destination |
| `Price` | `DECIMAL(18,2)` | Price |
| `DepartureTime` | `DATETIME` | Departure date/time |
| `AvailableSeats` | `INT` | Available seats |

The SQL file includes sample flight rows.

## Stored procedures

| Stored procedure | Purpose |
|---|---|
| `sp_GetAllFlights` | Retrieves all flights |
| `sp_GetFlightById` | Retrieves one flight by ID |
| `sp_InsertNewFlight` | Inserts a flight |
| `sp_UpdateExistingFlight` | Updates a flight |
| `sp_DeleteFlight` | Deletes a flight by ID |

The API repository calls the procedures through ADO.NET.

## Browser interface

The browser UI is contained in `wwwroot`.

| File | Purpose |
|---|---|
| `wwwroot/index.html` | Page structure |
| `wwwroot/scripts/app.js` | Browser-side behaviour and API requests |
| `wwwroot/styles/style.css` | Page styling |

`Program.cs` configures default-file and static-file middleware so that the frontend assets can be served by the ASP.NET Core host.

## Project structure

```text
07_Flight_Booking_Management_System_WebApp/
├── Controllers/
│   └── FlightController.cs
├── models/
│   └── Flight.cs
├── Repository/
│   ├── IFlight.cs
│   └── Flightrepositery.cs
├── Services/
│   ├── IFlightService.cs
│   └── FlightServices.cs
├── SQL/
│   ├── SQLScript.sql.txt
│   └── flight.txt
├── wwwroot/
│   ├── index.html
│   ├── scripts/
│   │   └── app.js
│   └── styles/
│       └── style.css
├── appsettings.json
├── appsettings.Development.json
├── Flight_Booking_Management_System.csproj
├── Flight_Booking_Management_System.http
├── Flight_Booking_Management_System.slnx
├── FlightDB.sql
└── Program.cs
```

## Technology stack

| Technology | Usage |
|---|---|
| C# | Application language |
| .NET 8 | Target framework |
| ASP.NET Core Web API | HTTP API |
| `Microsoft.Data.SqlClient` | SQL Server provider |
| ADO.NET | Database connectivity and commands |
| SQL Server | Relational database |
| Stored procedures | CRUD data operations |
| Dependency injection | Service composition |
| HTML, CSS, JavaScript | Browser interface |
| Swashbuckle | OpenAPI / Swagger integration |

## Configuration

The API reads the SQL Server connection string from application configuration. Configure `DefaultConnection` in the configuration used by the active environment to connect to `FlightDB`.

The API host and port are determined by the active launch profile or hosting environment. Any browser-side or console-client API base address must match the running API address.

## Build and run

This project targets **.NET 8**.

```bash
dotnet restore
dotnet build
dotnet run
```

To run from the project folder:

```bash
cd 07_Flight_Booking_Management_System_WebApp
dotnet restore
dotnet build
dotnet run
```

### Database setup

1. Open `FlightDB.sql` in SQL Server Management Studio.
2. Execute the script against the target SQL Server instance.
3. Confirm the `FlightDB` database, `Flights` table, and stored procedures exist.
4. Configure the `DefaultConnection` setting.
5. Start the API.

## Related project

The [Flight Booking Console API Client](../06_Flight_Booking_Management_System_ClientApp/README.md) consumes this API using `HttpClient`.

## Source files

- [Solution](Flight_Booking_Management_System.slnx)
- [Project file](Flight_Booking_Management_System.csproj)
- [Program startup](Program.cs)
- [Flight controller](Controllers/FlightController.cs)
- [Flight model](models/Flight.cs)
- [Service interface](Services/IFlightService.cs)
- [Service implementation](Services/FlightServices.cs)
- [Repository interface](Repository/IFlight.cs)
- [Repository implementation](Repository/Flightrepositery.cs)
- [Database script](FlightDB.sql)
- [Browser page](wwwroot/index.html)
- [Browser JavaScript](wwwroot/scripts/app.js)
- [Browser stylesheet](wwwroot/styles/style.css)

## Official documentation

- [Microsoft .NET](https://dotnet.microsoft.com/)
- [ASP.NET Core](https://learn.microsoft.com/aspnet/core/)
- [Create web APIs with ASP.NET Core](https://learn.microsoft.com/aspnet/core/web-api/)
- [SQL Server](https://learn.microsoft.com/sql/sql-server/)
- [ADO.NET](https://learn.microsoft.com/dotnet/framework/data/adonet/)
- [Microsoft.Data.SqlClient](https://learn.microsoft.com/sql/connect/ado-net/introduction-microsoft-data-sqlclient-namespace)
- [Swagger / OpenAPI in ASP.NET Core](https://learn.microsoft.com/aspnet/core/tutorials/getting-started-with-swashbuckle)
