Async await is not multithreading.
- It is syntax sugar
- Execution is non-blocking.

Before Async Problem:
- Threads get blocked while waiting for:
	- DB calls
	- Http calls
	- File I/O

	in web apps -> blocked threads = poor scalability
	in UI apps -> blocked UI = frozen screen 

Goal of Async/Await:
	- Don't block threads while waiting

What is async ?
- Async is a modifier that
	- That allows the use of await
	- changes how the method is compiled
	- Makes the method asynchronous- friendly.

What is await?
- await means:
	- Start this operation, pause this method, free the thread and resume later when the result is ready.

Example:

	await Task.Delay(1000);
		- Thread is released
		- Method is paused
		- When delay finishes -> method continues from the same line
	
Async is not equal to multithreading

async = non-blocking
Async does not mean:
	- New Thread
	- Parallel execution

Async means:
- Thread is freed.
- Work continues later.

Simple example:

	public async Task ExampleAsync()
	{
	    Console.WriteLine("1");
	    await Task.Delay(2000);
	    Console.WriteLine("2");
	}

### What actually happens:
1. Prints `1`
2. `Task.Delay` starts
3. Method **returns control**
4. Thread is freed
5. After 2 seconds → method resumes
6. Prints `2`

Why return Task/Task<T> ?
Because async methods:
	-Don't finish immediately.
	-Represent future completion.

Rules:
async Task        → no return value
async Task<T>     → returns T
async void        → ❌ avoid (except events)

async void SaveData() { } wrong 
async Task SaveDataAsync() {} correct

Real ASP.NET Core Example (VERY IMPORTANT)
[HttpGet]
public async Task<IActionResult> GetUsers()
{
    var users = await _context.Users.ToListAsync();
    return Ok(users);
}

Why async here?
- DB call is I/O bound
- Thread is freed while DB is working
- Server handles **more requests concurrently**

### Interview One-Liners (MEMORIZE)
- `async` allows awaiting asynchronous operations
- `await` frees the thread while waiting
- Async improves scalability, not speed
- Use async for I/O-bound work
- Never block async code using `.Result` or `.Wait()`

## Async DeadLocks (Most Important)
What is an async Deadlock?
A deadlock happens when:
	- A thread  is waiting for an async task to finish.
	- But the async task is trying to resume on the same thread.
	- Result -> both wait forever.

Classic Deadlock Code (Interview Favourite)

public string GetData()
{
    return GetDataAsync().Result;
}

public async Task<string> GetDataAsync()
{
    await Task.Delay(1000);
    return "Data";
}

### Why deadlock happens (step-by-step):

1. `GetData()` blocks the thread using `.Result`
2. `GetDataAsync()` awaits `Task.Delay`
3. After delay, it wants to resume on **original thread**
4. But that thread is blocked by `.Result`
5. 💥 Deadlock

Correct Fix:
public async Task<string> GetData()
{
    return await GetDataAsync();
}

What is ConfigureAwait? 
await SomeAsyncMethod().ConfigureAwait(false);

ConfigureAwait(false) means -> Resume on any thread I dont care about original context.


Question 1 - Execution Order

async Task TestAsync()
{
    Console.WriteLine("1");
    await Task.Delay(1000);
    Console.WriteLine("2");
}

Console.WriteLine("A");
TestAsync();
Console.WriteLine("B");

Quest 2: (same but await TestAsync(); is added in caller instead of TestAsync())
async Task TestAsync()
{
    Console.WriteLine("1");
    await Task.Delay(1000);
    Console.WriteLine("2");
}

Console.WriteLine("A");
await TestAsync();
Console.WriteLine("B");

What will be the output
A 1 2 B

- Caller **pauses (asynchronously)**
- Caller does NOT continue yet
- Caller resumes only after `TestAsync` completes
- Then prints `B`
- 
## Critical clarification (THIS answers your question)
> **The thread is freed in BOTH cases at `await Task.Delay(1000)`**  
> The difference is **whether the caller method pauses or not**.
- `await TestAsync()` → caller pauses
- `TestAsync()` → caller does not pause

In **neither case** is the thread blocked.


Question 2 — Missing await (VERY COMMON BUG):

	async Task SaveAsync()
	{
	    await Task.Delay(1000);
	    Console.WriteLine("Saved");
	}
	SaveAsync();
	Console.WriteLine("Done");

o/p Done Saved

Question 3 — async void (INTERVIEW TRAP)

async void Test()
{
    throw new Exception("Boom");
}

Test();
Console.WriteLine("End");

### ✅ Answer:
- Application **crashes**
- Exception is **not catchable**

Question 4: Parallel vs Sequential
await Task.Delay(1000);
await Task.Delay(1000);
Time: 2Seconds

await Task.WhenAll(
    Task.Delay(1000),
    Task.Delay(1000)
);
Time 1second
Question 5:
public IActionResult Get()
{
    var data = GetDataAsync().Result;
    return Ok(data);
}
possible deadlock because of Result

- Possible deadlock
- Blocks request thread
- Breaks scalability
fix:
public async Task<IActionResult> Get()
{
    var data = await GetDataAsync();
    return Ok(data);
}

FINAL INTERVIEW CHEAT SHEET
Deadlock → .Result / .Wait()
Middleware → always async
ConfigureAwait(false) → library code
async void → only events
await → non-blocking
async ≠ parallel
