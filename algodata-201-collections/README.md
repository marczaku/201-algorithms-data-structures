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

## Specification

A List's common interface contains the following:

```cs
// returns the current amount of items contained in the list.
int Count;
// adds one item to the end of the list.
void Add(T item);
// gets the item at the specified index. If the index is outside the correct range, an exception is thrown.
T Get(int index);
// removes all items from the list.
void Clear();
// removes one item from the list. If the 4th item is removed, then the 5th item becomes the 4th, the 6th becomes the 5th and so on.
void RemoveAt(int index);
// returns true, if the given item can be found in the list, else false.
bool Contains(T item);
// returns the index of the given item if it is in the list, else -1.
int IndexOf(T item);
// removes the specified item from the list, if it can be found.
void Remove(T item);
// adds multiple items to this list at once.
void AddRange(IEnumerable<T> items);
// gets the iterator for this collection. Used by IEnumerator to support foreach.
IEnumerator<T>.GetEnumerator();
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

For this implementation, we will set up a Solution together which will allow you to work Test-Driven.
