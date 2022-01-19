# Introduction

Collections are used to store and group some variable number of data items that have some shared significance to the problem being solved and need to be operated upon together in some controlled fashion.

This module introduces you to a variety of collections that are used in several, most common contexts. As well as the `IEnumerable`-Interface, which allows easy iteration over your collection using `foreach`.

# Reusable Collections

The goal of most software written is to provide reusable components, in order to prevent us from needing to reinvent the wheel. This was accomplished in .NET over the years in multiple fashion.

# Non-Generic Collections

Non Generic collections in .NET are old-fashioned collections that provide no Type-Safety. They are included in the `System.Collections`-Namespace.

Look at this example of an ArrayList:

```cs
using System.Collections;

ArrayList list = new ArrayList();
list.Add("Hello");
list.Add(true);
list.Add(-5);
```

You can see, that we can add all kinds of data into this Data Structure. This might not be a problem, if you don't want to limit the kind of information that is stored in this Collection.

But what if you store for example a bunch of numbers and now you want to read them from the Collection?

```cs
int first = (int) list[0];
int second = (int) list[1];
```

Well, you'll have to cast them first. Why is that?

```cs
// Extracted from System.Collections.ArrayList.cs
public virtual int Add(object? value);
// ...
public virtual object? this[int index];
```

It is because this collection only stores information by their base type `object`. Therefore, when accessing any information from the `ArrayList`, you can never be 100% sure, what Type it is and need to do Type-Checking and Type-Casting.

In Summary, Non-Generic Collections are reusable, general-purpose Data Structures provide no Type-Safety, but allow you to store a variable amount of Data.

# Generic Collections

Generic Collections have been added to .NET when Generic Classes were introduced. They are included in the `System.Collections.Generic`-Namespace.

Look at this example of a List:

```cs
using System.Collections.Generic;

List<string> names = new List<string>();
names.Add("Marc");
names.Add("Daniel");
names.Add("René");
```

You can see that now we had to specify a Generic Type Argument for the `List`. This Data Structure will now only allow objects of type `string` to be added to it. Trying to add any other Data Type will result in Compiler Errors.

This also makes accessing the Data very easy:

```cs
using System.Collections.Generic;

List<int> numbers = new List<int>();
numbers.Add(2);
numbers.Add(-5);
int first = numbers[0];
int second = numbers[1];
```

No Casting necessary. How come?

```cs
// Extracted from System.Collections.Generic.List.cs
public void Add(T item);
// ...
public T this[int index];
```

In other words, Generic Collections are reusable, general-purpose Data Structures that allow you to store a variable amount of Data in a Type-Safe manner.

# IEnumerable

The `IEnumerable`-Interface is defined twice. Once as `System.Collections.IEnumerable` and once as `System.Collections.Generic.IEnumerable<T>`. I guess you know, why? Because the latter is Type-Safe and used for Generic Collections.

The Purpose is for a class to implement the Iterator-Pattern, which is a very popular Design Pattern. A Design Pattern is a Pattern that has been specified as a solution to a certain kind of problem. Usually, Design Patterns require you to implement them for your specific use-case, but the code-structure usually looks very similar to the original.

The Iterator-Pattern is a smart solution which allows you to easily iterate over a collection of variable elements.

To iterate means to go through it and look at all elements which are stored in the Collection.

 You need to know, that not all collections have an Order to it. Which means, that not all Collections allow you to use `collection[0]` and `collection[25]` to access the elements at a certain place.

The Interface is defined as such:

```cs
namespace System.Collections;

public interface IEnumerable
{
    // Returns an IEnumerator for this enumerable Object.  The enumerator provides
    // a simple way to access all the contents of a collection.
    IEnumerator GetEnumerator();
}
```

Alright. This does not look all too interesting. All the Magic happens in the `IEnumerator`-Interface, I suppose?

# IEnumerator

```cs
namespace System.Collections;

public interface IEnumerator
{
    // Advances the enumerator to the next element of the enumeration and
    // returns a boolean indicating whether an element is available. Upon
    // creation, an enumerator is conceptually positioned before the first
    // element of the enumeration, and the first call to MoveNext
    // brings the first element of the enumeration into view.
    //
    bool MoveNext();

    // Returns the current element of the enumeration. The returned value is
    // undefined before the first call to MoveNext and following a
    // call to MoveNext that returned false. Multiple calls to
    // GetCurrent with no intervening calls to MoveNext
    // will return the same object.
    //
    object? Current
    {
        get;
    }

    // Resets the enumerator to the beginning of the enumeration, starting over.
    // The preferred behavior for Reset is to return the exact same enumeration.
    // This means if you modify the underlying collection then call Reset, your
    // IEnumerator will be invalid, just as it would have been if you had called
    // MoveNext or Current.
    //
    void Reset();
}
```

Alright, the Interface looks straightforward enough, doesn't it?

`bool MoveNext()` moves the Iterator to the next Element in the Collection. In the beginning, it is placed BEFORE the First Element. Which means, that you need to move it first before trying to access any element. It returns `true`, if it successfully moved the Iterator. If it returns `false`, it means that you've reached over the end of the Collection.

`Current {get;}` gives you the current Element of the Collection. It points at different Elements after calling `MoveNext`.

`void Reset()` allows you to reset the Enumerator, in case you want to start over from the first element again. This is pretty uncommon.

PRACTICE!
- Create a List with 5 numbers.
- Assign it to a Variable of Type `IEnumerable`.
- Use `GetEnumerator()` and a `while`-Loop to print all elements of the `IEnumerable`-Variable.
- Can you also add up all numbers of the List using `IEnumerable` and then print the sum? If not, then what do you need to change?

# foreach

If you have done the previous Exercise, then you have actually implemented your own Implementation of `foreach`.

`foreach` is only syntactic sugar which makes Code especially readable, if you want to iterate over a collection. Instead of calling `GetEnumerator()`, `MoveNext()` and `Current`, you can simply write:

```cs
using System.Collections.Generic;

List<int> numbers = new List<int>();
numbers.Add(2);
numbers.Add(5);
foreach(int number in numbers){
    System.Console.WriteLine(number);
}
```

Pretty neat, or? Now, you've come to understand that this keyword does not do any Magic, but just uses existing Program Code under the hood. And that you are able to provide `foreach`-Support for your class by implementing the `IEnumerable` or `IEnumerable<T>`-Interface.

# yield

## Introduction

Now, `yield` is one of the keywords that struck me as Black Magic when I saw it the first time. I think that it is best explained through a code sample:

```cs
using System.Collections;

static IEnumerable GetNumbersUntil(int number) {
    for(int i = 0; i < number; i++) {
        yield return i;
    }
}

foreach(var number in GetNumbersUntil(5)) {
    System.Console.WriteLine(number);
}
```

Output:
```
0
1
2
3
4
```

So, what has happened here? Somehow, the method was able to return multiple numbers. Does it pause and then continue again, or does it return a Collection of all numbers? Let's find out:

## Step-By-Step

```cs
using System.Collections;
using System;

static IEnumerable GetNumbersUntil(int number) {
    Console.WriteLine("[GetNumbersUntil] Start");
    for(int i = 0; i < number; i++) {
        Console.WriteLine($"[GetNumbersUntil] Before yield return {i}");
        yield return i;
        Console.WriteLine($"[GetNumbersUntil] After yield return {i}");
    }
    Console.WriteLine($"[GetNumbersUntil] Finish");
}

foreach(var number in GetNumbersUntil(2))
{
    Console.WriteLine($"[Main] Got Number: {number}");
}
```

Output:
```
[GetNumbersUntil] Start
[GetNumbersUntil] Before yield return 0
[Main] Got Number: 0
[GetNumbersUntil] After yield return 0
[GetNumbersUntil] Before yield return 1
[Main] Got Number: 1
[GetNumbersUntil] After yield return 1
[GetNumbersUntil] Finish
```

We can see, that the Method gets interrupted by the `yield` Statement, to return a value. And then can be returned to, when the `foreach` statement is triggered again.

We already know that `foreach` calls `GetEnumerator` first. This returns an Iterator-Object for this method.

Then, `foreach` calls `MoveNext()` in every iteration, which makes the Iterator execute code from the object until the next `yield return` statement, making the returned value available as `Current` and returning `true`, so the `foreach` continues.

If `MoveNext()` hits a `yield break` or the end of the method, then it returns `false` and the `foreach` Loop ends.

## Under the Hood

But how is it possible, that a Method gets interrupted? We can take a look at the [Compiled C# Code](https://sharplab.io/#v2:CYLg1APgAgDABFAjAOgMIHsA2mCmBjAFwEt0A7AZwG4BYAKCgCZE66kA2BRAZjgHEcCAOQCuAWwBGOAE7kAqqWKYAFEQVxSYyVICUcAN504RzgE4lAIgDa/IZulyFRTAF04AZQIBDKQXPaatMZwAGboUipqRHAAvHAwlHBRADzqdlIJRGBgugaBQcZIZgAkVjYiEvbyiq4AQjihUjiciAgA7PpEAL5+AfkFiC1Q7US9faZKJdYC5VoO1XAAgsEE0s1tHd3+hsad20aFE6XTaXNOrgBiqkTkABY9dLu0dA04nng3SgBu3qkVUomkPjHP6nZQMbTaOi5IIHSYAWU8qlcvHQBDgM2kIH0Gj+mwCnSAA).

Here, you'll see the following (simplified):

```cs
private sealed class GetNumbersUntil : IEnumerable<object>, IEnumerable, IEnumerator<object>, IDisposable, IEnumerator
{
    private int __state;

    private object __current;

    private int number;

    private int i;

    object IEnumerator<object>.Current
    {
        [DebuggerHidden]
        get
        {
            return __current;
        }
    }

    [DebuggerHidden]
    public GetNumbersUntil(int __state)
    {
        this.__state = __state;
    }

    private bool MoveNext()
    {
        int num = __state;
        if (num != 0)
        {
            if (num != 1)
            {
                return false;
            }
            __state = -1;
            Console.WriteLine(string.Format("[GetNumbersUntil] After yield return {0}", i));
            i++;
        }
        else
        {
            __state = -1;
            Console.WriteLine("[GetNumbersUntil] Start");
            i = 0;
        }
        if (i < number)
        {
            Console.WriteLine(string.Format("[GetNumbersUntil] Before yield return {0}", i));
            __current = i;
            __state = 1;
            return true;
        }
        Console.WriteLine("[GetNumbersUntil] Finish");
        return false;
    }

    [DebuggerHidden]
    IEnumerator<object> IEnumerable<object>.GetEnumerator()
    {
        if (thisStateMachineIsReset)
        {
            return this;
        }
        else
        {
            return new StateMachine();
        }
    }
}
```

Woah, that's not easy to read. But basically, our Method gets compiled into a `class` which is a State Machine. That increases `__state` every time `MoveNext()` is called and thereby moves on to the next statement. Also, `GetEnumerator` returns the State Machine itself as an `Enumerable`, but only, if it's currently not in use. Else, it will return a new State Machine.

Again, not really Black Magic, our Computer does not need to learn to interrupt methods. Instead, the Compiler converts our simple-looking code into something quite complex to enable this to work.

## Why not simply return a List?

For the previous example, we also could've written the following code:

```cs
using System.Collections;

static IEnumerable GetNumbersUntil(int number) {
    int[] result = new int[number];
    for(int i = 0; i < number; i++) {
        result[i] = i;
    }
    return result;
}
```

But what's the disadvantage here?

You'll find out, if you try executing the following:

```cs
double total = 0;
foreach(int number in GetNumbersUntil(1000000000)){
    total += number;
}
Console.WriteLine($"Total: {total}");
```

Output:
```
Total: 4.99999999067109E+17
```

This should work just fine (though take a while), if you used `yield`, but probably it will take forever or crash, if you use the solution that returns an Array. Why?

## One at a time

Because `yield` allows you to use a technique called `Streaming`. Where instead of doing all work at once and returning a giant object containing the whole result, you do the work bit by bit and always return a small bit of the result. This makes apps more responsive and can reduce Memory Usage in this case from 4GB to to 4 bytes!

The same technique is used almost everywhere else in Programming. For example, when you stream a show on Netflix. Instead of downloading the whole 4GB Movie, it will stream it bit by bit. Allowing you to start watching after a few kiloBytes already.

In other words: Don't underestimate the power of `yield` and use it where ever it makes sense.

## Coroutines

Let's have a look at Unity's Coroutines again:

```cs
using UnityEngine;
using System.Collections;

IEnumerator Despawn(){
    Debug.Log("Hello there!");
    yield return new WaitForSeconds(2);
    Debug.Log("Hello from the future!");
}
StartCoroutine(Despawn());
```

So, what Unity does also is not any sort of Black Magic. When you call `StartCoroutine` and pass it an `IEnumerator`, it will continue calling `MoveNext` until it returns false. If `Current` points to a `WaitForSeconds`-Object, then Unity will know to wait until the two seconds are over before calling `MoveNext` again. Brilliant :)

# List

## Introduction

Let's implement our first own Collection! It will not require the `yield` Statement, no worries :)

Our class will be called `TurboList` and it will be a Generic Collection. Not that our implementation would be any better or faster than the original .NET implementation, but I want to avoid us having name conflicts with the existing `List<T>` all the time.

The Idea of a List is that it behaves very similar to an Array. But instead of giving a fixed size to the List, it should Resize itself automatically to our needs. In other words, we should be able to Add and Remove Items as we want. The Method `Add` just puts an item at the end of the List. Making it especially easy to add an item.

## Usage

Anything that you have a variable number of; e.g.:
- Items
- Players
- Enemies
- PowerUps
- Achievements
- Orders
- ...


## Specification

A List's common interface contains the following:

```cs
// returns the current amount of items contained in the list.
int Count;
// adds one item to the end of the list.
void Add(T item);
// gets the item at the specified index. If the index is outside the correct range, an exception is thrown.
T Get(int index);
// replaces the item at the specified index. If the index is outside the correct range, an exception is thrown.
void Set(int index, T value);
// removes all items from the list.
void Clear();
// removes one item from the list. If the 4th item is removed, then the 5th item becomes the 4th, the 6th becomes the 5th and so on.
void RemoveAt(int index);
// --------------- optional ---------------
// returns true, if the given item can be found in the list, else false.
bool Contains(T item);
// returns the index of the given item if it is in the list, else -1.
int IndexOf(T item);
// removes the specified item from the list, if it can be found. Workes similar to RemoveAt.
void Remove(T item);
// adds multiple items ad the end of this list at once. Works similar to Add.
void AddRange(IEnumerable<T> items);
// --------------- important, but difficult ---------------
// gets the iterator for this collection. Used by IEnumerable<T>-Interface to support foreach.
IEnumerator<T> IEnumerable<T>.GetEnumerator();
```

## Remarks

We have not discussed `Exceptions` in Detail, yet. But in its easiest way, you can throw an Exception like this:

```cs
throw new System.Exception("Describe your error well here, so the user understands why his application crashed.");
```

Your `TurboList` will need to be able to store a variable amount of data. It should do so by containing an Array as a field which sometimes will need to be resized. When resizing an Array, you need to create a new, larger Array that can fit all elements and then copy the elements from the old to the new Array.

```cs
private T[] items;
```

Hint: The Method `Array.Resize(ref Array array, int newSize)` exists. And yes, it would be the correct way to use it instead of manually resizing the Array. But that method needs to do exactly that, too (create a new array and copy all items) and I want to show you just how much work needs to be done when an Array is being resized. To make a point later on in this course :)

## Repository / Solution

For this implementation, we will set up a Solution which will allow you to work Test-Driven.

Here's a Guideline for how to use the DotNet CLI to do so: [CLI Multi-Project Setup](https://gist.github.com/InaSLew/a0b174f07fb657e8a3133daa3e942fb9)

## Application: CustomerManager

The Idea is to write an application that allows you to add and remove customers to a list.

```
Choose one option:
(1) Add a Customer
(2) Remove a Customer by name
(3) Remove a Customer by index
(4) Display all Customers
<<< 1
What is the Customer's name?
<<< Marc
Choose an option:
(1) Add a Customer
(2) Remove a Customer by name
(3) Remove a Customer by index
(4) Display all Customers
<<< 1
What is the Customer's name?
<<< Anna
Choose an option:
(1) Add a Customer
(2) Remove a Customer by name
(3) Remove a Customer by index
(4) Display all Customers
<<< 1
What is the Customer's name?
<<< Max
Choose an option:
(1) Add a Customer
(2) Remove a Customer by name
(3) Remove a Customer by index
(4) Display all Customers
<<< 4
0: Marc
1: Anna
2: Max
Choose an option:
(1) Add a Customer
(2) Remove a Customer by name
(3) Remove a Customer by index
(4) Display all Customers
<<< 2
What is the Customer's name?
<<< Marc
Choose an option:
(1) Add a Customer
(2) Remove a Customer by name
(3) Remove a Customer by index
(4) Display all Customers
<<< 4
0: Anna
1: Max
Choose an option:
(1) Add a Customer
(2) Remove a Customer by name
(3) Remove a Customer by index
(4) Display all Customers
<<< 3
What index?
<<< 1
Choose an option:
(1) Add a Customer
(2) Remove a Customer by name
(3) Remove a Customer by index
(4) Display all Customers
<<< 4
0: Anna
```

## You did it wrong!

Have you completed all of the steps for implementing the `TurboList`? Well, then chances are that you did it wrong. Sorry. Well, not wrong really, but quite wasteful of resources.

Most probably, you resized the Array every time an element is added and probably also when it's removed? Like this:

| Action | List Count | Array Length
:---------:|:------------:|:---------------:
|     |     0      |  0
| Add |     1      |  1
| Add |     2      |  2
| Add |     3      |  3
| Add |     4      |  4
| Remove |     3      |  3
| Add |     4      |  4
| Add |     5      |  5

What is the problem with this? Well, every time when the Array needs to be Resized, a new Array is created and the old one is dumped. That's a lot of wasted resources. Ideally, your list would do something like this:

| Action | List Count | Array Length
:---------:|:------------:|:---------------:
|     |     0      |  0
| Add |     1      |  4
| Add |     2      |  4
| Add |     3      |  4
| Add |     4      |  4
| Remove |     3      |  4
| Add |     4      |  4
| Add |     5      |  8

In other words: always make sure that there's some extra space in the buffer. It is quite common to always resize the array to double the size when needing to increase the size. And to never decrease it. This will reduce performance wasted on: Memory Allocation, Copying Memory and Garbage Collection. But it will instead waste resources on some unused Memory (if your list only needs 9 elements, then a buffer for 16 elements is quite wasteful).

This is very common with Algorithms and Data Structures: you either benefit in less Memory Consumption, or better CPU Performance.

---

# Streams

Let's go back to streams for another second.

```cs
using System.IO;

// open a stream that can write into a file 
FileStream fileStream = File.OpenWrite("log.txt");
// open a stream writer for the filestream, which makes writing text easier
StreamWriter streamWriter = new StreamWriter(fileStream);

for (int i = 0; i < 10000; i++)
{
    streamWriter.WriteLine(i);
}
```

This is, how you can use .NET's Streaming classes to write information to a file. This is again very useful in order to make sure if you have big amount of data (imagine for example your game's log, which can have tens of thousands of logs per session), that you won't have to write it all at once as a huge 4MB-File, but can instead do so line by line.

.NET's Streams are very nicely designed. You can just nest Streams in Streams in Streams in order to get exactly the correct kind of Data into a Stream or out of a Stream. This is called the Decorator-Pattern.

Where one class looks to the outside the same like the inner class, but can add some functionality to it (decorate it).

Another example:
```cs
// this one can stream a byte[] into a Byte-Stream
MemoryStream memoryStream = new MemoryStream(bytes);
// this one can decompress GZIP from a Byte-Stream
GZipStream gzipStream = new GZipStream(memoryStream, CompressionMode.Decompress);
// this one can read text from a Byte-Stream
StreamReader streamReader = new StreamReader(gzipStream);
```

This is pretty cool. It's a bit like connecting a bunch of Adapters in order to get exactly what you want.

## Flush

Back to the original source code:

```cs
using System.IO;

// open a stream that can write into a file 
FileStream fileStream = File.OpenWrite("log.txt");
// open a stream writer for the filestream, which makes writing text easier
StreamWriter streamWriter = new StreamWriter(fileStream);

for (int i = 0; i < 10000; i++)
{
    streamWriter.WriteLine(i);
}
```

My output in `log.txt` for this code is:

```
...
9231
9232
9
```

Wait! I can not remember wanting to log only the number until 9232 and then a 9? What's the problem?

In order to optimize performance, Streams contain an internal buffer. Let's say for 10.000 bytes. And only, if that buffer is full, it will Flush the data and forward it to the next stream. This is really important, because Should the Stream write each byte one by one? That would be a lot of going back and forth to the Hard Drive for writing a file. Or sending data over the internet one byte at a time, waiting until it has been received, would also take forever.

Good thing is that we can flush it manually, though:

```cs
streamWriter.Flush();
```

## Dispose

Okay, that problem is solved. Let's try something else:

```cs
using System.IO;

FileStream fileStream = File.OpenWrite("log.txt");
StreamWriter streamWriter = new StreamWriter(fileStream);

for (int i = 0; i < 100; i++)
{
    streamWriter.WriteLine(i);
}

streamWriter.Flush();

// Let's think of a reason that causes us to need to open a new stream: 

FileStream newFileStream = File.OpenWrite("log.txt");
StreamWriter newStreamWriter = new StreamWriter(newFileStream);
for (int i = 500; i < 600; i++)
{
    newStreamWriter.WriteLine(i);
}

newStreamWriter.Flush();
```

Output:
```
Unhandled exception. System.IO.IOException: The process cannot access the file 'log.txt' because it is being used by another process.
```

Okay, makes sense. Better set the old stream to null first, so it gets Destroyed, right?

```cs
using System.IO;

FileStream fileStream = File.OpenWrite("log.txt");
StreamWriter streamWriter = new StreamWriter(fileStream);

for (int i = 0; i < 100; i++)
{
    streamWriter.WriteLine(i);
}

streamWriter.Flush();

// Let's think of a reason that causes us to need to open a new stream: 

// clean up the old ones first
streamWrite = null;
fileStream = null;

// then create new ones
FileStream newFileStream = File.OpenWrite("log.txt");
StreamWriter newStreamWriter = new StreamWriter(newFileStream);
for (int i = 500; i < 600; i++)
{
    newStreamWriter.WriteLine(i);
}

newStreamWriter.Flush();
```

Output:
```
Unhandled exception. System.IO.IOException: The process cannot access the file 'log.txt' because it is being used by another process.
```

Okay, that did not help. Ah yeah, the Garbage Collector will take some time before actually cleaning up the object. Let's force it:

```cs
using System;
using System.IO;

FileStream fileStream = File.OpenWrite("log.txt");
StreamWriter streamWriter = new StreamWriter(fileStream);

for (int i = 0; i < 100; i++)
{
    streamWriter.WriteLine(i);
}

streamWriter.Flush();

// set the resources to null
fileStream = null;
streamWriter = null;
// tell the Garbage Collector to run now
GC.Collect();
// wait for the finalizers to finish
GC.WaitForPendingFinalizers();

// Let's think of a reason that causes us to need to open a new stream: 

FileStream newFileStream = File.OpenWrite("log.txt");
StreamWriter newStreamWriter = new StreamWriter(newFileStream);
for (int i = 500; i < 600; i++)
{
    newStreamWriter.WriteLine(i);
}

newStreamWriter.Flush();
```

Even this did not help in my case :( Well, relying on Finalizers to clean up our classes doesn't help apparently. Is there any other solution?

# Dispose Pattern

Of course there is! The Dispose Pattern helps out here. Whenever a class has Unmanaged Resources (any resource that won't automatically be cleaned up like File Handles, Network Sockets or also other Processes or so), it should implement `System.IDisposable`

```cs
namespace System;
public interface IDisposable
{
    void Dispose();
}
```

Well, that's not very exciting technology. But let's try whether it does the job:

```cs
using System.IO;

FileStream fileStream = File.OpenWrite("log.txt");
StreamWriter streamWriter = new StreamWriter(fileStream);

for (int i = 0; i < 100; i++)
{
    streamWriter.WriteLine(i);
}

streamWriter.Flush();

streamWriter.Dispose();
fileStream.Dispose();

FileStream newFileStream = File.OpenWrite("log.txt");
StreamWriter newStreamWriter = new StreamWriter(newFileStream);

for (int i = 500; i < 600; i++)
{
    newStreamWriter.WriteLine(i);
}

newStreamWriter.Flush();

```

This works! Nice! In other words: If we have classes which implement `System.IDisposable` we should not forget to call `Dispose()` when we're done using them. This will in the case of Streams also automatically Flush the Stream, by the way. But I think that it's quite easy to forget to call `Dispose()`, or?

## Using using

The using Keyword actually handles Disposal of `IDisposable`-Classes for us! No extra code required. You assign the resource that you want to `use` within as a Function Argument and then use the File within the `{}`-Scope. When the Scope ends, `Dispose()` is called for you on the object:

```cs
using System.IO;

using (FileStream fileStream = File.OpenWrite("log.txt"))
{
    using (StreamWriter streamWriter = new StreamWriter(fileStream))
    {
        for (int i = 0; i < 100; i++)
        {
            streamWriter.WriteLine(i);
        }
    }
}

using (FileStream fileStream = File.OpenWrite("log.txt"))
{
    using (StreamWriter streamWriter = new StreamWriter(fileStream))
    {
        for (int i = 500; i < 600; i++)
        {
            streamWriter.WriteLine(i);
        }
    }
}
```

And because Code with a lot of Indentations becomes quite unreadable, .NET even came up with a neater syntax:

```cs
using System.IO;

using FileStream fileStream = File.OpenWrite("log.txt");
using StreamWriter streamWriter = new StreamWriter(fileStream);
for (int i = 0; i < 100; i++)
{
    streamWriter.WriteLine(i);
}
```

But careful! In this case, the Scope of the Variable only ends when the Outer Scope ends (which is the Main-Method). In other words, we would not be able to create a second FileStream for the same File anymore, because the previous fileStream would not have left the scope, yet:

```cs
using System.IO;

using FileStream fileStream = File.OpenWrite("log.txt");
using StreamWriter streamWriter = new StreamWriter(fileStream);
for (int i = 0; i < 100; i++)
{
    streamWriter.WriteLine(i);
}

// This will throw an Exception again:
using FileStream newFileStream = File.OpenWrite("log.txt");
using StreamWriter newStreamWriter = new StreamWriter(newFileStream);
for (int i = 0; i < 100; i++)
{
    newStreamWriter.WriteLine(i);
}
```

## What if your class uses an IDisposable Field?

Then your class should also implement `IDisposable` and make sure that all fields are disposed of when your class is disposed:

```cs
using System;
using System.IO;
public class Logger : IDisposable
{
    private readonly FileStream fileStream;
    private readonly StreamWriter streamWriter;
    bool disposed;

    public Logger()
    {
        fileStream = new FileStream("log.txt", FileMode.Create);
        streamWriter = new StreamWriter(fileStream);
    }

    public void Log(string message)
        => this.streamWriter.Write($"[{DateTime.Now}] {message}");

    public void Dispose()
    {
        fileStream.Dispose();
        streamWriter.Dispose();
    }
}
```

## Enumerator

Fun Fact: Most Enumerator Implementations implement `IDisposable`. Well, this wasn't that funny, was it? But this has reasons of some of them being reusable. And only if they've been disposed of in time, can they be reused for another method needing an Enumerator.

# Stack

Lists are quite useful. Technically, you can use them for any problem. You can access any element and you can also remove elements at any index. It is not very efficient at doing so, because it needs to move all further elements in the Array forward to the previous slots, but it can do the job.

But very often, you want to have a collection where you can only put things on Top and remove them from the Top as well. Kind of like a stack of plates. This Collection is called a Stack and follows the

## LIFO Principle
> Last in, first out.

This means that the items are returned in reverse order. The one that was added last is returned first and then the previous one etc. The item that was added first is returned last.

## Usage
If you have a bunch of work and need to:
- pause some work when other comes in.
- then continue when the new work is done.
- very often used in algorithms like Pathfinding, AI, ...
- also, every computer program (Call Stack)

If you have a game with a stack of things, where only the top one can be retrieved
- e.g. a deck of cards

Game States and UI States
- e.g. Main Menu (bottom) -> Settings -> Confirm Popup (top)

History
- e.g. Browser History, File Explorer History, Undo/Redo

## Specification

Class: `TurboStack<T>`

A Stack's common Interface contains the following:

```cs
// returns the current amount of items contained in the stack.
int Count;
// adds one item on top of the stack.
void Push(T item);
// returns the item on top of the stack without removing it.
T Peek();
// returns the item on top of the stack and removes it at the same time.
T Pop();
// removes all items from the stack.
void Clear();
// --------------- optional ---------------
// gets the iterator for this collection. Used by IEnumerable<T>-Interface to support foreach.
IEnumerator<T> IEnumerable<T>.GetEnumerator();
```

Hm, maybe, we should have started with this example? :)

## Application: GameStateHistory

Write a `GameStateHistory` - Console Application in which multiple states exist. 
- From Main Menu, you can go to Level 1, Settings or Quit.
- From Settings you can not go anywhere
- On Quit the Application Quits
- From Level 1, you can go to Level 2 or the Main Menu.
- From Level 2, you can go to Level 3 or the Main Menu
- etc.

On top of that, you have two more options at all times (unless it's not possible):
- Go Backward: Goes back to the previous State in the history

It should look something like this:

```
You are here: Main Menu
What do you want to do?
(0): Go to Level 1
(1): Go to Settings
<<< 0
You are here: Level 1
What do you want to do?
(0): Go to Level 2
(1): Go to Main Menu
(b): Go back to Main Menu
<<< 0
You are here: Level 2
What do you want to do?
(0): Go to Level 3
(1): Go to Main Menu
(b): Go back to Level 1
<<< b
You are here: Level 1
What do you want to do?
(0): Go to Level 2
(1): Go to Main Menu
(b): Go back to Main Menu
```

Use a Stack to keep track of the History.

- What if you also wanted to be able to go forward again?
- How could you implement it using the Data Structures you already have?
- Would another Data Structure maybe fit this problem better?

---

# Extension Methods

Imagine you were using this class from a Library:

```cs
public class Person {
    public DateTime BirthDate;
}
```

What you'd like to know is a Person's age. You could write a Method for this and use it like this:

```cs
int GetAgeForPerson(Person person){
    // this is by the way not exact. Why?
    return (DateTime.Now-person.BirthDate).Days/365;
}

Person person = GetSomePerson();
Console.WriteLine("Age: " + GetAgeForPerson(person));
```

Okay, this looks just fine. But now, you need this method in another place of your code as well. Maybe, above sample was in the Shop to verify the User's Age and the other place is in the User Profile, to display the User's Age. It's about time to avoid Code Duplication and put it into a static class:

```cs
public static class PersonMethods {
    public static int GetAgeForPerson(Person person){
        // this is by the way not exact. Why?
        return (DateTime.Now-person.BirthDate)/365;
    }
}
```

And now in the other place:
```cs
Person person = GetSomePerson();
Console.WriteLine("Age: " + PersonMethods.GetAgeForPerson(person));
```

If you want this code a bit more pretty, you can use `using static` to make the `static` Methods available as if they were local Methods:

```cs
using static PersonMethods;
Person person = GetSomePerson();
Console.WriteLine("Age: " + GetAgeForPerson(person));
```

Okay, this works. But the Problem is: It's hard to miss that this method exists. Other people might not know that it exists. Also, it would've been great, if the author just would've thought of adding the Method to the `Person` class right away, so you'd be able to write code like this:

```cs
Person person = GetSomePerson();
Console.WriteLine("Age: " + person.GetAge());
```

Sometimes, you might be able to inherit from the class and add that functionality, but that's often not possible. Luckily, you can extend even existing classes from other people. Restrictions: You can only access `public` Fields and Methods. It works like this:

```cs
// The class containing extension methods must be static:
public static class PersonExtensions{
    // the extension method must be static and use the `this` keyword:
    public static GetAge(this Person person){
        return (DateTime.Now-person.BirthDate)/365;
    }
}
```

Now, to all other classes. It will look as if the Method is part of the Person:

```cs
Person person = GetSomePerson();
Console.WriteLine("Age: " + person.GetAge());
```

Again, this is only syntactic sugar. The compiler will change this code to:

```cs
Person person = GetSomePerson();
Console.WriteLine("Age: " + PersonExtensions.GetAge(person));
```

# LINQ

LINQ, or Language-Integrated-Query, is a technology by .NET which allows to handle Collections like Streams and manipulate them in a similar fashion to how you can manipulate Streams by nesting them. Imagine the following case:

```cs
Animal animals = zoo.GetAnimals();

class Cat : Animal {
    public string FavoriteFood;
    public FoodBowl FoodBowl;
}

class Animal {
    public bool IsHungry;
}

class FoodBowl{
    public void Fill(string food){}
}
```

Now, we notice that we have a lot of tuna left. We want to feed the cats, because they've not eaten in a while. But of course, we only want to feed the cats which are Hungry and like Tuna. Because cats are pretty picky, we have learned that the hard way. Also, they have their favorite FoodBowls. Cats are not easy to please.

We could implement it like this:

```cs
foreach(Animal animal in animals){
    if(animal.IsHungry){
        if(animal is Cat cat){
            if(cat.FavoriteFood == "Tuna"){
                cat.FoodBowl.Fill("Tuna");
            }
        }
    }
}
```

This should work. But what if it's not our Job to actually fill the bowl? We have an assistant that we need to provide with the information, so we should give him a list of all FoodBowls to fill with Tuna:

```cs
List<FoodBowl> foodBowls = new List<FoodBowl>();
foreach(Animal animal in animals){
    if(animal.IsHungry){
        if(animal is Cat cat){
            if(cat.FavoriteFood == "Tuna"){
                foodBowls.Add(cat.FoodBowl);
            }
        }
    }
}
assistant.OrderToFill("Tuna", foodBowls);
```

Neat! But also quite complex. Also, if we have thousands of cats, this will be a very large array again. What if we could Stream the Data to our Assistant? Fact is, we can!

```cs
IEnumerable<FoodBowl> foodBowls = animals
    .Where(animal => animal.IsHungry)
    .OfType<Cat>()
    .Where(cat => cat.FavoriteFood == "Tuna")
    .Select(cat => cat.FoodBowl);

assistant.OrderToFill("Tuna", foodBowls);
```

Pretty cool and expressive, or? And you can do so many more complex things with LINQ: 
- `Concat`: Combine to Collections
- `GroupBy`: Group a Collection by some common Property
- `First`: Get the first item. Exception if none.
- `FirstOrDefault`: Get the first item. Default if none.
- `Single`: Get one element for a condition. Exception if not exactly one.
- `Any`: Returns true, if any item satisfies a condition.
- `OrderBy`: Sort in ascending order.
- `OrderByDescending`: Sort in descending order.
- `ToDictionary`, `ToArray`, `ToList`: Convert to Collection-Type
- ... and many more.

## Usage:
- AI: Find all buildings of the player. Filter them by only the ones the AI has discovered. Filter them by the ones that seem not too well defended. Sort them by Priority. Attack the First.
- Multiplayer: Show Clan-Members. Filter by the ones being online OR being a High Rank. Sort them by Rank.


# Type-Introspection

Type-Introspection is an extremely powerful tool that allows your code to work with types that it doesn't even know of. It comes automatically with .NET, is called `System.Reflection` and it provides Type-Information for every single Type and Object Instance of that Type.

## Usage
- Most obvious: To find out, whether a class has a certain type or inherits from a certain type or implements a certain interface. Keywords like `is` use Type-Introspection under the hood.
- Just as obvious: Safely casting the Type. Keywords like `as` use Type-Introspection under the hood.
- Showing Editors, like in Unity. This is how Unity knows what fields to Display in the Inspector. You could also build a Remote Inspector this way.
- Serializing or Deserializing any class.
- Scanning the codebase for all classes implementing a certain Interface, to automatically load them or show them in the UI.
- Building a Visual Scripting Editor that can call your methods or change you Properties.
- Dangerous: To access private fields and methods. This is of course not recommended, but sometimes you find a class that's almost perfect. But there's also this one method that is `private` that you'd like to use.
- To call methods or constructors of unknown Types. e.g. for an In-Game Console or for using Dependency Injection.

## GetType()

The Method `System.Type GetType();` is defined on the `object`-Baseclass and therefor usable on any object in C#:

```cs
using System;

int a = 5;
string name = "Hi!";

Console.WriteLine(a.GetType());
Console.WriteLine(name.GetType());
```

Output:
```
System.Int32
System.String
```

What do you think is the Result here?

```cs
using System;

Dog dog = new Dog();
Animal animal = new Dog();
object obj = new Dog();

Console.WriteLine(dog.GetType());
Console.WriteLine(animal.GetType());
Console.WriteLine(obj.GetType());

class Animal{}
class Dog : Animal{}
```

Output:
```
Dog
Dog
Dog
```

Okay cool, so this way, we can get the REAL Type of an object. Not only the base-Type that we know of.

## typeof

`typeof()` is a useful Keyword which can give you the `System.Type` of a Type that's known during compile-time. Look at this example:

```cs
Animal animal = new Dog();
Console.WriteLine(animal.GetType() == typeof(Dog));
Console.WriteLine(animal.GetType() == typeof(Animal));

class Animal{}
class Dog : Animal{}
```

Output:
```
True
False
```

Two things learned here: Into `typeof`, we can pass the name of a Type and it will return the `System.Type` of that Type.

And, we can compare `System.Type` and it returns `true` only, if it's exactly the same Type. It's not enough if there's a common base class. In comparison, if you check the Type with `is`, it returns `true` both times:

```cs
Animal animal = new Dog();
Console.WriteLine(animal is Dog);
Console.WriteLine(animal is Animal);

class Animal{}
class Dog : Animal{}
```

Output:
```
True
True
```

This is by the way, how Methods were made Generic before Generic Methods existed:

```cs
gameObject.GetComponent(typeof(Health));
```

Instead of:

```cs
gameObject.GetComponent<Health>();
```

## nameof

Another very useful feature is `nameof`, especially when using Reflection or displaying output to the user. Look at this example:

```cs
void Attack(Enemy enemy) {
    if(enemy == null) {
        Console.WriteLine($"Error: '{nameof(enemy)}' can not be null!");
    }
}
```

Output:
```
Error: 'enemy' can not be null!
```

Well, this was a very complicated way of writing this:

```cs
void Attack(Enemy enemy) {
    if(enemy == null) {
        Console.WriteLine($"Error: 'enemy' can not be null!");
    }
}
```

Why go the long way, then? Because the long way will cause errors, if the parameter was to be removed or renamed. This makes sure, that you don't change the code at some point and make it look like this:

```cs
void Attack(Unit unit) {
    if(unit == null) {
        Console.WriteLine($"Error: 'enemy' can not be null!");
    }
}
```

Now, the Log shows wrong information. There is no parameter named `enemy` anymore. If you use `nameof`, IDEs will actually automatically rename `enemy` to `unit` for you. This is such a useful feature, that I'll always strike you in Code Review, if I see you put the name of a class or field or parameter into a string without using `nameof`.

## Let's Find all Types that Implement `IEnumerable`

```cs
using System;
using System.Collections;
using System.Linq;

var allEnumerableTypes = 
    // All Assemblies that are currently loaded
    AppDomain.CurrentDomain.GetAssemblies()
    // Select from each assembly all types
    .SelectMany(assembly => assembly.GetTypes())
    // Filter them by types which can be assigned to `IEnumerable`
    .Where(type => type.IsAssignableTo(typeof(IEnumerable)))
    // Filter them by types which are not abstract (so we don't get interfaces or abstract base classes)
    .Where(type => !type.IsAbstract);

Console.WriteLine($"Total Amount of Enumerables: {allEnumerableTypes.Count()}");
foreach (var type in allEnumerableTypes)
{
    Console.WriteLine(type);
}
```

Output:
```
Total Amount of Enumerables: 106
System.String
System.ArraySegment`1[T]
```

Usage:
You can for example find all classes implementing `IGame` and then have a Main Menu that automatically scans the solution for all classes implementing `IGame`. Or you could scan for all classes implementing `IDifficulty` or so.

## Let's find out all the fields that a Type has

```cs
using System.Linq;
using System.Reflection;

var propertyInfos = 
    gameObject
    .GetType()
    .GetProperties();

Debug.Log($"Found a total of {propertyInfos.Count()} {nameof(propertyInfos)}");
Debug.Log(string.Join("\n", propertyInfos.Select(GetDescription)));

string GetDescription(PropertyInfo propertyInfo)
{
    return $"Found {nameof(propertyInfo)} {propertyInfo.Name} of Type {propertyInfo.PropertyType.FullName}";
}
```

Output:
```
Found a total of 25 propertyInfos:
Found propertyInfo rigidbody of Type UnityEngine.Component
Found propertyInfo rigidbody2D of Type UnityEngine.Component
Found propertyInfo camera of Type UnityEngine.Component
...
```

Usage:
You can write tools, editors, inspectors, serializers or deserializes using this Method. For example convert any C# class to a text-file and load it from a text-file again.

## Let's get sneaky and access something private

```cs
using System;
using System.Reflection;

Unit unit = new Unit();
unit.Health = 200;
Console.WriteLine(unit.Health); // 100
// Getting the Field Info of a private instance member:
var fieldInfo = unit.GetType().GetField("health", BindingFlags.NonPublic | BindingFlags.Instance);
// Using the Field Info to set a value on a unit:
fieldInfo.SetValue(unit, 200);
Console.WriteLine(unit.Health); // 200
// Using the Field Info to get the value from a unit:
int health = (int)fieldInfo.GetValue(unit);

class Unit
{
    private int health;

    public int Health
    {
        get => health;
        set => health = Math.Clamp(value, 0, 100);
    }
}
```

Output:
```
100
200
```

Well, here, we've used it for pure evil. And also in general, it's not a good idea to set private values of a class. But sometimes it might look like this is the easiest way of solving a problem. If you use a really good Physics Engine that for some reason has not made a really important value accessible to you, you might as well force access instead of switching the whole Physics Engine :)

## Many others

There is many other use-cases, like finding a constructor and using it to construct an object, or putting objects in a dictionary, sorted by their types or many, many more.

# Attributes

Attributes are kind of classes which are not intended to become part of your "real" code, but instead are used to decorate your code with hints and properties which you can then read using reflection. What? Let's have a look:

## Required Properties

Scenario: The user wants to create a new Profile. He needs to provide some information. Some information are required, others are optional. Our code should make sure that all required information have been set.

```cs
using System;
using System.Linq;

Profile marc = new Profile
{
    Title = "Mr.",
    Name = "Marc"
};

Console.WriteLine($"Valid Profile: {ValidateProfile(marc)}");

bool ValidateProfile(Profile profile)
{
    var requiredProperties = profile
        .GetType()
        .GetProperties()
        .Where(property => property.GetCustomAttributes(typeof(RequiredAttribute), true).Any());
    
    foreach (var property in requiredProperties)
    {
        if (string.IsNullOrWhiteSpace((string)property.GetValue(profile)))
        {
            string reason = (property.GetCustomAttributes(typeof(RequiredAttribute), true).First() as RequiredAttribute).Reason;
            Console.WriteLine($"Error: {property.Name} not set. Reason: {reason}");
            
            return false;
        }
    }
    return true;
}

public class Profile
{
    public string Title { get; set; }
    [Required] public string Name { get; set; }
    public string Gender { get; set; }
    [Required(Reason = "Without a description, your profile looks boring.")] public string Description { get; set; }
}

public class RequiredAttribute : System.Attribute
{
    public string Reason { get; set; }
}
```

So, you can define an Attribute by making it inherit from `System.Attribute`. It is common to add `Attribute` at the end of your attribute's name:

```cs
public class RequiredAttribute : System.Attribute
{
    public string Reason { get; set; }
}
```

When using your Attribute, you can actually skip the Attribute-Part of the name:
```cs
[Required] public string Name { get; set; }
```

You can put the Attribute behind a lot of things. Like: classes, fields, enums, enum values, properties, methods, parameters and many more.

Also, if your attribute has Properties, you can assign them to leave additional information:

```cs
[Required(Reason = "Without a description, your profile looks boring.")] public string Description { get; set; }
```

Now, you can use Reflection to find out, whether any Class, Property, Field, ... has the Attribute:

```cs
property.GetCustomAttributes(typeof(RequiredAttribute), true).Any()
```

And even look at the Attribute's values:

```cs
RequiredAttribute required = property.GetCustomAttributes(typeof(RequiredAttribute), true).First() as RequiredAttribute;
Console.WriteLine(required.Reason);
```

You have used Attributes in a few places already, I hope this helps you understand what they are.

# Queue

We have learned about Lists and Stacks, the third of the most important Collections is the Queue. It is used, when things should get in order to be dealt with one at a time. They follow the

## FIFO Principle
> First in, first out.

This means that the items are returned in correct order. The one that was added first is returned first and then the next one etc. The item that was added last is returned last. Like any Queue online or in front of the liquor store New Year's Eve.

## Usage
If you have more work coming in than you can handle and want to make sure that they all get handled in fair oder. First come, first serve.
- e.g. Players queueing for Matchmaking, festival ticket booking or a full Server.

Or you just want to do the work later for some reason.
- e.g. the Game is paused right now.

If you want things to happen sequenced one after another.
- e.g. download a file, then load the file as a scene, then start the game.

If you want to queue up Game Events or Player Input
- e.g. multiple pop-ups appearing on Game Star, or a player queueing actions in a strategy game. Walk, then Attack the Tower, then Kill the goddamn sheep.

## Specification

Class: `TurboQueue<T>`

A Queue's common Interface contains the following:

```cs
// returns the current amount of items contained in the stack.
int Count;
// adds one item to the back of the queue.
void Enqueue(T item);
// returns the item in the front of the queue without removing it.
T Peek();
// returns the item in the front of the queue and removes it at the same time.
T Dequeue();
// removes all items from the queue.
void Clear();
// --------------- optional ---------------
// gets the iterator for this collection. Used by IEnumerable<T>-Interface to support foreach.
IEnumerator<T> IEnumerable<T>.GetEnumerator();
```

Well, this one is a bit more trouble-some than the Stack, isn't it?

The first, most obvious implementation is quite understandable. I recommend you to go for that one.

But then, look at the Dequeue-Method. It's a lot of work, isn't it? It's a bit like with the List:
- removing something from the end is super easy.
- but removing something from the front or in between? That requires a lot of shifting.

Try to think of a method using one Array that only needs ot move items around if the Buffer needs to be resized.

How would you go at that?

<details>
  <summary>Giving up? Check here.</summary>
  
  Check out Ring-Buffers.
</details>

## Application: Shunting-Yard-Algorithm

Implement the Shunting-Yard-Algorithm.\
Write a Console Application that asks you to enter any Mathematical Expression that's supposed to be resolved. The expressions may contain at least `+`, `-`, `*`, `/` and `()`.

Output:
```
Enter an expression:
<<< 12+(2+4)*3
Result: 30
```

---

# Further Research
- Read up on Linked Lists: Singly, Doubly, Circular
- Implement `TurboLinkedQueue` and `TurboLinkedStack` as Linked Lists.
- Can you think of other situations in which you could use the solution for the Queue-Spoiler?
- Try publish your TurboCollections on NuGet. The name `TurboCollections` is already taken, though :P
- Create a new Project outside of the solution. Add your NuGet-Package using `dotnet add package <packagename>`. Can you use your Library? How does updating the Library work?

---

# Other Resources
- [TutorialsPoint](https://www.tutorialspoint.com/data_structures_algorithms/index.htm)
- [GeeksForGeeks](https://www.geeksforgeeks.org/data-structures/)
- [Programiz](https://www.programiz.com/dsa)