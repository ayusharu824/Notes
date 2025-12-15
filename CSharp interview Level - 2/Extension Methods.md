Extension methods allow you to add new methods to an existing type without modifying the original type or using the inheritance

They are:
 - Static methods
 - Defined in static classes
 - Called like instance methods

Basic Syntax

public static class StringExtensions{
	public static bool IsValidEmail(this string email){
		return email.Contains("@");	
	}
	}
	
string mail = "abc@test.com";
bool result = mail.IsValidEmail();

in static method we extend the class which we pass inside the parameter using this keyword
like in above example we are extending string class
# Can Extension Methods be Overridden?
❌ NO

Because:
- They are static
- Resolved at compile time

When we write:
mail.IsValidEmail();

Compiler converts it to:
StringExtensions.IsValidEmail(mail);

# Interview One-Line Answer

> Extension methods let us add new functionality to existing types without modifying them. They are static methods resolved at compile time and form the foundation of LINQ and fluent APIs in .NET.