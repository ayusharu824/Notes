DI is a technique where an object receives it's dependencies from outside instead of creating them itself

Don’t create objects inside a class; inject them from outside.

Solution with Dependency injection
public class OrderService
{
    private readonly IEmailService _email;
    public OrderService(IEmailService email)
    {
        _email = email;
    }
}

## Service Lifetimes (VERY IMPORTANT)
### 🔹 Transient
- New instance every time
Use when:
- Lightweight
- Stateless
Real example for transient registered services
EmailSender
SmsSender
PdfGenerator
PasswordHasher
ValidationService
Mapper

Why?
- Each operation is independent
- No shared state needed
- Cheap to create

### 🔹 Scoped
- One instance per request
`AddScoped<IService, Service>();`
Use when:
- DbContext
- Request-based logic
- Repository classes
### 🔹 Singleton
- One instance for entire app
`AddSingleton<IService, Service>();`
Use when:
- Caching 
- Configuration
- Stateless services
Configuration
Logger
Cache (MemoryCache)
HttpClient (via HttpClientFactory)
FeatureFlags
Settings
