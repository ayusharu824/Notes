Why SAGAs?
In a distributed system, ensuring consistency across multiple services without locking resources is a non-trivial problem. The SAGA pattern addresses this by breaking a transaction into a series of local steps, each with its compensating action to undo changes if something goes wrong. Unlike choreography (where services react independently to events), the orchestration flavor of SAGAs uses a central coordinator — our SAGA orchestrator — to manage the workflow explicitly.

For this example, we’ll implement a simple e-commerce workflow:

1. **Order Service**: Creates an order.
2. **Billing Service**: Generates an invoice.
3. **Notification Service**: This sends a confirmation email.
4. **Compensation**: Rolls back changes if any step fails.

## Types of Saga Patterns

There are two primary approaches to implementing the Saga Pattern: **Choreography-Based Saga** and **Orchestration-Based Saga**.

## 1. Choreography-Based Saga (Event-Driven)

In a choreography-based saga, each microservice listens for events from other services and performs its local transaction accordingly. This approach operates without a central coordinator, making it highly scalable.

### How It Works:

- Services communicate via an event broker like **Apache Kafka, Azure Service Bus, RabbitMQ, or AWS SNS/SQS**.
- Each service listens for relevant domain events and executes its operations.
- If a failure occurs, services publish compensating events to roll back changes.

### ✔ Advantages:

- **Reduces coupling** between microservices.
- **Scales efficiently** since there is no central bottleneck.
- **Best for simple workflows** that do not require strict ordering.

### ❌ Disadvantages:

- **Difficult to manage** as workflows become complex.
- **Hard to debug and trace failures** due to distributed event propagation.

## 2. Orchestration-Based Saga (Centralized Controller)

In this approach, a **Saga Orchestrator** (a dedicated service) coordinates the workflow by invoking microservices in a predefined sequence.

### How It Works:

- The orchestrator triggers each service in order and listens for success or failure responses.
- If an operation fails, it triggers compensating transactions to revert previous changes.
- The orchestrator maintains a log of each transaction step.

### ✔ Advantages:

- **Simplifies complex workflows** by providing a structured execution path.
- **Easier monitoring and debugging** since failures are centrally tracked.
- **Better control over transaction execution** with explicit retry mechanisms.

### ❌ Disadvantages:

- **Introduces a single point of failure** if the orchestrator is not highly available.
- **Increases coupling** between services compared to the choreography model.


## Choosing the Right Saga Pattern

The choice between **Choreography** and **Orchestration** depends on the use case:

- **Use Choreography** for **simple event-driven workflows** where services can operate independently.
- **Use Orchestration** when **transactional consistency and error handling** are critical.

|Choreography|Orchestration|
|---|---|
|No central service|Central Saga orchestrator|
|Event-driven|Command-driven|
|Loose coupling|More control|
|Harder to debug|Easier to trace|
|Scales well|Easier rollback logic|

# 🎭 Choreography-Based Saga (Core Idea)

> **There is NO central controller.**  
> Each service listens to events and decides what to do next.

Think of it like a **group dance**:
- No leader
- Everyone follows agreed rules
- Events drive the flow

# 🧩 Business Example (We’ll Code This)

### **Order Processing System**

Microservices:
1. **Order Service**
2. **Payment Service**
3. **Inventory Service**
4. **Shipping Service**

Broker:
- **Azure Service Bus**

OrderCreated
   ↓
PaymentCompleted
   ↓
InventoryReserved
   ↓
ShippingStarted
   ↓
OrderCompleted
Each arrow = **event published to Service Bus**

# 💥 Failure Path (Compensation)

Example: Inventory fails

`InventoryFailed  
   ↓
PaymentCancelled    
  ↓ 
OrderCancelled`

# 🧱 Azure Service Bus Setup (Conceptual)

We’ll use:

- **Topic**: `order-events`
- **Subscriptions**:
    - OrderService-sub
    - PaymentService-sub
    - InventoryService-sub
    - ShippingService-sub
        

Why Topic?  
➡ Multiple services need same events.

Order Service(Saga Starter)

Publishes: `OrderCreated`
public class OrderService
{
    private readonly ServiceBusSender _sender;
    public OrderService(ServiceBusClient client)
    {
        _sender = client.CreateSender("order-events");
    }
    public async Task CreateOrder(Guid orderId, decimal amount)
    {
        // Save order as Pending in DB
        var evt = new OrderCreated(orderId, amount);
        await _sender.SendMessageAsync(
            new ServiceBusMessage(JsonSerializer.Serialize(evt))
            {
                Subject = nameof(OrderCreated)
            });
    }
}

### 🔍 Explanation
- `ServiceBusClient` = connection to Azure Service Bus
- `ServiceBusSender` = can publish messages to a **topic**
- Topic name = `order-events`

Payment Service
publishes: PaymentCompleted or payment failed
public class PaymentConsumer
{
    public async Task Handle(ProcessMessageEventArgs args)
    {
        if (args.Message.Subject == nameof(OrderCreated))
        {
            var evt = JsonSerializer.Deserialize<OrderCreated>(
                args.Message.Body);

            bool success = true; // simulate (in real life call payment gateway)

            if (success)
                await Publish(new PaymentCompleted(evt.OrderId));
            else
                await Publish(new PaymentFailed(evt.OrderId, "Card declined"));
        }
    }
}

Saga officially starts here -> [if (args.Message.Subject == "OrderCreated")]

Inventory Service
Listens: `PaymentCompleted`
Publishes: `InventoryReserved` OR `InventoryFailed`

if (args.Message.Subject == nameof(PaymentCompleted))
{
    bool stockAvailable = false; // simulate failure [check inventory DB]

    if (stockAvailable)
        await Publish(new InventoryReserved(orderId));
    else
        await Publish(new InventoryFailed(orderId));
}

# 🔹 Shipping Service

### Listens: `InventoryReserved`

### Publishes: `ShippingStarted`

if (args.Message.Subject == nameof(InventoryReserved))
{
    // create shipment
    await Publish(new ShippingStarted(orderId));
}

Order Service (Final State)
### Listens:

- `ShippingStarted`
- `PaymentFailed`
- `InventoryFailed`

if (subject == nameof(ShippingStarted))
{
    // Mark order as Completed
}

if (subject == nameof(InventoryFailed) ||
    subject == nameof(PaymentFailed))
{
    // Mark order as Cancelled
}


Doing Same buisness login in SAGA Orchestrator 

# First: What is Saga Orchestration?

> **Saga Orchestration means there IS a central coordinator (orchestrator) that controls the workflow.**
> Instead of services reacting blindly to events:

- One **Orchestrator** tells services **what to do**
    
- Orchestrator decides **next step**

# 🧩 SAME BUSINESS EXAMPLE
### Order Processing Saga
Steps:
1. Create Order
2. Take Payment
3. Reserve Inventory
4. Start Shipping
    
Failures:
- Payment fails → cancel order
- Inventory fails → refund payment
- Shipping fails → release inventory + refund payment

# 🏗️ Technology Choice
We’ll use:
- **Azure Durable Functions**  
    Because:
- Built-in orchestration
- State persisted automatically
- Retry, replay, durability handled

OrderOrchestrator
   ↓
Call Payment Service
   ↓
Call Inventory Service
   ↓
Call Shipping Service
   ↓
Complete Order

❌ If any step fails:
   ↩ Compensate previous steps

# 1️⃣ Orchestrator Function (THE BRAIN)
This function **controls the saga**.

[FunctionName("OrderSagaOrchestrator")]
public async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var order = context.GetInput<OrderRequest>();

    try
    {
        await context.CallActivityAsync("CreateOrder", order);

        await context.CallActivityAsync("ProcessPayment", order);

        await context.CallActivityAsync("ReserveInventory", order);

        await context.CallActivityAsync("StartShipping", order);

        await context.CallActivityAsync("CompleteOrder", order);
    }
    catch (Exception)
    {
        // Compensation logic
        await context.CallActivityAsync("CancelOrder", order);
        throw;
    }
}


Compensation Activities (VERY IMPORTANT)

## Cancel Order

`[FunctionName("CancelOrder")] public async Task CancelOrder([ActivityTrigger] OrderRequest order) {     // Mark order as Cancelled }`

---

## 🔹 Refund Payment

`[FunctionName("RefundPayment")] public async Task RefundPayment([ActivityTrigger] OrderRequest order) {     // Refund payment }`

---

## 🔹 Release Inventory

`[FunctionName("ReleaseInventory")] public async Task ReleaseInventory([ActivityTrigger] OrderRequest order) {     // Put stock back }`