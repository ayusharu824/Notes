
| Interface            | Abstract Class         |
| -------------------- | ---------------------- |
| Multiple inheritance | Single inheritance     |
| No fields            | Can have fields        |
| No Constructors      | Can have constructors  |
| Best for contracts   | Best for base behavior |
Why choose Interface over abstract class?
Choose interface when:
1. You need multiple inheritance
		class Service: Iloggable, ICacheable, IAuditable
	  ❌ Abstract class → only one allowed  
	  ✔ Interface → many allowed
2. You want loose coupling
   Interfaces define capabilities not identity
	 We can use it for
	 a. Dependency injection
	 b. Clean architecture
	 c. Microservices
	 
3. You want better testability
	Mocks work best with interfaces
	
Why Choose Abstract Class over interface
1. Abstract class represents an IS-A relationship
	 An abstract class models inheritance not just capability
	public abstract class Vehicle
{
    public int Speed { get; protected set; }
    public abstract void Move();
}

public class Car : Vehicle
{
    public override void Move() { }
}

✔ A car **is a** vehicle  
❌ A car does not just “support” vehicle behavior

👉 Use abstract class when **identity matters**.

2. Abstract Class allows **shared state (fields & properties)**
   Interfaces cannot hold instance state.
     public abstract class PaymentBase
{
    protected decimal Amount;

    protected void LogTransaction()
    {
        Console.WriteLine("Logged");
    }

    public abstract void Pay();
}
✔ Shared fields  
✔ Protected helpers  
✔ Common lifecycle logic

3. Abstract Class allows **partial implementation**
	you can:
- Force some behavior
- Provide default behavior for the rest
Example (Template Method Pattern)
public abstract class OrderProcessor
{
    public void Process()
    {
        Validate();
        Save();
        Notify();
    }

    protected abstract void Validate();
    protected virtual void Notify() { }
}

4. Abstract Class provides **protected members**
     Interfaces expose everything publicly.
     Abstract classes can :
     - Hide internal helpers
	- Expose only what subclasses need
	protected void CalculateTax() { }

5. Abstract Class supports **constructors**
    Useful for:
- Mandatory initialization
- Invariants