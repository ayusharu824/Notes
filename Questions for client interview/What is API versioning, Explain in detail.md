
API versioning means maintaining multiple versions of the same API so that
-  Existing clients keep working
-  New clients can use improved or breaking changes.

[ApiVersion("1.0")]
[Route("api/v{version:apiVersion}/users")]
public class UsersController : ControllerBase
{
}
