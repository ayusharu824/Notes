- ref: Pass variable by reference, **must be initialized before** passing.
	void Increase(ref int x)
	{
	    x++;
	}
- out: Used to return multiple values
	Variable need not be initialized
	bool Divide(int a, int b, out int result)
	{
	    result = a / b;
	    return true;
	}
 - in (read only reference) 
	 void Print(in int x)
	{
		// x++;  // ❌ cannot modify
	}

example:
**REF**
public void UpdateValue(ref int number)
{
    number = number + 10; // modifies original variable
}

int x = 5;
UpdateValue(ref x);
Console.WriteLine(x);   // Output: 15

**OUT**
public bool Divide(int a, int b, out int result)
{
    if (b == 0)
    {
        result = 0;      // MUST assign something
        return false;
    }

    result = a / b;      // MUST assign something
    return true;
}

int output;
bool success = Divide(10, 2, out output);
Console.WriteLine(output); // 5

**IN**
public void PrintPoint(in Point p)
{
    // p.X = 10; // ❌ ERROR: cannot modify because it's readonly
    Console.WriteLine($"X = {p.X}, Y = {p.Y}");
}

public struct Point
{
    public int X;
    public int Y;
}

Point pt = new Point { X = 5, Y = 8 };
PrintPoint(in pt);


**Questions**

public void DoSomething(ref int x)
{
    Console.WriteLine("ref version");
}

public void DoSomething(out int x)
{
    x = 10;
    Console.WriteLine("out version");
}

int a = 5;
DoSomething(??);  // what goes inside?

What will happen here? Is it valid? Which method gets called?

- Why can’t you call this method with just `a`?
    
- Which one will compiler pick if both signatures are present?
    
- Is this considered valid overloading?

## **Q1 — Overloading with ref and out**

**Code:**

`public void DoSomething(ref int x) { Console.WriteLine("ref version"); } public void DoSomething(out int x) { x = 10; Console.WriteLine("out version"); }  int a = 5; DoSomething(??);`

**Answer:**

- You cannot call `DoSomething(a)` → compiler error (ambiguous call).
    
- You must write:
    
    - `DoSomething(ref a);`
        
    - `DoSomething(out a);`
        
- Both are valid overloads because `ref` and `out` are part of method signature.
    

**Key Point:** Caller must explicitly choose ref or out.

---

## **Q2 — ref with uninitialized variable**

**Code:**

`int value; Modify(ref value);  void Modify(ref int data) {     data = data + 10; }`

**Answer:**

- Does NOT compile.
    
- Error: use of unassigned local variable.
    
- `ref` requires the variable to be initialized before passing.
    

**Key Point:** `ref` = must be initialized before call.

---

## **Q3 — out but never assigned**

**Code:**

`void Calc(out int x) {     Console.WriteLine("Inside method"); }  int num; Calc(out num);`

**Answer:**

- Compile-time error: out parameter must be assigned before method returns.
    
- Caller doesn't need to initialize, but callee must assign.
    

**Key Point:** `out` = callee must assign before return.

---

## **Q4 — in parameter mutation**

**Code:**

`void Change(in int x) {     int y = x + 5;     Console.WriteLine(y);     // x = x + 1; // not allowed }  int a = 10; Change(a); Console.WriteLine(a);`

**Output:**

`15 10`

**Explanation:**

- `in` makes parameter readonly.
    
- You can read `x`, but you cannot modify it.
    
- `a` stays unchanged.
    

**Key Point:** `in` = readonly reference.

---

## **Q5 — ref with reference types**

**Code:**

`class Person { public string Name; }  void Update(ref Person p) {     p = new Person();     p.Name = "B"; }  Person obj = new Person(); obj.Name = "A";  Update(ref obj); Console.WriteLine(obj.Name);`

**Answer:**

- Output: `B`
    
- `ref` allows reassignment of caller's reference.
    
- `p = new Person()` changes which object `obj` points to.
    

**If we only do `p.Name = "B"` without `p = new Person()` → still prints `B` even without ref**, because both reference the same object.

**Key Point:** `ref` allows changing caller's reference itself.

---

## **Q6 — out with TryParse**

**Code:**

`int num; bool result = int.TryParse("123", out num); Console.WriteLine(num);`

**Output:**

`123`

**Explanation:**

- `num` can be uninitialized because `out` guarantees assignment inside method.
    
- `TryParse` always assigns:
    
    - Success → parsed value
        
    - Failure → 0
        

**Key Point:** `out` ensures variable is definitely assigned.

---

## **Q7 — struct with in, ref, out**

**Struct:**

`struct Point { public int X; public int Y; }`

### 1. `Update(in Point p)` modify `p.X`?

- Not allowed (readonly).
    
- Cannot modify fields.
    
- Caller value stays unchanged.
    

### 2. `Update(ref Point p)` modify `p.X`?

- Allowed.
    
- Changes caller’s struct instance.
    

### 3. `Update(out Point p)` modify `p.X`?

- Allowed only if all fields (or the struct) are assigned before returning.
    
- Cannot read from `p` before assigning.
    

**Key Point:**

- `in` = readonly ref
    
- `ref` = read/write alias
    
- `out` = must fully assign before return
    

---

# 📌 Quick Summary Cheat Sheet (super short)

`ref → must be initialized; can read/write; passes alias to caller variable. out → need not be initialized; method must assign; used for multiple returns. in  → must be initialized; readonly reference; improves performance for large structs.`