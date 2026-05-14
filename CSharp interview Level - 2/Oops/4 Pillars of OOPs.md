4 pillars of Oops are
1. Encapsulation
2. polymorphism
3. inheritance
4. Abstraction

Encapsulation: (Data protection)
Encapsulation means keeping data safe by controlling how it is accessed or modified.
why we need it.
1. Prevent invalid data
2. Hide internal details
3. control changes
public class BankAccount
{
    private decimal _balance;
    public decimal Balance
    {
        get { return _balance; }
        private set { _balance = value; }
    }

    public void Deposit(decimal amount)
    {
        if (amount <= 0)
            throw new Exception("Invalid amount");

        Balance += amount;
    }
}
✔ Data is protected  
✔ Rules are enforced

Encapsulation is achieved using access modifiers and properties to protect internal state.

**Abstraction**
Abstraction means exposing only what the object does, not how it does it.

Using interface
public interface IPayment
{
	void Pay();
}

public class UpiPayment : IPayment
{
    public void Pay()
    {
        // UPI logic
    }
}

Using Abstract class
public abstract class Vehicle
{
    public abstract void Start();
}

Abstraction focuses on ‘what’ an object does, not ‘how’ it does it.

**Inheritance(Code Reuse)**
Inheritance allows a class to reuse and extend another class

public class Employee
{
    public string Name { get; set; }

    public virtual decimal CalculateSalary()
    {
        return 30000;
    }
 }

public class Manager : Employee
{
    public override decimal CalculateSalary()
    {
        return 60000;
    }
}

Inheritance enables code reuse through an ‘is-a’ relationship.

**Polymorphism**
Polymorphism allows the same method call to behave differently based on the object.

Runtime polymorphism
Employee emp = new Manager();
Console.WriteLine(emp.CalculateSalary());

Method overriding

Compile-time polymorphism
public int Add(int a, int b) { }
public int Add(int a, int b, int c) { }

Method overloading
