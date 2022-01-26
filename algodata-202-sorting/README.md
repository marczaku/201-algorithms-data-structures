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

<img width="299" alt="image" src="https://user-images.githubusercontent.com/7360266/150952165-82dba5e0-bf8f-4552-b0fc-b19c7960c2cd.png">

<details>
  <summary>Pseudo-Code</summary>
  
  ```
procedure selection sort 
   list  : array of items
   n     : size of list

   for i = 1 to n - 1
   /* set current element as minimum*/
      min = i    
  
      /* check the element to be minimum */

      for j = i+1 to n 
         if list[j] < list[min] then
            min = j;
         end if
      end for

      /* swap the minimum element with the current element*/
      if indexMin != i  then
         swap list[min] and list[i]
      end if
   end for
	
end procedure
```

</details>

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

<img width="652" alt="image" src="https://user-images.githubusercontent.com/7360266/150950573-ad4bbd70-d1d1-46f8-8796-45fec0a2c38e.png">


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

<img width="98" alt="image" src="https://user-images.githubusercontent.com/7360266/151051143-ed284542-baf8-4e84-81f4-950571754efd.png">

O(1) - constant
- behavior does not depend on size

<img width="95" alt="image" src="https://user-images.githubusercontent.com/7360266/151051214-82d5d43b-139a-4038-8b20-7db2fc1eeed9.png">

O(log n) - logarithmic
- grows by constant factor when problem size doubles
- base of the logarithm does not matter (it barely does)

<img width="92" alt="image" src="https://user-images.githubusercontent.com/7360266/151051318-3e9928ae-e2dd-4ea7-8255-b8b7745b9915.png">

O(n) - linear
- resources grow linearly with size
- i.e. size doubles -> resources double

<img width="96" alt="image" src="https://user-images.githubusercontent.com/7360266/151051370-497a6470-9aa9-45b7-8196-09420f5a407f.png">

O(n log n) - linear-logarithmic
- more disadvantageous than logarithmic, but better than linear for large size

<img width="94" alt="image" src="https://user-images.githubusercontent.com/7360266/151051422-ef778f14-df56-46ae-ba8a-7510e7e474dc.png">

O(n^2) - quadratic
- grows 4x when size doubles

<img width="94" alt="image" src="https://user-images.githubusercontent.com/7360266/151051512-650d4f3e-2b02-4a5e-9190-f2727b2141fb.png">

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

<img width="525" alt="image" src="https://user-images.githubusercontent.com/7360266/150951649-dc813f7d-43f3-475e-8751-d0c085571750.png">

<details>
  <summary>Pseudo-Code</summary>

```
procedure bubbleSort( list : array of items )

   loop = list.count;
   
   for i = 0 to loop-1 do:
      swapped = false
		
      for j = 0 to loop-1 do:
      
         /* compare the adjacent elements */   
         if list[j] > list[j+1] then
            /* swap them */
            swap( list[j], list[j+1] )		 
            swapped = true
         end if
         
      end for
      
      /*if no number was swapped that means 
      array is sorted now, break the loop.*/
      
      if(not swapped) then
         break
      end if
      
   end for
   
end procedure return list
```

</details>

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

<img width="626" alt="image" src="https://user-images.githubusercontent.com/7360266/150951414-d539f23f-a025-486b-a123-3a4ffccda763.png">

<details>
  <summary>Pseudo-Code</summary>

```
function partitionFunc(left, right, pivot)
   leftPointer = left
   rightPointer = right - 1

   while True do
      while A[++leftPointer] < pivot do
         //do-nothing            
      end while
		
      while rightPointer > 0 && A[--rightPointer] > pivot do
         //do-nothing         
      end while
		
      if leftPointer >= rightPointer
         break
      else                
         swap leftPointer,rightPointer
      end if
		
   end while 
	
   swap leftPointer,right
   return leftPointer
	
end function

procedure quickSort(left, right)

   if right-left <= 0
      return
   else     
      pivot = A[right]
      partition = partitionFunc(left, right, pivot)
      quickSort(left,partition-1)
      quickSort(partition+1,right)    
   end if		
   
end procedure
```

</details>

## Divide et Impera
> Divide and rule
- In Programming and Politics used as "Divide and Conquer".

<img width="264" alt="image" src="https://user-images.githubusercontent.com/7360266/150950995-a8964711-0085-4305-9925-a2e796e49925.png">


This is a technique phrased and used by Julius Caesar to conquer his enemies:
> Break up larger concentrations of power into pieces that individually have less power than the one implementing the strategy.

<img width="983" alt="image" src="https://user-images.githubusercontent.com/7360266/150951168-c584d386-3678-408c-bbbc-028e7e44964b.png">


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

## ToDo:
- Create a `static class TurboSort`
- Add a `BubbleSort(TurboList<int>)`-Method that sorts the List's elements using Bubblesort.
- Add a `QuickSort(TurboList<int>)`-Method that sorts the List's elements using Quicksort.
- Bonus: Add a `SelectionSort(TurboList<int>)`-Method that sorts the List using SelectionSort.
- Compare the Speed of the algorithms using `System.Stopwatch`.
  - For random, unsorted lists.
  - For reverse-sorted lists.
  - For sorted lists.
- Write Tests for your Sorting-Algorithms!
  - Do they sort successfully?
  - Do they sort in-place or out-of-place?
- Can you implement the Algorithms in a way that they work for your `TurboList`, but also a `System.Array`, a `System.Collections.Generic.List` and your `TurboLinkedList`? Hint: Use the Interface `System.Collections.Generic.IList<T>`
- Can you make the Algorithm Generic? Hint: Use the Interface `IComparable` or `IComparer`

# Searching

## Problem
We need to find the first item that matches a condition in a collection.
- list simply refers to a sequential container type
- the actual data type does not matter
- e.g. Static Array, List, Linked List
We have an equality function `F` that defines whether on element matches the one being searched
- it returns `true`, if it matches
- and `false`, if it does not

Example:
- we have: `red`, `orange`, `white`, `green`, `blue`
- `F` is defined as: Color does not contain RED component
- result: `green`

## Use Case
Anything, really: 
- Find the next available Quest
- Find the position of a Player
- Find The GameManager-Class
- Find the correct Translation of a UI Text
- Find the Path Separator `/` in an URL
- Find the lowest Health Character
- Find a certain Card in a Card Pile.

## Linear Search
The most obvious Solution and also implemented by Static and Dynamic Arrays and any other Collection that does not provide special functionality for Searching:

Iterate over every Element and check each item, whether it matches the condition. If it does, stop the iteration and return the given item.

<details>
  <summary>Pseudo-Code</summary>

```
procedure linear_search (list, value)

   for each item in the list
      if match item == value
         return the item's location
      end if
   end for
   return item_not_found
end procedure
```

</details>

### Complexity

Well, what could Linear Search's Complexity be? Exactly!
- O(n) (Linear)

For a size of `n`, in worst case, we need to compare each of the `n` items.\

Does it matter, whether we search from the Front or the Back?
- Yes and No. It depends on whether you want to have the first or last Element satisfying the condition. But usually, the item is just as likely to be found in the beginning as in the end as in the middle.

That's why often, two Linear Search methods are provided. Like `IndexOf` and `LastIndexOf`.

Is this a good Algorithm?
Well, first of all, it scales really badly, If you have 10.000 items, it needs to run up to 10.000 checks. But as long as the collection is not too large, or you don't need to search something too often, it's absolutely fine.

Other techniques to reduce the complexity include:
- Sorting (costs extra performance preparing, only worth it if you search a lot)
- Hashing (costs extra memory and a small bit of performance, worth it, if you can afford)
- Caching (extra effort programming, harder to refactor, but if you don't need to find something AGAIN, that's great)

## Binary Search

 There's a better way of finding an item. Under a certain condition.

### Let's Play A Game

 - I pick a number between 1 and 100.
 - You guess the number.
 - I tell you, whether my number is less than or greater than your guess.
 - We continue, until you find the correct number.
 - How many tries do you need?

### Prerequisites

- Not only the equality Function `F`, but also a comparison Operator `R` is needed.
- The collection needs to be sorted by `R`

Now, we can apply the same strategy of the Number Guessing Game:
- Go to the middle element
- If it is the wanted item -> bingo!
- If not, use the comparison Operator to find out, whether the searched item is in the lower or higher half of the collection
- Repeat in the remaining half
- Make sure to stop, if you've run out of items and could not find it :)

<details>
  <summary>Pseudo-Code</summary>

```
Procedure binary_search
   A ← sorted array
   n ← size of array
   x ← value to be searched

   Set lowerBound = 1
   Set upperBound = n 

   while x not found
      if upperBound < lowerBound 
         EXIT: x does not exists.
   
      set midPoint = lowerBound + ( upperBound - lowerBound ) / 2
      
      if A[midPoint] < x
         set lowerBound = midPoint + 1
         
      if A[midPoint] > x
         set upperBound = midPoint - 1 

      if A[midPoint] = x 
         EXIT: x found at location midPoint
   end while
   
end procedure
```

</details>

### Complexity

So, we half the size of the problem and then we half it again and again and again. How many of you remember from Intermediate Grade Math Classes, what function / expression expresses this?

Exponent:
- 2^0 = 1
- 2^1 = 2
- 2^2 = 2*2 = 4
- 2^3 = 2*2*2 = 8
- 2^n = 2*2*...2 (1 n times multiplied with 2)

Negative component:
- 2^-1 = 1/2 = 0.5
- 2^-2 = 1/2/2 = 1/4 = 0.25
- 2^-3 = 1/2/2/2 = 1/8 = 0.125
- 2^-n = 1/2/...2 (1 n times divided by 2)

Logarithm:
- log2 1 = 0
- log2 2 = 1
- log2 4 = 2
- log2 8 = 3
- log2 n = The exponent of 2 to give n

Or, to phrase it more approachably: How many times 8 can be divided by two.

Oh, that's interesting. How many times 8 can be divided by two. Isn't that also, what Binary Search does? Divide a problem by two for as many times as necessary?

That's why Binary Search's Complexity is:
- O(log2 n) (logarithmic)

How good is that? Turns out extremely good:
- log2 1000 = ~10
- log2 2000 = ~11
- log2 1,000,000 = ~20
- log2 2,000,000 = ~21

As you can see, whenever the problem size doubles, the complexity only increases by one. Nice.

### When to Use?
When you need to find items regularly and can not afford the extra memory that Hashing requires. Also, if the items come sorted, anyways.

## Bonus: Interpolation Search

If you were trying to find "Anna" in a sorted List of Names. Would you start searching in the Middle of the List?

If not, where would you start searching? Do you think that we can apply this to our Binary Search to make it even more efficient?

What are the requirements for this to work?

How do you think its Best Case, Average Case and Worst Case Scenarios perform compared to Binary Search?

We don't have time to cover this topic in class, but I hope this serves as inspiration to the endless possibilities of algorithms :)

# Keeping Order

Well, apparently, putting everything randomly into one place, one List, one Pile, one Box, is not always ideal. We can save a lot of time if we have order in our items.

In Germany, we say:
> Wer Ordnung hält, ist nur zu faul zum Suchen.

(Who's tidy is just too lazy to search)

This applies perfectly to tidy Data Structures!

But sorting the whole collection every time we've added one item sounds crazy. What we'd need is a Data Structure that allows us to more easily maintain the order. If you spill a cup of coffee, you would not start cleaning the whole apartment, right?

What we need, is a method that allows us to keep our sorted collection sorted.

## Sorted Insertion

Turns out, this is not so hard. I will spare you the details, but we can simply:
- Use Binary Search to find the item that we want to insert.
- If we find an item with the same value, just put our item right behind it.
- If we don't, put it where we last searched for it.

e.g.:
- given: `3, 5, 7, 9, 11`
- we search for `4` and first in the middle (`7`)
- it's smaller, so we look in the left half's (`3, 5`) middle (`3`)
- it's greater, so we look in the right half's (`5`) middle (`5`)
- it's smaller, the collection is empty now, so we insert it left of `5`:
- `3, 4, 5, 7, 9, 11`

### Complexity
- Search: Binary Search O(log2 n)
- Insert: O(n) - because we need to move all items on the right away.

=> Complexity: O(n)

Darnit. We were so efficient at finding the right place, but we lose the whole advantage because of moving stuff around when making space.

Could there be a better Data Structure for this?

# Trees