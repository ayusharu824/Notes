1. Test Class
	- A normal C# class -> no special attribute needed
	- public class CalculatorTests
		- {
		- }
2.  [Fact] -> A single Test
	1. use [Fact] when:
			-No parameters
			-Fixed scenario
[Fact]
public void Add_TwoNumbers_ReturnsSum()
{
    var calc = new Calculator();
    int result = calc.Add(2, 3);
    Assert.Equal(5, result);
}

3.  `[Theory]` — Data-driven test
		Use [Theory] when:
		- Same test logic
		- Multiple inputs
	- [Theory]
	[InlineData(2, 3, 5)]
	[InlineData(10, 20, 30)]
	public void Add_ReturnsCorrectSum(int a, int b, int expected)
	{
		var calc = new Calculator();
	
		int result = calc.Add(a, b);
	
		Assert.Equal(expected, result);
	}

3. Common Assertions
		Assert.Equal(expected, actual);
		Assert.NotEqual(a, b);
		Assert.True(condition);
		Assert.False(condition);
		Assert.Null(value);
		Assert.NotNull(value);
		Assert.Contains(item, collection);
		Assert.Throws<Exception>(() => method());

	Async Tests in xUnit
	4. [Fact]
public async Task GetDataAsync_ReturnsData()
{
    var service = new DataService();

    var result = await service.GetDataAsync();

    Assert.NotNull(result);
}

	
5. Mocking with xUnit (usually Moq)
    var repoMock = new Mock<IUserRepository>();
repoMock.Setup(r => r.GetById(1)).Returns(user);

var service = new UserService(repoMock.Object);


### **Problem WITHOUT Moq**
public class OrderService
{
    private readonly UserRepository _repo;

    public OrderService(UserRepository repo)
    {
        _repo = repo;
    }

    public User GetUser(int id)
    {
        return _repo.GetById(id);
    }
}

To test `OrderService`:
- You need a **real database**
- Tests become **slow**
- Tests become **fragile**
- Tests are **not unit tests**

So how to solve this problem:

Simple Example (Very Important)

Interface:
public interface IUserRepository
{
    User GetById(int id);
}

Service to test
public class UserService
{
    private readonly IUserRepository _repo;

    public UserService(IUserRepository repo)
    {
        _repo = repo;
    }

    public User GetUser(int id)
    {
        return _repo.GetById(id);
    }
}

Unit Test using Moq

[Fact]
public void GetUser_ReturnsUser()
{
    var mockRepo = new Mock<IUserRepository>();

    mockRepo.Setup(r => r.GetById(1))
            .Returns(new User { Id = 1, Name = "Ayush" });

    var service = new UserService(mockRepo.Object);

    var result = service.GetUser(1);

    Assert.Equal("Ayush", result.Name);
}

✔ No DB  
✔ Fast  
✔ Isolated  
✔ Deterministic

Control what dependency returns.
mock.Setup(x => x.Method()).Returns(value);

Or throw async exception:
mock.Setup(x => x.GetAsync())
    .ThrowsAsync(new Exception());

Simulate exceptions
mock.Setup(x => x.Save())
    .Throws(new Exception("DB error"));

Question 1.
Do we test controller methods also or just service methods?
### Correct industry answer

👉 **We test BOTH, but for DIFFERENT reasons.**
- **Service layer** → Unit Tests (MOST important)
- **Controller layer** → Lightweight tests OR integration tests
//unit test of service layer
public class UserServiceTests{
[Fact]
public async Task GetUserAsync_ReturnsUser_WhenUserExists(){
//Arrange
var mockRepo = new Mock<IUserRepository>();
mockRepo.Setup(r =>r.GetByIdAsync(1)).ReturnsAsync(new User{Id=1, Name = "Ayush"});

var service = new UserService(mockRepo.Object);
var result = await service.GetUserAsync(1);
//assert
Assert.Equal("Ayush", result.Name);
}
}
//unit test for controller

public class UserControllerTests{
[Fact]
public async Task GetUser_ReturnsOk_WithUser()
{
	//Arrange
	var mockService = new Mock<IUserService>();
	 mockService.Setup(s=>s.GetUserAsync(1)).ReturnsAsync(
	 new User {Id=1,Name="Ayush"});
	 var controller = new UserController(mockService.Object);
	// Act
        var result = await controller.GetUser(1);

        // Assert
        var okResult = Assert.IsType<OkObjectResult>(result);
        var user = Assert.IsType<User>(okResult.Value);
        Assert.Equal("Ayush", user.Name);
}
}

public class UserApiTests : IClassFixture<WebApplicationFactory<Program>>
{
    private readonly HttpClient _client;

    public UserApiTests(WebApplicationFactory<Program> factory)
    {
        _client = factory.CreateClient();
    }

    [Fact]
    public async Task GetUser_Returns200()
    {
        var response = await _client.GetAsync("/api/users/1");
        response.EnsureSuccessStatusCode();
    }
}