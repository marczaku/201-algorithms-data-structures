# Mandatory
Guess what? Implement a `TurboHashSet<T>`.

You will need:
- `bool object.Equals(object other)`
- `int object.GetHashCode()`

Which conveniently are available on ALL classes in .NET.

A Collision Resolution Strategy.
- Might want to go for the most "stupid" one for now: try the next three slots

A Buffer Size Strategy. 
- Might want to go for 30% extra Space, as mentioned earlier

The following Methods:
- `bool Insert(T item)` returns `false`, if it was already inside, else it adds it and returns `true`
- `bool Exists(T item)` returns true, if it is inside
- `bool Remove(T item)` returns `false`, if it was not inside, else it removes it and returns `true`

Internal Method:
- `Resize` which will increase the buffer size, if necessary (Bonus)

# Bonus (Hard)
- `TurboDictionary<TKey, TValue>`

# Bonus (Hard)
- Coalesced Hashing

# Bonus (Intermediate)
- Write your own class with a Hashing-Function