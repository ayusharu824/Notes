# .NET Interview Q&A Notes

### Complete guide — Latest & Important Questions for Interview Preparation

---

## SECTION 1 — .NET Fundamentals

---

**Q1. What is .NET? What is the difference between .NET Framework, .NET Core and .NET 5/6/7/8?**

**A:**

||.NET Framework|.NET Core|.NET 5+|
|---|---|---|---|
|Platform|Windows only|Cross platform|Cross platform|
|Open source|❌ No|✅ Yes|✅ Yes|
|Performance|Moderate|High|Highest|
|Latest version|4.8 (last ever)|3.1 (last)|.NET 8 (current LTS)|
|Use when|Legacy Windows apps|New cross-platform apps|All new projects|

**.NET 5+** unified .NET Framework and .NET Core into one platform. Microsoft skipped version 4 to avoid confusion with .NET Framework 4.x.

---

**Q2. What is CLR (Common Language Runtime)?**

**A:** CLR is the runtime engine of .NET. It manages:

- **Memory management** — Garbage Collection
- **Type safety** — Ensures type correctness
- **Exception handling** — Structured exception handling
- **JIT Compilation** — Converts IL code to native machine code
- **Thread management** — Threading and synchronization
- **Security** — Code access security

```
C# Code → C# Compiler → IL (Intermediate Language) → CLR → JIT → Native Code → Execution
```

---

**Q3. What is IL (Intermediate Language)? What is JIT?**

**A:**

- **IL (MSIL)** — When you compile C# code, it doesn't compile to machine code directly. It compiles to IL — a CPU-independent instruction set.
- **JIT (Just-In-Time Compiler)** — CLR uses JIT to convert IL to native machine code at runtime, just before execution.

```
C# → IL Code (platform independent) → JIT → Native Code (platform specific)
```

**Types of JIT:**

- **Normal JIT** — Compiles methods on first call, caches result.
- **Eager JIT (AOT)** — Compiles everything upfront before execution. Used in .NET Native, Blazor WASM.

---

**Q4. What is the difference between managed and unmanaged code?**

**A:**

- **Managed Code** — Code that runs under CLR control. CLR handles memory, GC, exceptions. Example: C#, VB.NET code.
- **Unmanaged Code** — Code that runs outside CLR. Developer manages memory manually. Example: C, C++, COM components.

```csharp
// Managed code — CLR handles memory ✅
public class Employee { public string Name { get; set; } }

// Calling unmanaged code using P/Invoke
[DllImport("kernel32.dll")]
public static extern bool Beep(int frequency, int duration);

Beep(1000, 500);  // calls unmanaged Windows API
```

---

**Q5. What is the difference between Stack and Heap memory?**

**A:**

||Stack|Heap|
|---|---|---|
|Stores|Value types, method calls, references|Reference type objects|
|Size|Small, fixed|Large, dynamic|
|Speed|Fast|Slower|
|Management|Auto (LIFO)|GC managed|
|Thread|Each thread has own stack|Shared across threads|

```csharp
void Method()
{
    int x = 5;              // x stored on Stack
    Employee emp = new Employee();  // emp reference on Stack, object on Heap
}
// When method ends — x and emp reference removed from Stack
// Employee object on Heap — GC cleans up when no references
```

---

**Q6. What is Garbage Collection? How does it work?**

**A:** GC automatically reclaims memory of objects no longer referenced. Works in 3 generations:

- **Generation 0** — Newly created, short-lived objects. Collected most frequently.
- **Generation 1** — Survived Gen 0 collection. Buffer between 0 and 2.
- **Generation 2** — Long-lived objects (static, singletons). Collected least frequently.

```csharp
// GC lifecycle
Employee emp = new Employee();  // object in Gen 0
// ... emp used ...
emp = null;  // no reference — eligible for GC

// Force GC — avoid in production!
GC.Collect();
GC.WaitForPendingFinalizers();

// Check generation
Console.WriteLine(GC.GetGeneration(emp));  // 0, 1, or 2
```

**Large Object Heap (LOH)** — Objects > 85KB go directly to LOH, collected with Gen 2.

---

**Q7. What is the difference between Finalize and Dispose?**

**A:**

||Finalize (~Destructor)|Dispose (IDisposable)|
|---|---|---|
|Called by|GC (non-deterministic)|Developer (deterministic)|
|When|Unknown — whenever GC runs|Immediately when called|
|Control|No control|Full control|
|Use for|Last resort cleanup|Preferred way to release resources|

```csharp
public class ResourceManager : IDisposable
{
    private bool _disposed = false;

    // Destructor — called by GC as last resort
    ~ResourceManager()
    {
        Dispose(false);
    }

    // IDisposable — called by developer
    public void Dispose()
    {
        Dispose(true);
        GC.SuppressFinalize(this);  // tell GC not to call finalizer
    }

    protected virtual void Dispose(bool disposing)
    {
        if (!_disposed)
        {
            if (disposing)
            {
                // release managed resources
            }
            // release unmanaged resources
            _disposed = true;
        }
    }
}

// Always use using statement
using (var rm = new ResourceManager())
{
    // use resource
}  // Dispose() called automatically ✅
```

---

**Q8. What is boxing and unboxing?**

**A:**

- **Boxing** — Converting value type → object (reference type). Allocates on heap.
- **Unboxing** — Converting object → value type. Requires explicit cast.

```csharp
int num = 42;
object boxed = num;      // Boxing — heap allocation ⚠️
int unboxed = (int)boxed; // Unboxing — casting ⚠️

// Performance issue — avoid in loops
List<object> list = new List<object>();
for (int i = 0; i < 10000; i++)
    list.Add(i);  // 10000 boxing operations! ❌

// Use generics instead ✅
List<int> list = new List<int>();
for (int i = 0; i < 10000; i++)
    list.Add(i);  // no boxing ✅
```

---

---

## SECTION 2 — ASP.NET Core

---

**Q9. What is ASP.NET Core? How is it different from ASP.NET?**

**A:**

||ASP.NET|ASP.NET Core|
|---|---|---|
|Platform|Windows only|Cross platform|
|Performance|Moderate|Very high|
|Open source|❌|✅|
|Dependency injection|Optional|Built-in|
|Middleware|HTTP Modules/Handlers|Middleware pipeline|
|Hosting|IIS only|IIS, Kestrel, Docker, Linux|

ASP.NET Core is a complete rewrite — faster, modular, cross-platform.

---

**Q10. What is the request pipeline in ASP.NET Core? What is middleware?**

**A:** ASP.NET Core processes requests through a **middleware pipeline** — a series of components that handle requests and responses in order.

```csharp
// Program.cs — middleware order matters!
var app = builder.Build();

app.UseExceptionHandler();   // 1. handle exceptions
app.UseHttpsRedirection();   // 2. redirect HTTP to HTTPS
app.UseStaticFiles();        // 3. serve static files
app.UseRouting();            // 4. match routes
app.UseAuthentication();     // 5. who are you?
app.UseAuthorization();      // 6. what can you do?
app.MapControllers();        // 7. route to controllers

app.Run();
```

**Custom Middleware:**

```csharp
public class LoggingMiddleware
{
    private readonly RequestDelegate _next;

    public LoggingMiddleware(RequestDelegate next) { _next = next; }

    public async Task Invoke(HttpContext context)
    {
        Console.WriteLine($"Request: {context.Request.Path}");
        await _next(context);  // call next middleware
        Console.WriteLine($"Response: {context.Response.StatusCode}");
    }
}

// Register
app.UseMiddleware<LoggingMiddleware>();
```

---

**Q11. What is the difference between app.Use(), app.Run() and app.Map()?**

**A:**

- **app.Use()** — Adds middleware that CAN pass request to next middleware.
- **app.Run()** — Terminal middleware — does NOT pass to next. Ends pipeline.
- **app.Map()** — Branches pipeline based on path.

```csharp
app.Use(async (context, next) =>
{
    Console.WriteLine("Before");
    await next();  // passes to next middleware
    Console.WriteLine("After");
});

app.Map("/admin", adminApp =>
{
    adminApp.Run(async context =>
    {
        await context.Response.WriteAsync("Admin area");
    });
});

app.Run(async context =>
{
    await context.Response.WriteAsync("Hello World");  // terminal — ends here
});
```

---

**Q12. What is Dependency Injection in ASP.NET Core? How is it configured?**

**A:** ASP.NET Core has built-in DI container. Services are registered in Program.cs and injected via constructor.

```csharp
// Program.cs
builder.Services.AddScoped<IUserService, UserService>();
builder.Services.AddTransient<IEmailService, EmailService>();
builder.Services.AddSingleton<ILogger, Logger>();

// Controller — injected automatically
public class UserController : ControllerBase
{
    private readonly IUserService _userService;

    public UserController(IUserService userService)
    {
        _userService = userService;
    }
}
```

**Lifetime:**

- **Singleton** — One instance for app lifetime.
- **Scoped** — One instance per HTTP request.
- **Transient** — New instance every time.

---

**Q13. What is the difference between IActionResult and ActionResult<T>?**

**A:**

- **IActionResult** — Non-generic. Can return any result type.
- **ActionResult<T>** — Generic. Specifies return type — better for Swagger docs and type safety.

```csharp
// IActionResult — non-generic
[HttpGet("{id}")]
public IActionResult GetEmployee(int id)
{
    var emp = _service.GetById(id);
    if (emp == null) return NotFound();
    return Ok(emp);
}

// ActionResult<T> — generic, preferred ✅
[HttpGet("{id}")]
public ActionResult<Employee> GetEmployee(int id)
{
    var emp = _service.GetById(id);
    if (emp == null) return NotFound();
    return emp;  // implicit conversion to Ok(emp) ✅
}
```

---

**Q14. What are Filters in ASP.NET Core? What are the types?**

**A:** Filters run code before or after specific stages of request processing.

**Types:**

|Filter|When runs|Use case|
|---|---|---|
|Authorization|First|Check if user is authorized|
|Resource|After auth, before model binding|Caching|
|Action|Before/after action method|Logging, validation|
|Exception|When exception occurs|Global error handling|
|Result|Before/after result execution|Response formatting|

```csharp
// Custom Action Filter
public class LogActionFilter : IActionFilter
{
    public void OnActionExecuting(ActionExecutingContext context)
    {
        Console.WriteLine($"Before: {context.ActionDescriptor.DisplayName}");
    }

    public void OnActionExecuted(ActionExecutedContext context)
    {
        Console.WriteLine($"After: {context.ActionDescriptor.DisplayName}");
    }
}

// Apply to controller or action
[ServiceFilter(typeof(LogActionFilter))]
public class EmployeeController : ControllerBase { }

// Global filter
builder.Services.AddControllers(options =>
{
    options.Filters.Add<LogActionFilter>();
});
```

---

**Q15. What is Model Binding and Model Validation in ASP.NET Core?**

**A:**

- **Model Binding** — Automatically maps HTTP request data (route, query, body) to action parameters.
- **Model Validation** — Validates model using Data Annotations.

```csharp
public class CreateEmployeeDto
{
    [Required(ErrorMessage = "Name is required")]
    [StringLength(100)]
    public string Name { get; set; }

    [Range(18, 65, ErrorMessage = "Age must be between 18 and 65")]
    public int Age { get; set; }

    [EmailAddress]
    public string Email { get; set; }

    [Required]
    public decimal Salary { get; set; }
}

[HttpPost]
public ActionResult<Employee> Create([FromBody] CreateEmployeeDto dto)
{
    if (!ModelState.IsValid)
        return BadRequest(ModelState);

    // process...
    return CreatedAtAction(nameof(GetEmployee), new { id = emp.Id }, emp);
}
```

**Binding Sources:**

```csharp
public IActionResult Create(
    [FromBody] CreateDto dto,       // from request body (JSON)
    [FromRoute] int id,             // from URL route
    [FromQuery] string search,      // from query string ?search=abc
    [FromHeader] string token,      // from request header
    [FromForm] IFormFile file)      // from form data
```

---

**Q16. What is the difference between AddMvc(), AddControllers(), AddRazorPages()?**

**A:**

```csharp
// AddControllers() — API only, no views ✅ for Web API
builder.Services.AddControllers();

// AddRazorPages() — Razor pages only, no API controllers
builder.Services.AddRazorPages();

// AddControllersWithViews() — MVC with views, no Razor pages
builder.Services.AddControllersWithViews();

// AddMvc() — everything (controllers + views + razor pages)
builder.Services.AddMvc();  // heaviest, use only if you need all
```

---

**Q17. What is Routing in ASP.NET Core?**

**A:** Routing matches incoming HTTP requests to controller actions.

```csharp
// Conventional routing
app.MapControllerRoute(
    name: "default",
    pattern: "{controller=Home}/{action=Index}/{id?}");

// Attribute routing — preferred for APIs ✅
[ApiController]
[Route("api/[controller]")]
public class EmployeeController : ControllerBase
{
    [HttpGet]                    // GET api/employee
    public IActionResult GetAll() { }

    [HttpGet("{id}")]            // GET api/employee/1
    public IActionResult GetById(int id) { }

    [HttpPost]                   // POST api/employee
    public IActionResult Create([FromBody] Employee emp) { }

    [HttpPut("{id}")]            // PUT api/employee/1
    public IActionResult Update(int id, [FromBody] Employee emp) { }

    [HttpDelete("{id}")]         // DELETE api/employee/1
    public IActionResult Delete(int id) { }

    [HttpGet("department/{deptId}/employees")]  // custom route
    public IActionResult GetByDept(int deptId) { }
}
```

---

---

## SECTION 3 — Entity Framework Core

---

**Q18. What is Entity Framework Core? How is it different from EF 6?**

**A:** EF Core is an ORM (Object Relational Mapper) — maps C# classes to database tables.

||EF 6|EF Core|
|---|---|---|
|Platform|.NET Framework|Cross platform|
|Performance|Moderate|Better|
|Database support|SQL Server, limited|SQL Server, SQLite, PostgreSQL, MySQL, CosmosDB|
|Lazy loading|Default on|Opt-in|
|Compiled queries|❌|✅|

```csharp
// DbContext
public class AppDbContext : DbContext
{
    public AppDbContext(DbContextOptions<AppDbContext> options) : base(options) { }

    public DbSet<Employee> Employees { get; set; }
    public DbSet<Department> Departments { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Employee>()
            .HasOne(e => e.Department)
            .WithMany(d => d.Employees)
            .HasForeignKey(e => e.DeptId);
    }
}
```

---

**Q19. What is Code First vs Database First in EF Core?**

**A:**

- **Code First** — Write C# classes first, EF generates database schema. ✅ Most common.
- **Database First** — Database exists first, EF scaffolds C# classes from it.

```csharp
// Code First — define model
public class Employee
{
    public int Id { get; set; }
    public string Name { get; set; }
    public decimal Salary { get; set; }
    public int DeptId { get; set; }
    public Department Department { get; set; }
}

// Create migration
// dotnet ef migrations add InitialCreate
// dotnet ef database update

// Database First — scaffold from existing DB
// dotnet ef dbcontext scaffold "ConnectionString" Microsoft.EntityFrameworkCore.SqlServer
```

---

**Q20. What are Migrations in EF Core?**

**A:** Migrations track changes to your model and apply them to database incrementally.

```bash
# Create migration
dotnet ef migrations add AddSalaryColumn

# Apply to database
dotnet ef database update

# Rollback to previous migration
dotnet ef database update PreviousMigrationName

# Remove last migration (if not applied)
dotnet ef migrations remove

# Generate SQL script
dotnet ef migrations script
```

```csharp
// Migration file generated
public partial class AddSalaryColumn : Migration
{
    protected override void Up(MigrationBuilder migrationBuilder)
    {
        migrationBuilder.AddColumn<decimal>(
            name: "Salary",
            table: "Employees",
            nullable: false,
            defaultValue: 0m);
    }

    protected override void Down(MigrationBuilder migrationBuilder)
    {
        migrationBuilder.DropColumn(name: "Salary", table: "Employees");
    }
}
```

---

**Q21. What is the difference between eager loading, lazy loading and explicit loading?**

**A:**

- **Eager Loading** — Load related data immediately with Include(). One query.
- **Lazy Loading** — Load related data on access. Multiple queries — N+1 problem.
- **Explicit Loading** — Manually load related data when needed.

```csharp
// Eager Loading — one query with JOIN ✅
var employees = context.Employees
    .Include(e => e.Department)
    .Include(e => e.Leaves)
    .ToList();

// Lazy Loading — loads Department when accessed (N+1 problem ⚠️)
// Must install: Microsoft.EntityFrameworkCore.Proxies
services.AddDbContext<AppDbContext>(o =>
    o.UseLazyLoadingProxies().UseSqlServer(connStr));

var employees = context.Employees.ToList();
foreach (var emp in employees)
    Console.WriteLine(emp.Department.Name);  // extra query per employee! ⚠️

// Explicit Loading — load when you decide
var employee = context.Employees.First();
context.Entry(employee).Reference(e => e.Department).Load();
context.Entry(employee).Collection(e => e.Leaves).Load();
```

---

**Q22. What is the N+1 problem in EF Core? How to fix it?**

**A:** N+1 problem — 1 query to get list + N queries for each item's related data.

```csharp
// ❌ N+1 problem — 1 + N queries
var employees = context.Employees.ToList();  // 1 query
foreach (var emp in employees)
    Console.WriteLine(emp.Department.Name);  // N queries — one per employee!

// ✅ Fix — use Include() for eager loading
var employees = context.Employees
    .Include(e => e.Department)
    .ToList();  // 1 query with JOIN ✅
```

---

**Q23. What is AsNoTracking() in EF Core? When to use it?**

**A:** By default EF Core tracks all entities for change detection. AsNoTracking() disables tracking — faster for read-only queries.

```csharp
// With tracking — EF watches for changes (slower)
var employees = context.Employees.ToList();

// Without tracking — read-only, faster ✅
var employees = context.Employees
    .AsNoTracking()
    .ToList();

// Use AsNoTracking() when:
// - Read-only operations (GET requests)
// - No need to update the data
// - Performance is priority
```

---

**Q24. What is the Repository pattern with EF Core?**

**A:**

```csharp
public interface IRepository<T> where T : class
{
    Task<T> GetByIdAsync(int id);
    Task<IEnumerable<T>> GetAllAsync();
    Task AddAsync(T entity);
    Task UpdateAsync(T entity);
    Task DeleteAsync(int id);
    Task SaveAsync();
}

public class Repository<T> : IRepository<T> where T : class
{
    private readonly AppDbContext _context;
    private readonly DbSet<T> _dbSet;

    public Repository(AppDbContext context)
    {
        _context = context;
        _dbSet = context.Set<T>();
    }

    public async Task<T> GetByIdAsync(int id) => await _dbSet.FindAsync(id);

    public async Task<IEnumerable<T>> GetAllAsync() =>
        await _dbSet.AsNoTracking().ToListAsync();

    public async Task AddAsync(T entity) => await _dbSet.AddAsync(entity);

    public async Task UpdateAsync(T entity) => _dbSet.Update(entity);

    public async Task DeleteAsync(int id)
    {
        var entity = await GetByIdAsync(id);
        if (entity != null) _dbSet.Remove(entity);
    }

    public async Task SaveAsync() => await _context.SaveChangesAsync();
}
```

---

**Q25. What is a Transaction in EF Core?**

**A:**

```csharp
// Default — SaveChanges() wraps in transaction automatically
await context.SaveChangesAsync();  // all changes in one transaction ✅

// Explicit transaction — for multiple SaveChanges()
using var transaction = await context.Database.BeginTransactionAsync();
try
{
    context.Employees.Add(newEmployee);
    await context.SaveChangesAsync();

    context.Departments.Update(dept);
    await context.SaveChangesAsync();

    await transaction.CommitAsync();  // both saved ✅
}
catch
{
    await transaction.RollbackAsync();  // both rolled back ✅
    throw;
}
```

---

---

## SECTION 4 — Authentication & Authorization

---

**Q26. What is the difference between Authentication and Authorization?**

**A:**

- **Authentication** — WHO are you? Verifying identity. (Login)
- **Authorization** — WHAT can you do? Verifying permissions. (Access control)

```csharp
app.UseAuthentication();  // first — identify user
app.UseAuthorization();   // second — check permissions

// Authentication — JWT token validates WHO you are
// Authorization — [Authorize(Role="Admin")] checks WHAT you can do
```

---

**Q27. What is JWT (JSON Web Token)? How does it work?**

**A:** JWT is a self-contained token for authentication. Contains: Header + Payload + Signature.

```
eyJhbGciOiJIUzI1NiJ9.eyJzdWIiOiJBbGljZSIsInJvbGUiOiJBZG1pbiJ9.signature
     Header                        Payload                            Signature
```

```csharp
// Generate JWT token
public string GenerateToken(User user)
{
    var claims = new[]
    {
        new Claim(ClaimTypes.Name, user.Username),
        new Claim(ClaimTypes.Role, user.Role),
        new Claim(JwtRegisteredClaimNames.Jti, Guid.NewGuid().ToString())
    };

    var key = new SymmetricSecurityKey(Encoding.UTF8.GetBytes(_config["Jwt:Key"]));
    var creds = new SigningCredentials(key, SecurityAlgorithms.HmacSha256);

    var token = new JwtSecurityToken(
        issuer: _config["Jwt:Issuer"],
        audience: _config["Jwt:Audience"],
        claims: claims,
        expires: DateTime.Now.AddHours(1),
        signingCredentials: creds
    );

    return new JwtSecurityTokenHandler().WriteToken(token);
}

// Configure JWT in Program.cs
builder.Services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
    .AddJwtBearer(options =>
    {
        options.TokenValidationParameters = new TokenValidationParameters
        {
            ValidateIssuer = true,
            ValidateAudience = true,
            ValidateLifetime = true,
            ValidateIssuerSigningKey = true,
            ValidIssuer = builder.Configuration["Jwt:Issuer"],
            ValidAudience = builder.Configuration["Jwt:Audience"],
            IssuerSigningKey = new SymmetricSecurityKey(
                Encoding.UTF8.GetBytes(builder.Configuration["Jwt:Key"]))
        };
    });
```

---

**Q28. What is the difference between [Authorize] and [AllowAnonymous]?**

**A:**

```csharp
[ApiController]
[Route("api/[controller]")]
[Authorize]  // all actions require authentication
public class EmployeeController : ControllerBase
{
    [HttpGet]
    public IActionResult GetAll() { }  // requires auth

    [HttpGet("public")]
    [AllowAnonymous]  // overrides controller-level Authorize
    public IActionResult GetPublic() { }  // no auth needed

    [HttpDelete("{id}")]
    [Authorize(Roles = "Admin")]  // only Admin role
    public IActionResult Delete(int id) { }

    [HttpPut("{id}")]
    [Authorize(Policy = "HRPolicy")]  // custom policy
    public IActionResult Update(int id) { }
}
```

---

**Q29. What is Claims-based authorization?**

**A:** Claims are key-value pairs about a user (name, role, email, department). Used for fine-grained authorization.

```csharp
// Define policy with claims
builder.Services.AddAuthorization(options =>
{
    options.AddPolicy("HRPolicy", policy =>
        policy.RequireClaim("Department", "HR"));

    options.AddPolicy("SeniorEmployee", policy =>
        policy.RequireClaim("YearsOfExperience")
              .RequireAssertion(ctx =>
                  int.Parse(ctx.User.FindFirst("YearsOfExperience").Value) >= 5));
});

// Add claims to token
var claims = new[]
{
    new Claim("Department", user.Department),
    new Claim("YearsOfExperience", user.Experience.ToString())
};
```

---

---

## SECTION 5 — ASYNC & PERFORMANCE

---

**Q30. What is async/await? How does it work internally?**

**A:** async/await enables non-blocking asynchronous code. Compiler converts async method into a state machine.

```csharp
// ❌ Synchronous — blocks thread
public Employee GetEmployee(int id)
{
    return _context.Employees.Find(id);  // thread blocked during DB call
}

// ✅ Async — thread free during await
public async Task<Employee> GetEmployeeAsync(int id)
{
    return await _context.Employees.FindAsync(id);  // thread returned to pool
}

// Usage
var emp = await GetEmployeeAsync(1);

// Multiple async operations
// ❌ Sequential — total time = t1 + t2
var emp = await GetEmployeeAsync(1);
var orders = await GetOrdersAsync(1);

// ✅ Parallel — total time = max(t1, t2)
var empTask = GetEmployeeAsync(1);
var ordersTask = GetOrdersAsync(1);
await Task.WhenAll(empTask, ordersTask);
var emp = empTask.Result;
var orders = ordersTask.Result;
```

---

**Q31. What is the difference between Task.WhenAll() and Task.WhenAny()?**

**A:**

```csharp
// Task.WhenAll — waits for ALL tasks to complete
var tasks = new[]
{
    SendEmailAsync(),
    SendSMSAsync(),
    LogAsync()
};
await Task.WhenAll(tasks);  // all three must complete

// Task.WhenAny — waits for FIRST task to complete
var tasks = new[]
{
    GetFromCacheAsync(),
    GetFromDatabaseAsync()
};
var firstCompleted = await Task.WhenAny(tasks);  // whichever is faster
var result = await firstCompleted;
```

---

**Q32. What is CancellationToken? Why is it used?**

**A:** CancellationToken allows cancelling async operations — e.g. when client disconnects.

```csharp
[HttpGet]
public async Task<IActionResult> GetAll(CancellationToken cancellationToken)
{
    var employees = await _context.Employees
        .AsNoTracking()
        .ToListAsync(cancellationToken);  // cancels if client disconnects ✅

    return Ok(employees);
}

// Manual cancellation
var cts = new CancellationTokenSource();
cts.CancelAfter(TimeSpan.FromSeconds(30));  // timeout after 30 sec

try
{
    var result = await LongRunningOperationAsync(cts.Token);
}
catch (OperationCanceledException)
{
    Console.WriteLine("Operation cancelled");
}
```

---

**Q33. What is IMemoryCache and IDistributedCache?**

**A:**

- **IMemoryCache** — In-memory cache, single server. Fast.
- **IDistributedCache** — Distributed cache (Redis, SQL Server). Multi-server.

```csharp
// IMemoryCache — single server
builder.Services.AddMemoryCache();

public class EmployeeService
{
    private readonly IMemoryCache _cache;

    public async Task<Employee> GetEmployeeAsync(int id)
    {
        string key = $"employee_{id}";

        if (!_cache.TryGetValue(key, out Employee employee))
        {
            employee = await _context.Employees.FindAsync(id);

            _cache.Set(key, employee, new MemoryCacheEntryOptions
            {
                AbsoluteExpirationRelativeToNow = TimeSpan.FromMinutes(10),
                SlidingExpiration = TimeSpan.FromMinutes(2)
            });
        }

        return employee;
    }
}

// IDistributedCache — Redis
builder.Services.AddStackExchangeRedisCache(options =>
{
    options.Configuration = "localhost:6379";
});
```

---

**Q34. What is Response Caching in ASP.NET Core?**

**A:**

```csharp
// Program.cs
builder.Services.AddResponseCaching();
app.UseResponseCaching();

// Controller action
[HttpGet]
[ResponseCache(Duration = 60, Location = ResponseCacheLocation.Any)]
public IActionResult GetAll()
{
    return Ok(_service.GetAll());
}

// No cache
[ResponseCache(NoStore = true, Location = ResponseCacheLocation.None)]
public IActionResult GetSensitiveData() { }
```

---

---

## SECTION 6 — WEB API DESIGN

---

**Q35. What is REST? What are REST principles?**

**A:** REST (Representational State Transfer) — architectural style for APIs.

**6 Principles:**

1. **Client-Server** — Separation of UI and data.
2. **Stateless** — Each request contains all info needed. No session.
3. **Cacheable** — Responses can be cached.
4. **Uniform Interface** — Standard HTTP methods, consistent URLs.
5. **Layered System** — Client doesn't know if talking to server or proxy.
6. **Code on Demand** (optional) — Server can send executable code.

```
GET    /api/employees        — get all
GET    /api/employees/1      — get by id
POST   /api/employees        — create
PUT    /api/employees/1      — update all fields
PATCH  /api/employees/1      — update partial fields
DELETE /api/employees/1      — delete
```

---

**Q36. What is the difference between PUT and PATCH?**

**A:**

- **PUT** — Replace the ENTIRE resource.
- **PATCH** — Update PARTIAL fields only.

```csharp
// PUT — send entire object
[HttpPut("{id}")]
public async Task<IActionResult> Update(int id, [FromBody] UpdateEmployeeDto dto)
{
    // replaces ALL fields
    var emp = await _context.Employees.FindAsync(id);
    emp.Name = dto.Name;
    emp.Salary = dto.Salary;
    emp.Dept = dto.Dept;
    await _context.SaveChangesAsync();
    return NoContent();
}

// PATCH — send only changed fields using JsonPatchDocument
[HttpPatch("{id}")]
public async Task<IActionResult> PartialUpdate(int id,
    [FromBody] JsonPatchDocument<Employee> patchDoc)
{
    var emp = await _context.Employees.FindAsync(id);
    patchDoc.ApplyTo(emp);  // applies only the fields in patch document
    await _context.SaveChangesAsync();
    return NoContent();
}
```

---

**Q37. What are HTTP status codes? Which are most important?**

**A:**

|Code|Meaning|When to use|
|---|---|---|
|200 OK|Success|GET, PUT success|
|201 Created|Resource created|POST success|
|204 No Content|Success, no body|DELETE, PUT success|
|400 Bad Request|Invalid input|Validation failure|
|401 Unauthorized|Not authenticated|No/invalid token|
|403 Forbidden|Not authorized|No permission|
|404 Not Found|Resource not found|Wrong ID|
|409 Conflict|Conflict in state|Duplicate record|
|422 Unprocessable|Validation error|Invalid data|
|500 Internal Error|Server error|Unhandled exception|

```csharp
return Ok(data);                    // 200
return Created(url, data);          // 201
return CreatedAtAction(...);        // 201
return NoContent();                 // 204
return BadRequest(ModelState);      // 400
return Unauthorized();              // 401
return Forbid();                    // 403
return NotFound();                  // 404
return Conflict();                  // 409
return StatusCode(500, "Error");    // 500
```

---

**Q38. What is API Versioning? How to implement it?**

**A:**

```csharp
// Install: Microsoft.AspNetCore.Mvc.Versioning
builder.Services.AddApiVersioning(options =>
{
    options.DefaultApiVersion = new ApiVersion(1, 0);
    options.AssumeDefaultVersionWhenUnspecified = true;
    options.ReportApiVersions = true;
    options.ApiVersionReader = ApiVersionReader.Combine(
        new UrlSegmentApiVersionReader(),    // /api/v1/employees
        new HeaderApiVersionReader("api-version"),  // header
        new QueryStringApiVersionReader("version")  // ?version=1.0
    );
});

// Controller
[ApiController]
[ApiVersion("1.0")]
[Route("api/v{version:apiVersion}/[controller]")]
public class EmployeeV1Controller : ControllerBase
{
    [HttpGet]
    public IActionResult Get() => Ok("Version 1");
}

[ApiController]
[ApiVersion("2.0")]
[Route("api/v{version:apiVersion}/employee")]
public class EmployeeV2Controller : ControllerBase
{
    [HttpGet]
    public IActionResult Get() => Ok("Version 2 with more features");
}
```

---

**Q39. What is Global Exception Handling in ASP.NET Core?**

**A:**

```csharp
// Method 1 — UseExceptionHandler middleware
app.UseExceptionHandler(appBuilder =>
{
    appBuilder.Run(async context =>
    {
        context.Response.StatusCode = 500;
        context.Response.ContentType = "application/json";

        var error = context.Features.Get<IExceptionHandlerFeature>();
        if (error != null)
        {
            await context.Response.WriteAsync(JsonSerializer.Serialize(new
            {
                StatusCode = 500,
                Message = "Internal Server Error",
                Detail = error.Error.Message
            }));
        }
    });
});

// Method 2 — Custom Exception Middleware ✅ Best approach
public class ExceptionMiddleware
{
    private readonly RequestDelegate _next;

    public ExceptionMiddleware(RequestDelegate next) { _next = next; }

    public async Task Invoke(HttpContext context)
    {
        try
        {
            await _next(context);
        }
        catch (NotFoundException ex)
        {
            await HandleExceptionAsync(context, ex, 404);
        }
        catch (ValidationException ex)
        {
            await HandleExceptionAsync(context, ex, 400);
        }
        catch (Exception ex)
        {
            await HandleExceptionAsync(context, ex, 500);
        }
    }

    private async Task HandleExceptionAsync(HttpContext context, Exception ex, int statusCode)
    {
        context.Response.StatusCode = statusCode;
        context.Response.ContentType = "application/json";

        await context.Response.WriteAsync(JsonSerializer.Serialize(new
        {
            StatusCode = statusCode,
            Message = ex.Message
        }));
    }
}

app.UseMiddleware<ExceptionMiddleware>();
```

---

**Q40. What is CORS? How to configure it in ASP.NET Core?**

**A:** CORS (Cross-Origin Resource Sharing) — allows/restricts requests from different domains.

```csharp
// Program.cs
builder.Services.AddCors(options =>
{
    // Allow specific origins
    options.AddPolicy("AllowReactApp", policy =>
        policy.WithOrigins("http://localhost:3000")
              .AllowAnyMethod()
              .AllowAnyHeader()
              .AllowCredentials());

    // Allow all — development only ⚠️
    options.AddPolicy("AllowAll", policy =>
        policy.AllowAnyOrigin()
              .AllowAnyMethod()
              .AllowAnyHeader());
});

app.UseCors("AllowReactApp");  // apply policy

// Per controller/action
[EnableCors("AllowReactApp")]
public class EmployeeController : ControllerBase { }
```

---

---

## SECTION 7 — CONFIGURATION & LOGGING

---

**Q41. How does Configuration work in ASP.NET Core?**

**A:**

```json
// appsettings.json
{
    "ConnectionStrings": {
        "Default": "Server=localhost;Database=MyDb;"
    },
    "Jwt": {
        "Key": "my-secret-key",
        "Issuer": "my-app"
    },
    "AppSettings": {
        "MaxPageSize": 50,
        "AllowedHosts": ["localhost", "myapp.com"]
    }
}
```

```csharp
// Access configuration
public class EmployeeService
{
    private readonly IConfiguration _config;

    public EmployeeService(IConfiguration config) { _config = config; }

    public void Configure()
    {
        var connStr = _config.GetConnectionString("Default");
        var jwtKey = _config["Jwt:Key"];
        var maxSize = _config.GetValue<int>("AppSettings:MaxPageSize");
    }
}

// Strongly typed configuration — preferred ✅
public class JwtSettings
{
    public string Key { get; set; }
    public string Issuer { get; set; }
}

builder.Services.Configure<JwtSettings>(
    builder.Configuration.GetSection("Jwt"));

// Inject
public class AuthService
{
    private readonly JwtSettings _jwtSettings;
    public AuthService(IOptions<JwtSettings> options)
    {
        _jwtSettings = options.Value;
    }
}
```

---

**Q42. What is the difference between appsettings.json and appsettings.Development.json?**

**A:** Environment-specific settings override base settings.

```
appsettings.json                — base/default settings
appsettings.Development.json    — overrides for Development
appsettings.Production.json     — overrides for Production
appsettings.Staging.json        — overrides for Staging
```

```csharp
// Set environment
// ASPNETCORE_ENVIRONMENT = Development / Production / Staging

// In code
if (app.Environment.IsDevelopment())
{
    app.UseSwagger();
    app.UseSwaggerUI();
}
```

---

**Q43. What is Logging in ASP.NET Core? What are log levels?**

**A:**

```
Trace < Debug < Information < Warning < Error < Critical
```

```csharp
// Built-in logging
public class EmployeeService
{
    private readonly ILogger<EmployeeService> _logger;

    public EmployeeService(ILogger<EmployeeService> logger)
    {
        _logger = logger;
    }

    public async Task<Employee> GetAsync(int id)
    {
        _logger.LogInformation("Getting employee {Id}", id);

        var emp = await _context.Employees.FindAsync(id);

        if (emp == null)
            _logger.LogWarning("Employee {Id} not found", id);

        return emp;
    }
}

// Serilog — popular third party logging ✅
builder.Host.UseSerilog((ctx, config) =>
{
    config
        .ReadFrom.Configuration(ctx.Configuration)
        .WriteTo.Console()
        .WriteTo.File("logs/log-.txt", rollingInterval: RollingInterval.Day)
        .WriteTo.Seq("http://localhost:5341");
});
```

---

---

## SECTION 8 — DESIGN PATTERNS & ARCHITECTURE

---

**Q44. What is the difference between Monolithic and Microservices architecture?**

**A:**

||Monolithic|Microservices|
|---|---|---|
|Structure|Single deployable unit|Multiple small services|
|Scaling|Scale whole app|Scale individual services|
|Technology|Same tech stack|Different tech per service|
|Deployment|One deployment|Independent deployments|
|Complexity|Simple to start|Complex (networking, discovery)|
|Use when|Small teams, simple apps|Large teams, complex domains|

---

**Q45. What is CQRS pattern?**

**A:** CQRS (Command Query Responsibility Segregation) — separates read (Query) and write (Command) operations.

```csharp
// Command — write operation
public class CreateEmployeeCommand
{
    public string Name { get; set; }
    public decimal Salary { get; set; }
}

public class CreateEmployeeCommandHandler
{
    private readonly AppDbContext _context;

    public async Task<int> Handle(CreateEmployeeCommand command)
    {
        var emp = new Employee { Name = command.Name, Salary = command.Salary };
        _context.Employees.Add(emp);
        await _context.SaveChangesAsync();
        return emp.Id;
    }
}

// Query — read operation
public class GetEmployeeQuery { public int Id { get; set; } }

public class GetEmployeeQueryHandler
{
    private readonly AppDbContext _context;

    public async Task<EmployeeDto> Handle(GetEmployeeQuery query)
    {
        return await _context.Employees
            .AsNoTracking()
            .Where(e => e.Id == query.Id)
            .Select(e => new EmployeeDto { Id = e.Id, Name = e.Name })
            .FirstOrDefaultAsync();
    }
}
```

---

**Q46. What is MediatR? How is it used with CQRS?**

**A:** MediatR is a library that implements mediator pattern — decouples senders from handlers.

```csharp
// Install: MediatR.Extensions.Microsoft.DependencyInjection
builder.Services.AddMediatR(typeof(Program));

// Command
public class CreateEmployeeCommand : IRequest<int>
{
    public string Name { get; set; }
    public decimal Salary { get; set; }
}

// Handler
public class CreateEmployeeHandler : IRequestHandler<CreateEmployeeCommand, int>
{
    private readonly AppDbContext _context;
    public CreateEmployeeHandler(AppDbContext context) { _context = context; }

    public async Task<int> Handle(CreateEmployeeCommand request, CancellationToken ct)
    {
        var emp = new Employee { Name = request.Name, Salary = request.Salary };
        _context.Employees.Add(emp);
        await _context.SaveChangesAsync(ct);
        return emp.Id;
    }
}

// Controller — no direct service dependency
[HttpPost]
public async Task<IActionResult> Create(
    [FromBody] CreateEmployeeCommand command,
    [FromServices] IMediator mediator)
{
    var id = await mediator.Send(command);
    return CreatedAtAction(nameof(GetById), new { id }, null);
}
```

---

**Q47. What is the Unit of Work pattern?**

**A:** Unit of Work groups multiple operations into a single transaction.

```csharp
public interface IUnitOfWork : IDisposable
{
    IEmployeeRepository Employees { get; }
    IDepartmentRepository Departments { get; }
    Task<int> SaveAsync();
}

public class UnitOfWork : IUnitOfWork
{
    private readonly AppDbContext _context;
    public IEmployeeRepository Employees { get; }
    public IDepartmentRepository Departments { get; }

    public UnitOfWork(AppDbContext context)
    {
        _context = context;
        Employees = new EmployeeRepository(context);
        Departments = new DepartmentRepository(context);
    }

    public async Task<int> SaveAsync() => await _context.SaveChangesAsync();

    public void Dispose() => _context.Dispose();
}

// Usage — both saved in same transaction ✅
using var uow = new UnitOfWork(context);
uow.Employees.Add(newEmployee);
uow.Departments.Update(dept);
await uow.SaveAsync();  // one transaction for both ✅
```

---

---

## SECTION 9 — SIGNALR & BACKGROUND SERVICES

---

**Q48. What is SignalR? When to use it?**

**A:** SignalR enables real-time bidirectional communication between server and client.

```csharp
// Hub
public class NotificationHub : Hub
{
    public async Task SendMessage(string user, string message)
    {
        await Clients.All.SendAsync("ReceiveMessage", user, message);
    }

    public async Task SendToGroup(string group, string message)
    {
        await Clients.Group(group).SendAsync("ReceiveMessage", message);
    }
}

// Program.cs
builder.Services.AddSignalR();
app.MapHub<NotificationHub>("/notificationHub");

// Push from controller/service
public class OrderService
{
    private readonly IHubContext<NotificationHub> _hub;

    public async Task PlaceOrder(Order order)
    {
        // ... save order ...
        await _hub.Clients.All.SendAsync("OrderPlaced", order);
    }
}
```

Use cases: Live notifications, chat, real-time dashboards, collaborative editing.

---

**Q49. What is a Background Service in ASP.NET Core?**

**A:** Long-running tasks that run in background — IHostedService or BackgroundService.

```csharp
// BackgroundService — simplest way
public class EmailQueueProcessor : BackgroundService
{
    private readonly ILogger<EmailQueueProcessor> _logger;

    public EmailQueueProcessor(ILogger<EmailQueueProcessor> logger)
    {
        _logger = logger;
    }

    protected override async Task ExecuteAsync(CancellationToken stoppingToken)
    {
        while (!stoppingToken.IsCancellationRequested)
        {
            _logger.LogInformation("Processing email queue...");
            // process emails from queue
            await Task.Delay(TimeSpan.FromMinutes(1), stoppingToken);
        }
    }
}

// Register
builder.Services.AddHostedService<EmailQueueProcessor>();
```

---

---

## SECTION 10 — TESTING

---

**Q50. What is Unit Testing in .NET? What is xUnit?**

**A:** Unit testing tests individual units of code in isolation.

```csharp
// xUnit — most popular .NET test framework
public class EmployeeServiceTests
{
    private readonly Mock<IEmployeeRepository> _mockRepo;
    private readonly EmployeeService _service;

    public EmployeeServiceTests()
    {
        _mockRepo = new Mock<IEmployeeRepository>();
        _service = new EmployeeService(_mockRepo.Object);
    }

    [Fact]
    public async Task GetById_ShouldReturnEmployee_WhenExists()
    {
        // Arrange
        var expected = new Employee { Id = 1, Name = "Alice" };
        _mockRepo.Setup(r => r.GetByIdAsync(1)).ReturnsAsync(expected);

        // Act
        var result = await _service.GetByIdAsync(1);

        // Assert
        Assert.NotNull(result);
        Assert.Equal("Alice", result.Name);
    }

    [Fact]
    public async Task GetById_ShouldReturnNull_WhenNotFound()
    {
        _mockRepo.Setup(r => r.GetByIdAsync(99)).ReturnsAsync((Employee)null);

        var result = await _service.GetByIdAsync(99);

        Assert.Null(result);
    }

    [Theory]
    [InlineData(1)]
    [InlineData(2)]
    [InlineData(3)]
    public async Task GetById_ShouldCallRepository_WithCorrectId(int id)
    {
        await _service.GetByIdAsync(id);
        _mockRepo.Verify(r => r.GetByIdAsync(id), Times.Once);
    }
}
```

---

**Q51. What is the difference between unit test, integration test and end-to-end test?**

**A:**

||Unit Test|Integration Test|E2E Test|
|---|---|---|---|
|Scope|Single class/method|Multiple components|Full application|
|Database|Mocked|Real/test DB|Real|
|Speed|Very fast|Moderate|Slow|
|Tools|xUnit + Moq|WebApplicationFactory|Selenium, Playwright|

```csharp
// Integration test with WebApplicationFactory
public class EmployeeControllerTests : IClassFixture<WebApplicationFactory<Program>>
{
    private readonly HttpClient _client;

    public EmployeeControllerTests(WebApplicationFactory<Program> factory)
    {
        _client = factory.CreateClient();
    }

    [Fact]
    public async Task GetAll_ReturnsOk()
    {
        var response = await _client.GetAsync("/api/employee");
        response.EnsureSuccessStatusCode();

        var content = await response.Content.ReadAsStringAsync();
        Assert.NotEmpty(content);
    }
}
```

---

---

## SECTION 11 — PERFORMANCE & SECURITY

---

**Q52. What is rate limiting in ASP.NET Core?**

**A:** Rate limiting restricts number of requests from a client in a time window.

```csharp
// .NET 7+ built-in rate limiting
builder.Services.AddRateLimiter(options =>
{
    options.AddFixedWindowLimiter("fixed", opt =>
    {
        opt.Window = TimeSpan.FromMinutes(1);
        opt.PermitLimit = 100;  // 100 requests per minute
        opt.QueueLimit = 10;
    });

    options.AddSlidingWindowLimiter("sliding", opt =>
    {
        opt.Window = TimeSpan.FromMinutes(1);
        opt.SegmentsPerWindow = 4;
        opt.PermitLimit = 100;
    });
});

app.UseRateLimiter();

// Apply to controller
[EnableRateLimiting("fixed")]
public class EmployeeController : ControllerBase { }
```

---

**Q53. What are common security practices in ASP.NET Core?**

**A:**

```csharp
// 1. HTTPS only
app.UseHttpsRedirection();
app.UseHsts();

// 2. Secure headers
app.Use(async (context, next) =>
{
    context.Response.Headers.Add("X-Content-Type-Options", "nosniff");
    context.Response.Headers.Add("X-Frame-Options", "DENY");
    context.Response.Headers.Add("X-XSS-Protection", "1; mode=block");
    await next();
});

// 3. Input validation — always validate
if (!ModelState.IsValid) return BadRequest(ModelState);

// 4. SQL injection prevention — use parameterized queries / EF Core
// ❌ Never do this
var sql = $"SELECT * FROM Employees WHERE Name = '{name}'";

// ✅ EF Core prevents SQL injection
var emp = context.Employees.Where(e => e.Name == name).ToList();

// 5. Secret management — never in appsettings.json for production
// Use: Azure Key Vault, AWS Secrets Manager, Environment Variables

// 6. Anti-forgery token for forms
builder.Services.AddAntiforgery();

// 7. Sensitive data — hash passwords, never store plain text
var hashedPassword = BCrypt.Net.BCrypt.HashPassword(password);
var isValid = BCrypt.Net.BCrypt.Verify(inputPassword, hashedPassword);
```

---

---

## SECTION 12 — .NET 8 LATEST FEATURES

---

**Q54. What are the new features in .NET 8?**

**A:**

```csharp
// 1. Primary Constructors (C# 12)
public class EmployeeService(IRepository<Employee> repository, ILogger<EmployeeService> logger)
{
    public async Task<Employee> GetAsync(int id)
    {
        logger.LogInformation("Getting employee {Id}", id);
        return await repository.GetByIdAsync(id);
    }
}

// 2. Collection Expressions (C# 12)
int[] numbers = [1, 2, 3, 4, 5];
List<string> names = ["Alice", "Bob", "Charlie"];

// 3. Minimal APIs improvements
app.MapGet("/employees", async (IEmployeeService service) =>
    await service.GetAllAsync());

app.MapPost("/employees", async (CreateEmployeeDto dto, IEmployeeService service) =>
{
    var emp = await service.CreateAsync(dto);
    return Results.CreatedAtRoute("GetEmployee", new { id = emp.Id }, emp);
});

// 4. Native AOT — faster startup, smaller size
// 5. Frozen Collections — immutable, faster lookups
var frozenDict = new Dictionary<string, int> { ["a"] = 1 }.ToFrozenDictionary();

// 6. Keyed Services in DI (new in .NET 8)
builder.Services.AddKeyedScoped<IPaymentService, CreditCardService>("creditcard");
builder.Services.AddKeyedScoped<IPaymentService, UPIService>("upi");

public class OrderService([FromKeyedServices("creditcard")] IPaymentService payment) { }
```

---

**Q55. What are Minimal APIs in ASP.NET Core?**

**A:** Minimal APIs provide a lightweight way to build APIs without controllers.

```csharp
var builder = WebApplication.CreateBuilder(args);
builder.Services.AddScoped<IEmployeeService, EmployeeService>();

var app = builder.Build();

// Define endpoints directly
app.MapGet("/api/employees", async (IEmployeeService service) =>
{
    var employees = await service.GetAllAsync();
    return Results.Ok(employees);
});

app.MapGet("/api/employees/{id}", async (int id, IEmployeeService service) =>
{
    var emp = await service.GetByIdAsync(id);
    return emp is null ? Results.NotFound() : Results.Ok(emp);
});

app.MapPost("/api/employees", async (CreateEmployeeDto dto, IEmployeeService service) =>
{
    var emp = await service.CreateAsync(dto);
    return Results.Created($"/api/employees/{emp.Id}", emp);
});

app.MapDelete("/api/employees/{id}", async (int id, IEmployeeService service) =>
{
    await service.DeleteAsync(id);
    return Results.NoContent();
});

app.Run();
```

---

**Q56. What is the difference between IOptions, IOptionsSnapshot and IOptionsMonitor?**

**A:**

||IOptions<T>|IOptionsSnapshot<T>|IOptionsMonitor<T>|
|---|---|---|---|
|Lifetime|Singleton|Scoped|Singleton|
|Reloads config|❌ No|✅ Per request|✅ Real-time|
|Use in|Singleton services|Scoped/Transient|Background services|

```csharp
// IOptions — registered once, never reloads
public class EmailService
{
    public EmailService(IOptions<EmailSettings> options)
    {
        var settings = options.Value;
    }
}

// IOptionsSnapshot — reloads per request ✅
public class UserService
{
    public UserService(IOptionsSnapshot<AppSettings> options)
    {
        var settings = options.Value;  // fresh per request
    }
}

// IOptionsMonitor — real-time reload ✅ for background services
public class BackgroundWorker : BackgroundService
{
    private readonly IOptionsMonitor<WorkerSettings> _options;

    public BackgroundWorker(IOptionsMonitor<WorkerSettings> options)
    {
        _options = options;
        _options.OnChange(settings => Console.WriteLine("Settings changed!"));
    }

    protected override async Task ExecuteAsync(CancellationToken ct)
    {
        var currentSettings = _options.CurrentValue;  // always latest
    }
}
```

---

---

## ⚡ Quick Reference Cheatsheet

### .NET Execution Flow

```
C# Code → Compile → IL Code → CLR → JIT → Native Code → Execute
```

### Middleware Order (Important!)

```
Exception Handler → HSTS → HTTPS Redirect → Static Files →
Routing → CORS → Authentication → Authorization → Controllers
```

### EF Core Loading Strategies

|Strategy|Query Count|Use when|
|---|---|---|
|Eager (.Include)|1 with JOIN|Always need related data|
|Lazy|N+1 ⚠️|Rarely need related data|
|Explicit|Manual|Conditionally need related data|

### HTTP Methods

|Method|Action|Body|Idempotent|
|---|---|---|---|
|GET|Read|❌|✅|
|POST|Create|✅|❌|
|PUT|Replace all|✅|✅|
|PATCH|Update partial|✅|❌|
|DELETE|Delete|❌|✅|

### DI Lifetime

|Scope|Created|Use|
|---|---|---|
|Singleton|Once|Logger, Config, Cache|
|Scoped|Per request|DbContext, UserService|
|Transient|Each inject|EmailService, Validators|

### Response Status Codes

|Code|Meaning|
|---|---|
|200|OK|
|201|Created|
|204|No Content|
|400|Bad Request|
|401|Unauthorized|
|403|Forbidden|
|404|Not Found|
|500|Server Error|

### Top 10 Interview Tips for 7 Years Exp

1. Know middleware pipeline order — very commonly asked
2. Understand EF Core loading — eager vs lazy vs N+1 problem
3. Know JWT authentication flow end to end
4. Explain CQRS and Repository pattern with real project usage
5. Know async/await deeply — Task.WhenAll vs WhenAny
6. Understand DI lifetime scopes — captive dependency trap
7. Know the difference between .NET versions clearly
8. Mention performance practices — AsNoTracking, caching, indexes
9. Know global exception handling — middleware approach
10. Always relate answers to real project experience