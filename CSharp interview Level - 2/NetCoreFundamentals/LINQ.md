
1. Deferred Execution(Critical)
	LINQ queries do not execute immediately

var query = users.Where(u=>u.Age>19);
//no execution yet

Execution happens when
1. ToList()
2. ToArray()
3. First()
4. Count()
5. foreach

var adults = users.Where(u => u.Age > 18);
users.Add(new User { Age = 25 });

foreach(var u in adults) { } // includes new user!


4. Immediate Execution (Materialization)
var list = users.Where(u=>u.IsActive).ToList();


joins in LINQ

inner join
var result = from o in orders
			join c in customers on o.CustomerId equals c.Id
			 select new {o,c};

Two worlds of LINQ (Critical)

World 1: IEnumerable<T> [in-memory LINQ]
1. Runs in CLR
2. Uses delegates
3. Executes in C#

World 2: IQueryable<T> (Remote LINQ)
1. Runs in database
2. Uses Expression Trees
3. Translates to SQL

Why Deferred Execution Exists (Design Reason)

1. Query composition
2. Optimization
3. Filtering before execution
4. Provider translation (SQL)

Immediate Execution
var list = users.Where(u=>u.Active).ToList();


Select vs SelectMany(Deep)
Select -> one to one
users.Select(u=>u.Name);

SelectMany -> one to many -> flatten

orders.SelectMany(o => o.Items); it is equivalent to
foreach (var order in orders)
    foreach (var item in order.Items)

Projection

db.Users.Select(u=>new UserDtoP{u.Id, u.Name})
.ToList();

Coding Questions;
Print all even nos
var numbers = new List<int>{1,2,3,4,5,6}
var evenNos = numbers.Where(n=>n%2 == 0);
        foreach(var e in evenNos)
            Console.WriteLine(e);

Get names with Length > 4
var names = new []{"Ram","Shyam","Mohan","Amit","Rohit"};
var maxNames = names.Where(n => n.Length > 4);

3. Convert list of users to list of names

class User
{
    public int Id { get; set; }
    public string Name { get; set; }
}

var users = new List<User>
{
    new User { Id = 1, Name = "Amit" },
    new User { Id = 2, Name = "Rohit" }
};
Soln
var userNames = users.Select(u=>u.Name);

4. Square each number
var numbers = new[] { 1, 2, 3, 4 };
var squareNumb = numbers.Select(n => n*n);

5.Get names of active users only
class User
{
    public string Name { get; set; }
    public bool IsActive { get; set; }
}

var activeUsers = users.Where(u => u.IsActive).Select(u => u.Name);

6. Group users by city
class User
{
    public string Name { get; set; }
    public string City { get; set; }
}
soln

var users = new List<User>{ new User {Name = "ayush", City="Delhi"},
            new User {Name="kanchi", City="Delhi"},
            new User{Name="mahi", City="banglore"},
            new User{Name="khushi", City="banglore"}
};
var groups = users.GroupBy(u=>u.City);

foreach(var group in groups){
	Console.WriteLine(group.Key)
	 foreach(var user in group)
		 Console.WriteLine(user.Name)	
}

o/p
Delhi
ayush
kanchi
banglore
mahi
khushi

7. Count users per city
soln
var result = users.GroupBy(u => u.City).Select(g => new{
City = g.Key,
Count = g.Count()
})

foreach(var r in result){
    Console.WriteLine(r);
}

8. Flatten orders => items
class Order
{
    public List<string> Items { get; set; }
}

var orders = new List<Order> { new Order { Items = new List<string> { "Laptop", "Mouse" } }, new Order { Items = new List<string> { "Monitor", "Keyboard", "HDMI Cable" } }, new Order { Items = new List<string> { "Desk Lamp" } } };

Single list of all items
var allItems = orders.SelectMany(o => o.Items);

9. Join orders with customers
class Order
{
    public int CustomerId { get; set; }
}
class Customer
{
    public int Id { get; set; }
    public string Name { get; set; }
}
Soln
var result = from o in orders
join c in customers
on o.CustomerId equals c.Id
select new {o, c.Name}

10. Check if any user is admin
var hasAdmin = users.Any(u=>u.IsAdmin);

11. Get top 3 highest paid employees
employees.OrderByDescending(e=>e.Salary).Take(3);
12. Remove duplicates
var unique = numbers.Distinct();