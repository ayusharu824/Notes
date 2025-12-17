Async != Multithreading


| Async        | Multithreading        |
| ------------ | --------------------- |
| Non-Blocking | Parallel execution    |
| I/O - Bound  | CPU bound             |
| Frees thread | Uses multiple threads |
| Scalability  | Performances          |

CPU-BOUND work
1. Complex loops
2. Encryption  / hashing
3. Image processing
4. PDF generation
5. Data compression
6. Sorting millions of records

I/O - bound work
Work where CPU is mostly waiting
Examples:
1. Database calls
2. Http API calls
3. File read/write
4. calling another microservices
5. Redis, Cosmos DB, S3, Blob storage.

public async Task<string> GetDataAsync()
{
    return await httpClient.GetStringAsync(url);
}

Why async/await is bad for cpu-bound work
public async Task CalculateAsync()
{
    for(int i=0;i is less than 100000;i++){
    }
}
### Problem:
- CPU is busy anyway
- `async` does NOT free CPU
- No I/O to wait on
👉 `async` gives **NO benefit** here

====

What is Thread?
A thread is the smallest unit of execution within a process.

1. A process can have multiple threads
2. Thread share:
	-Memory
	-Heap
	-Static data

Why multithreading exists?
Multithreading is used to:
1. Utilize multiple CPU cores
2. Run CPU-heavy work in parallel
3. Improve throughput

Example:
	-Image Processing
	-Encryption
	-Data transformation
	- Financial calculations