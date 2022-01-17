# Introduction

Algorithms and Data Structures are a very important field in Computer Science.

## Data Structures
A Data Structure is the organization, management and storage format of your Data / State.

We have already learned that our application's state can be described by all information that's currently stored in the application's memory.

And we have also learned that a Program, according to the `IPO-Model`, can be broken down into Input - Processing - Output. Which means, that Data or State is put into a Program (or a sub-program, like a method), some processing happens in between and then Data or State is put out of a Program, which usually is being stored somewhere and thereby changing the application's state.

Some Structures are more efficient for reading, some for writing, some for adding, some for removing, some for searching, some for resizing, ...

Choosing the correct Data Structure enables efficient access and modification. Using the wrong Data Structure can easily make a difference between a method needing 10ms or 5 minutes.

Examples include `Stack`, `Queue`, `LinkedList`, `HashSet`, `Graph`, `BinaryTree` and many more.

## Algorithms
An Algorithm is a sequence of defined instructions used to solve a certain class of problem or perform a computation.

Usually, Algorithms are language-agnostic and can be written down as logical decision-trees or Pseudo-Code on paper.

Examples include `Math.Max`, `Dijkstra`, `Floyd`, `Sudoku-Solver`, `BinarySearch`, `QuickSort`, but also Finding an available Uber Driver near you, Recommending a TV Show on Netflix and Finding Websites on Google among many others.

# Goal

A profound understanding of common Data Structures and Algorithms as well as a self-written implementation of some of them.

The ability to choose the correct data structure for your application data.

The ability to break down a computation or problem into one or more routines that can solve it.

# Overview
## Module 201: Collections
Collections are used to store and group some variable number of data items that have some shared significance to the problem being solved and need to be operated upon together in some controlled fashion.

This module introduces you to a variety of collections that are used in several, most common contexts. As well as the `IEnumerable`-Interface, which allows easy iteration over your collection using `foreach`.
## Module 202: Sort & Search
Having unsorted collections of data will become unacceptable for growing problems. Searching for the correct item in a list of 10 might be okay, but searching for the correct citizen in a country of 10 Million would take a looooong time.

Finding things becomes more easy, if things are sorted. Like in a phone book. But sorting information is a whole other problem. That's why we'll explore both in this module.
## Module 203: Hashing
If you have a really large amount of data that you need to find something in, or you need to do it very frequently, searching through a Phone Book won't cut it anymore. You need something like bookmarks, which allows you to find the correct page right away.

This is, what the use of Hashes and Hash Tables can accomplish. It comes with some extra work and computational overhead, but it allows instantaneously finding something in a collection.
## Module 204: Pathfinding
One of the most traditional problems that's solvable through algorithms is pathfinding. What's the closest connection between you and your work place? What public transport should you use to get to the movie theater?

We'll first have a look at what Data Structure can hold all of the required information. Then, we'll look at a few naive approaches to this problem and where their limits lie, before then looking at an algorithm that can solve this problem in a semi-easy, successful way.
## Module 205: Game Trees
Here, we'll apply our Pathfinding knowledge to other problems and find out that Game States can also be represented in a similar Data Structure as our Path Information. And that this in conclusion allows us to use algorithms to solve problems and find solutions in Game States. This knowledge will enable us to solve puzzles or write AI players for our Games.

## Assignment
The assignment for this course will be distributed through Google and GitHub Classroom at the end of this course.