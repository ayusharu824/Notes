# C# OOP Interview Notes

### Complete guide for interview preparation

---
	
## SECTION 1 — THE 4 PILLARS OF OOP

The 4 pillars are the most common starting point in any OOP interview.

|Pillar|One Line Definition|
|---|---|
|Encapsulation|Hide internal data, expose only what is needed|
|Abstraction|Hide complexity, show only relevant details|
|Inheritance|Child class reuses properties and methods of parent class|
|Polymorphism|Same method name, different behaviour|

---

---

## SECTION 2 — ENCAPSULATION

**Definition:** Wrapping data (fields) and methods together in a class, and restricting direct access to internal data using access modifiers.

### Example

```csharp
public class BankAccount
{
    private decimal balance;  // private — hidden from outside

    public void Deposit(decimal amount)
    {
        if (amount > 0)
            balance += amount;
    }

    public void Withdraw(decimal amount)
    {
        if (amount > 0 && amount <= balance)
            balance -= amount;
        else
            Console.WriteLine("Insufficient funds!");
    }

    public decimal GetBalance()  // controlled access
    {
        return balance;
    }
}

// Usage
BankAccount acc = new BankAccount();
acc.Deposit(5000);
acc.Withdraw(2000);
Console.WriteLine(acc.GetBalance());  // 3000

// acc.balance = -99999;  ❌ ERROR — private, cannot access directly
```

### Access Modifiers

|Modifier|Accessible From|
|---|---|
|private|Same class only|
|protected|Same class + child classes|
|internal|Same assembly/project|
|public|Everywhere|
|protected internal|Same assembly OR child classes|
|private protected|Same class AND child classes in same assembly|

### Properties — C# way of Encapsulation

```csharp
public class Employee
{
    private string name;
    private int age;

    // Full property
    public string Name
    {
        get { return name; }
        set
        {
            if (!string.IsNullOrEmpty(value))
                name = value;
        }
    }

    // Auto property (most common)
    public string Department { get; set; }

    // Read only property
    public int Age
    {
        get { return age; }
        private set { age = value; }  // only settable inside class
    }

    // Expression bodied property
    public string Info => $"{Name}, Age: {Age}";
}
```

**Interview Answer:** Encapsulation protects data integrity. By making fields private and exposing them through properties or methods, we control how data is accessed and modified — preventing invalid states.

---

---

## SECTION 3 — ABSTRACTION

**Definition:** Hiding implementation complexity and showing only essential features. Achieved through abstract classes and interfaces.

### Abstract Class

```csharp
public abstract class Shape
{
    public string Color { get; set; }

    // Abstract method — no body, MUST be overridden by child
    public abstract double CalculateArea();

    // Concrete method — has body, can be used as-is
    public void DisplayColor()
    {
        Console.WriteLine($"Color: {Color}");
    }
}

public class Circle : Shape
{
    public double Radius { get; set; }

    // Must override abstract method
    public override double CalculateArea()
    {
        return Math.PI * Radius * Radius;
    }
}

public class Rectangle : Shape
{
    public double Width { get; set; }
    public double Height { get; set; }

    public override double CalculateArea()
    {
        return Width * Height;
    }
}

// Usage
Shape s1 = new Circle { Radius = 5, Color = "Red" };
Shape s2 = new Rectangle { Width = 4, Height = 6, Color = "Blue" };

Console.WriteLine(s1.CalculateArea());  // 78.54
Console.WriteLine(s2.CalculateArea());  // 24
s1.DisplayColor();  // Color: Red
```

### Interface

```csharp
public interface IPayment
{
    bool ProcessPayment(decimal amount);  // no body
    string GetPaymentStatus();
}

public class CreditCard : IPayment
{
    public bool ProcessPayment(decimal amount)
    {
        Console.WriteLine($"Processing ₹{amount} via Credit Card");
        return true;
    }

    public string GetPaymentStatus() => "Credit Card Payment Successful";
}

public class UPI : IPayment
{
    public bool ProcessPayment(decimal amount)
    {
        Console.WriteLine($"Processing ₹{amount} via UPI");
        return true;
    }

    public string GetPaymentStatus() => "UPI Payment Successful";
}

// Usage
IPayment payment = new UPI();
payment.ProcessPayment(500);
```

### Abstract Class vs Interface

||Abstract Class|Interface|
|---|---|---|
|Methods with body|✅ Yes|✅ Yes (from C# 8)|
|Fields/Variables|✅ Yes|❌ No|
|Constructor|✅ Yes|❌ No|
|Multiple inheritance|❌ No (single only)|✅ Yes (multiple)|
|Access modifiers|✅ Yes|❌ No (all public)|
|Use when|Shared base with some implementation|Contract / capability|

```csharp
// Multiple interfaces — allowed ✅
public class SmartPayment : IPayment, ILoggable, IRefundable
{
    // must implement all methods from all interfaces
}
```

**Interview Answer:** Abstraction hides HOW something works and shows WHAT it does. Abstract class is used when classes share common behaviour. Interface is used to define a contract — what a class CAN DO regardless of what it IS.

---

---

## SECTION 4 — INHERITANCE

**Definition:** A child class inherits properties and methods from a parent class, promoting code reuse.

### Basic Inheritance

```csharp
public class Animal
{
    public string Name { get; set; }
    public int Age { get; set; }

    public Animal(string name, int age)
    {
        Name = name;
        Age = age;
    }

    public virtual void MakeSound()  // virtual = can be overridden
    {
        Console.WriteLine("Some animal sound");
    }

    public void Eat()
    {
        Console.WriteLine($"{Name} is eating");
    }
}

public class Dog : Animal
{
    public string Breed { get; set; }

    public Dog(string name, int age, string breed)
        : base(name, age)  // calls parent constructor
    {
        Breed = breed;
    }

    public override void MakeSound()  // overrides parent method
    {
        Console.WriteLine($"{Name} says: Woof!");
    }

    public void Fetch()
    {
        Console.WriteLine($"{Name} is fetching the ball!");
    }
}

public class Cat : Animal
{
    public Cat(string name, int age) : base(name, age) { }

    public override void MakeSound()
    {
        Console.WriteLine($"{Name} says: Meow!");
    }
}

// Usage
Dog dog = new Dog("Bruno", 3, "Labrador");
dog.MakeSound();  // Bruno says: Woof!
dog.Eat();        // Bruno is eating (inherited from Animal)
dog.Fetch();      // Bruno is fetching the ball!

Cat cat = new Cat("Whiskers", 2);
cat.MakeSound();  // Whiskers says: Meow!
```

### Types of Inheritance in C#

```csharp
// 1. Single Inheritance
public class Vehicle { }
public class Car : Vehicle { }

// 2. Multi-level Inheritance
public class Vehicle { }
public class Car : Vehicle { }
public class ElectricCar : Car { }  // ElectricCar → Car → Vehicle

// 3. Hierarchical Inheritance
public class Animal { }
public class Dog : Animal { }  // both inherit from Animal
public class Cat : Animal { }

// 4. Multiple Inheritance — NOT allowed with classes in C#
// public class Hybrid : Car, Truck { }  ❌ ERROR

// Multiple inheritance through interfaces — ALLOWED ✅
public class SmartDevice : IPhone, ICamera, INavigator { }
```

### sealed — Prevent Inheritance

```csharp
public sealed class FinalClass
{
    // no one can inherit this
}

// public class Child : FinalClass { }  ❌ ERROR

// sealed method — prevent overriding in further child classes
public class Parent
{
    public virtual void Display() { }
}

public class Child : Parent
{
    public sealed override void Display()
    {
        // grandchild cannot override this
    }
}
```

### base keyword

```csharp
public class Employee
{
    public string Name { get; set; }
    public decimal Salary { get; set; }

    public Employee(string name, decimal salary)
    {
        Name = name;
        Salary = salary;
    }

    public virtual decimal CalculateBonus()
    {
        return Salary * 0.10m;
    }
}

public class Manager : Employee
{
    public int TeamSize { get; set; }

    public Manager(string name, decimal salary, int teamSize)
        : base(name, salary)  // calling parent constructor
    {
        TeamSize = teamSize;
    }

    public override decimal CalculateBonus()
    {
        decimal baseBonus = base.CalculateBonus();  // calling parent method
        return baseBonus + (TeamSize * 1000);       // adding extra
    }
}

// Usage
Manager mgr = new Manager("Alice", 90000, 5);
Console.WriteLine(mgr.CalculateBonus());  // 9000 + 5000 = 14000
```

**Interview Answer:** Inheritance promotes code reuse. Child class gets all public and protected members of parent. C# supports single and multilevel inheritance for classes, but multiple inheritance is achieved through interfaces.

---

---

## SECTION 5 — POLYMORPHISM

**Definition:** Same method name behaves differently based on the object. Two types: Compile-time (Method Overloading) and Runtime (Method Overriding).

### Compile-time Polymorphism — Method Overloading

Same method name, different parameters. Resolved at compile time.

```csharp
public class Calculator
{
    // Same name, different parameters
    public int Add(int a, int b)
    {
        return a + b;
    }

    public double Add(double a, double b)
    {
        return a + b;
    }

    public int Add(int a, int b, int c)
    {
        return a + b + c;
    }

    public string Add(string a, string b)
    {
        return a + b;  // string concatenation
    }
}

// Usage
Calculator calc = new Calculator();
Console.WriteLine(calc.Add(2, 3));          // 5
Console.WriteLine(calc.Add(2.5, 3.5));      // 6.0
Console.WriteLine(calc.Add(1, 2, 3));       // 6
Console.WriteLine(calc.Add("Hi", " There")); // Hi There
```

### Runtime Polymorphism — Method Overriding

Same method name in parent and child, resolved at runtime based on actual object type.

```csharp
public class Notification
{
    public virtual void Send(string message)
    {
        Console.WriteLine($"Sending notification: {message}");
    }
}

public class EmailNotification : Notification
{
    public override void Send(string message)
    {
        Console.WriteLine($"Sending EMAIL: {message}");
    }
}

public class SMSNotification : Notification
{
    public override void Send(string message)
    {
        Console.WriteLine($"Sending SMS: {message}");
    }
}

public class PushNotification : Notification
{
    public override void Send(string message)
    {
        Console.WriteLine($"Sending PUSH: {message}");
    }
}

// Runtime polymorphism in action
List<Notification> notifications = new List<Notification>
{
    new EmailNotification(),
    new SMSNotification(),
    new PushNotification()
};

foreach (var n in notifications)
{
    n.Send("Your order is confirmed!");
    // Each calls its OWN Send method at runtime
}

// Output:
// Sending EMAIL: Your order is confirmed!
// Sending SMS: Your order is confirmed!
// Sending PUSH: Your order is confirmed!
```

### virtual vs override vs new

```csharp
public class Parent
{
    public virtual void Show()   // virtual = overridable
        => Console.WriteLine("Parent Show");

    public void Display()
        => Console.WriteLine("Parent Display");
}

public class Child : Parent
{
    public override void Show()  // override = replaces parent
        => Console.WriteLine("Child Show");

    public new void Display()    // new = hides parent (not overriding)
        => Console.WriteLine("Child Display");
}

// Difference between override and new
Parent p = new Child();
p.Show();     // Child Show    ← override works polymorphically
p.Display();  // Parent Display ← new does NOT work polymorphically

Child c = new Child();
c.Show();     // Child Show
c.Display();  // Child Display
```

### Operator Overloading

```csharp
public class Vector
{
    public int X { get; set; }
    public int Y { get; set; }

    public Vector(int x, int y) { X = x; Y = y; }

    // Overload + operator
    public static Vector operator +(Vector v1, Vector v2)
    {
        return new Vector(v1.X + v2.X, v1.Y + v2.Y);
    }

    public override string ToString() => $"({X}, {Y})";
}

// Usage
Vector v1 = new Vector(1, 2);
Vector v2 = new Vector(3, 4);
Vector v3 = v1 + v2;
Console.WriteLine(v3);  // (4, 6)
```

**Interview Answer:** Polymorphism means one interface, many forms. Overloading = same method, different parameters (compile time). Overriding = same method, different behaviour in child class (runtime). Runtime polymorphism is achieved using virtual + override keywords.

---

---

## SECTION 6 — CLASSES AND OBJECTS

### Types of Classes

```csharp
// 1. Regular Class
public class Person { }

// 2. Abstract Class — cannot be instantiated
public abstract class Vehicle
{
    public abstract void Start();
}

// 3. Sealed Class — cannot be inherited
public sealed class Singleton { }

// 4. Static Class — only static members, cannot instantiate
public static class MathHelper
{
    public static int Square(int n) => n * n;
}
MathHelper.Square(5);  // 25
// new MathHelper();   ❌ ERROR

// 5. Partial Class — split across multiple files
public partial class Customer
{
    public string Name { get; set; }
}
public partial class Customer  // same class, different file
{
    public string Email { get; set; }
}
```

### Constructors

```csharp
public class Product
{
    public string Name { get; set; }
    public decimal Price { get; set; }
    public int Stock { get; set; }

    // Default constructor
    public Product()
    {
        Stock = 0;
    }

    // Parameterized constructor
    public Product(string name, decimal price)
    {
        Name = name;
        Price = price;
        Stock = 0;
    }

    // Constructor chaining using this()
    public Product(string name, decimal price, int stock)
        : this(name, price)  // calls above constructor first
    {
        Stock = stock;
    }

    // Copy constructor
    public Product(Product other)
    {
        Name = other.Name;
        Price = other.Price;
        Stock = other.Stock;
    }

    // Static constructor — runs once when class is first used
    static Product()
    {
        Console.WriteLine("Product class initialized");
    }
}
```

### Destructor / Finalizer

```csharp
public class ResourceHandler
{
    public ResourceHandler()
    {
        Console.WriteLine("Resource acquired");
    }

    ~ResourceHandler()  // destructor — called by GC
    {
        Console.WriteLine("Resource released");
    }
}
```

### IDisposable — Better than Destructor

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

// Using statement — auto calls Dispose()
using (var conn = new DatabaseConnection())
{
    conn.Open();
}  // Dispose() called automatically here
```

---

---

## SECTION 7 — INTERFACE IN DEPTH

```csharp
// Interface definition
public interface IAnimal
{
    string Name { get; set; }       // property
    void Eat();                     // method
    string Sound { get; }           // read-only property
}

// Interface with default method (C# 8+)
public interface ILogger
{
    void Log(string message);

    // Default implementation
    void LogError(string message)
    {
        Log($"ERROR: {message}");
    }
}

// Implementing multiple interfaces
public class Dog : IAnimal, ILogger
{
    public string Name { get; set; }
    public string Sound => "Woof";

    public void Eat() => Console.WriteLine($"{Name} is eating");
    public void Log(string message) => Console.WriteLine($"[LOG] {message}");
}

// Explicit interface implementation — when two interfaces have same method name
public interface IShape
{
    double GetArea();
}

public interface IPrintable
{
    void Print();
}

public class Square : IShape, IPrintable
{
    public double Side { get; set; }

    double IShape.GetArea() => Side * Side;  // explicit

    void IPrintable.Print() => Console.WriteLine($"Square: {Side}x{Side}");
}
```

---

---

## SECTION 8 — STATIC KEYWORD

```csharp
public class Counter
{
    private static int count = 0;  // shared across ALL instances
    private int id;

    public Counter()
    {
        count++;
        id = count;
    }

    public static int GetCount() => count;  // static method
    public int GetId() => id;               // instance method
}

// Usage
Counter c1 = new Counter();
Counter c2 = new Counter();
Counter c3 = new Counter();

Console.WriteLine(Counter.GetCount());  // 3 — called on class, not object
Console.WriteLine(c1.GetId());          // 1
Console.WriteLine(c2.GetId());          // 2

// Static class
public static class AppConfig
{
    public static string ConnectionString = "Server=localhost;";
    public static int Timeout = 30;

    public static void PrintConfig()
    {
        Console.WriteLine(ConnectionString);
    }
}

AppConfig.PrintConfig();  // called on class directly
```

---

---

## SECTION 9 — GENERICS

Generics allow writing code that works with any data type.

```csharp
// Generic class
public class Box<T>
{
    private T value;

    public void Set(T val) { value = val; }
    public T Get() { return value; }
}

// Usage
Box<int> intBox = new Box<int>();
intBox.Set(42);
Console.WriteLine(intBox.Get());  // 42

Box<string> strBox = new Box<string>();
strBox.Set("Hello");
Console.WriteLine(strBox.Get());  // Hello

// Generic method
public class Utility
{
    public static void Swap<T>(ref T a, ref T b)
    {
        T temp = a;
        a = b;
        b = temp;
    }
}

int x = 5, y = 10;
Utility.Swap(ref x, ref y);
Console.WriteLine($"x={x}, y={y}");  // x=10, y=5

// Generic with constraint
public class Repository<T> where T : class  // T must be a reference type
{
    private List<T> items = new List<T>();

    public void Add(T item) => items.Add(item);
    public List<T> GetAll() => items;
}

// Common constraints
// where T : class          — reference type
// where T : struct         — value type
// where T : new()          — must have parameterless constructor
// where T : BaseClass      — must inherit from BaseClass
// where T : IInterface     — must implement interface
```

---

---

## SECTION 10 — DELEGATES AND EVENTS

### Delegate — Type-safe function pointer

```csharp
// Declare delegate
public delegate int MathOperation(int a, int b);

public class Program
{
    public static int Add(int a, int b) => a + b;
    public static int Multiply(int a, int b) => a * b;

    static void Main()
    {
        MathOperation op = Add;
        Console.WriteLine(op(3, 4));  // 7

        op = Multiply;
        Console.WriteLine(op(3, 4));  // 12

        // Multicast delegate
        MathOperation multi = Add;
        multi += Multiply;  // both methods called
    }
}
```

### Func, Action, Predicate — Built-in delegates

```csharp
// Func — has return value
Func<int, int, int> add = (a, b) => a + b;
Console.WriteLine(add(3, 4));  // 7

Func<string, int> getLength = s => s.Length;
Console.WriteLine(getLength("Hello"));  // 5

// Action — no return value (void)
Action<string> print = msg => Console.WriteLine(msg);
print("Hello World");

Action<int, int> printSum = (a, b) => Console.WriteLine(a + b);
printSum(3, 4);  // 7

// Predicate — returns bool
Predicate<int> isEven = n => n % 2 == 0;
Console.WriteLine(isEven(4));  // True
Console.WriteLine(isEven(5));  // False
```

### Events

```csharp
public class OrderService
{
    // Declare event using EventHandler
    public event EventHandler<string> OrderPlaced;

    public void PlaceOrder(string product)
    {
        Console.WriteLine($"Order placed for: {product}");

        // Raise event
        OrderPlaced?.Invoke(this, product);
    }
}

public class EmailService
{
    public void OnOrderPlaced(object sender, string product)
    {
        Console.WriteLine($"Email sent: Order confirmed for {product}");
    }
}

public class SMSService
{
    public void OnOrderPlaced(object sender, string product)
    {
        Console.WriteLine($"SMS sent: Your order for {product} is confirmed");
    }
}

// Usage
OrderService orderService = new OrderService();
EmailService emailService = new EmailService();
SMSService smsService = new SMSService();

// Subscribe to event
orderService.OrderPlaced += emailService.OnOrderPlaced;
orderService.OrderPlaced += smsService.OnOrderPlaced;

orderService.PlaceOrder("Laptop");
// Output:
// Order placed for: Laptop
// Email sent: Order confirmed for Laptop
// SMS sent: Your order for Laptop is confirmed
```

---

---

## SECTION 11 — EXCEPTION HANDLING

```csharp
public class BankService
{
    public void Transfer(decimal amount, decimal balance)
    {
        try
        {
            if (amount <= 0)
                throw new ArgumentException("Amount must be positive");

            if (amount > balance)
                throw new InvalidOperationException("Insufficient balance");

            Console.WriteLine($"Transferred ₹{amount}");
        }
        catch (ArgumentException ex)
        {
            Console.WriteLine($"Invalid input: {ex.Message}");
        }
        catch (InvalidOperationException ex)
        {
            Console.WriteLine($"Operation failed: {ex.Message}");
        }
        catch (Exception ex)  // catch-all — always last
        {
            Console.WriteLine($"Unexpected error: {ex.Message}");
        }
        finally
        {
            Console.WriteLine("Transaction attempt completed");  // always runs
        }
    }
}

// Custom Exception
public class InsufficientFundsException : Exception
{
    public decimal Amount { get; }
    public decimal Balance { get; }

    public InsufficientFundsException(decimal amount, decimal balance)
        : base($"Cannot withdraw ₹{amount}. Available balance: ₹{balance}")
    {
        Amount = amount;
        Balance = balance;
    }
}

// Usage
throw new InsufficientFundsException(5000, 3000);
```

---

---

## SECTION 12 — COLLECTIONS

```csharp
// List<T> — dynamic array
List<string> names = new List<string>();
names.Add("Alice");
names.Add("Bob");
names.Remove("Bob");
names.Contains("Alice");  // true
names.Count;              // 1

// Dictionary<K,V> — key value pairs
Dictionary<int, string> employees = new Dictionary<int, string>();
employees.Add(1, "Alice");
employees[2] = "Bob";
employees.ContainsKey(1);   // true
employees.TryGetValue(1, out string name);

// HashSet<T> — unique values only
HashSet<int> ids = new HashSet<int>();
ids.Add(1);
ids.Add(1);  // duplicate ignored
ids.Count;   // 1

// Queue<T> — FIFO
Queue<string> queue = new Queue<string>();
queue.Enqueue("First");
queue.Enqueue("Second");
queue.Dequeue();  // returns "First"

// Stack<T> — LIFO
Stack<string> stack = new Stack<string>();
stack.Push("First");
stack.Push("Second");
stack.Pop();  // returns "Second"
```

---

---

## SECTION 13 — LINQ

```csharp
List<Employee> employees = new List<Employee>
{
    new Employee { Name = "Alice", Salary = 90000, Dept = "Engineering" },
    new Employee { Name = "Bob",   Salary = 75000, Dept = "Marketing" },
    new Employee { Name = "Frank", Salary = 95000, Dept = "Engineering" },
    new Employee { Name = "Eve",   Salary = 70000, Dept = "Marketing" },
};

// Filter
var highEarners = employees.Where(e => e.Salary > 80000).ToList();

// Select (projection)
var names = employees.Select(e => e.Name).ToList();

// Order
var sorted = employees.OrderByDescending(e => e.Salary).ToList();

// Group By
var byDept = employees.GroupBy(e => e.Dept);
foreach (var group in byDept)
{
    Console.WriteLine($"{group.Key}: {group.Count()} employees");
}

// Aggregate
var avgSalary = employees.Average(e => e.Salary);
var maxSalary = employees.Max(e => e.Salary);
var totalSalary = employees.Sum(e => e.Salary);

// First, FirstOrDefault
var first = employees.First(e => e.Dept == "Engineering");
var firstOrNull = employees.FirstOrDefault(e => e.Dept == "HR");  // null if not found

// Any, All
bool anyHighEarner = employees.Any(e => e.Salary > 90000);  // true
bool allHighEarner = employees.All(e => e.Salary > 60000);  // true

// Query syntax (SQL-like)
var result = from e in employees
             where e.Salary > 75000
             orderby e.Salary descending
             select new { e.Name, e.Salary };
```

---

---

## SECTION 14 — SOLID PRINCIPLES

### S — Single Responsibility Principle

Each class should have only ONE reason to change.

```csharp
// ❌ Wrong — one class doing too much
public class Employee
{
    public void CalculateSalary() { }
    public void SaveToDatabase() { }   // DB concern
    public void GenerateReport() { }   // Report concern
}

// ✅ Correct — separate responsibilities
public class Employee
{
    public void CalculateSalary() { }
}
public class EmployeeRepository
{
    public void Save(Employee e) { }
}
public class EmployeeReport
{
    public void Generate(Employee e) { }
}
```

### O — Open/Closed Principle

Open for extension, closed for modification.

```csharp
// ❌ Wrong — must modify class to add new discount type
public class DiscountCalculator
{
    public decimal Calculate(string type, decimal price)
    {
        if (type == "Student") return price * 0.9m;
        if (type == "Senior") return price * 0.8m;
        // adding new type = modifying this class ❌
        return price;
    }
}

// ✅ Correct — extend without modifying
public abstract class Discount
{
    public abstract decimal Calculate(decimal price);
}
public class StudentDiscount : Discount
{
    public override decimal Calculate(decimal price) => price * 0.9m;
}
public class SeniorDiscount : Discount
{
    public override decimal Calculate(decimal price) => price * 0.8m;
}
public class NewUserDiscount : Discount  // new type = new class ✅
{
    public override decimal Calculate(decimal price) => price * 0.85m;
}
```

### L — Liskov Substitution Principle

Child class should be substitutable for parent class without breaking behaviour.

```csharp
// ❌ Wrong — Square breaks Rectangle behaviour
public class Rectangle
{
    public virtual int Width { get; set; }
    public virtual int Height { get; set; }
    public int Area() => Width * Height;
}
public class Square : Rectangle
{
    public override int Width
    {
        set { base.Width = base.Height = value; }
    }
    // Rectangle r = new Square(); r.Width = 5; r.Height = 10;
    // Area = 100, expected 50 ❌ — LSP violated
}

// ✅ Correct — common interface
public interface IShape { int Area(); }
public class Rectangle : IShape { public int Area() => Width * Height; }
public class Square : IShape { public int Area() => Side * Side; }
```

### I — Interface Segregation Principle

Don't force classes to implement methods they don't need.

```csharp
// ❌ Wrong — fat interface
public interface IWorker
{
    void Work();
    void Eat();
    void Sleep();
}
// Robot implements IWorker but doesn't eat or sleep ❌

// ✅ Correct — split interfaces
public interface IWorkable { void Work(); }
public interface IEatable  { void Eat(); }
public interface ISleepable { void Sleep(); }

public class Human : IWorkable, IEatable, ISleepable { }
public class Robot : IWorkable { }  // only implements what it needs ✅
```

### D — Dependency Inversion Principle

Depend on abstractions, not concrete implementations.

```csharp
// ❌ Wrong — high level module depends on low level
public class OrderService
{
    private SqlDatabase db = new SqlDatabase();  // tightly coupled ❌

    public void SaveOrder(Order order)
    {
        db.Save(order);
    }
}

// ✅ Correct — depend on interface
public interface IDatabase
{
    void Save(Order order);
}
public class SqlDatabase : IDatabase
{
    public void Save(Order order) => Console.WriteLine("Saved to SQL");
}
public class MongoDatabase : IDatabase
{
    public void Save(Order order) => Console.WriteLine("Saved to Mongo");
}
public class OrderService
{
    private readonly IDatabase db;

    public OrderService(IDatabase db)  // injected — loosely coupled ✅
    {
        this.db = db;
    }

    public void SaveOrder(Order order) => db.Save(order);
}

// Usage
IDatabase db = new SqlDatabase();
OrderService service = new OrderService(db);

// Swap database without changing OrderService ✅
IDatabase mongoDB = new MongoDatabase();
OrderService service2 = new OrderService(mongoDB);
```

---

---

## SECTION 15 — DESIGN PATTERNS (Most Asked)

### Singleton — Only one instance of a class

```csharp
public class Singleton
{
    private static Singleton instance;
    private static readonly object lockObj = new object();

    private Singleton() { }  // private constructor

    public static Singleton GetInstance()
    {
        if (instance == null)
        {
            lock (lockObj)  // thread safe
            {
                if (instance == null)
                    instance = new Singleton();
            }
        }
        return instance;
    }

    public void DoWork() => Console.WriteLine("Singleton working");
}

// Usage
Singleton s1 = Singleton.GetInstance();
Singleton s2 = Singleton.GetInstance();
Console.WriteLine(s1 == s2);  // True — same instance
```

### Factory — Create objects without specifying exact class

```csharp
public abstract class Notification { public abstract void Send(string msg); }
public class EmailNotification : Notification
{
    public override void Send(string msg) => Console.WriteLine($"Email: {msg}");
}
public class SMSNotification : Notification
{
    public override void Send(string msg) => Console.WriteLine($"SMS: {msg}");
}

public class NotificationFactory
{
    public static Notification Create(string type)
    {
        return type switch
        {
            "Email" => new EmailNotification(),
            "SMS"   => new SMSNotification(),
            _ => throw new ArgumentException("Unknown type")
        };
    }
}

// Usage
Notification n = NotificationFactory.Create("Email");
n.Send("Hello!");
```

### Repository Pattern — Data access abstraction

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
    private List<Employee> employees = new List<Employee>();

    public Employee GetById(int id) => employees.FirstOrDefault(e => e.Id == id);
    public List<Employee> GetAll() => employees;
    public void Add(Employee emp) => employees.Add(emp);
    public void Delete(int id) => employees.RemoveAll(e => e.Id == id);
}
```

---

---

## SECTION 16 — VALUE TYPE vs REFERENCE TYPE

```csharp
// Value Types — stored on STACK, copied on assignment
int a = 10;
int b = a;  // copy
b = 20;
Console.WriteLine(a);  // 10 — unchanged

// Reference Types — stored on HEAP, reference copied
int[] arr1 = { 1, 2, 3 };
int[] arr2 = arr1;  // same reference!
arr2[0] = 99;
Console.WriteLine(arr1[0]);  // 99 — changed!

// Value types: int, double, float, bool, char, struct, enum
// Reference types: class, array, string, interface, delegate
```

### struct vs class

```csharp
// struct — value type
public struct Point
{
    public int X { get; set; }
    public int Y { get; set; }

    public Point(int x, int y) { X = x; Y = y; }
}

// class — reference type
public class PointClass
{
    public int X { get; set; }
    public int Y { get; set; }
}

// Key differences:
// struct — no inheritance, value type, no null, stack allocated
// class  — inheritance, reference type, can be null, heap allocated
```

---

---

## SECTION 17 — STRING HANDLING

```csharp
// string is immutable — every operation creates new string
string s = "Hello";
s += " World";  // new string created, old one garbage collected

// StringBuilder — mutable, use for multiple concatenations
StringBuilder sb = new StringBuilder();
sb.Append("Hello");
sb.Append(" World");
sb.Insert(5, ",");
sb.Replace("World", "C#");
Console.WriteLine(sb.ToString());  // Hello, C#

// Common string methods
string str = "  Hello World  ";
str.Trim();              // "Hello World"
str.ToUpper();           // "  HELLO WORLD  "
str.ToLower();           // "  hello world  "
str.Contains("World");   // true
str.StartsWith("  H");   // true
str.Replace("World", "C#");
str.Split(' ');          // array of words
str.Substring(2, 5);     // "Hello"
string.IsNullOrEmpty(str);
string.IsNullOrWhiteSpace(str);

// String interpolation
string name = "Alice";
int age = 30;
Console.WriteLine($"Name: {name}, Age: {age}");

// Verbatim string — ignore escape characters
string path = @"C:\Users\Alice\Documents";
```

---

---

## ⚡ Quick Reference — Interview Cheatsheet

### OOP 4 Pillars

|Pillar|Keyword|Example|
|---|---|---|
|Encapsulation|private, get, set|Bank balance hidden|
|Abstraction|abstract, interface|Shape.CalculateArea()|
|Inheritance|: BaseClass|Dog : Animal|
|Polymorphism|virtual, override|MakeSound() differs per animal|

---

### Abstract Class vs Interface

||Abstract Class|Interface|
|---|---|---|
|Instance|❌ Cannot|❌ Cannot|
|Constructor|✅ Yes|❌ No|
|Fields|✅ Yes|❌ No|
|Multiple inherit|❌ No|✅ Yes|
|Method body|✅ Yes|✅ C# 8+|
|Use when|Share code + contract|Pure contract|

---

### virtual vs abstract vs override vs new

|Keyword|Where|Meaning|
|---|---|---|
|virtual|Parent|Can be overridden|
|abstract|Parent|Must be overridden|
|override|Child|Replaces parent method|
|new|Child|Hides parent method (not polymorphic)|
|sealed|Child|Prevent further overriding|
|base|Child|Call parent method/constructor|

---

### Value Type vs Reference Type

||Value Type|Reference Type|
|---|---|---|
|Stored on|Stack|Heap|
|Copy behaviour|Full copy|Reference copy|
|Default value|0/false/etc|null|
|Examples|int, struct, enum|class, array, string|

---

### SOLID in one line

|Principle|One Line|
|---|---|
|S — Single Responsibility|One class, one job|
|O — Open/Closed|Add new code, don't change old|
|L — Liskov Substitution|Child must work where parent works|
|I — Interface Segregation|Don't force unused methods|
|D — Dependency Inversion|Depend on interfaces not classes|

---

### Common Design Patterns

|Pattern|Use case|
|---|---|
|Singleton|Only one instance (Logger, Config)|
|Factory|Create objects based on input|
|Repository|Abstract data access layer|
|Observer|Event-driven (Publisher-Subscriber)|
|Decorator|Add behaviour without changing class|
|Strategy|Swap algorithms at runtime|

---

## 🧠 Top Interview Tips for 7 Years Exp

1. Always explain OOP concepts with **real world examples**
2. Know difference between **abstract class and interface** — most asked
3. Explain **SOLID principles** with code examples
4. Know at least **2-3 design patterns** with use cases
5. Know **when to use struct vs class**
6. Understand **virtual vs override vs new** — tricky question
7. Know **value type vs reference type** — memory behaviour
8. Understand **Generics** — show you write reusable code
9. Know **delegates, events, LINQ** — shows C# depth
10. Always mention **real project usage** — "In my project I used Singleton for..."