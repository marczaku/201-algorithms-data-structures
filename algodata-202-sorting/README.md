# Introduction

Imagine, we had a List of 1 Million citizens. And we had to find one specific one from that collection. We know the citizen's name. How long would it take us to find that citizen?

Depends on where in the List the citizen is placed. And from where we start searching. Let's assume that it takes 1ms to look at one entry of the List and compare the entry's name with the name that we're looking for.

If we were to start looking from the front, then either:

1. We're lucky (Best-Case Scenario) and it's the first entry of the list. It would take us 1ms to find him.
2. We're unlucky (Worst-Case Scenario) and it's the last entry of the list. It would take us 1 Million ms to find him.
3. We're averagely lucky (Average-Case Scenario) and it would take us about 0.5 Million ms to find him. (Little Quiz: what's the exact average?)
<details>
  <summary>Solution</summary>
  
- 500,000.5ms
</details>

What do you think is the most important number of these?

<details>
  <summary>Solution</summary>
  
- All of them, depending on the situation. But often, the Worst-Case-Scenario is the most important one.
</details>

What would make finding one of these 10 Million citizen easier?

<details>
  <summary>Solution</summary>
  
- If the List was sorted. Like in a Phone Book. If you were searching for a person named Peter, you could check the middle of the Phone Book and see, what letter is printed there. if it's T, then you check the left half of the book, if it's N, then you check the right half of the book etc.
</details>

How long would it take a smart algorithm to find ANY of these 1 Million Citizen if they were sorted?

<details>
  <summary>Solution</summary>
  
- A maximum of 20ms. NO WAY!! Yes, indeed.
</details>

That's how powerful sorted lists can be. And smart Algorithms. Now, that you have understood the importance of these, let's look at how we can get our stuff sorted.

# Algorithms
An algorithm is a procedure that fulfils a few characteristics:
- **Unambiguous**. Each of its steps should be clear and each input should have one expected output.
- **Input**. Can be 0 or more.
- **Output**. Can be 1 or more.
- **Finiteness**. It should run forever.
- **Feasibility**. It should work with given resources.
- **Independent**. The steps are independent of any programming code.

Since Algorithms always return something, we say that it solves a problem.

And the problem can often be of variable size.

## Samples:
- Search, Sort, Insert, Update, Delete

You will see that most algorithmic problems can be reduced to one of these, actually.

=> We have written Algorithms already. No Magic. :)

# Sorting

## Problem
We have a list of data to be sorted.
- list simply refers to a sequential container type
- the actual data type does not matter
- e.g. Static Array, List, Linked List
We have a comparison operator `R` that defines a linear order of elements
- for two elements `a!=b`, `aRb` or `bRa` must hold true, not both
- e.g. operator `<`: if `a!=b`, then either `a<b` or `b<a`, not both

Example:
- we have: `5, 3, 2, 4, 1`
- operator: `>`
- result: `5, 4, 3, 2, 1`

## Selectionsort
Let's start with an example right away. Probably the most intuitive one:
- find the smallest element in the list
- swap the smallest element with the first
- continue the previous steps for the remaining list until the list is empty

Is this a good algorithm?

## Classifying Algorithms

Many algorithms work with a variable size of data. 10, 100, 1000, 1000000, ...

How can we classify algorithms in terms of efficiency?

### Time Factor
- How much time does it take from start till end?
### Space Factor
- How much memory does it consume?
### Fluctuation
- Both have Min, Max and Average values.
- Also called Best Case, Worst Case and Average Case Scenarios.
### Complexity
Both Time and Space Factor can be split into two parts:
- Constant Part. Stays the same, no matter the size of the problem.
  - e.g. a `for` loop always needs **one** `int`. No matter the size.
- Variable Part. It scales with the size of the problem.
  - e.g. some sorting algorithms need a second array, same size as the array to be sorted.

This field is called:
> Asymptotic Analysis

### Greediness

Imagine the following problem: We need to write an algorithm that hands out coins to the customer. The type of coins are 1,2,5,10.

A greedy algorithm would always return the largest coin first. Example: 23:
- Give: 10, Remaining: 13
- Give: 10, Remaining 3
- Give: 2, Remaining: 1
- Give: 1, Remaining: 0

Result: 10, 10, 2, 1

=> Does this Algorithm work for any amount of money given?

What, if thee type of coins were 1, 7, 10?

Example: 15:
- Give: 10, Remaining: 5
- Give: 1, Remaining: 4
- Give: 1, Remaining: 3
- Give: 1, Remaining: 2
- Give: 1, Remaining: 1
- Give: 1, Remaining: 0

Result: 10, 1, 1, 1, 1, 1

Wouldn't 7, 7, 1 be the best result?

This is, why it's called a greedy algorithm. It always tries to go for the most obvious solution first. The one, that brings us to the goal more efficiently. Through that, it might discard the best solution, though.

This is called the difference between a Local and Global Optimum. Greedy Algorithms fall into a trap of a Local Optimum, where a solution seems to be the best given a limited field of vision, but there might be a Global Optimum that's even better.

Analogy: You want to visit a friend next town and you wonder how to get there efficiently. You check a few options and notice that taking the bike will greatly speed you up and get you there much more quickly on a small forest route that leads there almost directly. But you forget to consider walking the opposite way to the train station, which will bring you there much faster.

Are greedy algorithms bad? No, they're essential! Algorithms that are not greedy often need to analyze every possible combination of a problem. In chess, that number is somewhere around 
> 999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999 possible moves. 

That's 10^110. In comparison, the amount of atoms in the observable universe is only estimated at 10^80. To be fair, the amount of typical chess moves is "only" around 10^40.

But anyways, that number of possible combinations is literally impossible to calculate. Even 1 Million people on 1 Million Earths in 1 Million Galaxies, each using 1 Million Computers and looking at 1 Million states per second would still need 10^72 years. In comparison, the age of the universe is only 10^9 years. Just to give you some idea of what complexity we're talking about here. So, sometimes, greed is good after all :D

### Stability
Some sorting algorithms maintain the order of items of the same value, while others don't.

e.g. imagine an algorithm that only sorts by the first digit:
- given: `7A, 5B, 5A`
- operator: `<`
- output: `5B, 5A, 7A`

Here, the order of both items with digit `5` has been maintained. This would be called a stable sorting algorithm.

Some algorithms might end up with:
- output: `5B, 5A, 7A`

Here, the order of items with the "same" value has changed. This would be called an unstable sorting algorithm.

### In-Place
In Sorting Algorithms, for them to be called In-Place, the Variable Part of the Space Complexity should be 0. As in, the algorithm should not consume Memory relative to the size of the collection. This can be very important for large collections.

Remember the software crashing when calculating the sum of 1.000.000.000 Numbers when they were an array?

Opposite: Out-of-place sorting algorithm.

## Classifying Selectionsort
### Unstable
- consider: 133234
- stable implementation is possible, but at extra costs (how?)
### In-place
- memory required only for loop counter `i` and swap space for one element. The swap space can be avoided, but at extra costs (how?)
### Complexity
- For `n` elements:
- Swap Operations (worst case): `n-1`
- Compare operations:
  - (n-1) + (n-2) + ... + 2 + 1
  - (n^2)/2 - n/2
  - O(n^2)

## Big O Notation
The Big-O Notation looks at the upper bound of a function. Common complexities are:

O(1) - constant
- behavior does not depend on size

O(log n) - logarithmic
- grows by constant factor when problem size doubles
- base of the logarithm does not matter (it barely does)

O(n) - linear
- resources grow linearly with size
- i.e. size doubles -> resources double

O(n log n) - linear-logarithmic
- more disadvantageous than logarithmic, but better than linear for large size

O(n^2) - quadratic
- grows 4x when size doubles

O(2^n) - exponential
- grows 2x when size increases by 1

## Evaluation of Sorting Algorithms
Like most things in programming, there's no clear winner. You can look at:

- the memory requirements. Can you afford extra memory consumption? This can give you speed gains.

- the time behavior of the algorithm
  - number of comparisons
  - number of swaps
  - depending on problem size

Which one is more expensive? Comparison or Swaps?

=> Depends on your data!

## Special cases
How does an Algorithm behave
- in best case?
- in worst case? (and how likely is that?)
- the average case?

What if the list
- is already sorted?
- almost sorted? 
- sorted in reverse order?

## Bubblesort
- extremely naive sorting algorithm
  - mostly used as a bad example
- compares two neighboring elements
- swaps them if necessary
- does this for the whole list
- repeats above steps until no more swaps happen in one run

### Optimization:
- largest element is guaranteed to be at the correct position after first pass
- can decrement sorting area size after every pass

## Classifying Bubblesort
### Stable
- better than Selectionsort
### In-place
- similar to Selectionsort
### Complexity
- For `n` elements:
- Swap Operations (worst case): 
  - 0.5*(n^2-n)
  - O(n^2)
  - worse than Selectionsort, which had O(n)

- Compare operations (worst case):
  - same as swap operations
  - same as Selectionsort

- BUT optimal best case!
- Much better than Selectionsort. How much?

## Quicksort
- very fast algorithm

Idea:
- divide and conquer
- split the problem in half
- solve each half independently
- combine the results
- can split as often as necessary

## Divide et Impera
> Divide and rule
- In Programming and Politics used as "Divide and Conquer".

This is a technique phrased and used by Julius Caesar to conquer his enemies:
> Break up larger concentrations of power into pieces that individually have less power than the one implementing the strategy.

How To Eat an Elephant?
> One bite at a time.
- For us it's "One Byte at a time". Hehe.

These wisdoms both follow one idea: Don't get overwhelmed by one giant problem. Either 
- split it up into multiple small problems and solve them one at a time
- or just get at it bit by bit in a row

These wisdoms apply to almost any other field of life, too.

I often like to think of programmers as problem-solvers. The techniques that we apply at solving software problems can often be applied to other areas as well. You need to solve a problem? Find an algorithm. Sounds nerdy, but it's sort of true.

## Other Sorting Algorithms
### Heapsort
- O(n log n)
- improves Selectionsort

### Mergesort
- O(n log n)
- like Quicksort, but stable
- but requires O(n) memory

### Timsort / Introsort
- O(n log n)
- version of Quicksort that falls back to other algorithm in bad cases
- fastest known stable/unstable sorting algorithms