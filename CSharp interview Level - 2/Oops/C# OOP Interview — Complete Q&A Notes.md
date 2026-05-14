# C# OOP Interview — Complete Q&A Notes
z
### 68 Questions with Answers for Quick Revision

---

## SECTION 1 — Core OOP

---

**Q1. What are the 4 pillars of OOP? Explain with real world examples.**

**A:**

- **Encapsulation** — Hide internal data, expose through properties. Example: Bank balance is private, exposed via Deposit/Withdraw methods.
- **Abstraction** — Hide implementation, show only what is needed. Example: Shape.CalculateArea() — you know what it does, not how.
- **Inheritance** — Child class reuses parent code. Example: Dog : Animal — Dog gets Eat(), Sleep() from Animal.
- **Polymorphism** — Same method, different behaviour. Example: Animal.Sound() → Dog says Woof, Cat says Meow.

---

**Q2. What is the difference between Abstract Class and Interface? When to use which?**

**A:**

||Abstract Class|Interface|
|---|---|---|
|Fields|✅ Yes|❌ No|
|Constructor|✅ Yes|❌ No|
|Method body|✅ Yes|✅ C# 8+|
|Multiple inherit|❌ No|✅ Yes|

- Use **Abstract Class** when classes share common code (IS-A relationship). Example: Payment class with common Log() method.
- Use **Interface** when defining a contract across unrelated classes (CAN-DO). Example: IPayment, IDisposable, ILogger.

```csharp
public abstract class Payment
{
    public void Log() => Console.WriteLine("Logging...");  // shared
    public abstract void Process();  // each payment implements differently
}

public interface IRefundable
{
    void Refund(decimal amount);
}

public class CreditCard : Payment, IRefundable
{
    public override void Process() => Console.WriteLine("Credit card processed");
    public void Refund(decimal amount) => Console.WriteLine($"Refunded {amount}");
}
```

---

**Q3. What is the difference between virtual, override, new and sealed?**

**A:**

- **virtual** — Parent method that CAN be overridden by child.
- **override** — Child replaces parent method. Polymorphic — works through parent reference.
- **new** — Child hides parent method. NOT polymorphic — only works through child reference.
- **sealed** — Prevents further inheritance (class) or overriding (method).

```csharp
public class Animal
{
    public virtual void Sound() => Console.WriteLine("Animal");
    public void Eat() => Console.WriteLine("Animal eating");
}
public class Dog : Animal
{
    public override void Sound() => Console.WriteLine("Woof");  // override
    public new void Eat() => Console.WriteLine("Dog eating");   // hiding
}

Animal a = new Dog();
a.Sound();  // Woof        ← override is polymorphic ✅
a.Eat();    // Animal eating ← new is NOT polymorphic
```

---

**Q4. What is method overloading vs method overriding?**

**A:**

- **Overloading** — Same method name, different parameters. Compile-time polymorphism.
- **Overriding** — Child replaces parent virtual method. Runtime polymorphism.

```csharp
// Overloading — same class, different params
public int Add(int a, int b) => a + b;
public int Add(int a, int b, int c) => a + b + c;

// Overriding — parent/child, same signature
public class Animal { public virtual void Sound() { } }
public class Dog : Animal { public override void Sound() => Console.WriteLine("Woof"); }
```

---

**Q5. What is the difference between == and .Equals()?**

**A:**

- **==** — Compares reference by default for objects (memory address).
- **.Equals()** — Can be overridden to compare values.
- **String** is special — both == and .Equals() compare values.

```csharp
object o1 = new object();
object o2 = new object();
Console.WriteLine(o1 == o2);      // False — different references
Console.WriteLine(o1.Equals(o2)); // False — same result by default

string s1 = "Hello";
string s2 = "Hello";
Console.WriteLine(s1 == s2);      // True — string overrides ==
Console.WriteLine(s1.Equals(s2)); // True
```

---

**Q6. What is shallow copy vs deep copy?**

**A:**

- **Shallow Copy** — Copies primitive fields but shares reference to nested objects.
- **Deep Copy** — Creates completely independent copy including all nested objects.

```csharp
// Shallow — MemberwiseClone
public Employee ShallowCopy() => (Employee)this.MemberwiseClone();

// Deep — manual copy
public Employee DeepCopy() => new Employee
{
    Name = this.Name,
    Address = new Address { City = this.Address.City }  // new object!
};

// Problem with shallow copy
Employee e1 = new Employee { Name = "Alice", Address = new Address { City = "Delhi" } };
Employee e2 = e1.ShallowCopy();
e2.Address.City = "Mumbai";
Console.WriteLine(e1.Address.City);  // Mumbai — both affected! 😱
```

---

---

## SECTION 2 — Inheritance & Polymorphism

---

**Q7. What is the difference between single and multiple inheritance in C#?**

**A:**

- **Single Inheritance** — A class can inherit from only ONE class in C#.
- **Multiple Inheritance** — Not allowed with classes in C#. Achieved through multiple interfaces.

```csharp
// Single — allowed ✅
public class Dog : Animal { }

// Multiple classes — NOT allowed ❌
// public class Hybrid : Car, Truck { }

// Multiple interfaces — allowed ✅
public class SmartDevice : IPhone, ICamera, INavigator { }
```

**Why C# doesn't allow multiple class inheritance?** — Diamond problem. If two parent classes have same method, compiler doesn't know which to call.

---

**Q8. What is constructor chaining? Explain this() and base().**

**A:**

- **this()** — Calls another constructor in the SAME class.
- **base()** — Calls constructor of the PARENT class.

```csharp
public class Employee
{
    public string Name { get; set; }
    public int Age { get; set; }
    public string Dept { get; set; }

    public Employee(string name) : this(name, 0) { }  // chains to below

    public Employee(string name, int age) : this(name, age, "General") { }

    public Employee(string name, int age, string dept)
    {
        Name = name; Age = age; Dept = dept;
    }
}

public class Manager : Employee
{
    public int TeamSize { get; set; }

    public Manager(string name, int age, int teamSize)
        : base(name, age)  // calls Employee constructor
    {
        TeamSize = teamSize;
    }
}
```

---

**Q9. Can a child class call parent class constructor? How?**

**A:** Yes, using the **base** keyword in the constructor definition.

```csharp
public class Animal
{
    public string Name { get; set; }
    public Animal(string name) { Name = name; }
}

public class Dog : Animal
{
    public string Breed { get; set; }

    public Dog(string name, string breed)
        : base(name)  // calls Animal(string name)
    {
        Breed = breed;
    }
}
```

---

**Q10. What happens if you don't define a constructor in a class?**

**A:** C# automatically provides a **default parameterless constructor**. But if you define ANY constructor with parameters, the default constructor is NO longer provided automatically.

```csharp
public class Person { }  // compiler adds: public Person() { }

public class Employee
{
    public Employee(string name) { }  // custom constructor defined
    // default constructor removed by compiler!
}

// new Employee();         ❌ ERROR — no parameterless constructor
// new Employee("Alice");  ✅ OK
```

---

**Q11. Can we override a static method in C#?**

**A:** No. Static methods belong to the class, not the instance. They cannot be virtual or overridden. You can only **hide** them using the `new` keyword.

```csharp
public class Parent
{
    public static void Show() => Console.WriteLine("Parent");
}
public class Child : Parent
{
    public new static void Show() => Console.WriteLine("Child");  // hiding, not overriding
}

Parent.Show();  // Parent
Child.Show();   // Child
```

---

**Q12. What is the difference between early binding and late binding?**

**A:**

- **Early Binding (Compile time)** — Method resolved at compile time. Method overloading, non-virtual methods.
- **Late Binding (Runtime)** — Method resolved at runtime. Method overriding with virtual/override.

```csharp
// Early binding — resolved at compile time
Calculator calc = new Calculator();
calc.Add(1, 2);  // compiler knows exactly which Add() to call

// Late binding — resolved at runtime
Animal a = new Dog();
a.Sound();  // compiler doesn't know which Sound() until runtime
```

---

---

## SECTION 3 — Classes & Objects

---

**Q13. What is the difference between a class and a struct?**

**A:**

||Class|Struct|
|---|---|---|
|Type|Reference type|Value type|
|Stored|Heap|Stack|
|Inheritance|✅ Yes|❌ No|
|Null|✅ Can be null|❌ Cannot|
|Constructor|✅ Default provided|❌ Must define|
|Use when|Complex objects|Small, simple data|

```csharp
public struct Point { public int X, Y; }  // value type
public class Person { public string Name; } // reference type

Point p1 = new Point { X = 1, Y = 2 };
Point p2 = p1;   // full copy
p2.X = 99;
Console.WriteLine(p1.X);  // 1 — unchanged ✅
```

---

**Q14. What is a static class? When would you use it?**

**A:** A static class can only have static members and cannot be instantiated. Used for utility/helper methods.

```csharp
public static class MathHelper
{
    public static int Square(int n) => n * n;
    public static double CircleArea(double r) => Math.PI * r * r;
}

// Usage — called on class, not object
MathHelper.Square(5);  // 25
// new MathHelper();   ❌ ERROR
```

Use cases: utility classes (StringHelper, DateHelper), extension methods, constants.

---

**Q15. What is a partial class? Where is it used?**

**A:** A class split across multiple files but compiled as one class. Useful for large classes or auto-generated code.

```csharp
// File 1: Employee.cs
public partial class Employee
{
    public string Name { get; set; }
    public void Work() => Console.WriteLine("Working");
}

// File 2: Employee.Validation.cs
public partial class Employee
{
    public bool IsValid() => !string.IsNullOrEmpty(Name);
}

// Used as one class
Employee emp = new Employee { Name = "Alice" };
emp.Work();
emp.IsValid();
```

Common use: Entity Framework auto-generated DbContext, Windows Forms designer code.

---

**Q16. What is a sealed class? Give a real world use case.**

**A:** A sealed class cannot be inherited.

```csharp
public sealed class PaymentProcessor
{
    // critical business logic — no one should override
}
// public class CustomProcessor : PaymentProcessor { }  ❌ ERROR
```

Real world use: String class in .NET is sealed. Singleton class should be sealed.

---

**Q17. What is object initializer syntax in C#?**

**A:** Setting properties at the time of object creation without a constructor.

```csharp
// Without initializer
Employee emp = new Employee();
emp.Name = "Alice";
emp.Age = 30;

// With object initializer ✅
Employee emp = new Employee { Name = "Alice", Age = 30 };

// With constructor + initializer
Employee emp = new Employee("Alice") { Age = 30, Dept = "IT" };
```

---

**Q18. What is the difference between new keyword for objects vs new for method hiding?**

**A:**

- `new` for **objects** — allocates memory and creates instance.
- `new` for **methods** — hides parent class method in child class (not polymorphic).

```csharp
Employee emp = new Employee();  // new — creates object

public class Child : Parent
{
    public new void Display() { }  // new — hides parent method
}
```

---

---

## SECTION 4 — Encapsulation & Access Modifiers

---

**Q19. What are access modifiers in C#? Explain each one.**

**A:**

|Modifier|Accessible From|
|---|---|
|private|Same class only|
|protected|Same class + child classes|
|internal|Same project/assembly|
|public|Everywhere|
|protected internal|Same assembly OR child classes|
|private protected|Same class AND child in same assembly|

---

**Q20. What is the difference between private and protected?**

**A:**

- **private** — Only accessible inside the same class. Not even child class can access.
- **protected** — Accessible in same class AND all child classes.

```csharp
public class Animal
{
    private string secret = "hidden";       // only Animal can access
    protected string name = "Animal";       // Animal + all children can access
}

public class Dog : Animal
{
    public void Show()
    {
        // Console.WriteLine(secret);  ❌ ERROR — private
        Console.WriteLine(name);       // ✅ OK — protected
    }
}
```

---

**Q21. What is internal and protected internal?**

**A:**

- **internal** — Accessible only within the same project (assembly).
- **protected internal** — Accessible within same assembly OR from derived classes in other assemblies.

```csharp
internal class DatabaseConfig { }  // only this project can use it

public class Base
{
    protected internal void Configure() { }  // same assembly OR child classes
}
```

---

**Q22. What is the difference between a field and a property?**

**A:**

- **Field** — Direct variable. No control over access.
- **Property** — Exposes field with get/set. Can add validation logic.

```csharp
public class Employee
{
    private int age;  // field — private

    public int Age    // property — controlled access
    {
        get { return age; }
        set
        {
            if (value > 0 && value < 100)
                age = value;
            else
                throw new ArgumentException("Invalid age");
        }
    }

    public string Name { get; set; }  // auto property
}
```

---

**Q23. What is an auto property? What is a read-only property?**

**A:**

```csharp
// Auto property — compiler generates backing field
public string Name { get; set; }

// Read-only auto property — can only be set in constructor
public string Id { get; }

// Read-only property
public string FullName
{
    get { return $"{FirstName} {LastName}"; }
}

// Init only property (C# 9+) — set only during initialization
public string Code { get; init; }

Employee e = new Employee { Code = "E001" };  // ✅
// e.Code = "E002";  ❌ cannot change after init
```

---

---

## SECTION 5 — Interfaces & Abstraction

---

**Q24. Can an interface have a constructor?**

**A:** No. Interfaces cannot have constructors because they cannot be instantiated directly.

---

**Q25. Can we have method body in an interface? (C# 8+)**

**A:** Yes, from C# 8 onwards interfaces can have default method implementations.

```csharp
public interface ILogger
{
    void Log(string message);

    // Default implementation — C# 8+
    void LogError(string message)
    {
        Log($"ERROR: {message}");  // uses abstract Log()
    }
}

public class ConsoleLogger : ILogger
{
    public void Log(string message) => Console.WriteLine(message);
    // LogError is inherited from interface default ✅
}
```

---

**Q26. What is explicit interface implementation? Why is it used?**

**A:** When two interfaces have same method name, explicit implementation separates them.

```csharp
public interface IShape { void Draw(); }
public interface IPrintable { void Draw(); }

public class Document : IShape, IPrintable
{
    void IShape.Draw() => Console.WriteLine("Drawing shape");
    void IPrintable.Draw() => Console.WriteLine("Printing document");
}

// Usage
Document doc = new Document();
((IShape)doc).Draw();      // Drawing shape
((IPrintable)doc).Draw();  // Printing document
```

---

**Q27. Can a class implement two interfaces with the same method name?**

**A:** Yes, using explicit interface implementation (see Q26).

---

**Q28. What is the difference between IEnumerable and IQueryable?**

**A:**

||IEnumerable|IQueryable|
|---|---|---|
|Execution|In memory (client side)|Database side|
|Use with|Lists, arrays|Entity Framework, DB|
|LINQ|LINQ to Objects|LINQ to SQL|
|Performance|Loads all data first|Filters at DB level|

```csharp
// IEnumerable — loads ALL employees then filters in memory ❌
IEnumerable<Employee> employees = dbContext.Employees;
var result = employees.Where(e => e.Salary > 50000);  // filter in memory

// IQueryable — sends WHERE to database ✅
IQueryable<Employee> employees = dbContext.Employees;
var result = employees.Where(e => e.Salary > 50000);  // filter in SQL query
```

---

**Q29. What is IDisposable? When and why to use it?**

**A:** IDisposable is used to release unmanaged resources (DB connections, file handles, streams) explicitly without waiting for GC.

```csharp
public class DatabaseConnection : IDisposable
{
    private bool disposed = false;

    public void Open() => Console.WriteLine("Connection opened");

    public void Dispose()
    {
        if (!disposed)
        {
            Console.WriteLine("Connection closed");
            disposed = true;
        }
    }
}

// using statement — auto calls Dispose() ✅
using (var conn = new DatabaseConnection())
{
    conn.Open();
}  // Dispose() called automatically

// C# 8+ using declaration
using var conn = new DatabaseConnection();
conn.Open();
// Dispose() called at end of scope
```

---

---

## SECTION 6 — Dependency Injection

---

**Q30. What is Dependency Injection? Why is it used?**

**A:** DI is getting instance of a service from outside instead of creating it inside the class. Promotes loose coupling, testability, and maintainability.

```csharp
// ❌ Without DI — tightly coupled
public class OrderService
{
    private EmailService _email = new EmailService();  // hardcoded!
}

// ✅ With DI — loosely coupled
public class OrderService
{
    private readonly IEmailService _email;
    public OrderService(IEmailService email) { _email = email; }
}
```

---

**Q31. What are the three lifetime scopes — Singleton, Scoped, Transient?**

**A:**

|Scope|Instance Created|Use Case|
|---|---|---|
|Singleton|Once for app lifetime|Logger, Config, Cache|
|Scoped|Once per HTTP request|DbContext, UserService|
|Transient|Every time requested|EmailService, Validators|

```csharp
// Program.cs
builder.Services.AddSingleton<ILogger, Logger>();
builder.Services.AddScoped<IUserService, UserService>();
builder.Services.AddTransient<IEmailService, EmailService>();
```

---

**Q32. What is constructor injection vs property injection?**

**A:**

```csharp
// Constructor injection — most common, recommended ✅
public class OrderService
{
    private readonly IEmailService _email;
    public OrderService(IEmailService email) { _email = email; }
}

// Property injection — optional dependency
public class OrderService
{
    public IEmailService EmailService { get; set; }  // can be null
}
```

Constructor injection is preferred — dependencies are clear, required, and immutable.

---

**Q33. What is captive dependency? Why is it a problem?**

**A:** Captive dependency is when a longer-lived service holds a shorter-lived service, causing unexpected behaviour.

```csharp
// ❌ Problem — Singleton holds Scoped service
builder.Services.AddSingleton<IOrderService, OrderService>();
builder.Services.AddScoped<IUserService, UserService>();

// OrderService (Singleton) injects UserService (Scoped)
// UserService will behave like Singleton — same instance for all requests!
// This causes stale data and bugs 😱
```

**Fix:** Never inject Scoped into Singleton. Use IServiceScopeFactory instead.

---

**Q34. What is IoC (Inversion of Control) container?**

**A:** IoC container is a framework that manages object creation and lifetime. It automatically resolves and injects dependencies.

In .NET — `IServiceCollection` is the IoC container. You register services, and the framework injects them automatically.

```csharp
// You tell the container what to create
builder.Services.AddScoped<IUserService, UserService>();

// Container creates and injects automatically
public class OrderController
{
    public OrderController(IUserService userService) { }  // injected by container ✅
}
```

---

**Q35. How do you register services in Program.cs in .NET 6+?**

**A:**

```csharp
var builder = WebApplication.CreateBuilder(args);

// Register services
builder.Services.AddScoped<IUserService, UserService>();
builder.Services.AddTransient<IEmailService, EmailService>();
builder.Services.AddSingleton<IConfiguration>(builder.Configuration);

// Register DbContext
builder.Services.AddDbContext<AppDbContext>(options =>
    options.UseSqlServer(builder.Configuration.GetConnectionString("Default")));

// Register controllers, swagger etc
builder.Services.AddControllers();
builder.Services.AddEndpointsApiExplorer();
builder.Services.AddSwaggerGen();

var app = builder.Build();
```

---

---

## SECTION 7 — SOLID Principles

---

**Q36. Explain SOLID principles with examples.**

**A:** See Q37-Q41 below for each principle in detail.

---

**Q37. What is Single Responsibility Principle?**

**A:** A class should have only ONE reason to change — one job.

```csharp
// ❌ Wrong — multiple responsibilities
public class Employee
{
    public void CalculateSalary() { }
    public void SaveToDatabase() { }  // DB concern
    public void SendEmail() { }       // Email concern
}

// ✅ Correct
public class Employee { public void CalculateSalary() { } }
public class EmployeeRepository { public void Save(Employee e) { } }
public class EmailService { public void SendEmail(string to) { } }
```

---

**Q38. What is Open/Closed Principle?**

**A:** Open for extension, closed for modification. Add new code without changing existing code.

```csharp
// ❌ Wrong — must modify class for new discount
public class DiscountService
{
    public decimal Calculate(string type, decimal price)
    {
        if (type == "Student") return price * 0.9m;
        if (type == "Senior") return price * 0.8m;
        return price;
    }
}

// ✅ Correct — add new class for new discount
public abstract class Discount { public abstract decimal Calculate(decimal price); }
public class StudentDiscount : Discount { public override decimal Calculate(decimal p) => p * 0.9m; }
public class SeniorDiscount : Discount { public override decimal Calculate(decimal p) => p * 0.8m; }
public class NewUserDiscount : Discount { public override decimal Calculate(decimal p) => p * 0.85m; }
// Adding new discount = new class, no existing code changed ✅
```

---

**Q39. What is Liskov Substitution Principle?**

**A:** Child class should work wherever parent class is used without breaking behaviour.

```csharp
// ❌ Violation — Square breaks Rectangle behaviour
public class Rectangle
{
    public virtual int Width { get; set; }
    public virtual int Height { get; set; }
    public int Area() => Width * Height;
}
public class Square : Rectangle
{
    public override int Width { set { base.Width = base.Height = value; } }
}
// Rectangle r = new Square(); r.Width=5; r.Height=10; r.Area() = 100 not 50 ❌

// ✅ Fix — use common interface
public interface IShape { int Area(); }
public class Rectangle : IShape { public int Area() => Width * Height; }
public class Square : IShape { public int Area() => Side * Side; }
```

---

**Q40. What is Interface Segregation Principle?**

**A:** Don't force classes to implement methods they don't need. Split fat interfaces into smaller ones.

```csharp
// ❌ Fat interface
public interface IWorker { void Work(); void Eat(); void Sleep(); }
// Robot must implement Eat() and Sleep() which makes no sense ❌

// ✅ Segregated
public interface IWorkable { void Work(); }
public interface IEatable { void Eat(); }
public interface ISleepable { void Sleep(); }

public class Human : IWorkable, IEatable, ISleepable { }
public class Robot : IWorkable { }  // only what it needs ✅
```

---

**Q41. What is Dependency Inversion Principle?**

**A:** High-level modules should not depend on low-level modules. Both should depend on abstractions.

```csharp
// ❌ Wrong — high level depends on low level
public class OrderService
{
    private SqlDatabase db = new SqlDatabase();  // tightly coupled
}

// ✅ Correct — depend on abstraction
public interface IDatabase { void Save(Order o); }
public class SqlDatabase : IDatabase { public void Save(Order o) { } }

public class OrderService
{
    private readonly IDatabase _db;
    public OrderService(IDatabase db) { _db = db; }  // injected ✅
}
```

---

---

## SECTION 8 — Design Patterns

---

**Q42. What is Singleton pattern? How do you make it thread safe?**

**A:** Ensures only ONE instance of a class exists throughout the application.

```csharp
public class Singleton
{
    private static Singleton _instance;
    private static readonly object _lock = new object();

    private Singleton() { }

    public static Singleton GetInstance()
    {
        if (_instance == null)
        {
            lock (_lock)  // thread safe ✅
            {
                if (_instance == null)
                    _instance = new Singleton();
            }
        }
        return _instance;
    }
}

// Simpler thread-safe version using Lazy<T>
public class Singleton
{
    private static readonly Lazy<Singleton> _instance =
        new Lazy<Singleton>(() => new Singleton());

    private Singleton() { }
    public static Singleton Instance => _instance.Value;
}
```

Use cases: Logger, Configuration, Cache, Database connection pool.

---

**Q43. What is Factory pattern? When would you use it?**

**A:** Creates objects without exposing creation logic. Client doesn't know which class is instantiated.

```csharp
public abstract class Notification { public abstract void Send(string msg); }
public class EmailNotification : Notification
{ public override void Send(string msg) => Console.WriteLine($"Email: {msg}"); }
public class SMSNotification : Notification
{ public override void Send(string msg) => Console.WriteLine($"SMS: {msg}"); }

public class NotificationFactory
{
    public static Notification Create(string type) => type switch
    {
        "Email" => new EmailNotification(),
        "SMS"   => new SMSNotification(),
        _ => throw new ArgumentException("Unknown type")
    };
}

// Client doesn't know which class — just calls factory ✅
var notification = NotificationFactory.Create("Email");
notification.Send("Hello!");
```

---

**Q44. What is Repository pattern? Why is it used?**

**A:** Abstracts data access layer. Business logic doesn't directly talk to database.

```csharp
public interface IEmployeeRepository
{
    Employee GetById(int id);
    List<Employee> GetAll();
    void Add(Employee emp);
    void Delete(int id);
}

public class EmployeeRepository : IEmployeeRepository
{
    private readonly AppDbContext _context;
    public EmployeeRepository(AppDbContext context) { _context = context; }

    public Employee GetById(int id) => _context.Employees.Find(id);
    public List<Employee> GetAll() => _context.Employees.ToList();
    public void Add(Employee emp) { _context.Employees.Add(emp); _context.SaveChanges(); }
    public void Delete(int id)
    {
        var emp = GetById(id);
        if (emp != null) { _context.Employees.Remove(emp); _context.SaveChanges(); }
    }
}
```

Benefits: Easy to unit test (mock repository), swap database without changing business logic.

---

**Q45. What is Observer pattern? How is it related to events in C#?**

**A:** Observer pattern — when one object changes, all its dependents are notified. C# events implement this pattern.

```csharp
public class OrderService
{
    public event EventHandler<string> OrderPlaced;  // publisher

    public void PlaceOrder(string product)
    {
        Console.WriteLine($"Order placed: {product}");
        OrderPlaced?.Invoke(this, product);  // notify all subscribers
    }
}

// Subscribers
orderService.OrderPlaced += (s, product) => Console.WriteLine($"Email sent for {product}");
orderService.OrderPlaced += (s, product) => Console.WriteLine($"SMS sent for {product}");

orderService.PlaceOrder("Laptop");
// Email sent for Laptop
// SMS sent for Laptop
```

---

**Q46. What is Strategy pattern? Give a real world example.**

**A:** Defines a family of algorithms, encapsulates each one, and makes them interchangeable.

```csharp
public interface ISortStrategy { void Sort(List<int> data); }
public class BubbleSort : ISortStrategy { public void Sort(List<int> data) { /* bubble sort */ } }
public class QuickSort : ISortStrategy { public void Sort(List<int> data) { /* quick sort */ } }

public class Sorter
{
    private ISortStrategy _strategy;
    public Sorter(ISortStrategy strategy) { _strategy = strategy; }
    public void SetStrategy(ISortStrategy strategy) { _strategy = strategy; }
    public void Sort(List<int> data) => _strategy.Sort(data);
}

// Swap strategy at runtime ✅
Sorter sorter = new Sorter(new BubbleSort());
sorter.Sort(data);
sorter.SetStrategy(new QuickSort());  // switch algorithm
sorter.Sort(data);
```

---

**Q47. What is Decorator pattern?**

**A:** Adds behaviour to an object dynamically without changing its class.

```csharp
public interface ICoffee { string GetDescription(); decimal GetCost(); }

public class SimpleCoffee : ICoffee
{
    public string GetDescription() => "Simple Coffee";
    public decimal GetCost() => 50;
}

public class MilkDecorator : ICoffee
{
    private ICoffee _coffee;
    public MilkDecorator(ICoffee coffee) { _coffee = coffee; }
    public string GetDescription() => _coffee.GetDescription() + " + Milk";
    public decimal GetCost() => _coffee.GetCost() + 20;
}

public class SugarDecorator : ICoffee
{
    private ICoffee _coffee;
    public SugarDecorator(ICoffee coffee) { _coffee = coffee; }
    public string GetDescription() => _coffee.GetDescription() + " + Sugar";
    public decimal GetCost() => _coffee.GetCost() + 10;
}

// Usage
ICoffee coffee = new SimpleCoffee();
coffee = new MilkDecorator(coffee);
coffee = new SugarDecorator(coffee);
Console.WriteLine(coffee.GetDescription());  // Simple Coffee + Milk + Sugar
Console.WriteLine(coffee.GetCost());         // 80
```

---

---

## SECTION 9 — Delegates, Events & Generics

---

**Q48. What is a delegate in C#?**

**A:** A delegate is a type-safe function pointer. It holds a reference to a method.

```csharp
public delegate int MathOperation(int a, int b);

public static int Add(int a, int b) => a + b;
public static int Multiply(int a, int b) => a * b;

MathOperation op = Add;
Console.WriteLine(op(3, 4));  // 7

op = Multiply;
Console.WriteLine(op(3, 4));  // 12

// Multicast — multiple methods
op += Add;
op(3, 4);  // calls both Multiply and Add
```

---

**Q49. What is the difference between Func, Action and Predicate?**

**A:**

||Return Type|Use|
|---|---|---|
|Func<T, TResult>|Has return value|Any method returning value|
|Action<T>|void|Any method returning nothing|
|Predicate<T>|bool|Condition check|

```csharp
Func<int, int, int> add = (a, b) => a + b;
Console.WriteLine(add(3, 4));  // 7

Func<string, int> length = s => s.Length;
Console.WriteLine(length("Hello"));  // 5

Action<string> print = msg => Console.WriteLine(msg);
print("Hello");  // Hello

Action<int, int> printSum = (a, b) => Console.WriteLine(a + b);
printSum(3, 4);  // 7

Predicate<int> isEven = n => n % 2 == 0;
Console.WriteLine(isEven(4));  // True
Console.WriteLine(isEven(5));  // False
```

---

**Q50. What is an event? How is it different from a delegate?**

**A:**

||Delegate|Event|
|---|---|---|
|Invoke|Anyone can invoke|Only declaring class can invoke|
|Assign|Can reassign (=)|Can only add/remove (+=, -=)|
|Use|Method reference|Publisher-subscriber pattern|

```csharp
public class Button
{
    public event EventHandler Clicked;  // event — restricted access

    public void Click()
    {
        Clicked?.Invoke(this, EventArgs.Empty);  // only Button can invoke
    }
}

Button btn = new Button();
btn.Clicked += (s, e) => Console.WriteLine("Button clicked!");  // subscribe ✅
// btn.Clicked?.Invoke(...) ❌ — cannot invoke from outside
```

---

**Q51. What is a lambda expression?**

**A:** Anonymous function written inline. Short way to write delegates.

```csharp
// Regular method
public int Add(int a, int b) { return a + b; }

// Lambda expression
Func<int, int, int> add = (a, b) => a + b;

// Used with LINQ
var highEarners = employees.Where(e => e.Salary > 80000).ToList();

// Multi-line lambda
Func<int, int> factorial = n =>
{
    int result = 1;
    for (int i = 1; i <= n; i++) result *= i;
    return result;
};
```

---

**Q52. What are generics? Why are they used?**

**A:** Generics allow writing code that works with any data type. Provides type safety without boxing/unboxing.

```csharp
// Without generics — uses object, requires boxing
public class Box { public object Value { get; set; } }

// With generics — type safe ✅
public class Box<T>
{
    public T Value { get; set; }
    public void Set(T val) { Value = val; }
    public T Get() { return Value; }
}

Box<int> intBox = new Box<int>();
intBox.Set(42);
int val = intBox.Get();  // no casting needed ✅

Box<string> strBox = new Box<string>();
strBox.Set("Hello");
```

---

**Q53. What is a generic constraint? Give examples.**

**A:** Constraints limit what types can be used with generics.

```csharp
// where T : class — must be reference type
public class Repository<T> where T : class { }

// where T : struct — must be value type
public class ValueWrapper<T> where T : struct { }

// where T : new() — must have parameterless constructor
public T Create<T>() where T : new() => new T();

// where T : BaseClass — must inherit from BaseClass
public class Container<T> where T : Animal { }

// where T : IInterface — must implement interface
public class Logger<T> where T : ILoggable { }

// Multiple constraints
public class Service<T> where T : class, IDisposable, new() { }
```

---

---

## SECTION 10 — Exception Handling

---

**Q54. What is the difference between throw and throw ex?**

**A:**

- **throw** — Re-throws exception preserving original stack trace. ✅
- **throw ex** — Re-throws but RESETS stack trace — harder to debug. ❌

```csharp
try { /* some code */ }
catch (Exception ex)
{
    // throw ex;  ❌ — loses original stack trace
    throw;       // ✅ — preserves original stack trace
}
```

---

**Q55. What is a custom exception? When should you create one?**

**A:** Create custom exceptions when you need to represent specific business errors.

```csharp
public class InsufficientFundsException : Exception
{
    public decimal Amount { get; }
    public decimal Balance { get; }

    public InsufficientFundsException(decimal amount, decimal balance)
        : base($"Cannot withdraw ₹{amount}. Available: ₹{balance}")
    {
        Amount = amount;
        Balance = balance;
    }
}

// Usage
if (amount > balance)
    throw new InsufficientFundsException(amount, balance);

// Catch specifically
try { /* withdraw */ }
catch (InsufficientFundsException ex)
{
    Console.WriteLine($"Failed: {ex.Message}. Requested: {ex.Amount}");
}
```

---

**Q56. What is the finally block? Will it always execute?**

**A:** finally always executes — whether exception occurs or not. Used to release resources.

```csharp
try
{
    // risky code
}
catch (Exception ex)
{
    // handle error
}
finally
{
    // ALWAYS runs ✅
    // close connections, release resources
    connection.Close();
}
```

**Exception:** finally does NOT run if:

- `Environment.Exit()` is called
- Power failure / process killed
- Infinite loop in try block

---

**Q57. What is the difference between Exception and ApplicationException?**

**A:**

- **Exception** — Base class for all exceptions in .NET.
- **ApplicationException** — Meant for application-level exceptions but Microsoft no longer recommends using it. Inherit directly from Exception instead.

```csharp
// ❌ Old way
public class MyException : ApplicationException { }

// ✅ Recommended
public class MyException : Exception { }
```

---

---

## SECTION 11 — Memory & Performance

---

**Q58. What is the difference between value type and reference type?**

**A:**

||Value Type|Reference Type|
|---|---|---|
|Stored|Stack|Heap|
|Copy|Full copy|Reference copy|
|Null|Cannot be null|Can be null|
|Examples|int, double, bool, struct, enum|class, string, array, interface|

```csharp
int a = 5;
int b = a;  // copy
b = 10;
Console.WriteLine(a);  // 5 — unchanged ✅

int[] arr1 = {1, 2, 3};
int[] arr2 = arr1;  // same reference!
arr2[0] = 99;
Console.WriteLine(arr1[0]);  // 99 — changed! ⚠️
```

---

**Q59. What is boxing and unboxing? Why is it a performance concern?**

**A:**

- **Boxing** — Converting value type to reference type (object). Allocates heap memory.
- **Unboxing** — Converting back from object to value type. Requires casting.

```csharp
int num = 42;
object boxed = num;    // boxing — heap allocation ⚠️
int unboxed = (int)boxed;  // unboxing — casting required ⚠️

// Performance concern — use generics instead
List<object> list = new List<object>();
list.Add(42);  // boxing every int! ❌

List<int> list = new List<int>();
list.Add(42);  // no boxing ✅
```

---

**Q60. What is garbage collection in C#?**

**A:** GC automatically manages memory by reclaiming objects no longer referenced. Works in 3 generations (0, 1, 2).

- **Gen 0** — Short-lived objects. Collected most frequently.
- **Gen 1** — Medium-lived objects. Buffer between 0 and 2.
- **Gen 2** — Long-lived objects. Collected least frequently.

```csharp
// Force GC — avoid in production
GC.Collect();

// Best practice — implement IDisposable for unmanaged resources
// Don't rely on GC for cleanup of DB connections, file handles etc.
```

---

**Q61. What is the difference between string and StringBuilder?**

**A:**

||string|StringBuilder|
|---|---|---|
|Mutability|Immutable|Mutable|
|Concatenation|Creates new object each time|Modifies same object|
|Performance|Slow for many operations|Fast for many operations|
|Use when|Few concatenations|Loop/many concatenations|

```csharp
// string — bad for loops ❌
string result = "";
for (int i = 0; i < 1000; i++)
    result += i;  // 1000 new string objects created!

// StringBuilder — good for loops ✅
StringBuilder sb = new StringBuilder();
for (int i = 0; i < 1000; i++)
    sb.Append(i);  // same object modified
string result = sb.ToString();
```

---

**Q62. What is ref vs out vs in keywords?**

**A:**

||ref|out|in|
|---|---|---|---|
|Must initialize before passing|✅ Yes|❌ No|✅ Yes|
|Must assign in method|❌ No|✅ Yes|❌ No|
|Can modify|✅ Yes|✅ Yes|❌ No (readonly)|

```csharp
// ref — must be initialized, can read and write
void Double(ref int x) { x *= 2; }
int num = 5;
Double(ref num);
Console.WriteLine(num);  // 10

// out — doesn't need initialization, must assign inside method
bool TryParse(string s, out int result)
{
    result = 0;  // must assign ✅
    return int.TryParse(s, out result);
}
TryParse("42", out int value);

// in — readonly reference, cannot modify
void Print(in int x) { Console.WriteLine(x); /* x = 5; ❌ */ }
```

---

---

## SECTION 12 — Advanced

---

**Q63. What is a deadlock? How do you prevent it?**

**A:** Deadlock — two threads waiting for each other to release a lock. Neither can proceed.

```
Thread A locks Resource 1, wants Resource 2
Thread B locks Resource 2, wants Resource 1
→ Both wait forever = Deadlock 💀
```

**Prevention:**

- Always acquire locks in same order
- Use `lock` with timeout
- Use `async/await` instead of blocking calls
- Use `SemaphoreSlim` for async locking

```csharp
// Always lock in same order
lock (resource1) { lock (resource2) { /* work */ } }  // ✅ consistent order
```

---

**Q64. What is async and await? How does it work internally?**

**A:** async/await enables non-blocking asynchronous code without complex threading.

```csharp
// ❌ Synchronous — blocks thread
public string GetData()
{
    var result = httpClient.GetString(url);  // blocks!
    return result;
}

// ✅ Async — doesn't block thread
public async Task<string> GetDataAsync()
{
    var result = await httpClient.GetStringAsync(url);  // frees thread while waiting
    return result;
}

// Usage
string data = await GetDataAsync();
```

Internally — compiler converts async method into a state machine. `await` suspends execution and returns thread to thread pool. When task completes, execution resumes.

---

**Q65. What is the difference between Task and Thread?**

**A:**

||Thread|Task|
|---|---|---|
|Level|Low level|High level|
|Thread pool|❌ Creates new thread|✅ Uses thread pool|
|Return value|❌ No|✅ Yes (Task<T>)|
|async/await|❌ No|✅ Yes|
|Cancellation|Manual|✅ CancellationToken|

```csharp
// Thread — low level, avoid for most cases
Thread t = new Thread(() => Console.WriteLine("Thread"));
t.Start();

// Task — preferred ✅
Task t = Task.Run(() => Console.WriteLine("Task"));
await t;

Task<int> task = Task.Run(() => 42);
int result = await task;
```

---

**Q66. What is IEnumerable vs ICollection vs IList?**

**A:**

|Interface|Can Iterate|Count|Add/Remove|Index Access|
|---|---|---|---|---|
|IEnumerable|✅|❌|❌|❌|
|ICollection|✅|✅|✅|❌|
|IList|✅|✅|✅|✅|

```csharp
IEnumerable<int> e = new List<int> {1,2,3};  // can only iterate
ICollection<int> c = new List<int> {1,2,3};  // iterate + count + add/remove
IList<int> l = new List<int> {1,2,3};        // everything + index access
l[0] = 99;  // ✅ index access
```

Use most restrictive interface in method parameters — program to abstraction.

---

**Q67. What is covariance and contravariance in generics?**

**A:**

- **Covariance (out)** — Can use more derived type. Read only.
- **Contravariance (in)** — Can use less derived type. Write only.

```csharp
// Covariance — out keyword
IEnumerable<Dog> dogs = new List<Dog>();
IEnumerable<Animal> animals = dogs;  // ✅ Dog is Animal

// Contravariance — in keyword
Action<Animal> animalAction = a => Console.WriteLine(a.Name);
Action<Dog> dogAction = animalAction;  // ✅ Action<Animal> can handle Dog
dogAction(new Dog());
```

---

**Q68. What is reflection in C#?**

**A:** Reflection allows inspecting and invoking type information at runtime — class names, methods, properties, attributes.

```csharp
// Get type info at runtime
Type type = typeof(Employee);
Console.WriteLine(type.Name);  // Employee

// Get all properties
foreach (var prop in type.GetProperties())
    Console.WriteLine(prop.Name);

// Get all methods
foreach (var method in type.GetMethods())
    Console.WriteLine(method.Name);

// Create instance dynamically
object emp = Activator.CreateInstance(type);

// Invoke method dynamically
var method = type.GetMethod("GetName");
method.Invoke(emp, null);

// Get custom attributes
var attributes = type.GetCustomAttributes(typeof(ObsoleteAttribute), false);
```

Use cases: Dependency injection containers, ORMs (Entity Framework), serialization, unit testing frameworks.

---

---

## ⚡ Final Quick Reference Cheatsheet

### OOP Pillars

|Pillar|Keyword|Example|
|---|---|---|
|Encapsulation|private + properties|Bank balance|
|Abstraction|abstract / interface|Shape.Area()|
|Inheritance|: BaseClass|Dog : Animal|
|Polymorphism|virtual + override|Sound() per animal|

### Abstract Class vs Interface

||Abstract|Interface|
|---|---|---|
|Fields|✅|❌|
|Constructor|✅|❌|
|Multiple inherit|❌|✅|
|Use when|IS-A + shared code|CAN-DO contract|

### DI Lifetime Scopes

|Scope|When|Use|
|---|---|---|
|Singleton|Once ever|Logger, Config|
|Scoped|Per request|DbContext|
|Transient|Every time|EmailService|

### SOLID One Liners

||Rule|
|---|---|
|S|One class, one job|
|O|Extend not modify|
|L|Child works like parent|
|I|No unused interface methods|
|D|Depend on interfaces|

### string vs StringBuilder

||string|StringBuilder|
|---|---|---|
|Mutable|❌|✅|
|Loops|❌ Slow|✅ Fast|

### Value vs Reference

||Value|Reference|
|---|---|---|
|Stack/Heap|Stack|Heap|
|Copy|Full|Reference|
|Null|❌|✅|

### Top 10 Interview Tips

1. Always give real world examples — don't just define
2. Know abstract class vs interface inside out — most asked
3. Explain SOLID with code examples
4. Know at least 3 design patterns with use cases
5. Understand DI lifetime scopes — Singleton trap is common question
6. Know virtual vs override vs new — tricky but common
7. Understand value vs reference type deeply
8. Know IEnumerable vs IQueryable — EF interview must
9. Know async/await basics — every .NET interview asks this
10. Always mention real project usage — "In my project I used..."