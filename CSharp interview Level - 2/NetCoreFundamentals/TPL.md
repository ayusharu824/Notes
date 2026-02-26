Task parallel library (TPL)

What is TPL?
TPL is a framework for running work in parallel & asynchronously using tasks instead of threads. TPL simplifies multithreading by managing thread scheduling and execution efficiently.

Tpl lives in System.Threading.Tasks

With TPL 
Task.Run(() => DoWork());

Benefits:
1. Uses thread pool
2. lightweight
3. built-in exception handling
4. Easy composition

	static void Main()
    {
        // Creating and starting two 
        // tasks to run concurrently
        Task task1 = Task.Run(() => PrintNumbers());
        Task task2 = Task.Run(() => PrintNumbers());

        // Waiting for both tasks to complete 
        // before exiting the program
        Task.WhenAll(task1, task2).Wait();
    }