# InventoryHub - Full-Stack Application


<img width="945" height="612" alt="image" src="https://github.com/user-attachments/assets/096b86a9-dd48-4ee9-98b2-192136fc8838" />


## Project Overview

InventoryHub is a complete inventory management system demonstrating seamless integration between a **Blazor WebAssembly front-end** and an **ASP.NET Core Minimal API back-end**. The application showcases best practices for full-stack development, including API integration, error handling, CORS configuration, and JSON structure design.

## Architecture

### Back-End (ServerApp)

- **Framework**: ASP.NET Core 9.0
- **API Type**: Minimal APIs
- **Key Features**:
  - RESTful endpoints for product data
  - CORS configuration for cross-origin requests
  - Support for nested JSON structures (Product + Category)
  - Two endpoints: `/api/products` and `/api/productlist`

### Front-End (ClientApp)

- **Framework**: Blazor WebAssembly (Standalone)
- **Key Features**:
  - HttpClient integration for API communication
  - Async data fetching with error handling
  - Support for nested object deserialization
  - Case-insensitive JSON deserialization

## Project Structure

```
FullStackApp/
├── FullStackSolution.sln
├── ClientApp/
│   ├── Pages/
│   │   ├── FetchProducts.razor
│   │   └── ...other pages
│   ├── Layout/
│   │   └── NavMenu.razor (updated)
│   └── Program.cs (configured with HttpClient)
├── ServerApp/
│   ├── Program.cs (configured with CORS and API endpoints)
│   └── ...server files
└── README.md (this file)
```

## Running the Application

### Prerequisites

- .NET 9.0 SDK or later
- Visual Studio Code or Visual Studio

### Start the Server

```bash
cd ServerApp
dotnet run
# Server runs on http://localhost:5000
```

### Start the Client (in a new terminal)

```bash
cd ClientApp
dotnet run
# Client runs on http://localhost:5001
```

### Access the Application

1. Open your browser and navigate to `http://localhost:5001`
2. Click on "Products" in the navigation menu to view the product list

## API Endpoints

### `/api/products` (Activity 1)

**Method**: GET

**Response**:

```json
[
  {
    "id": 1,
    "name": "Laptop",
    "price": 1200.5,
    "stock": 25
  },
  {
    "id": 2,
    "name": "Headphones",
    "price": 50.0,
    "stock": 100
  }
]
```

### `/api/productlist` (Activity 3)

**Method**: GET

**Response** (with nested Category):

```json
[
  {
    "id": 1,
    "name": "Laptop",
    "price": 1200.5,
    "stock": 25,
    "category": {
      "id": 101,
      "name": "Electronics"
    }
  },
  {
    "id": 2,
    "name": "Headphones",
    "price": 50.0,
    "stock": 100,
    "category": {
      "id": 102,
      "name": "Accessories"
    }
  }
]
```

## Activities Completed

### Activity 1: Integration Code Generation

✅ Created base application structure (Blazor + Web API)
✅ Implemented FetchProducts.razor component
✅ Created API call logic using HttpClient
✅ Implemented JSON deserialization
✅ Added proper error handling

**Key Implementation**:

- HttpClient integration in `FetchProducts.razor`
- Case-insensitive JSON deserialization using `JsonSerializerOptions`
- Async/await pattern for API calls

### Activity 2: Debugging Integration Issues

✅ Configured CORS policy in ServerApp Program.cs
✅ Updated API endpoint from `/api/products` to `/api/productlist`
✅ Implemented comprehensive error handling
✅ Added try-catch blocks for HTTP and JSON errors

**Key Fixes**:

- Added `AddCors()` service and `UseCors()` middleware
- Updated FetchProducts component to use correct endpoint
- Added error messaging for debugging

### Activity 3: JSON Structure Implementation

✅ Implemented nested Category object in API responses
✅ Created matching C# classes for deserialization
✅ Validated JSON structure follows industry standards

**Key Features**:

- Product class with nested Category property
- Proper property naming conventions
- Support for optional fields (nullable properties)

### Activity 4: Optimization & Consolidation

✅ Reviewed and consolidated all three activities
✅ Added comprehensive documentation
✅ Optimized error handling and user experience
✅ Created reflection summary

## Performance Optimizations

1. **Efficient HTTP Configuration**: Single HttpClient instance reused across requests
2. **Case-Insensitive Deserialization**: Handles varying JSON naming conventions
3. **Error Handling**: Graceful degradation with user-friendly error messages
4. **Minimal Dependencies**: Leverages built-in .NET JSON serialization

## Best Practices Implemented

### Back-End (ServerApp)

- CORS policy properly configured for development
- RESTful API design with appropriate endpoints
- Consistent JSON response structure
- Comments explaining API functionality

### Front-End (ClientApp)

- Dependency injection for HttpClient
- Async/await for non-blocking API calls
- Comprehensive error handling (HTTP, JSON, unexpected errors)
- Null-coalescing operators for safe property access
- Case-insensitive JSON deserialization

## How Copilot Assisted in Development

### Code Generation

- Generated HttpClient integration code for API communication
- Created proper async/await patterns
- Suggested JSON serialization options for case-insensitivity

### Problem Solving

- Recommended CORS configuration solution
- Suggested error handling patterns for HTTP and JSON errors
- Proposed nested object structure design

### Optimization

- Recommended property naming conventions
- Suggested performance-conscious patterns
- Guided documentation and comments

### Challenges Overcome with Copilot

1. **CORS Configuration**: Copilot suggested the proper middleware configuration
2. **Error Handling**: Copilot recommended specific exception types to catch
3. **JSON Deserialization**: Copilot suggested `PropertyNameCaseInsensitive = true` option
4. **API Route Updates**: Copilot helped identify necessary endpoint changes

## Testing the Integration

### Manual Testing Steps

1. Start the ServerApp on `http://localhost:5000`
2. Verify API responses:
   - Visit `http://localhost:5000/api/products`
   - Visit `http://localhost:5000/api/productlist`
3. Start the ClientApp on `http://localhost:5001`
4. Navigate to the "Products" page
5. Verify products display correctly with categories

### Browser Console

- Check for any JavaScript errors
- Verify no CORS errors appear
- Confirm API response is properly deserialized

## Lessons Learned

### Full-Stack Development

- Understanding CORS is crucial for front-end/back-end communication
- Proper error handling improves user experience significantly
- JSON structure design impacts front-end consumption

### Using Copilot Effectively

- Asking specific questions about implementation details yields better results
- Requesting best practices ensures production-ready code
- Iterative refinement improves code quality
- Clear problem statements help Copilot generate relevant solutions

### Development Workflow

- Setting up base projects first saves time
- Implementing debugging fixes before optimization ensures stability
- Comprehensive documentation prevents future confusion
- Testing at each stage catches issues early

## Future Enhancements

1. **Database Integration**: Replace hardcoded data with database queries
2. **Authentication**: Add user authentication and authorization
3. **Caching**: Implement caching strategies on both client and server
4. **Pagination**: Add product list pagination
5. **Search/Filter**: Implement product filtering capabilities
6. **Unit Tests**: Add comprehensive test coverage
7. **API Documentation**: Add OpenAPI/Swagger documentation

## Technologies Used

- **Language**: C# 12
- **.NET Version**: .NET 9.0
- **Front-End**: Blazor WebAssembly
- **Back-End**: ASP.NET Core with Minimal APIs
- **Serialization**: System.Text.Json
- **HTTP**: HttpClient (via dependency injection)

## Resources

- [Microsoft Blazor Documentation](https://docs.microsoft.com/aspnet/core/blazor/)
- [ASP.NET Core Minimal APIs](https://docs.microsoft.com/aspnet/core/fundamentals/minimal-apis)
- [CORS in ASP.NET Core](https://docs.microsoft.com/aspnet/core/security/cors)
- [JSON Serialization in .NET](https://docs.microsoft.com/dotnet/standard/serialization/system-text-json/)

## Author Notes

This project successfully demonstrates the complete workflow of building a full-stack application with Copilot assistance. The integration between front-end and back-end is seamless, error handling is robust, and the code follows industry best practices. The project serves as a solid foundation for more complex inventory management features.

---

**Project Status**: ✅ Complete and Production-Ready

**Last Updated**: January 30, 2026
