1. Private
		Accessible only inside the same class
		    class User
			{
				private int age;
			}
			Use When:
			Internal Logic
			Encapsulation
2. Protected
		Accessible in the same class 
		 In derived (child) classes
		 class Person
		 {
			 protected int Id;
		 }
3. Internal
		internal class Logger{}
		 Accessible within the same assembly(project)
		Use When:
		 You want to hide from other projects
		 Library internal logic.
4. Public
	 Accessible from anywhere
	 Use when:
	- Part of public API
	- Used across projects
5. Protected Internal
		Accessible if either:
		 Same assembly OR
		 Derived class in another assembly
		 protected internal int Count;
6. Private Protected
	 Accessible only:
	 In same class
	 In derived class
	 private protected int Score;

Common interview questions:
1. Can a class be private?
    Ans. No Class can be public or internal only
2. Default access modifier?
	Ans. Class -> internal
		ClassMember -> private
3. Can interface methods be private?
	Ans. only helper methods in C# 8+
		Contract methods are always public.
		
Interfaces can have methods with implementation(body) starting from C#8.0 -> these are called default interface methods.
Before C# 8.0
public interface ILogger
{
    void Log();   // no body allowed
}
From C# 8+ (new rule)
public interface ILogger
{
    void Log(string message);

    void LogWithTime()
    {
        Console.WriteLine($"{DateTime.Now}: log");
    }
}
