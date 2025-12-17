Design patterns solve recurring design problems:
	-Tight coupling
	-Hard to test code
	-Changing requirements
Patterns give maintainability, testability and flexibility.

## Repository Pattern

What problem does repository solve?
Problem without repository:
1. Controller or services directly talk to DbContext.
2. Business logic mixed with data access.
3. Hard to unit test.
4. Hard to switch data source.

// ❌ Bad practice
public class UserController
{
    public IActionResult Get()
    {
        var users = _context.Users.Where(u => u.IsActive).ToList();
        return Ok(users);
    }
}

Problems:
1. Controller depends on EF core.
2. Cannot mock DB easily.
3. Tight Coupling

What is Repository Pattern ?
Repository pattern is a way to keep all database-related code in one place and stop the rest of the application from directly talking to the database.

Implementation of Repository pattern.
Interface
public interface IUserRepository
{
    IEnumerable<User> GetActiveUsers();
    User GetById(int id);
    void Add(User user);
}
Service
public class UserRepository : IUserRepository
{
    private readonly AppDbContext _context;
    public UserRepository(AppDbContext context)
    {
        _context = context;
    }

    public IEnumerable<User> GetActiveUsers()
    {
        return _context.Users.Where(u => u.IsActive).ToList();
    }

    public User GetById(int id)
    {
        return _context.Users.Find(id);
    }

    public void Add(User user)
    {
        _context.Users.Add(user);
    }
}
Usage in Controller
public class UserController
{
    private readonly IUserRepository _repo;

    public UserController(IUserRepository repo)
    {
        _repo = repo;
    }
}

Benefits(Interview must)
1. Loose coupling
2. Better unit testing
3. Centralized data logic.
4. Swap EF/Dapper/API easily

When not to use Repository pattern
1. Simple CRUD apps
2. Too many repositories with no logic.

Use repository when
1. Business rules exist
2. Queries are complex.
3. Testing is important.


what is called repository in repository pattern
Repository Interface + Repository Implementation together are called repository.