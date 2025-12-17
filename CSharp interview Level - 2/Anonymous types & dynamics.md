Anonymous types let you create object instances without defining a class, mainly for temporary data grouping.

They are:
 - Created using new {}
 - Immutable
 - strongly typed
 - Resolved at compile time
 Eg: 1
var person = new 
{
	 Name = "Ayush",
	 Age = 28
};

- complier creates a hidden class
- properties are read only
Eg:2
using anonymous types in linq
var users = db.Users.Select(u => new {u.Id, u.Name}).ToList();
Eg:3
return Ok(new {success = true, message = "Saved"});
## Dynamic in .Net

what is dynamic?

dynamic tells the compiler -> don't check this at compile time -> decide at runtime

- Type checking happens at runtime
- Uses DLR (Dynamic laguage runtime)

dynamic obj = "Hello";
obj = 10;
obj = new Person();

✔ Allowed  
❌ No compile-time checking
This code runs **without compile-time error and without runtime error**.