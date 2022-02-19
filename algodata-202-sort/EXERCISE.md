# Mandatory
- Create a `static class TurboSort`
- Add a `BubbleSort(TurboList<int>)`-Method that sorts the List's elements using Bubblesort.
- Add a `QuickSort(TurboList<int>)`-Method that sorts the List's elements using Quicksort.

# Bonus (Easy)
- Bonus: Add a `SelectionSort(TurboList<int>)`-Method that sorts the List using SelectionSort.

# Bonus (Easy)
- Compare the Speed of the algorithms using `System.Stopwatch`.
  - For random, unsorted lists.
  - For reverse-sorted lists.
  - For sorted lists.

# Bonus (Intermediate)
- Write Tests for your Sorting-Algorithms!
  - Do they sort successfully?
  - Do they sort in-place or out-of-place?

# Bonus (Hard)
- Can you implement the Algorithms in a way that they work for your `TurboList`, but also a `System.Array`, a `System.Collections.Generic.List` and your `TurboLinkedList`? Hint: Use the Interface `System.Collections.Generic.IList<T>`

# Bonus (Hard)
- Can you make the Algorithm Generic? Hint: Use the Interface `IComparable` or `IComparer`