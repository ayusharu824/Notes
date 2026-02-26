there are three types of loading
1.Eager loading
2. Lazy loading
3. Explicit loading

A. Eager Loading (Most used)
Related data is loaded at the same time as the main entity in the single query.
class order
{
	public int Id { get;set;}
	// Navigation: One Order → Many OrderItems 
	public ICollection<OrderItem> Items{get;set;} 
}

class OrderItem{
public int Id{get;set;}
// Foreign Key
    public int OrderId { get; set; }

    // Navigation back to Order
    public Order Order { get; set; }
}

Foreign Key + Navigation Property = Complete EF relationship

var orders = _context.Orders.Include(o => o.Items).ToList();
var orders = _context.Orders.Include(o=>o.Items).ThenInclude(i=>i.Product).ToList();

## Pros
✔️ Predictable  
✔️ No surprise DB calls  
✔️ Best for APIs

## Cons
❌ Can fetch unnecessary data  
❌ Heavy queries if overused

B. Lazy Loading
what is lazy loading?
Related data is loaded automatically when it is accessed for the first time.

var orders = _context.Orders.ToList();

foreach (var order in orders)
{
    Console.WriteLine(order.Items.Count);
}

1 query for Orders
N queries for OrderItems

1. ## AsNoTracking()
AsNoTracking() tells EF not to track the 