# skinet

An e-commerce store built with ASP.NET Core and SQL Server, following the Udemy course *.NET & Angular e-commerce store*. The backend is organized in layers (API, Core, Infrastructure); the Angular client is planned and not part of the repository yet.

> [!NOTE]
> This project is a work in progress. Currently, only the products API is implemented.

## Features

- RESTful products API with full CRUD (`GET`, `POST`, `PUT`, `DELETE`)
- Layered solution: `API` → `Infrastructure` → `Core`
- Entity Framework Core with SQL Server and migrations
- OpenAPI document exposed in development
- Docker Compose for SQL Server and Redis (Redis is provisioned but not used yet)

## Tech stack

- [.NET 10](https://dotnet.microsoft.com/) / ASP.NET Core
- [Entity Framework Core 10](https://learn.microsoft.com/ef/core/) with SQL Server
- [Docker](https://www.docker.com/) (SQL Server 2022, Redis)

## Project structure

```text
skinet/
├── API/             # Web API: controllers, startup, configuration
├── Core/            # Domain entities (no external dependencies)
├── Infrastructure/  # EF Core DbContext, entity configurations, migrations
├── docker-compose.yml
└── skinet.slnx
```

## Getting started

### Prerequisites

- [.NET 10 SDK](https://dotnet.microsoft.com/download)
- [Docker](https://www.docker.com/products/docker-desktop/)
- [`dotnet-ef`](https://learn.microsoft.com/ef/core/cli/dotnet) tool: `dotnet tool install --global dotnet-ef`

### Run locally

1. Clone the repository and start the infrastructure:

   ```bash
   git clone <repository-url>
   cd skinet
   docker compose up -d
   ```

2. Apply the database migrations:

   ```bash
   dotnet ef database update --project Infrastructure --startup-project API
   ```

3. Start the API:

   ```bash
   dotnet watch --project API
   ```

The API listens on `http://localhost:5000` and `https://localhost:5001`.

> [!WARNING]
> The SQL Server credentials in `docker-compose.yml` and `API/appsettings.Development.json` are for local development only. Do not reuse them elsewhere.

## API

Base route: `/api/products`

| Method   | Endpoint             | Description          |
| -------- | -------------------- | -------------------- |
| `GET`    | `/api/products`      | List all products    |
| `GET`    | `/api/products/{id}` | Get a product by id  |
| `POST`   | `/api/products`      | Create a product     |
| `PUT`    | `/api/products/{id}` | Update a product     |
| `DELETE` | `/api/products/{id}` | Delete a product     |

Example request:

```bash
curl -X POST http://localhost:5000/api/products \
  -H "Content-Type: application/json" \
  -d '{
    "name": "Angular Speedster Board",
    "description": "Lightweight board for fast riders",
    "price": 199.99,
    "pictureUrl": "images/products/board.png",
    "type": "Boards",
    "brand": "Angular",
    "quantityInStock": 10
  }'
```

In development, the OpenAPI document is available at `/openapi/v1.json`. The `API/API.http` file contains ready-to-run requests for editors that support it.

## Configuration

| Setting                          | Location                            | Default                                   |
| -------------------------------- | ----------------------------------- | ----------------------------------------- |
| `ConnectionStrings:DefaultConnection` | `API/appsettings.Development.json` | SQL Server on `localhost,1433`, db `skinet` |
| Ports                            | `API/Properties/launchSettings.json` | `5000` (HTTP), `5001` (HTTPS)            |

## Database migrations

Add a new migration after changing entities:

```bash
dotnet ef migrations add <Name> --project Infrastructure --startup-project API
dotnet ef database update --project Infrastructure --startup-project API
```
