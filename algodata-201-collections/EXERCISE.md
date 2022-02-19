# IEnumerable
- Create an Array with 5 numbers.
- Assign it to a Variable of Type `IEnumerable`.
- Use `GetEnumerator()` and a `while`-Loop to print all elements of the `IEnumerable`-Variable.
- Can you also add up all numbers of the List using `IEnumerable` and then print the sum? If not, then what do you need to change?

---

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

# Stack

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

# Queue

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
  
- Check out Ring-Buffers.
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