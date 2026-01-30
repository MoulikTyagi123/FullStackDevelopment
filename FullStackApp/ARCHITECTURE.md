# InventoryHub - Project Structure and Architecture

## Directory Structure

```
FullStackApp/
│
├── FullStackSolution.sln                 # Solution file containing both projects
├── README.md                             # Main project documentation
├── REFLECTION.md                         # Development reflection and Copilot insights
├── TESTING_GUIDE.md                      # Comprehensive testing procedures
├── ARCHITECTURE.md                       # This file - Project structure guide
│
├── ClientApp/                            # Blazor WebAssembly Front-End
│   ├── ClientApp.csproj                  # Project file
│   ├── Program.cs                        # Application entry point and DI configuration
│   ├── App.razor                         # Root component
│   ├── _Imports.razor                    # Global imports
│   │
│   ├── Pages/
│   │   ├── Home.razor                    # Home page (unchanged)
│   │   ├── Counter.razor                 # Counter page (unchanged)
│   │   ├── Weather.razor                 # Weather page (unchanged)
│   │   └── FetchProducts.razor           # ✨ MAIN COMPONENT - Products integration
│   │
│   ├── Layout/
│   │   ├── MainLayout.razor              # Main layout structure
│   │   └── NavMenu.razor                 # Navigation menu (updated with Products link)
│   │
│   ├── wwwroot/                          # Static files
│   │   ├── css/
│   │   ├── js/
│   │   └── index.html
│   │
│   ├── Properties/
│   │   └── launchSettings.json           # Launch configuration
│   │
│   └── bin/, obj/                        # Build output directories
│
├── ServerApp/                            # ASP.NET Core Minimal API Back-End
│   ├── ServerApp.csproj                  # Project file
│   ├── Program.cs                        # ✨ API configuration and endpoints
│   │
│   ├── Properties/
│   │   └── launchSettings.json           # Launch configuration
│   │
│   └── bin/, obj/                        # Build output directories
│
└── Documentation/
    ├── API_ENDPOINTS.md                  # (Recommended) API endpoint documentation
    ├── ARCHITECTURE_DECISIONS.md         # (Recommended) Design decisions explanation
    └── DEPLOYMENT.md                     # (Recommended) Deployment instructions
```

## Key Files and Their Purpose

### 1. FullStackApp/FullStackSolution.sln

**Purpose**: Solution file that ties together both projects

```xml
Project("{FAE04EC0-301F-11D3-BF4B-00C04F79EFBC}") = "ClientApp", "ClientApp\ClientApp.csproj", "{...}"
EndProject
Project("{FAE04EC0-301F-11D3-BF4B-00C04F79EFBC}") = "ServerApp", "ServerApp\ServerApp.csproj", "{...}"
EndProject
```

**When to Use**: Open this file in Visual Studio or VS Code to work with both projects together

---

### 2. ClientApp/Program.cs

**Purpose**: Blazor WebAssembly application entry point and dependency injection setup

**Key Components**:

```csharp
var builder = WebAssemblyHostBuilder.CreateDefault(args);
builder.RootComponents.Add<App>("#app");
builder.Services.AddScoped(sp => new HttpClient { ... });
```

**Configuration for**: HttpClient for API communication

**When Modified**: To add new services or change DI configuration

---

### 3. ClientApp/Pages/FetchProducts.razor ⭐

**Purpose**: Main component for fetching and displaying products from the API

**Key Features**:

- Injects `HttpClient` for API communication
- Implements `OnInitializedAsync()` for data loading
- Comprehensive error handling with three exception types
- Case-insensitive JSON deserialization
- Displays products with categories

**Component Lifecycle**:

```
1. Component initializes
2. OnInitializedAsync() called
3. API request to /api/productlist
4. JSON response deserialized
5. products array populated
6. Component re-renders with data
```

**Nested Classes**:

- `Product`: Represents a single product with category
- `Category`: Nested object for product category

**Error States**:

- Loading: Shows "Loading..." message
- Error: Shows error message in red
- Success: Displays product list

---

### 4. ClientApp/Layout/NavMenu.razor

**Purpose**: Navigation menu component

**Updated Features**:

- Changed title from "ClientApp" to "InventoryHub"
- Added "Products" link pointing to `/fetchproducts`
- Removed counter and weather navigation items

```razor
<NavLink class="nav-link" href="fetchproducts">
    <span class="bi bi-basket-fill-nav-menu"></span> Products
</NavLink>
```

---

### 5. ServerApp/Program.cs ⭐

**Purpose**: ASP.NET Core application configuration and API endpoint definitions

**Key Components**:

#### CORS Configuration

```csharp
builder.Services.AddCors(options =>
{
    options.AddPolicy("AllowAll", policy =>
        policy.AllowAnyOrigin()
              .AllowAnyMethod()
              .AllowAnyHeader());
});
```

**Purpose**: Allows browser-based requests from any origin

#### API Endpoints

**Endpoint 1: `/api/products` (Legacy)**

- Returns basic product data without categories
- Simpler response structure
- Useful for backwards compatibility

**Endpoint 2: `/api/productlist` (Current)**

- Returns products with nested category objects
- More complete data structure
- Main endpoint used by FetchProducts component

---

## Data Flow Diagram

```
┌─────────────────────────────────────────────────────────────┐
│                     User's Browser                          │
├─────────────────────────────────────────────────────────────┤
│                                                              │
│  1. User clicks "Products" link                             │
│     ↓                                                        │
│  2. FetchProducts.razor component loads                     │
│     ↓                                                        │
│  3. OnInitializedAsync() executes                           │
│     ↓                                                        │
│  4. HttpClient.GetAsync("/api/productlist") called          │
│     ┌──────────────────────────────────────┐               │
│     │   HTTP GET Request                   │               │
│     │   Host: localhost:5000                │               │
│     │   Path: /api/productlist              │               │
│     └──────────────────────────────────────┘               │
│            ↓                                    │            │
├────────────────────────────────────────────────┼────────────┤
│                   Network                      │            │
├────────────────────────────────────────────────┼────────────┤
│                                                │            │
│  5. ServerApp receives request                 │            │
│     ↓                                          │            │
│  6. MapGet("/api/productlist") handler runs    │            │
│     ↓                                          │            │
│  7. Returns anonymous objects with categories  │            │
│     ┌──────────────────────────────────────┐  │            │
│     │   HTTP 200 OK Response               │  │            │
│     │   Content-Type: application/json     │  │            │
│     │   Body: [{ id, name, price, ... }]  │  │            │
│     └──────────────────────────────────────┘  │            │
│            ↑                                   │            │
│            └───────────────────────────────────┘            │
│                                                              │
│  8. Response received in HttpClient                         │
│     ↓                                                        │
│  9. JSON parsed with case-insensitive option                │
│     ↓                                                        │
│  10. Product[] array populated                              │
│     ↓                                                        │
│  11. Component re-renders                                   │
│     ↓                                                        │
│  12. Products displayed in browser                          │
│     ↓                                                        │
│     • Laptop - $1200.5 (Stock: 25)                          │
│     • Headphones - $50 (Stock: 100)                         │
│                                                              │
└─────────────────────────────────────────────────────────────┘
```

---

## Component Hierarchy

```
App.razor
├── MainLayout.razor
│   ├── NavMenu.razor
│   │   └── ProductsLink → FetchProducts.razor
│   └── PageView (@Body)
│       └── FetchProducts.razor (when /fetchproducts)
│           ├── <h3>Product List</h3>
│           ├── <ul>
│           │   ├── Product Items
│           │   ├── Loading State
│           │   └── Error State
│           └── @code section
│               ├── products[] array
│               ├── errorMessage string
│               └── OnInitializedAsync() method
└── App.razor ends
```

---

## Technology Stack

### Front-End (ClientApp)

| Technology         | Version  | Purpose             |
| ------------------ | -------- | ------------------- |
| .NET               | 9.0+     | Runtime framework   |
| Blazor WebAssembly | Built-in | UI framework        |
| System.Text.Json   | Built-in | JSON serialization  |
| HttpClient         | Built-in | HTTP communication  |
| Razor Components   | Built-in | Component framework |

### Back-End (ServerApp)

| Technology      | Version  | Purpose                   |
| --------------- | -------- | ------------------------- |
| .NET            | 9.0+     | Runtime framework         |
| ASP.NET Core    | 9.0+     | Web framework             |
| Minimal APIs    | Built-in | Lightweight API framework |
| CORS Middleware | Built-in | Cross-origin requests     |

---

## Configuration Files

### launchSettings.json (ClientApp)

```json
{
  "profiles": {
    "http": {
      "commandName": "Project",
      "dotnetRunMessages": true,
      "launchBrowser": true,
      "applicationUrl": "http://localhost:5002",
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      }
    },
    "https": {
      "commandName": "Project",
      "dotnetRunMessages": true,
      "launchBrowser": true,
      "applicationUrl": "https://localhost:5003;http://localhost:5002",
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      }
    }
  }
}
```

**Key Points**:

- HTTP port: 5002
- HTTPS port: 5003
- Launches browser automatically

### launchSettings.json (ServerApp)

```json
{
  "profiles": {
    "http": {
      "commandName": "Project",
      "dotnetRunMessages": true,
      "launchBrowser": false,
      "applicationUrl": "http://localhost:5000",
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      }
    },
    "https": {
      "commandName": "Project",
      "dotnetRunMessages": true,
      "launchBrowser": false,
      "applicationUrl": "https://localhost:5001;http://localhost:5000",
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      }
    }
  }
}
```

**Key Points**:

- HTTP port: 5000
- HTTPS port: 5001
- Does NOT launch browser (headless API)

---

## Project Files (.csproj)

### ClientApp.csproj

Key properties:

```xml
<TargetFramework>net9.0</TargetFramework>
<OutputType>Exe</OutputType>
<ImplicitUsings>enable</ImplicitUsings>
<Nullable>enable</Nullable>
```

**Nullable Reference Types**: Enabled for type safety

### ServerApp.csproj

Key properties:

```xml
<TargetFramework>net9.0</TargetFramework>
<OutputType>Exe</OutputType>
<ImplicitUsings>enable</ImplicitUsings>
<Nullable>enable</Nullable>
<InvariantGlobalization>true</InvariantGlobalization>
```

---

## Build and Output Structure

```
ClientApp/
├── bin/
│   └── Debug/
│       └── net9.0/
│           ├── ClientApp.wasm        # Compiled WebAssembly
│           ├── ClientApp.dll         # .NET assemblies
│           └── [dependencies]
└── obj/                               # Intermediate compilation files

ServerApp/
├── bin/
│   └── Debug/
│       └── net9.0/
│           ├── ServerApp.dll         # Compiled executable
│           └── [dependencies]
└── obj/                               # Intermediate compilation files
```

---

## Dependency Injection (DI) Container

### ClientApp

```csharp
builder.Services.AddScoped(sp =>
    new HttpClient {
        BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
    }
);
```

**Scope**: Scoped (one per component)
**Type**: HttpClient
**Used in**: FetchProducts.razor via `@inject HttpClient`

### ServerApp

```csharp
builder.Services.AddCors(...);
```

**Scope**: Singleton (application-wide)
**Type**: CORS Policy
**Used in**: `app.UseCors()` middleware

---

## Error Handling Architecture

```
FetchProducts.razor
    │
    └─ OnInitializedAsync()
        │
        ├─ try
        │  ├─ HttpClient.GetAsync()
        │  ├─ response.EnsureSuccessStatusCode()
        │  ├─ JsonSerializer.Deserialize()
        │  └─ products = [...]
        │
        └─ catch blocks
           ├─ HttpRequestException → Network/API errors
           ├─ JsonException → Deserialization errors
           └─ Exception → Unexpected errors
               │
               └─ errorMessage = message
                  │
                  └─ UI displays in red color
```

---

## Security Considerations

### CORS Policy

- **Current**: `AllowAnyOrigin()` - Development only
- **Production**: Should restrict to specific origins

```csharp
policy.WithOrigins("https://example.com")
```

### HTTPS

- Enabled in launchSettings.json
- Both apps support HTTPS
- Development uses self-signed certificates

### Nullable Reference Types

- Enabled project-wide
- Prevents null reference exceptions
- Enforces null-checking

---

## Extension Points

### To Add More Endpoints

Edit `ServerApp/Program.cs`:

```csharp
app.MapGet("/api/categories", () => { ... });
app.MapGet("/api/products/{id}", (int id) => { ... });
app.MapPost("/api/products", (Product product) => { ... });
```

### To Add More Components

Create new file in `ClientApp/Pages/`:

```razor
@page "/new-page"
@inject HttpClient HttpClient

<!-- Component markup -->

@code {
    // Component logic
}
```

### To Add Services

In `ClientApp/Program.cs`:

```csharp
builder.Services.AddScoped<IProductService, ProductService>();
```

---

## Troubleshooting by Location

### Problems in ClientApp

- **File**: ClientApp/Pages/FetchProducts.razor
- **Check**: Component logic, API calls, error handling
- **Port**: 5002

### Problems in ServerApp

- **File**: ServerApp/Program.cs
- **Check**: CORS, endpoints, response format
- **Port**: 5000

### Problems with Communication

- **Check**: Both servers running
- **Check**: Ports match (5000 for API, 5002 for client)
- **Check**: CORS configured
- **Check**: Firewall allows localhost communication

---

## Recommended Future Improvements

### Architecture

1. Create `ProductService` class to encapsulate API calls
2. Implement repository pattern for data access
3. Add logging across both projects
4. Create shared models/DTOs

### Back-End

1. Add database integration (Entity Framework Core)
2. Implement proper API versioning
3. Add authentication/authorization
4. Create comprehensive API documentation

### Front-End

1. Add client-side caching
2. Implement loading spinners
3. Add pagination/filtering UI
4. Create unit tests

---

**Architecture Document Version**: 1.0
**Last Updated**: January 30, 2026
**Framework**: .NET 9.0
**Status**: Complete and Documented
