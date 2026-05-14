**Middleware is a component in ASP.NET Core that handles HTTP requests and responses in the request pipeline.**

It can:

- Process requests before they reach the controller
- Process responses before they go back to the client
- Modify requests/responses
- Stop the request pipeline if needed


When a request comes to an ASP.NET Core API, it goes through a **pipeline of middlewares**.

Client Request
      ↓
Middleware 1 (Authentication)
      ↓
Middleware 2 (Authorization)
      ↓
Middleware 3 (Logging)
      ↓
Controller
      ↓
Response back through middlewares

Example of Stopping Pipeline

public async Task InvokeAsync(HttpContext context)
{
    if (!context.Request.Headers.ContainsKey("x-api-key"))
    {
        context.Response.StatusCode = 401;
        await context.Response.WriteAsync("Unauthorized");
        return;
    }

    await _next(context);
}