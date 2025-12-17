Authentication — **WHO are you?**
Examples:
- Username + password
- OTP
- JWT token validation
- OAuth login (Google, Azure AD)
✔ Happens **first**  
✔ Creates a **user identity**

Authorization -> what are you allowed to do?
Authorization verifies permissions.
-> Are you allowed to access this resource?

Request
  ↓
Authentication Middleware
  ↓
User Identity created (ClaimsPrincipal)
  ↓
Authorization Middleware
  ↓
Controller / Action

If authentication fails → **401**  
If authorization fails → **403**

builder.Services.AddAuthentication("Bearer")
    .AddJwtBearer(options =>
    {
        options.TokenValidationParameters = new TokenValidationParameters
        {
            ValidateIssuer = true,
            ValidateAudience = true,
            ValidateLifetime = true
        };
    });


Authorization in ASP.Net
1. Role based authorization
    [Authorize(Roles = "Admin")]
2. Claim-based Authorization(better)
		[Authorize(Policy = "CanApproveOrder")]
		
options.AddPolicy("AdminOnly",
    policy => policy.RequireRole("Admin"));

|Feature|Authentication|Authorization|
|---|---|---|
|Purpose|Identity|Access control|
|Question|Who are you?|What can you do?|
|Order|First|Second|
|Output|User principal|Access decision|
|Failure code|401|403|

# ⚠️ Common Interview Traps

## ❌ Trap 1: “JWT = Authorization”

❌ Wrong

JWT is used for **authentication**.  
Authorization uses **claims inside JWT**.

## ❌ Trap 2: “Authorize attribute does authentication”

❌ Wrong
`[Authorize]`:
- Triggers authentication
- Then checks authorization
Authentication is done by **middleware**, not the attribute.