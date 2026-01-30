# InventoryHub - Complete Setup and Testing Guide

## Quick Start Guide

### Prerequisites

- .NET 9.0 SDK or later
- A terminal or VS Code with integrated terminal
- A web browser (Chrome, Firefox, Edge, Safari)

### Step 1: Navigate to the Project

```powershell
cd "C:\Users\user\OneDrive\Desktop\FullStack\FullStackApp"
```

### Step 2: Open Two Terminal Windows

**Terminal 1 - Back-End Server**:

```powershell
cd ServerApp
dotnet run
```

You should see output like:

```
Building...
info: Microsoft.Hosting.Lifetime[14]
      Now listening on: http://localhost:5000
      Now listening on: https://localhost:5001
```

**Terminal 2 - Front-End Client**:

```powershell
cd ClientApp
dotnet run
```

You should see output like:

```
Building...
info: Microsoft.Hosting.Lifetime[14]
      Now listening on: http://localhost:5002
      Now listening on: https://localhost:5003
```

### Step 3: Open the Application

1. Open your browser
2. Navigate to `http://localhost:5002` (or `https://localhost:5003` for HTTPS)
3. Click on the "Products" link in the navigation menu

### Expected Result

You should see a list of products:

- Laptop - $1200.5 (Stock: 25)
- Headphones - $50 (Stock: 100)

---

## Detailed Testing Procedures

### Test 1: API Endpoint Testing

#### Test 1.1: Legacy Products Endpoint

**Endpoint**: `GET /api/products`

**Using Browser**:

1. Navigate to `http://localhost:5000/api/products`
2. You should see JSON response with basic product data

**Expected Response**:

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

#### Test 1.2: Enhanced Product List Endpoint

**Endpoint**: `GET /api/productlist`

**Using Browser**:

1. Navigate to `http://localhost:5000/api/productlist`
2. You should see JSON response with nested category data

**Expected Response**:

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

#### Test 1.3: Using PowerShell with Invoke-WebRequest

```powershell
# Test the API endpoint using PowerShell
Invoke-WebRequest -Uri "http://localhost:5000/api/productlist" | Select-Object -ExpandProperty Content | ConvertFrom-Json | Format-Table

# Output should display products with their details
```

### Test 2: Front-End Component Testing

#### Test 2.1: Component Renders Successfully

1. Navigate to `http://localhost:5002`
2. Click "Products" in navigation menu
3. Verify the "Product List" heading appears
4. Verify the loading state initially shows "Loading..."
5. After 1-2 seconds, verify products display

#### Test 2.2: Product Display

Verify each product shows:

- Product name
- Price with $ symbol
- Stock quantity

Example of expected display:

```
• Laptop - $1200.5 (Stock: 25)
• Headphones - $50 (Stock: 100)
```

#### Test 2.3: Browser Console Check

1. Open Browser Developer Tools (F12)
2. Go to Console tab
3. Verify no CORS errors appear
4. Verify no JavaScript exceptions appear
5. You should see successful API calls logged

### Test 3: Error Handling Testing

#### Test 3.1: Network Error Simulation

1. Stop the ServerApp (Ctrl+C in Terminal 1)
2. Navigate back to the Products page or refresh
3. Verify error message appears: "Error: HTTP Error: Connection refused"
4. Verify the error displays in the UI, not just console
5. Restart ServerApp for next tests

#### Test 3.2: Invalid JSON Response

This test requires modifying the API response (advanced):

1. Edit `ServerApp/Program.cs`
2. Intentionally break the JSON (e.g., missing closing brace)
3. Verify client catches JSON error gracefully
4. Expected message: "Error: JSON Deserialization Error: ..."

#### Test 3.3: Case Insensitivity Testing

1. The API returns lowercase JSON properties (`"name"`, `"price"`)
2. The C# classes use PascalCase (`Name`, `Price`)
3. Due to `PropertyNameCaseInsensitive = true`, deserialization succeeds
4. If this setting is removed, deserialization should fail

---

## Performance Testing

### Test 4: Page Load Time

#### Without Developer Tools

1. Open DevTools (F12)
2. Go to Network tab
3. Set throttling to "Fast 3G" or "Slow 3G"
4. Navigate to Products page
5. Observe network request timing
6. Expected: API call should complete in under 1-2 seconds

#### Expected Network Timeline

```
GET /api/productlist:
  - DNS Lookup: ~50ms
  - Connection: ~100ms
  - Wait (TTFB): ~10ms
  - Content Download: ~5ms
  - Total: ~165ms
```

### Test 5: Concurrent Requests

1. Rapidly navigate to Products page multiple times
2. Verify no race conditions occur
3. Verify data remains consistent
4. Verify no duplicate API calls on component initialization

---

## CORS Testing

### Test 6: CORS Headers

Using Browser DevTools or curl:

```powershell
# Check CORS headers in response
$response = Invoke-WebRequest -Uri "http://localhost:5000/api/productlist" -Verbose 4>&1
$response.Headers | Select-Object -Property "*"
```

You should see headers like:

```
Access-Control-Allow-Origin: *
Access-Control-Allow-Methods: *
Access-Control-Allow-Headers: *
```

### Test 7: CORS Error Scenario (Advanced)

To test CORS restrictions:

1. Modify `Program.cs` CORS policy:

```csharp
// Change from AllowAnyOrigin() to specific origin
policy.WithOrigins("http://localhost:5003")
```

2. Access from `http://localhost:5002` (different port)
3. Verify CORS error appears

---

## Component Functionality Testing

### Test 8: Navigation

1. Start on Home page
2. Click "Products" link
3. Verify FetchProducts component loads
4. Verify URL shows `/fetchproducts`
5. Click "Home" link
6. Verify home page displays
7. Click "Products" again
8. Verify component reinitializes and fetches fresh data

### Test 9: Data Binding

1. Products page displays
2. Inspect rendered HTML (F12 > Elements)
3. Verify each product is wrapped in `<li>` tags
4. Verify all product data displays correctly
5. Verify nested category data is NOT displayed (by design)

### Test 10: Refresh Behavior

1. On Products page
2. Press F5 to refresh browser
3. Verify API call happens again
4. Verify data reloads and displays correctly
5. No caching yet (confirm expected behavior)

---

## Debugging Tips

### If Products Don't Display:

**Check 1: Is the server running?**

```powershell
# Check if ServerApp is listening
netstat -ano | findstr :5000
```

**Check 2: Check browser console for errors**

- Press F12 to open DevTools
- Go to Console tab
- Look for red error messages
- Common errors:
  - `TypeError: Cannot read properties of null`
  - `Failed to fetch` (network error)
  - CORS policy violations

**Check 3: Check server logs**

- Look at Terminal 1 (ServerApp)
- Should show HTTP requests being processed
- Should show 200 OK responses

**Check 4: Verify endpoints exist**

```powershell
# Test endpoints directly
curl http://localhost:5000/api/products
curl http://localhost:5000/api/productlist
```

### If CORS Errors Appear:

1. Verify `UseCors()` middleware is called in `Program.cs`
2. Verify it's called BEFORE `MapGet()` endpoints
3. Verify CORS is properly configured:

```csharp
builder.Services.AddCors(options =>
{
    options.AddPolicy("AllowAll", policy =>
        policy.AllowAnyOrigin()
              .AllowAnyMethod()
              .AllowAnyHeader());
});
```

### If JSON Deserialization Fails:

1. Check server is returning valid JSON (test in browser)
2. Verify C# property names match JSON fields
3. Verify `PropertyNameCaseInsensitive = true` is set
4. Check for null reference exceptions

---

## Advanced Testing

### Test 11: Adding More Products

Edit `ServerApp/Program.cs` to add more products:

```csharp
app.MapGet("/api/productlist", () =>
{
    return new[]
    {
        new
        {
            Id = 1,
            Name = "Laptop",
            Price = 1200.50,
            Stock = 25,
            Category = new { Id = 101, Name = "Electronics" }
        },
        new
        {
            Id = 2,
            Name = "Headphones",
            Price = 50.00,
            Stock = 100,
            Category = new { Id = 102, Name = "Accessories" }
        },
        new
        {
            Id = 3,
            Name = "Monitor",
            Price = 299.99,
            Stock = 15,
            Category = new { Id = 101, Name = "Electronics" }
        }
    };
});
```

### Test 12: Testing with Postman (Optional)

If you have Postman installed:

1. Create new request
2. Set method to GET
3. Enter URL: `http://localhost:5000/api/productlist`
4. Click Send
5. Verify JSON response displays

### Test 13: Testing with cURL (PowerShell)

```powershell
# Install curl if needed
# Test endpoint
curl.exe -X GET http://localhost:5000/api/productlist

# Pretty print JSON (requires ConvertFrom-Json)
curl.exe -X GET http://localhost:5000/api/productlist | ConvertFrom-Json | ConvertTo-Json
```

---

## Final Verification Checklist

- [ ] ServerApp runs on `http://localhost:5000`
- [ ] ClientApp runs on `http://localhost:5002`
- [ ] `/api/products` endpoint returns basic product data
- [ ] `/api/productlist` endpoint returns nested category data
- [ ] Products page displays product list without errors
- [ ] No CORS errors in browser console
- [ ] No JavaScript errors in browser console
- [ ] Product names display correctly
- [ ] Prices display with $ symbol
- [ ] Stock quantities display correctly
- [ ] Component handles errors gracefully
- [ ] Navigating back and forth works smoothly
- [ ] API requests complete in reasonable time

---

## Next Steps

After successful testing:

1. **Review the Code**: Read through `FetchProducts.razor` and understand the implementation
2. **Study the Patterns**: Note how error handling and async/await are implemented
3. **Explore Enhancements**: Consider adding filtering, sorting, or pagination
4. **Add Tests**: Write unit tests for the component logic
5. **Optimize**: Profile and optimize based on measurements

---

## Troubleshooting Summary Table

| Issue                  | Symptom                                     | Solution                                              |
| ---------------------- | ------------------------------------------- | ----------------------------------------------------- |
| Server not running     | "Connection refused" error                  | Start ServerApp: `cd ServerApp && dotnet run`         |
| Client can't connect   | No products displayed                       | Verify ServerApp port is 5000 in FetchProducts.razor  |
| CORS errors            | "Access to XMLHttpRequest blocked"          | Ensure `UseCors()` is configured in ServerApp         |
| JSON parsing fails     | "JSON Deserialization Error"                | Verify JSON format matches C# class structure         |
| Products don't display | Blank list after loading                    | Check browser console for errors; verify API response |
| Component not loading  | 404 error when navigating to /fetchproducts | Verify FetchProducts.razor is in Pages folder         |

---

**Test Plan Version**: 1.0
**Last Updated**: January 30, 2026
**Status**: Ready for Comprehensive Testing
