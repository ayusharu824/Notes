**We register dependencies in `Program.cs` so the DI container knows:**
- **What to create**
- **How to create it**
- **How long to keep it alive**
Without registration, **.NET has no idea how to instantiate your classes**.

Example without DI registration (problem)

public class OrderController
{
    private readonly IPaymentService _payment;

    public OrderController(IPaymentService payment)
    {
        _payment = payment;
    }
}
### ❌ What will happen if not registered?

At runtime:
InvalidOperationException:
Unable to resolve service for type 'IPaymentService'

Because .NET says:
> “You asked me for IPaymentService, but I don’t know what concrete class to create.”