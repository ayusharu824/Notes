1. S - Single responsibility principle (SRP)

Violation of SRP
public class OrderService
{
    public void CreateOrder() { }
    public void SaveToDatabase() { }
    public void SendEmail() { }
}

Good (SRP followed)
public class OrderService
{
    public void CreateOrder() { }
}
public class OrderRepository
{
    public void Save() { }
}
public class EmailService
{
    public void Send() { }
}

SRP improves maintainability by isolating changes to one responsibility.

2. O - Open/Closed Principle (OCP)
	Definition
		Open for extension closed for modification
Violates OCP
public double CalculateDiscount(string type)
{
	if (type == "Student") return 10;
	if (type == "Senior") return 20;
	return 0;
}
Every new discount  -> modify method (wrong)

Good(follows OCP) [Polymorphism]
public interfaces IDiscount
{
	double Calculate();
}

public class StudentDiscount : IDiscount
{
	public double Calculate() => 10;
}
public class SeniorDiscount: IDiscount
{
	public double Calculate() => 20;
}
public class DiscountService
{
	public double GetDiscount(IDiscount discount)
			=> discount.Calculate();
}

3 L - Liskov substitution principle (LSP)
	Definition(very imp)
		A derived class should be replaceable for its base class without breaking behavior.
Bad Example
	public class Bird
{
    public virtual void Fly() { }
}

public class Ostrich : Bird
{
    public override void Fly()
    {
        throw new Exception("Can't fly");
    }
}

Good
public abstract class Bird 
{
	public abstract void Eat();
}

public interface IFlyingBird
{
    void Fly();
}

public class Sparrow : Bird, IFlyingBird
{
    public override void Eat()
    {
        Console.WriteLine("Sparrow eats seeds");
    }

    public void Fly()
    {
        Console.WriteLine("Sparrow is flying");
    }
}

public class Ostrich : Bird
{
    public override void Eat()
    {
        Console.WriteLine("Ostrich eats fish");
    }
}

Here LSP rule will follow which is
==objects of a base class (or interface) should be replaceable with objects of a derived class without affecting the correctness or expected behavior of the program==.

Bird bird1 = new Sparrow();  // ✔ valid
Bird bird2 = new Penguin(); // ✔ valid


4. Interface Segregation Principle (ISP)
		Definition
			Clients should not be forced to depend on methods they don't use.
	BAD
	public interface IWorker
{
    void Work();
    void Eat();
}

Good
public interface IWork
{
    void Work();
}

public interface IEat
{
    void Eat();
}

5. D - Dependency inversion principle (DIP)
		High-level modules should not depend on low-level modules. Both should depend on abstractions.
	BAD
public class OrderService
{
    private readonly SqlOrderRepository _repo = new SqlOrderRepository();
}

✅ GOOD
public class OrderService
{
    private readonly IOrderRepository _repo;

    public OrderService(IOrderRepository repo)
    {
        _repo = repo;
    }
}

