# Flight Booking Management System — Console API Client

[![.NET](https://img.shields.io/badge/.NET-512BD4?style=for-the-badge&logo=dotnet&logoColor=white)](https://dotnet.microsoft.com/)
[![C#](https://img.shields.io/badge/C%23-239120?style=for-the-badge&logo=csharp&logoColor=white)](https://learn.microsoft.com/dotnet/csharp/)
[![Microsoft Learn](https://img.shields.io/badge/Microsoft%20Learn-258FFA?style=for-the-badge&logo=microsoft&logoColor=white)](https://learn.microsoft.com/)

A menu-driven C# console client that consumes the Flight Booking ASP.NET Core Web API over HTTP. It uses `HttpClient` for flight retrieval and CRUD requests and Newtonsoft.Json for JSON serialization and deserialization.

## Contents

- [Overview](#overview)
- [Features](#features)
- [Console workflow](#console-workflow)
- [Flight model](#flight-model)
- [HTTP client service](#http-client-service)
- [API route contract](#api-route-contract)
- [Request and response flow](#request-and-response-flow)
- [Project structure](#project-structure)
- [Technology stack](#technology-stack)
- [Configuration](#configuration)
- [Build and run](#build-and-run)
- [Related project](#related-project)
- [Source files](#source-files)

## Overview

The console application provides a text-based interface for managing flight records through the companion Flight Booking Web API. It does not connect directly to SQL Server. Instead, it creates `Flight` objects from console input, serializes them to JSON, and sends HTTP requests to the API.

The API base address is assigned in the `FlightService` constructor in `Services/FlightServices.cs`.

## Features

- Retrieve and display all flights.
- Retrieve one flight by flight ID.
- Add a flight.
- Update an existing flight.
- Delete a flight by ID.
- Send asynchronous HTTP requests.
- Serialize C# flight objects into JSON request bodies.
- Deserialize JSON responses into C# model objects.
- Display flight identifiers and flight numbers.
- Check and display the HTTP response status for update requests.

## Console workflow

The menu in `Program.cs` provides:

```text
1. View Flights
2. View Flight By FlightID
3. Add Flight
4. Update Flight
5. Delete Flight
6. Exit
```

### View flights

Calls `FlightService.GetFlights()`, receives a list of `Flight` objects, and displays each flight ID and flight number.

### View by flight ID

Reads an ID, calls `GetFlightById(id)`, and displays the returned flight ID and flight number.

### Add flight

The console prompts for flight number, source city, destination city, departure time, price, and available seats. These values are assigned to a `Flight` object and passed to `AddFlight()`.

### Update flight

The user enters the flight ID and updated flight details. The service serializes the object and sends a PUT request.

### Delete flight

The user enters a flight ID and the service sends a DELETE request for that ID.

## Flight model

The `Flight` class in `Models/Flight.cs` represents the data exchanged with the API.

| Property | Description |
|---|---|
| `FlightId` | Flight record identifier |
| `FlightNumber` | Airline flight number/code |
| `SourceCity` | Departure city |
| `DestinationCity` | Arrival city |
| `DepartureTime` | Scheduled departure date and time |
| `Price` | Flight price |
| `AvailableSeats` | Number of available seats |

## HTTP client service

`Services/FlightServices.cs` contains the `FlightService` class. The constructor creates an `HttpClient` and assigns its base address.

| Method | HTTP operation | Behaviour |
|---|---|---|
| `GetFlights()` | GET | Requests the flight collection and deserializes a list |
| `GetFlightById(id)` | GET | Requests one flight by ID |
| `AddFlight(flight)` | POST | Serializes the flight and sends JSON |
| `UpdateFlight(flight)` | PUT | Serializes the flight and checks the response status |
| `DeleteFlight(id)` | DELETE | Sends a delete request for the flight ID |

The service uses `JsonConvert.SerializeObject()` for outgoing request data and `JsonConvert.DeserializeObject<T>()` for API responses.

## API route contract

The companion API uses the controller route prefix `/api/Flight`.

| Operation | Method | Route |
|---|---|---|
| List flights | GET | `/api/Flight` |
| Retrieve one flight | GET | `/api/Flight/{id}` |
| Add flight | POST | `/api/Flight` |
| Update flight | PUT | `/api/Flight/{id}` |
| Delete flight | DELETE | `/api/Flight/{id}` |

The current client sends the update request to its configured base address without appending the flight ID. The API controller's update route includes an `{id}` segment. The client and API route must therefore be aligned for update requests to reach that action.

## Request and response flow

```text
Console input
    |
    v
Create Flight object
    |
    v
FlightService
    |
    +-- Serialize object to JSON
    |
    v
HttpClient
    |
    v
Flight Booking Web API
    |
    v
JSON response
    |
    v
Deserialize to Flight / List<Flight>
    |
    v
Console output
```

## Project structure

```text
06_Flight_Booking_Management_System_ClientApp/
├── Models/
│   └── Flight.cs
├── Properties/
│   └── AssemblyInfo.cs
├── Services/
│   └── FlightServices.cs
├── App.config
├── FlightHttpClientApp.csproj
├── FlightHttpClientApp.slnx
└── packages.config
```

## Technology stack

| Technology | Usage |
|---|---|
| C# | Client application language |
| .NET Framework 4.7.2 | Target framework |
| `HttpClient` | HTTP communication |
| REST API | Client-server interaction |
| Newtonsoft.Json | JSON serialization and deserialization |
| `async` / `await` | Asynchronous HTTP calls |
| Console application | Menu-driven interface |
| NuGet | External package management |

## Configuration

The API base address is hardcoded in the `FlightService` constructor. It must match the address and route where the companion API is running.

The API project in this repository uses ASP.NET Core. Start the API first, check its active launch profile, and ensure that the client uses the same protocol, host, port, and route.

## Build and run

This project targets **.NET Framework 4.7.2** and uses a classic project file with `packages.config`.

1. Start the [Flight Booking Web API](../07_Flight_Booking_Management_System_WebApp/README.md).
2. Confirm the API listening address.
3. Open `FlightHttpClientApp.slnx` in Visual Studio with the .NET Framework 4.7.2 targeting pack.
4. Restore NuGet packages.
5. Build the solution.
6. Run the console client.

## Related project

The client consumes the API documented in [Flight Booking Management System — Web API](../07_Flight_Booking_Management_System_WebApp/README.md).

## Source files

- [Solution](FlightHttpClientApp.slnx)
- [Project file](FlightHttpClientApp.csproj)
- [Program entry point](Program.cs)
- [Flight model](Models/Flight.cs)
- [HTTP client service](Services/FlightServices.cs)
- [NuGet package manifest](packages.config)
- [Application configuration](App.config)

## Official documentation

- [Microsoft .NET](https://dotnet.microsoft.com/)
- [C# documentation](https://learn.microsoft.com/dotnet/csharp/)
- [HttpClient](https://learn.microsoft.com/dotnet/api/system.net.http.httpclient)
- [Asynchronous programming](https://learn.microsoft.com/dotnet/csharp/asynchronous-programming/)
- [Newtonsoft.Json](https://www.newtonsoft.com/json/help/html/Introduction.htm)
- [ASP.NET Core Web API](https://learn.microsoft.com/aspnet/core/web-api/)
