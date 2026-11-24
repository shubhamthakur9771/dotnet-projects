# Hospital Appointment and Patient Management System

[![.NET](https://img.shields.io/badge/.NET-512BD4?style=for-the-badge&logo=dotnet&logoColor=white)](https://dotnet.microsoft.com/)
[![C#](https://img.shields.io/badge/C%23-239120?style=for-the-badge&logo=csharp&logoColor=white)](https://learn.microsoft.com/dotnet/csharp/)
[![Microsoft Learn](https://img.shields.io/badge/Microsoft%20Learn-258FFA?style=for-the-badge&logo=microsoft&logoColor=white)](https://learn.microsoft.com/)

A hospital management backend built with C#, ASP.NET Core Web API, ADO.NET, SQL Server, stored procedures, dependency injection, and a layered service/repository architecture. The system covers patient registration and maintenance, doctor profiles and availability, appointment booking and cancellation, and operational reporting.

## Contents

- [Overview](#overview)
- [Project requirements](#project-requirements)
- [Functional modules](#functional-modules)
- [REST API reference](#rest-api-reference)
- [Database design](#database-design)
- [Stored procedures](#stored-procedures)
- [Architecture](#architecture)
- [Technical implementation](#technical-implementation)
- [HTTP response behaviour](#http-response-behaviour)
- [Project structure](#project-structure)
- [Technology stack](#technology-stack)
- [Configuration](#configuration)
- [Build and run](#build-and-run)
- [Requirements and implementation alignment](#requirements-and-implementation-alignment)
- [Source files](#source-files)

## Overview

The supplied presentation describes the **City General Hospital** backend mini-project. The system is organized around three primary entities—Patients, Doctors, and Appointments—and exposes operations through an ASP.NET Core Web API.

The implementation separates HTTP handling, request DTOs, service logic, repository logic, and SQL Server data access. The SQL script creates the relational schema, inserts example records, and defines stored procedures for patient, doctor, appointment, and reporting operations.

## Project requirements

The supplied PowerPoint presentation describes the following requirements.

### Patient management

- Unique patient code.
- Patient name and date of birth.
- Gender.
- Unique phone and email validation.
- Full record updates.
- Soft deactivation using an active-status field.
- Active-patient listing.
- Live age calculation.

### Doctor management

- Unique doctor code.
- Doctor name and specialization.
- Consultation fee.
- Availability status.
- Filter by specialization.
- Filter by availability.

### Appointment management

- Book patient-doctor appointments by date and time.
- Reject booking when the doctor is unavailable.
- Appointment statuses: `Scheduled`, `Completed`, and `Cancelled`.
- Record cancellation timestamp.
- Retrieve upcoming appointments.
- Retrieve appointments by doctor.

### Reporting

- Consolidated appointment details.
- Doctors with more than two appointments.
- Revenue by medical specialization.
- Duplicate appointment detection.
- Appointments in the next seven days.

## Functional modules

### Patient management

The patient controller supports creating, listing, updating, and deactivating patient records.

The `Patient` model includes:

| Property | Description |
|---|---|
| `Id` | Patient identifier |
| `FullName` | Patient name |
| `Dob` | Date of birth |
| `Gender` | Gender |
| `Mob` | Mobile number |
| `Email` | Email address |
| `Status` | Active/inactive status |
| `Age` | Calculated age |

The database uses an identity primary key and uniqueness constraints for mobile and email. Deactivation updates the patient's status instead of deleting the row.

### Doctor management

The doctor module supports:

- Creating a doctor.
- Updating doctor details.
- Updating availability independently.
- Filtering by specialization and availability.

The `Doctor` model contains ID, full name, specialization, mobile number, consultation fee, and availability.

Request DTOs include `CreateDoctorDto`, `UpdateDoctorDto`, `UpdateDoctorAvailabilityDto`, and `GetDoctorFilterDto`.

### Appointment management

The appointment module supports:

- Creating an appointment from patient ID, doctor ID, and appointment date.
- Cancelling an appointment.
- Retrieving upcoming appointments.
- Retrieving appointments for a selected doctor.

The `Appointment` model contains appointment ID, patient ID, doctor ID, appointment date, status, and cancellation timestamp. It also contains date-related helper methods.

### Appointment reporting

The appointment service and repository expose the following report operations:

| Report | Purpose |
|---|---|
| Appointment details | Consolidated appointment information |
| Doctors with more appointments | Returns doctors meeting the appointment-count condition |
| Revenue by specialization | Aggregates revenue by medical specialization |
| Duplicate appointments | Retrieves duplicate appointment records |
| Next seven days | Retrieves appointments in the upcoming seven-day window |

## REST API reference

The endpoint paths below are derived from the controller class names and route/action attributes in the current source code.

### Patients — `/api/Patients`

The controller class is `PatientsController` and uses `[Route("api/[controller]")]`.

| Method | Endpoint | Action | Description |
|---|---|---|---|
| GET | `/api/Patients` | `Get()` | Retrieves active patients |
| POST | `/api/Patients` | `Create()` | Creates a patient |
| PUT | `/api/Patients/{id}` | `Update()` | Updates patient details |
| DELETE | `/api/Patients/{id}` | `Delete()` | Deactivates a patient |

### Doctors — `/api/Doctors`

| Method | Endpoint | Action | Description |
|---|---|---|---|
| POST | `/api/Doctors` | `Create()` | Creates a doctor |
| PUT | `/api/Doctors/{id}` | `Update()` | Updates doctor details |
| PATCH | `/api/Doctors/{id}/availability` | `UpdateAvailability()` | Updates doctor availability |
| GET | `/api/Doctors?specialization=value&available=true` | `Get()` | Filters doctors by optional query parameters |

Both `specialization` and `available` are optional query parameters in the controller action.

### Appointments — `/api/Appointments`

| Method | Endpoint | Action | Description |
|---|---|---|---|
| POST | `/api/Appointments` | `Create()` | Creates an appointment |
| DELETE | `/api/Appointments/{id}` | `Cancel()` | Cancels an appointment |
| GET | `/api/Appointments/upcoming` | `GetUpcoming()` | Retrieves upcoming appointments |
| GET | `/api/Appointments/doctor/{doctorId}` | `GetByDoctor()` | Retrieves appointments by doctor |
| GET | `/api/Appointments/report/details` | `GetDetails()` | Consolidated appointment details |
| GET | `/api/Appointments/report/top-doctors` | `GetTopDoctors()` | Doctors with more appointments |
| GET | `/api/Appointments/report/revenue` | `GetRevenue()` | Revenue grouped by specialization |
| GET | `/api/Appointments/report/duplicates` | `GetDuplicates()` | Duplicate appointment records |
| GET | `/api/Appointments/report/next7days` | `GetNext7Days()` | Appointments in the next seven days |

## Database design

`HospitalDB.sql` creates the `HospitalDB` database and three main tables.

### Patients

| Column | Purpose |
|---|---|
| `id` | Identity primary key |
| `fullName` | Patient name |
| `dob` | Date of birth |
| `gender` | Gender, constrained to Male, Female, or Other |
| `mob` | Unique mobile number |
| `email` | Unique email address |
| `status` | Active or Inactive |

### Doctors

| Column | Purpose |
|---|---|
| `id` | Identity primary key |
| `fullName` | Doctor name |
| `specialization` | Medical specialization |
| `mob` | Unique mobile number |
| `fee` | Non-negative consultation fee |
| `available` | Availability flag |

### Appointments

| Column | Purpose |
|---|---|
| `appointmentId` | Identity primary key |
| `patientId` | Foreign key to Patients |
| `doctorId` | Foreign key to Doctors |
| `appointmentDate` | Appointment date and time |
| `status` | Scheduled, Completed, or Cancelled |

The SQL script includes foreign keys from appointments to patients and doctors, plus sample records for the three entities.

## Stored procedures

The SQL script defines procedures for all three functional areas and the reports.

### Patient procedures

- `sp_AddPatient`
- `sp_UpdatePatient`
- `sp_DeactivatePatient`
- `sp_GetActivePatients`

### Doctor procedures

- `sp_AddDoctor`
- `sp_UpdateDoctorDetails`
- `sp_UpdateDoctorAvailability`
- `sp_GetDoctorsByFilter`

### Appointment procedures

- `sp_AddAppointment`
- `sp_CancelAppointment`
- `sp_GetUpcomingAppointments`
- `sp_GetAppointmentsByDoctor`

### Reporting procedures

- `sp_GetAppointmentDetails`
- `sp_GetDoctorsWithMoreAppointments`
- `sp_GetRevenueBySpecialization`
- `sp_GetDuplicateAppointments`
- `sp_GetNext7DaysAppointments`

The SQL file contains 17 procedure definitions. The supplied presentation describes the deliverable as containing 14 stored procedures; the names above are the definitions found in the SQL script.

## Architecture

```text
API consumer
(Postman / HTTP client / Swagger)
              |
              v
       ASP.NET Core API
              |
              v
          Controllers
              |
              v
       Service interfaces
              |
              v
        Service classes
              |
              v
     Repository interfaces
              |
              v
    Repository implementations
              |
              v
             ADO.NET
              |
              v
          SQL Server
           HospitalDB
```

## Technical implementation

### Dependency injection

`Program.cs` registers service and repository implementations with the ASP.NET Core dependency injection container. Controllers depend on service interfaces.

### DTO-based request handling

The API defines request DTOs for patient creation, doctor creation and updates, doctor availability, doctor filtering, and appointment creation.

### ADO.NET repositories

Repository implementations use SQL Server connections and commands to execute stored procedures and map returned rows into model objects.

### Middleware

- `LoggingMiddleware` records request method, request path, and response timing.
- `ExceptionMiddleware` handles exceptions at the middleware layer and creates an API error response.

### Domain logic

The models and service layer contain date and business-operation logic. The appointment model exposes methods for checking past dates and obtaining future-day values.

## HTTP response behaviour

The controller actions use the following response types according to the operation and outcome.

| Status | Meaning |
|---|---|
| 200 OK | Successful retrieval or operation returning a body |
| 201 Created | Successful patient, doctor, or appointment creation |
| 204 No Content | Successful update, deactivation, or cancellation where no body is returned |
| 400 Bad Request | Invalid request model or argument |
| 404 Not Found | Requested record or report data not found |
| 409 Conflict | Conflict detected by the relevant appointment action |
| 500 Internal Server Error | Unexpected server or database error |

The exact exception mapping is implemented separately in each controller action.

## Project structure

```text
08_Hospital_Management_System/
├── Hospital_Management_System/
│   ├── Controllers/
│   │   ├── AppointmentsController.cs
│   │   ├── DoctorsController.cs
│   │   ├── PatientController .cs
│   │   └── WeatherForecastController.cs
│   ├── DTOs/
│   │   ├── CreateAppointmentDto.cs
│   │   ├── CreateDoctorDto.cs
│   │   ├── CreatePatientDto.cs
│   │   ├── GetDoctorFilterDto.cs
│   │   ├── UpdateDoctorAvailabilityDto.cs
│   │   └── UpdateDoctorDto.cs
│   ├── Data/
│   │   └── Repositories/
│   │       ├── Implementations/
│   │       └── Interfaces/
│   ├── Middleware/
│   │   ├── ExceptionMiddleware.cs
│   │   └── LoggingMiddleware.cs
│   ├── Models/
│   │   ├── Appointment.cs
│   │   ├── Doctor.cs
│   │   └── Patient.cs
│   ├── Services/
│   │   ├── Implementations/
│   │   └── Interfaces/
│   ├── appsettings.json
│   ├── Hospital_Management_System.csproj
│   └── Program.cs
├── HospitalDB.sql
└── Hospital_Management_System.slnx
```

## Technology stack

| Technology | Usage |
|---|---|
| C# | Application language |
| .NET 8 | Target framework |
| ASP.NET Core Web API | API framework |
| SQL Server | Relational database |
| ADO.NET | Data access |
| `Microsoft.Data.SqlClient` | SQL Server provider |
| Stored procedures | Data operations and reporting |
| Dependency injection | Service/repository composition |
| REST | API communication |
| Swagger / OpenAPI | API exploration in development |
| Middleware | Request logging and exception handling |
| Postman | API testing tool listed in the project presentation |

## Configuration

The API reads the SQL Server connection string from application configuration. Configure `DefaultConnection` in the appropriate `appsettings` file for the SQL Server instance hosting `HospitalDB`.

The project includes launch settings and an HTTP request file for local development. The active launch profile determines the host and port.

## Build and run

This project targets **.NET 8**.

### Database

1. Open `HospitalDB.sql` in SQL Server Management Studio.
2. Execute the script against a SQL Server instance.
3. Confirm that `HospitalDB`, its tables, and stored procedures are available.
4. Configure the API connection string.

### API

```bash
cd Hospital_Management_System
dotnet restore
dotnet build
dotnet run
```

Use the address configured by the active launch profile to send requests to the API. Swagger is registered for development use.

## Requirements and implementation alignment

The presentation and source code are both part of this project. The endpoint reference above follows the current controller attributes and action methods. Some route details differ from the presentation:

| Area | Presentation | Current controller source |
|---|---|---|
| Patient route | `/api/patients` | `/api/Patients` |
| Patient deactivation | `DELETE /api/patients/{id}/deactivate` | `DELETE /api/Patients/{id}` |
| Appointment cancellation | `PUT /api/appointments/{id}/cancel` | `DELETE /api/Appointments/{id}` |
| Doctor filter | `spec` and `available` query names | `specialization` and `available` |
| Stored procedure count | Presentation states 14 | SQL script defines 17 procedures |

## Source files

- [Solution](Hospital_Management_System.slnx)
- [API project](Hospital_Management_System/Hospital_Management_System.csproj)
- [Database script](HospitalDB.sql)
- [API startup](Hospital_Management_System/Program.cs)
- [Patient controller](Hospital_Management_System/Controllers/PatientController%20.cs)
- [Doctors controller](Hospital_Management_System/Controllers/DoctorsController.cs)
- [Appointments controller](Hospital_Management_System/Controllers/AppointmentsController.cs)
- [Patient repository](Hospital_Management_System/Data/Repositories/Implementations/PatientRepository%20.cs)
- [Doctor repository](Hospital_Management_System/Data/Repositories/Implementations/DoctorRepository.cs)
- [Appointment repository](Hospital_Management_System/Data/Repositories/Implementations/AppointmentRepository.cs)
- [Logging middleware](Hospital_Management_System/Middleware/LoggingMiddleware.cs)
- [Exception middleware](Hospital_Management_System/Middleware/ExceptionMiddleware.cs)

## Official documentation

- [Microsoft .NET](https://dotnet.microsoft.com/)
- [ASP.NET Core](https://learn.microsoft.com/aspnet/core/)
- [ASP.NET Core Web API](https://learn.microsoft.com/aspnet/core/web-api/)
- [ADO.NET](https://learn.microsoft.com/dotnet/framework/data/adonet/)
- [SQL Server](https://learn.microsoft.com/sql/sql-server/)
- [Microsoft.Data.SqlClient](https://learn.microsoft.com/sql/connect/ado-net/introduction-microsoft-data-sqlclient-namespace)
- [Dependency injection in .NET](https://learn.microsoft.com/dotnet/core/extensions/dependency-injection)
- [Middleware in ASP.NET Core](https://learn.microsoft.com/aspnet/core/fundamentals/middleware/)
- [Swagger / OpenAPI](https://learn.microsoft.com/aspnet/core/tutorials/getting-started-with-swashbuckle)
