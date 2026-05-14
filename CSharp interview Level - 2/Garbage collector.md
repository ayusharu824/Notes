What is garbage collection ?
Ans. Garbage collector in .Net automatically
1. Frees unused memory.
2. Destroys object that are no longer referenced.
3. Prevents memory leaks.

Q. How does GC know when to clean the objects?
Ans. When the object goes out of scope GC reclaims the memory and gives it to operating system

static void Method1(c)
{
	SomeClass x = new SomeClass();
	
} -> When we come out of scope means out from this bracket then x is removed from the stack but the memory is still there in heap.

So GC runs and clean up the memory which not referenced any more.

![[GC_basic_memory_flow.excalidraw]]

Q. Does garbage collector clean primitive types?.
Ans. primitive types -> int, double, string...
No it does not clean primitive types.
for(int i = 0;_;_){
int x=10;
int y=20;
}
GC does not clean primitive types. They are allocated on stack & stack removes them as soon as the variable goes out of scope.

Q. Managed vs UnManaged code/ object/resources.
Ans. Managed resources are those which are pure .net objects and these objects are controlled by .Net CLR.

below one is managed code
void Method1(){
	object t = 'Hello';
	SomeClass x= new SomeClass();
	x.SomeData = DateTime.Now.ToString();
}

below one is unmanaged code
FileHandle, COM objects, Database connection objects.

