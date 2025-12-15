
What is Delegate?
A delegate is a type-safe function pointer.
It holds a reference to a method and allows you to call that method indirectly.

Why delegates exist?
- To pass methods as parameters
- To implement callbacks
- To support events
- To enable LINQ

public delegate int MathOperation(int a, int b);

int Add(int x, int y) => x + y;
int Multiply(int x, int y) => x * y;

MathOperation op = Add;
op(2,4);
MathOperation op = Multiply(2,5);
op(2,5)
### Key Points (interview):
- Delegate defines **method signature**
- Methods with same signature can be assigned
- Supports **multicast** (invoke multiple methods)

Action<T> -> delegate with No return value
What is Action
A built-in generic delegate that
- Takes 0 to 16 parameters
- Returns void

Action<T1, T2, T3, T4, .............> -> void (returns void)

Action<string> print = msg => Console.WriteLine(msg);
print("Hello");

Action<int, int> logSum = (a,b) => {
Console.Writeline(a+b);
}

Use Action when:
- You don't need to return anything
- Logging, notifications, side effects

Func<T> - delegate with return value
what is Func?
A built-in generic delegate that:
	- Takes  0 to 16 parameters
	- Last parameter is return type
Signature
Func<T1, T2, TResult>

Example::
Func<int, int, int> add = (a,b) => {
a+b;
}
Eg. with Zero parameters

Func<DateTime> now () => DateTime.Now;

Use Func when:
	- You need a return value
	- LINQ expressions
	- Calculations

Predicate<T> - special case of Func

What is Predicate?
A delegate that always
	- Takes one parameter
	- Returns bool
Signature:
Predicate<T> => bool

Example:
Predicate<int> isEven = (x) => {x%2==0;}

List<int> numbers = new List<int> {1,2,3,4};
List<int> even = numbers.FindAll(x=>x%2 == 0);

Cheat Rule
No return  → Action
Return val → Func
Return bool → Predicate
Custom signature → Delegate


Examples of these in .net
1. List<int> numbers = new() { 1, 2, 3, 4 };
var evens = numbers.FindAll(x => x % 2 == 0);

`Find`, `FindAll`, `Exists` all use `Predicate<T>` internally.

2.  public async Task InvokeAsync(
    HttpContext context,
    Func<HttpContext, Task> next,
    Action<string> logger)
	{
	    logger("Request started");
	
	    await next(context);
	
	    logger("Request finished");
	}
 What is used?
- Func → represents the next middleware    
- Action → logging
