Caching is storing frequently used data in a faster storage layer to reduce latency and load on the main system

Client → API → Database (slow)

Client → API → Cache → (if miss) → Database

👉 Benefits:
- Faster response ⚡
- Reduced DB load
- Better scalability

# ⚙️ 2. Types of Caching

---
## 🔹 1. In-Memory Cache (Local)

- Stored inside application memory
Example:
- ASP.NET Core `IMemoryCache`
👉 Pros:
- Very fast  
    👉 Cons:
- Not shared across servers (problem in scaling)

---
## 🔹 2. Distributed Cache

Stored in external system like:
- Redis
👉 Pros:
- Shared across multiple servers
- Works with horizontal scaling
👉 Cons:
- Slight network latency

Disadvantages of caching
1. suppose the size of caching is that it can store upto 3 data so what it will do it will remove the LRU and LFU (least recently and least frequently used) data from cache

so suppose this is the data

1,2,3,4,1,2

1 is not in cache so it will add
[1,2,3] after that for 4 it has to remove LRU which is 1 [2,3,4]
now 1 is coming again but it has to be added in cache as it is not there anymore
same for 2

so this is one disadvantange

2. Second disandvantage can be data not in cache or it is not in sync with db (real data) which means data in db is new one but in cache it is still using old out dated data
