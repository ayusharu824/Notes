Ans.
Authorization means what is this user allowed to do.
Authentication -> who are you?
Authorization -> are you allowed to access this resource ?

Authorization flow
Request -> Authentication Middleware -> Authorization Middleware -> Controller/Action
Authorization fails -> 403

Claims: A claim is a key-value pair about the user.
Examples:
Role = Admin

Policies(Most important)
A policy is a set of rules that must be satisfied.
A policy can check
	-Roles
	-Claims
	-Custom logic
Types of Authorization in .Net Core
A. Role based Authorization

[Authorize(Roles = "Admin")]
only user with role as Admin can access this
B.Claim based authorization
builder.Services.AddAuthorization(options =>
{
    options.AddPolicy("HRPolicy", policy =>
        policy.RequireClaim("Department", "HR"));
});
C. Policy-based authorization
options.AddPolicy("AdminWithEditPermission", policy =>
{
    policy.RequireRole("Admin");
    policy.RequireClaim("Permission", "Edit");
});

## What is API versioning, Explain in detail ?

API versioning means maintaining multiple versions of the same API so that
-  Existing clients keep working
-  New clients can use improved or breaking changes.

[ApiVersion("1.0")]
[Route("api/v{version:apiVersion}/users")]
public class UsersController : ControllerBase
{
}
Nuget package required: dotnet add package Microsoft.AspNetCore.Mvc.Versioning
We can have [ApiVersion] tag at controller level only not at action method level

[ApiVersion("1.0")]
[ApiVersion("2.0")]
[Route("api/v{version:apiVersion}/products")]
public class ProductsController : ControllerBase
{
    [HttpGet]
    [MapToApiVersion("1.0")]
    public IActionResult GetV1()
        => Ok("Products v1");

    [HttpGet]
    [MapToApiVersion("2.0")]
    public IActionResult GetV2()
        => Ok("Products v2");
}


## JWT

A jwt has 3 parts.
1. Header
2. Payload
3. Signature

Header
{
  "alg": "HS256",
  "typ": "JWT"
}

Payload (claims)
{
  "sub": "123",
  "email": "ayush@gmail.com",
  "role": "Admin",
  "exp": 1712345678
}

## Explain dependency injection lifetimes in detail.

Singleton, Transient, Scoped

# What is cache management in .NET Core? How does it work, where do you define it in your code, and provide the code for it.

**Cache management** is the process of **temporarily storing frequently accessed data** so that:

- You **avoid repeated expensive operations** (DB calls, API calls)    
- Improve **performance**
- Reduce **latency**
- Reduce **load on database / services**

# Explain the structure of your controller method, including authorization. Write down the code for it.

[ApiController]
[Route("api/employees")]
[Authorize] // Authentication required for all endpoints
public class EmployeesController : ControllerBase
{
    private readonly IEmployeeService _employeeService;

    public EmployeesController(IEmployeeService employeeService)
    {
        _employeeService = employeeService;
    }
    [HttpGet("{id}")]
    [Authorize(Policy = "AdminOnly")] // Authorization
    public async Task<IActionResult> GetEmployeeById(int id){}

# Ways to optimize Sql Server Stored procedure.

1. Avoid Select * use column names which are required.
2. Use proper indexing, index columns used in where, join, order by.
3. Use Pagination for Large Result Sets
4. SELECT *
	FROM Employees e
	LEFT JOIN Departments d ON e.DeptId = d.Id
	WHERE d.Id = 1;
	converts left join to inner join implicitly

# Nth highest number in sql server

Select top 1 salary from(
select distinct top 2 salary from employees order by salary desc
) as t
order by salary asc;

# Different types of collections in C#

1. Non generic [System.Collections] -> non generic means not of specific data type
ArrayList HashTable Queue Stack

ArrayList list = new ArrayList();
list.Add(10);
list.Add("Hello") //mixed types are allowed

2. Generic Collections (System.Collections.Generic)
     1. type safe
     2. high performance
 
 2.1 List<T> Dynamic array
	 List<int> numbers = new List<int> {1,2,3};
2.2 Dictionary<Tkey, TValue>
2.3 Queue<T>

Interfaces for Collections:
1. Ienumerable<T>
2. ICollection<T>
3. IList<T>
4. IDictionary<Tkey,TValue>


5. What are HttpGet & HttpPost
Get -> retrieve data
Post -> Send Data/ create resource



## If we use the int variables without initialization, how will it behave?
1. Local variables inside a method
void Test()
{
    int x;
    Console.WriteLine(x); // ❌ ERROR
}
in this case we will get compiler error
Use of unassigned local variable 'x'
2. Instance variables (fields of a class)
Automatically initialized to default value
class Employee
{
	int age; //not initialized
}

in that case it will take default value
age=0


What is ?? operator in c#

result = value1 ?? value2;
if value1 is null return value2
if value1 is not null return value1

string name = null;
string displayName = name ?? "Guest";

Console.WriteLine(displayName); // Guest
Old way
string result;

if (name != null)
    result = name;
else
    result = "Guest";


**Anonymous type in c#**

An anonymous type in c# is a way to create an object without explicitly defining a class.
example:
var obj = new {name="ayush", age="25"}
why do we need anonymous type
class Temp{
public string Name{get;set;}
public int Age{get;set;}
}
but if the data is needed once only
then in that case we have to create C# class, anonymous types avoid creating unnecessary classes

properties inside anonymous type are read only
which means
obj.name = "ayush 1" -> not allowed

it is necessary to use var only while creating anonymous type
because the actual type name is unknown

in linq
var users = _context.Users.Select(u=> new {
u.Id,u.Name,Role = u.Role.Name
}).ToList()

Q. Difference between int, int32 , int64 and long
Ans. int is an alias for int32
long is an alias for int64

Q. How to pass the parameter to Abstract Base Class from Child class?
Ans. public abstract class Employee
{
    protected int Id;

    protected Employee(int id)
    {
        Id = id;
    }

    public abstract string GetRole();
}

public class Manager : Employee
{
    public Manager(int id) : base(id)
    {
    }

    public override string GetRole()
    {
        return "Manager";
    }
}

Q. why do we need interface when we have abstract class?
Ans. A class can inherit **only ONE abstract class**
A class can implement **MULTIPLE interfaces**
Interfaces Enable Loose Coupling (VERY IMPORTANT)
### Without interface ❌ (Tightly coupled)

`class OrderService {     private EmailService _email = new EmailService(); }`
With interface
interface IEmailService
{
    void Send();
}

class OrderService
{
    private readonly IEmailService _email;

    public OrderService(IEmailService email)
    {
        _email = email;
    }
}

## Interfaces Have NO State (Contract Only)

### Abstract Class ❌
Can have:
- Fields
- State
- Constructors
- Implementation
    
### Interface ✅
- ❌ No fields
- ❌ No state
- ❌ No constructors
- ✅ Only behavior definition

Interfaces Are Better for Testing (Mocking)
var mock = new Mock<IEmailService>();
mock.Setup(x => x.Send());

Q. What happens if we dont use break in switch case?
Ans. In C#, if you don’t use `break` (or another jump statement), the code will NOT compile. C# does not allow implicit fall-through between switch cases.
Control cannot fall through from one case label to another

Q. Can we assign Null to Datetime variable
Ans. No, you cannot assign `null` to a `DateTime` variable directly.
DateTime dt = null; // ❌ Compile-time error

Q. What is DI? (Dependency Injection) What is it used for?
Ans. DI is a design pattern where class receives it's dependencies from the outside instead of creating them itself.
without DI (tight coupling)
class OrderService
{
    private readonly EmailService _email = new EmailService();
}

with DI
class OrderService
{
    private readonly IEmailService _email;

    public OrderService(IEmailService email)
    {
        _email = email;
    }
}
Q. How to read values from appsettings.json?
Ans. Read values using IConfiguration
public class HomeController:Controller{
private readonly IConfiguration _configuration;
public HomeController(IConfiguration configuration){
	_configuration = configuration;
}
string appName = _configuration["ApplicationName"];
}

Q. How to insert multiple records without using foreach in EF?
Ans. var employees = new List<Employee>{
new Employee {Name = "ayush", id =1},
new Employee {Name = "ayush", id =2},
new Employee {Name = "ayush", id =3},
}
_dbcontext.Employees.AddRangeAsync(employees);
_dbcontext.SavechangesAsync()

Q. Which is better either SQL queries or EF for more records?
Ans. For large volumes of records, raw SQL queries are generally faster and more efficient than EF, while EF is better for maintainability, safety, and moderate data sizes.

Q. What is the lifetime for an instance in .net core?
Ans. Some should be new every time, some should live per http request, some should live for entire application.
Transient, singleton,  scoped

Q. Why **`DbContext` must be Scoped** (NOT Singleton)
Ans. DbContext must be scoped because it is not thread safe, it tracks entity state per request and it represents a unit of work tied to a single request/transactions.

## 2️⃣ Why `DbContext` CANNOT be Singleton ❌

### ❌ Reason 1: Not thread-safe
- Singleton = shared across all requests
- Multiple users → multiple threads
- `DbContext` is **not thread-safe**
    

➡️ Leads to:

- Race conditions
    
- Random data corruption
    
- Crashes
- **Q:** Why not Transient?  
**A:** You _can_, but it causes:
- Multiple DbContext instances in one request
- Broken transaction consistency
So **Scoped is best**, not Transient.


Q. Singleton vs static.
Ans. 
Singleton.
One instance per application, managed by DI container
services.AddSingleton<ICacheService, CacheService>();
Static.
Definition: one  global instance per appdomain, managed by clr.
public static class CacheHelper{
public static string Get(string key){}
}


SOLID

O -> open for extension and close for modification
Bad Example
class DiscountCalculator 
{
	public double Calculate(string type, double amount)
	{
		if (type == "Festival") return amount * 0.2;
        if (type == "Employee") return amount * 0.3;
        return 0;
	}
}

good example
interface IDiscount
{
	double Calculate(double amount);
}
class FestivalDiscount : IDiscount
{
	public double Calculate(double amount) => amount *0.2;
}
class EmployeeDiscount:IDiscount 
{
	public double Calculate(double amount) => amount *0.3 
}

Liskov Substitution Principle (LSP)
Objects of a derived class must be substitutable for objects of the base class without breaking the application.

class Bird
{
    public virtual void Fly() { }
}

class Ostrich : Bird
{
    public override void Fly()
    {
        throw new NotSupportedException();
    }
}
Problem:
1. ostrich is a bird but can't fly
2. substitution breaks behavior

interface IBird{}
interface IFlyingBird{
void Fly();
}
class sparrow:IBird, IFlyingBird{
public void Fly()
}
class Ostrich : IBird {}


Q. Finally vs Finalize
Ans. 
A. Finally is a block that always executes whether an exception occurs or not.
used with: 
try{} catch{} finally{}
why do we need finally?
1. cleanup code
2. close db connections
3. release resources
4. unlock files
B. Finalize()/ Destructor (Garbage Collection)
what is finalize()?
Finalize is a method called by the GC before reclaiming an object's memory.?