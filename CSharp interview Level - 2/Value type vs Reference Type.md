✔Value Types
Stored **on the stack** (usually), copied **by value**.  
Examples: `int`, `bool`, `double`, `struct`, `enum`.

✔ Reference Types
Stored **on the heap**, copied **by reference**.  
Examples: `class`, `interface`, `array`, `string`, `delegate`.

sample question
You pass an `int` and a `List<int>` to a method. Inside the method, both are modified. After the method returns, which one actually changes in the caller? Why?

### **What interviewer expects:**

- `int` → **Value type**, method gets a **copy**, so original does **not** change.
- `List<int>` → **Reference type**, method gets a **reference**, so changes reflect outside.