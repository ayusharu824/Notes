### IEnumerable
### ✔ Where it executes
**In memory (C#)** — NOT in the database.
### ✔ When to use
- When data is already loaded in memory
- Collections: List, Array, Dictionary
- Small/medium datasets
### ✔ How it works
- Supports **deferred execution**
- Processes items **one by one**
- Uses **foreach** iteration
-Example
IEnumerable<int> data = list.Where(x => x > 10);

