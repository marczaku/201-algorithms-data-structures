# Introduction

Now, after looking at sorting, we are ready ro return to searching. This chapter assumes that we have a simple collection in which items have an order and can be accessed via their indices.

This knowledge, you will be able to apply whenever you need to find an item matching a certain criteria.

You will understand the performance impact that searching will have and how you can reduce the impact.

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