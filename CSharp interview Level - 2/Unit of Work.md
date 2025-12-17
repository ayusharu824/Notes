Unit of Work is a design  pattern that ensures multiple database operations are treated as one single transaction.
Either all changes succeed or none of them are saved.

Problem:
1. Multiple repositories.
2. Each repository saves independently.
3. Partial updates -> inconsistent DB.

repo1.Add(entity1);
repo2.Add(entity2);
_context.SaveChanges(); // Who controls transaction?

public interface IUnitOfWork : IDisposable
{
    IUserRepository Users { get; }
    IOrderRepository Orders { get; }
    int Save();
}

public class UnitOfWork : IUnitOfWork
{
    private readonly AppDbContext  _context;

    public IUserRepository Users { get; }
    public IOrderRepository Orders { get; }

    public UnitOfWork(AppDbContext context)
    {
        _context = context;
        Users = new UserRepository(_context);
        Orders = new OrderRepository(_context);
    }

    public int Save()
    {
        return _context.SaveChanges();
    }

    public void Dispose()
    {
        _context.Dispose();
    }
}

_unitOfWork.Users.Add(user);
_unitOfWork.Orders.Add(order);
_unitOfWork.Save();

## Important Interview Point

> EF Core `DbContext` itself implements **Unit of Work + Repository**.

Repository (No saveChanges here )
public class UserRepository : IUserRepository
{
    private readonly AppDbContext _context;

    public UserRepository(AppDbContext context)
    {
        _context = context;
    }

    public void Add(User user)
    {
        _context.Users.Add(user); // only tracks changes
    }
}

Unit of work Interface:
public interface IUnitOfWork : IDisposable
{
    IUserRepository Users { get; }
    IOrderRepository Orders { get; }
    int Commit();
}

Unit of Work Implementation

public class UnitOfWork : IUnitOfWork
{
    private readonly AppDbContext _context;

    public IUserRepository Users { get; }
    public IOrderRepository Orders { get; }

    public UnitOfWork(AppDbContext context)
    {
        _context = context;
        Users = new UserRepository(context);
        Orders = new OrderRepository(context);
    }

    public int Commit()
    {
        return _context.SaveChanges(); // 🔥 TRANSACTION HERE
    }

    public void Dispose()
    {
        _context.Dispose();
    }
}

Usage in Service Layer (Correct way)

public class OrderService
{
    private readonly IUnitOfWork _uow;

    public OrderService(IUnitOfWork uow)
    {
        _uow = uow;
    }

    public void PlaceOrder(User user, Order order)
    {
        _uow.Users.Add(user);
        _uow.Orders.Add(order);

        _uow.Commit(); // ✅ single transaction
    }
}


But this is not needed in EF core.
DbContext = Unit of Work (THIS IS THE KEY)

### What DbContext already provides
`DbContext`:
- Tracks entity changes (`Added`, `Modified`, `Deleted`)
- Keeps all changes **in memory**
- Executes them together on `SaveChanges()`
- Wraps `SaveChanges()` in a **transaction**

_context.Users.Add(user);
_context.Orders.Add(order);
_context.SaveChanges();

BEGIN TRANSACTION
INSERT INTO Users ...
INSERT INTO Orders ...
COMMIT

ROLLBACK

So if we are using EF core already then this is not required at all it will be a redundant work.