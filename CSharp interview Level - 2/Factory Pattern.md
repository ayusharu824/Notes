What problem does factory solve?
Problem:
1. Object creation logic scattered.
2. Code depends on concrete classes.
3. Violates open/closed principle.

Real-time scenario: payment system
Problem code (without factory)
if(type == "card"){
payment = new CreditCardPayment();
}
else if(type == "UPI"){
payment = new UpiPayment();
else if(type == "NETBANKING")
    payment = new NetBankingPayment();

Why is this a problem in real project?
1. Too many conditions
2. Later more payment options might come
3. Hard to test

So factory encapsulates object creation logic and returns objects based on input.

Caller does not know:
1. Which class is created
2. How it is created

Factory pattern implementation
Interface
public interface IPaymentService{
void Pay();
}

Implementations:
public class CrediCardPayment : IPaymentService{
public void pay(){
Console.WriteLine("Credit card");
}
public class UpiPayment : IPaymentService
{
    public void Pay() => Console.WriteLine("UPI");
}
}

public class PaymentFactory
{
    public IPaymentService GetPaymentService(string type)
    {
        return type switch
        {
            "CC" => new CreditCardPayment(),
            "UPI" => new UpiPayment(),
            _ => throw new Exception("Invalid type")
        };
    }
}

Usage 
var service = factory.GetPaymentService("UPI");
service.Pay();

How to register Dependencies in Program.cs

// Register payment implementations
builder.Services.AddScoped<CreditCardPayment>();
builder.Services.AddScoped<UpiPayment>();

// Register factory
builder.Services.AddScoped<PaymentFactory>();

❌ This is WRONG:
builder.Services.AddScoped<IPaymentService, CreditCardPayment>();
builder.Services.AddScoped<IPaymentService, UpiPayment>();

Because:
- DI cannot decide **which one to inject**
- You need **runtime decision**
- That’s why **Factory exists**

## When to use Factory?
- Multiple implementations
- Runtime selection
- Strategy-like behavior

# Interview Talking Points (MEMORIZE)
- Factory handles **runtime selection**
- DI handles **lifetime & creation**
- Factory + DI together is a common enterprise pattern
- Avoid `new` keyword in controllers/services


