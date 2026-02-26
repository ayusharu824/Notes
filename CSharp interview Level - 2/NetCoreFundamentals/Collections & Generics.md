Why not arrays?
1. Fixed Size
2. No rich APIs
3. Hard to Modify

Collections solve:
1. Dynamic size
2. Searching
3. Sorting
4. Groupings
5. Performance trade-offs

Categories of Collections
1. Non-Generic Collections (OLD avoid)
2. Generic Collections (Use These)

Non-Generic Collections:
Namespace: System.Collections
Examples:
1. ArrayList
2. HashTable
3. Queue
4. Stack
Problems:
a. Store object
b. Boxing/Unboxing
c. No type safety
d. Runtime errors

ArrayList list = new ArrayList();
list.Add(10);
list.Add("hello"); // allowed 

Generic Collections:
Namespace: System.Collections.Generic

Examples:
1. List<T>
2. Dictionary<Tkey, TValue>
3. Queue<T>
4. Stack<T>
5. HashSet<T>

Benefits:
Type Safe
Faster -> as it does not have to boxing unboxing
Compile type checking

List<int> numbers = new List<int>();
numbers.Add(10);
// numbers.Add("hi"); ❌ compile-time error


IEnumberable Vs ICollection Vs IList (Very Important)

IEnumerable<T>
1. Read only iteration
2. Deferred Execution
3. Lowest abstraction

ICollection<T>
1. Add/Remove
2. Count
ICollection<int> data;

IList<T>
1. index-based
2. Insert/Remove at position
IList<int> data;


## **Generics**
Generics allow us to write reusable, type-safe code without knowing the type safe code without knowing the type beforehand.

public class Box<T>
{
	public T Value{get;set;}
}

calling Box class
Box<int> intBox = new Box<int>();
intBox.Value = 10;

Box<string> stringBox = new Box<string>();
stringBox.Value = "Hello";

Console.WriteLine(stringBox.Value);


Why Generics Matter(Interview answer)
1. Compile time safety
2. No boxing / unboxing
3. better performance
4. Reusable logic

How Generics work internally
They are resolved at compile time

What is Generic Constraint ?
Ans. A generic constraint restricts what type can be used as the generic type parameter.
Constraints tell the compiler what `T` is allowed to be.

Example (problem)
public class Box<T>
{
	public T Create()
	{
		return new T();  // it gives compile time error
	}
}

why new T() is dangerous without a constraint
Let's imaging someone uses your class like this

Box<string> box = new Box<string>();
box.Create();

now as T is string
so it becomes
new string(); //not allowed in c#

How constraints fix this
when you write
public class Box<T> where T : new()
{
    public T Create()
    {
        return new T(); // ✔ now allowed
    }
}

you are telling the compiler
“Hey compiler, I promise that `T` will always have a public parameterless constructor.”

so if we refer this 
public class Box<T> where T : new()
{
    public T Create()
    {
        return new T(); // ✔ now allowed
    }
}

Case 1: Valid usage (NO error)
public class MyClass
{
    public MyClass() { }
}

var box = new Box<MyClass>();
var obj = box.Create(); // ✔ Works perfectly -> because MyClass constructor is parameter less therefore constraint is satisfied


Case 2: Invalid usage (COMPILE-TIME error, not runtime)
public class NoDefaultCtor
{
    public NoDefaultCtor(int x) { }
}

var box = new Box<NoDefaultCtor>(); // ❌ Compile-time error
`NoDefaultCtor` must have a public parameterless constructor

Case 3:Interface or abstract class (COMPILE-TIME error)
interface IService { }
var box = new Box<IService>(); // ❌ Compile-time error


All constraints in generics

1. where T:class
Reference type constraint
public class Repository<T> where T : class
{
}
### Meaning

- `T` must be a **reference type**
- Allows `null`
- Disallows value types

### Used in
- EF Core repositories
- Domain entities

2. where T : struct
3. where T : new() [Parameterless constructor constraintd]
4. where T : notnull

Example 1: Repository (very common)
public class Repository<T> where T : class
{
    public T Entity { get; set; }

    public void Add(T entity)
    {
        if (entity == null)
            throw new ArgumentNullException(nameof(entity));
    }
}

Repository<string> repo1 = new Repository<string>(); // ✔
Repository<User> repo2 = new Repository<User>();     // ✔

Repository<int> repo = new Repository<int>(); // ❌ int is value type

Cache / Dictionary Key (perfect use case)
public class Cache<T> where T : notnull
{
    private Dictionary<T, string> _cache = new();

    public void Add(T key, string value)
    {
        _cache[key] = value;
    }
}

Cache<int> cache1 = new Cache<int>();        // ✔ value type
Cache<string> cache2 = new Cache<string>();  // ✔ reference type

Cache<string?> cache = new Cache<string?>(); // ❌ nullable reference
