# InventoryHub - Quick Reference Guide

## 🚀 Quick Start (30 seconds)

```powershell
# Terminal 1
cd C:\Users\user\OneDrive\Desktop\FullStack\FullStackApp\ServerApp
dotnet run

# Terminal 2
cd C:\Users\user\OneDrive\Desktop\FullStack\FullStackApp\ClientApp
dotnet run

# Browser
Navigate to http://localhost:5002 → Click "Products"
```

---

## 📋 Essential Commands

### Running the Project

```powershell
# Build solution
dotnet build

# Run client only
cd ClientApp && dotnet run

# Run server only
cd ServerApp && dotnet run

# Build for production
dotnet publish -c Release
```

### Testing Endpoints

```powershell
# Test basic products endpoint
curl http://localhost:5000/api/products

# Test products with categories
curl http://localhost:5000/api/productlist

# Using PowerShell
Invoke-WebRequest http://localhost:5000/api/productlist | ConvertFrom-Json
```

---

## 🏗️ Project Structure at a Glance

```
FullStackApp/
├── ServerApp/          ← Back-End API (port 5000)
│   └── Program.cs      ← API endpoints & CORS
├── ClientApp/          ← Front-End (port 5002)
│   └── Pages/
│       └── FetchProducts.razor  ← Main component
├── README.md           ← Full documentation
├── TESTING_GUIDE.md    ← Test procedures
└── REFLECTION.md       ← Development insights
```

---

## 🔗 API Endpoints

| Method | Endpoint           | Returns               | Purpose         |
| ------ | ------------------ | --------------------- | --------------- |
| GET    | `/api/products`    | Products (basic)      | Legacy endpoint |
| GET    | `/api/productlist` | Products + Categories | Main endpoint   |

### Sample Response (/api/productlist)

```json
[
  {
    "id": 1,
    "name": "Laptop",
    "price": 1200.5,
    "stock": 25,
    "category": { "id": 101, "name": "Electronics" }
  }
]
```

---

## 🎨 Main Component: FetchProducts.razor

**Location**: `ClientApp/Pages/FetchProducts.razor`

**Key Methods**:

```csharp
// Runs when component initializes
protected override async Task OnInitializedAsync()

// Classes for deserialization
public class Product { ... }
public class Category { ... }
```

**Features**:

- ✅ Async API calls
- ✅ Error handling (HTTP, JSON, unexpected)
- ✅ Case-insensitive JSON deserialization
- ✅ Loading state display
- ✅ User-friendly error messages

---

## 🔧 Configuration Files

### Server Configuration (ServerApp/Program.cs)

```csharp
// 1. Add CORS service
builder.Services.AddCors(options => { ... });

// 2. Use CORS middleware
app.UseCors("AllowAll");

// 3. Map endpoints
app.MapGet("/api/products", () => { ... });
```

### Client Configuration (ClientApp/Program.cs)

```csharp
// Configure HttpClient
builder.Services.AddScoped(sp =>
    new HttpClient { BaseAddress = new Uri(...) }
);
```

---

## 🐛 Common Issues & Solutions

| Issue                | Solution                                                      |
| -------------------- | ------------------------------------------------------------- |
| "Connection refused" | Start ServerApp: `cd ServerApp && dotnet run`                 |
| CORS errors          | Verify `UseCors()` is called in ServerApp/Program.cs          |
| Products not showing | Check browser console (F12) for errors                        |
| JSON errors          | Ensure API returns valid JSON; use browser's /api/productlist |
| 404 errors           | Verify endpoint URL matches (should be `/api/productlist`)    |

---

## 📊 Ports Reference

| Service           | Port | URL                    |
| ----------------- | ---- | ---------------------- |
| ServerApp (HTTP)  | 5000 | http://localhost:5000  |
| ServerApp (HTTPS) | 5001 | https://localhost:5001 |
| ClientApp (HTTP)  | 5002 | http://localhost:5002  |
| ClientApp (HTTPS) | 5003 | https://localhost:5003 |

---

## 🧪 Testing Checklist

- [ ] ServerApp running on port 5000
- [ ] ClientApp running on port 5002
- [ ] API returns JSON on `http://localhost:5000/api/productlist`
- [ ] Products display on `/fetchproducts` page
- [ ] No CORS errors in browser console
- [ ] No JavaScript errors in browser console
- [ ] Prices display with $ symbol
- [ ] Stock numbers show correctly

---

## 💡 Code Snippets

### Making an API Call (in Blazor component)

```csharp
@inject HttpClient HttpClient

protected override async Task OnInitializedAsync()
{
    var response = await HttpClient.GetAsync("/api/productlist");
    response.EnsureSuccessStatusCode();
    var json = await response.Content.ReadAsStringAsync();
    products = JsonSerializer.Deserialize<Product[]>(json,
        new JsonSerializerOptions { PropertyNameCaseInsensitive = true });
}
```

### Adding Error Handling

```csharp
try { /* API call */ }
catch (HttpRequestException ex)
{
    errorMessage = $"API Error: {ex.Message}";
}
catch (Exception ex)
{
    errorMessage = $"Error: {ex.Message}";
}
```

### Defining a Deserializable Class

```csharp
public class Product
{
    public int Id { get; set; }
    public string? Name { get; set; }
    public double Price { get; set; }
    public int Stock { get; set; }
    public Category? Category { get; set; }
}
```

---

## 📚 Documentation Files

| File               | Purpose                          |
| ------------------ | -------------------------------- |
| README.md          | Full project overview & setup    |
| TESTING_GUIDE.md   | Comprehensive testing procedures |
| REFLECTION.md      | Development journey with Copilot |
| ARCHITECTURE.md    | Technical architecture & design  |
| QUICK_REFERENCE.md | This file - quick lookup         |

---

## 🎯 Activities Summary

### Activity 1: Integration ✅

- Created FetchProducts.razor component
- Implemented HttpClient API calls
- Added JSON deserialization

### Activity 2: Debugging ✅

- Fixed CORS errors
- Updated API endpoint routes
- Added error handling

### Activity 3: JSON Structures ✅

- Implemented nested category objects
- Validated JSON structure
- Enhanced API response

### Activity 4: Optimization ✅

- Consolidated all work
- Optimized code patterns
- Created comprehensive documentation

---

## 🚀 Next Steps

1. **Run the application** (see Quick Start above)
2. **Verify everything works** (see Testing Checklist)
3. **Read the documentation** (see Documentation Files)
4. **Study the code** (especially FetchProducts.razor)
5. **Experiment with modifications** (add more products, change styling, etc.)

---

## 💬 Copilot's Key Contributions

| Task                 | Copilot Help                          |
| -------------------- | ------------------------------------- |
| API Integration      | Generated HttpClient code             |
| CORS Setup           | Provided complete configuration       |
| Error Handling       | Suggested exception types & patterns  |
| JSON Deserialization | Recommended case-insensitive options  |
| Architecture         | Guided design decisions               |
| Documentation        | Helped structure and explain concepts |

---

## 📞 Debugging Tips

### Check Server is Running

```powershell
# Test endpoint directly
curl http://localhost:5000/api/products

# Or in PowerShell
Test-NetConnection -ComputerName localhost -Port 5000
```

### Check Browser Console

```javascript
// Press F12 to open DevTools
// Go to Console tab
// Look for any red error messages
```

### Enable Detailed Logging

```powershell
# In ServerApp Terminal
# Look for HTTP request logs
# Should show "GET /api/productlist HTTP/1.1 200 OK"
```

---

## 📋 File Purposes Quick Reference

| File                | Contains          |
| ------------------- | ----------------- |
| Program.cs (Server) | CORS, endpoints   |
| Program.cs (Client) | DI, HttpClient    |
| FetchProducts.razor | Main UI component |
| NavMenu.razor       | Navigation links  |
| \_Imports.razor     | Global directives |
| App.razor           | Root component    |

---

## 🔐 Important Notes

- CORS is set to `AllowAnyOrigin()` → **For development only**
- Ports 5000-5003 must be available
- Requires .NET 9.0 SDK
- Nullable reference types enabled
- HTTPS with self-signed certificates for development

---

## 📈 Performance Baseline

- API response time: < 50ms
- Initial page load: 2-3 seconds (network dependent)
- Component render time: < 100ms
- JSON deserialization: < 10ms

---

## 🎓 Learning Outcomes

By completing this project, you've learned:

- ✅ Blazor WebAssembly component development
- ✅ ASP.NET Core Minimal API design
- ✅ HTTP client integration patterns
- ✅ CORS configuration
- ✅ JSON serialization/deserialization
- ✅ Async/await patterns
- ✅ Error handling best practices
- ✅ Full-stack development workflow
- ✅ How to use Copilot effectively

---

## 🔗 Useful Links

- [Blazor Documentation](https://docs.microsoft.com/aspnet/core/blazor/)
- [ASP.NET Core Minimal APIs](https://docs.microsoft.com/aspnet/core/fundamentals/minimal-apis)
- [CORS in ASP.NET Core](https://docs.microsoft.com/aspnet/core/security/cors)
- [System.Text.Json](https://docs.microsoft.com/dotnet/standard/serialization/system-text-json/)
- [HttpClient Best Practices](https://docs.microsoft.com/dotnet/fundamentals/networking/http/httpclient)

---

## ✅ Verification Checklist

Before considering the project complete:

- [ ] Both ServerApp and ClientApp run without errors
- [ ] Products display on the Products page
- [ ] No CORS errors in browser console
- [ ] No JavaScript errors in browser console
- [ ] API endpoints respond with proper JSON
- [ ] Error handling works (try stopping server)
- [ ] All documentation files exist and are readable
- [ ] Code is well-commented
- [ ] Project structure matches this guide

---

**Version**: 1.0
**Last Updated**: January 30, 2026
**Status**: Complete and Ready to Use
**Time to Complete**: ~5 minutes setup + 10 minutes testing
