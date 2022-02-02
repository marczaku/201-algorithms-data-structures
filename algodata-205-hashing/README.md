# Firstly, some Code:

Before I start boring you with all the details, let's first look at some code to demonstrate, what the upcoming technologies will enable us to do:

## HashSet

Imagine having a Graph of Nodes where each Node knows its neighbors.

We want to create a List of all Nodes by starting at one Node and walking all of its neighbors and so on until all Nodes have been collected.

But we need to make sure that we don't walk in Circles.
- e.g. A -> B -> A -> B -> A -> B -> ...
- or A -> B -> C -> A -> B -> C -> ...

Let's use a `HashSet` to keep track of what nodes we have walked already and not walk that node again. A `HashSet` does not allow duplicate values and will friendly remind us, if we inserted something twice:

```cs
public class Node {
   public Node[] neighbors;
}
```

```cs
public HashSet<Node> WalkAllNodes(Node startNode){
   // all nodes that we want to visit
   Queue<Node> toWalk = new Queue<Node>(){startNode};
   // all nodes that we have found
   HashSet<Node> visited = new HashSet<Node>{startNode};

   while(toWalk.Count > 0){
      var current = toWalk.Dequeue();
      foreach(var neighbor in node.neighbors){
         if(visited.Add(neighbor)){
            // returns true only, if the node was not inside already. A HashSet cannot have duplicates.
            // Which means, that we have a new node
            toWalk.Enqueue(neighbor);
         }
      }
   }

   return visited;
}
```

Have you noticed, that we did not need a `public bool visited;`-Field on the `Node`?

## HashMap

And now, we want to imagine having a ScoreBoard for all Players of a BattleRoyale-Match. A Dictionary (HashMap) allows us to store and retrieve one type of Value/information (the score) associated by its Key (the player):

```cs
public class Player{}
```

```cs
public class ScoreBoard {
   public Dictionary<Player, int> scores;

   public void AddScore(Player player, int score){
      scores[player] += score;
   }

   public int GetScore(Player player){
      return scores[player];
   }

   public int PrintAllScores(){
      // Using some LINQ Magic here to order the Pairs of Key and Value by their score, large to small
      foreach(var score in scores.OrderByDescending(kv => kv.score)){
         Console.WriteLine($"Player {score.Key} has Score: {score.Value}");
      }
   }
}
```

## Why not Both?

And now an example, in which we register all spawned buildings in the GameController, which groups the Buildings by their BuildingType. This allows our EvilBoi to pick a GoldStash as a target without needing to search through all buildings.

Even for a million buildings, this would find the 12 GoldStashes without breaking a sweat!

First, the `BuildingType`; a simple enum:
```cs
public enum BuildingType
{
   Tower,
   Bunker,
   GoldStash
}
```

Now, the `EvilBoi`, he'd like to find all buildings of type `BuildingType.GoldStash`. That must be expensive! D:
```cs
public class EvilBoi
{
   public Building target;
   void Update()
   {
      if (target == null)
      {
            var goldStashes = GameController.instance.AllBuildingsOfType(BuildingType.GoldStash);
            // TODO: Attack!
      }
   }
}
```

Okay, the `Building` has a `BuildingType` and registers itself with the `GameController`. Nothing special here.
```cs
public class Building
{
   // If the BuildingType can change, you need to Remove if (before changing) and Add it again (after changing)!
   public BuildingType type;
   void OnEnable()
   {
      GameController.instance.AddBuilding(this);
   }

   void OnDisable()
   {
      // TODO: Remove Again!
   }
}
```

The `GameController` is the class that does the magic: It uses a Dictionary in Combination with a HashSet to keep track of all buildings, grouped by their BuildingType:
```cs
public class GameController
{
   // TODO: Fix Evil Singleton!
   public static GameController instance;
   private Dictionary<BuildingType, HashSet<Building>> allBuildings = new ();

   public void AddBuilding(Building building)
   {
      if (!allBuildings.TryGetValue(building.type, out var buildings))
      {
         // if there's no building of this type, yet
         // we need to add a new collection for this type
         buildings = new HashSet<Building>();
         allBuildings.Add(building.type, buildings);
      }

      // add the building to the new or existing collection
      buildings.Add(building);
   }

   public IEnumerable<Building> AllBuildingsOfType(BuildingType buildingType)
   {
      allBuildings.TryGetValue(buildingType, out var buildings);
      // make sure, not to return null here
      // rather, return an empty array
      // officially saying: Count: 0
      return buildings ?? Array.Empty<Building>();
   }
}
```

Now, that you've understood, how impressive these collections can be, let's look at with what intention they were developed, what problems were faced on their way and what strategies can be used to overcome the issues:

# Introduction

If we have a Collection of a lot of items and we need to find items very often.
- e.g. because we need to remove them again
- or because we want to check, whether a certain item is already inside

Then a Linear Search has catastrophic performance: `O(n)`

Also, a Binary Search might not be so nice
- The complexity of `O(log n)` might be not good enough (even though it's extremely good)
- Or the items might not be clearly orderable (e.g. what order do you put Colors in? Or Transforms?)

# Inspiration: Static Array

In many ways, Static Arrays are the ideal structure performance-wise.
- If you know the index of the item that you want... e.g. 20
- Then you just receive it: `int item = array[20];`

Similar to how in an excel sheet you can quite quickly find row 22, since you know that every row is 5mm high, it should be at ca. 11cm distance to the top of the screen.

This is very efficient for your RAM. If you know the exact address of an object, it can directly jump there and read the value.

That's why Database-Tables love to use Numeric Primary Indices: Every Customer gets a Unique, Auto-Incremented id. And if Customer #2000 wants to place an order, the Database won't have to start searching, it knows exactly where the Customer-Information is stored and can directly look at entry #2000 without breaking a sweat.

But would it be nice to work with numeric IDs in our application all the time?

```cs
// check, whether the unit is queued to be spawned:
if(queuedUnits[unit.id] != null)
```

Also, what if we only need to store the Units #7987-#8217 in a small collection for a while?

|0|1|2|500|...|5000|...|7987|7988|...|8217|8218|...
|-|-|-|---|---|----|---|----|----|---|----|----|---
| | | |   |   |    |   |Unit|Unit|Unit|Unit|Unit|

That looks like a lot of wasted memory just to allow instant lookup. More than 97%...

# Generating a Unique Number for the Data

We could generate unique numbers for each possible information.

Let's play this through for Names:

e.g. 
- Letter A = 0
- Letter B = 1
- Letter C = 2
- ... 
- Letter Z = 23

And then convert each name to a number, e.g.:
- A = 1
- B = 2
- B = 2
- A = 2

We could just add these numbers and use them as an ID:
- A + B + B + A
- = 1 + 2 + 2 + 1
- = 6

Problem: Collisions:
- B + A + B + A
- = 2 + 1 + 2 + 1
- = 6

That's not nice, ABBA and BABA are not the same thing at all!

To allow for all kinds of combinations, e.g.:
- AAAA
- ABBA
- BABA
- ZZZZ

We'd have to "invent" a 24-based numeric system:
- A = 1 * 24^0 = 1
- B = 2 * 24^1 = 48
- B = 2 * 24^2 = 1152
- A = 1 * 24^2 = 13824
- = 15025

That's quite a big number already. 

`Maximilian Mustermann` would already need 24^20 = a number with 27 digits to create a unique index.

And what about all the special characters? UTF-16 allows 16 bit = 65k different combinations for each character.

=> Not possible.

This process would actually rather be called serialization or loss-free compression (a bad one at that). By using some specific rules, we are able to transform a name into a numeric format which we can store byte-by-byte on the Hard Drive and load it without problems again.

# Almost Unique might be enough

What if we were okay with the possibility of Overlaps between names like ABBA and BABA, though? Maybe, we could store all information in the array, but allow multiple items to be in the same place? An Array containing arrays so to say?

|0|1|2|...|6|-|
|-|-|-|---|-|-|
| | | |   |ABBA, BABA| |

I mean sure, it might require us to look at a second item in the rare case of a "collision", but 2 instead of 1 is still much better than searching through all names, right?

# Hashing

Hashing describes the process of taking arbitrary-sized information and compressing it into fixed-sized values.

This is a one-way road: A Hash can usually not be reverted back into its original value.

## Example

A Hashing function for Names could for example just take the first character (ASCII, 1 Byte) of a name and take its numeric value as a Byte:

<img src="https://c-for-dummies.com/blog/wp-content/uploads/2021/08/0801-figure1.png">

- Marc -> M -> 77
- Ansgar -> A -> 65
- Tom -> T -> 84
- Martha -> M -> 77

## Use-Cases
- Hash-Tables: Used to look up data in O(1)
  - ensures no duplicates
  - allows O(1) removal
  - allows O(1) check for whether sth. is inside
  - Pathfinding (visited Nodes)
  - Reactive Programming (update caching)
- Bloom Filter
- Geometric Hashing: Creating Grids for QuadTrees, Pathfinding, ...

(Variations)
- Checksums: Reduce a whole File to one Number. Used to find Corrupted Data, Blockchain
- Check Digit: Reduces a number to one digit. Used to detect faulty bank account numbers (the last digit is the Check Digit)
- Fingerprint: Reduce a whole data item (e.g. file) to one string. Used to Identify a File, find duplicates etc.
- Lossy Compression: Convert Data from one format to another one, which can be reverted to something close to the original.
- Randomization Function: Generates completely random numbers from slightly changing data (seed + index)
- Error Correction Code: Send some part of the data compressed as a redundant extra information. This redundant data allows the receiver to find Errors and often even fix them himself. e.g. QR-Codes, Live-Streaming
- Cipher: Convert information from one encoding into another to make it hard to reconstruct the original. But it is possible to reconstruct it, if you can reverse engineer the Cipher.

## Idea:
- calculate the location of an item from the item itself
- this creates an ordering system such that
- by looking at an item, you have a very good idea where it is located

## Vocabulary
- associative container
- data consists of ky and value
  - so-called key-value-pairs

Example: key = name, value = phone number

Items can be located by their key only
- hashing is just concerned with storing and finding keys
- the values just happen to be attached to the keys
- some collections, don't even require values

## Hash Function
A function that generates a hash value from the key. Assuming that the data is going to be stored in an Array, we need to generate an Array-Index from the key.

e.g.: Key = Name (`string`) => Hash Function => Hash (`int`)

Examples: 
- Add up all Characters
- Use first Character
- More realistic: Add up all characters, each bit-shifted and multiplied with a prime number

For the upcoming chapters, we will assume that we take a very simple one: The first letter an ASCII Character.

# HashSet

The following example will look at a certain .NET Collection as an example for Hashing. A HashSet. It utilizes Hashing to Allow for the following functions:

- Insert O(1)
- Search O(1)
- Delete O(1)

- No Order
- No Duplicates (Bug or Feature?)

## Hash Table
Now, we need a Hash-Table. Allocate an array that should be at least 30% larger than the number of expected items.

- It can still be resized later.
- But that costs extra performance.

For the upcoming example, we will assume a Hash table of size 15:

|0|1|2|3|4|5|6|7|8|9|10|11|12|13|14
|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|
||||||||||||||||||||||

## Insert

When you want to insert an Item, calculate its HashCode. And then its Hash Table index by using % HashTableSize.

If we want to insert "Marc" into the Hash-Table:

- Key: Marc
- Hash-Function: First Char ASCII (M) -> 77
- % 15 -> 2

We store Marc at Index 2 in Array.

|0|1|>2<|3|4|5|6|7|8|9|10|11|12|13|14
|-|-|:-:|-|-|-|-|-|-|-|-|-|-|-|-|
|||Marc|||||||||||||||||||

If we want to insert "Tom" into the Hash-Table:

- Key: Tom
- Hash-Function: (T) -> 84
- % 15 -> 9

We store Tom at Index 9 in Array.

|0|1|2|3|4|5|6|7|8|>9<|10|11|12|13|14
|-|-|:-:|-|-|-|-|-|-|:-:|-|-|-|-|-|
|||Marc|||||||Tom||||||||||||

## Search

We can find out, whether in Item already is in the Table using the same method:

If we want to check, whether "Nina" is in the Hash-Table:

- Key: Nina
- Hash-Function: (N) -> 78
- % 15 -> 3

|0|1|2|>3<|4|5|6|7|8|9|10|11|12|13|14
|-|-|:-:|-|-|-|-|-|-|:-:|-|-|-|-|-|
|||Marc|||||||Tom||||||||||||

Not found! Nina is not stored in the collection!

Let's see, whether Marc is in the Hash-Table:

- Key: Marc
- Hash-Function: First Char ASCII (M) -> 77
- % 15 -> 2

|0|1|>2<|3|4|5|6|7|8|9|10|11|12|13|14
|-|-|:-:|-|-|-|-|-|-|:-:|-|-|-|-|-|
|||Marc|||||||Tom||||||||||||

Turns out, he is, hurray!

## Hash-Collision

Of course, the Hash-Function cannot deliver an absolutely unique result for every name!
- but it should try to spread keys as evenly as possible

Let's look at a collision. We want to insert Elise into the Hash-Table:

- Key: Elise
- Hash-Function: First Char ASCII (E) -> 69
- % 15 -> 9

|0|1|2|3|4|5|6|7|8|>9<|10|11|12|13|14
|-|-|:-:|-|-|-|-|-|-|:-:|-|-|-|-|-|
|||Marc|||||||Tom||||||||||||

Oh no! It turns out Tom is already blocking Slot 9 of the Array. What now?

## Collision Resolution

We need to find a method for solving Collisions in our Hash-Tables. All of the upcoming methods mean one change, though: We can not rely an on object in a slot being the one that we're looking for just by it being there. We need to also confirm that it is the object that we're actually looking for:

```ts
procedure insert(item){
   hash = item.GetHashCode()
   hash %= buckets.length
   if(hash buckets[hash] == item)
      return // already inside. we're done.
   
   // resolve hash conflict
   // ...
}
```

### Chaining

We could make every slot an Array of Objects. This allows us to store multiple values in one Slot.

Now, if a slot has more than one item, we need to search through all entries of a slot.

This solution is okay, but inefficient and means a lot of extra Memory Allocations (extra Array for each slot, extra Array Buffer, ...)

Pro:
- can postpone resizing for a long time
- very easy to implement

Con:
- like linked list
  - bad cache performance
  - needs more memory

Could also a Binary Tree instead of a list
- only reasonable, if the list becomes very long

### Open Addressing

Store all entries directly in the Hash Table.
- if the slot is already used, just pick another slot using a `Resolution Strategy`

Simple `Resolution Strategy`: use one of the next two slots. If they are both in use as well, resize the table.

Addressing Strategies include:
#### Linear Probing
- use next x slots
- disadvantage: prone to clustering, some areas of the table will be "full' before others

#### Double Hashing
- calculate alternative slot using `Second Hash Function`
- prevents clustering
- disadvantage: bad cache performance, additional hash calculation

#### Quadratic Probing
- calculate alternative slots using quadratic function
- behavior somewhere between Linear Probing and Double Hashing

### Coalesced Hashing
- Combines Chaining and Open Addressing
- Entries stored directly in Table
- But receive index of next entry
- Or `-1` if there is none:

Index|0|1|2|3|4|5|6|7|8|9|10|11|12|13|14
|-|-|-|:-:|:-:|-|-|-|-|-|:-:|:-:|-|-|-|-|
Key|||Marc|||||||Tom||||||||||||
Next|||-1|||||||-1|||||||||||

Now, let's look at a collision again. We want to insert Elise into the Hash-Table:

- Key: Elise
- Hash-Function: First Char ASCII (E) -> 69
- % 15 -> 9


Index|0|1|2|3|4|5|6|7|8|>9<|10|11|12|13|14
|-|-|-|:-:|:-:|-|-|-|-|-|:-:|:-:|-|-|-|-|
Key|||Marc|||||||Tom||||||||||||
Next|||-1|||||||-1|||||||||||

Okay, of course Tom is still here, but let's just use some Addressing Strategy to find a new Address (e.g. 2 stops to the right). And then set the Next-Pointer to the new address:

Index|0|1|2|3|4|5|6|7|8|9|10|>11<|12|13|14
|-|-|-|:-:|:-:|-|-|-|-|-|:-:|:-:|:-:|:-:|-|-|
Key|||Marc|||||||Tom||Elise||||||||||
Next|||-1|||||||11||-1|||||||||

Very efficient and used by .NET!
- But additional memory usage

### Cuckoo Hashing
- Two Hash Function
- Meaning, for every key there are two possible slots

- If a slot is already taken
- Kick out the old entry
  - and put it into its alternative slot
  - if that slot is taken as well...
    - continue kicking that one out etc.

(The European cuckoo does this with eggs)

Very efficient and no Memory Overhead

### Perfect Hashing

Find a perfect Hashing Function that produces no collisions
- whole data set needs to be known
- calculation takes time
- space required to store hash function can reach size of data set
- makes sense only for large data set that does not change often

### Dynamic Perfect Hashing

Perfect Hashing with unknown data set
- possible, but very complicated

## Delete

We've not talked about deleting objects, but it's again the same as Searching: Find it and then remove it if you've found it.

Make sure, though, that if it has a `next`-Pointer, that you move the `next`-Object to the original slot. Maybe, you should only move the last one in the chain of `next`?

## Resizing

Hashing only works well if a certain percentage of the Hash Table is empty

Therefore, it probably has to be resized at some point.

Easiest way: `Rehashing`
- allocate new, larger hash table
- insert all elements into new Table
- not a copy operation: hash value will change
  - 57%15 = 12, but 57%30 = 27!

## Hash Function and Table Size
A Good Hash Function
- produces little collisions
- distributes entries evenly across table
- is fast to compute

Table Size should be a prime Number
- has no dividers
- prevents cycles and clustering

## Summary
Pro:
- Often much faster than binary search

Con:
- uses more memory, always wastes memory
- hash calculation has some cpu overhead that only pays off if there's enough entries

## Useful Example

This is very useful in order to collect all items with a shared meaning in a collection and quickly find out, whether an object is in there.

This basically allows you to add a `bool`-Property to a class that you can't change.

Imagine the following class in a Strategy Game with Fog of War:

```ts
class Building
   position: Vector2
```

We want to know, which buildings are known to a player. If a player happens to find that building, we want to remember that the player has seen this building. So, each time he finds it, we want to make sure that it's already (and still) in the collection.

We could make this change;

```ts
class Building
   position: Vector2
   seenByPlayer: bool
end class

procedure OnBuildingSeen(building)
   seenByPlayer = true
end procedure

procedure CheckBuildingSeen(building)
   return building.seenByPlayer
end procedure
```

But then again, is it really the Building's Property? What if there's multiple players?

We could instead do this:


```ts
class Player
   seenBuildings: HashSet<Building>
end class

procedure OnBuildingSeen(building, player)
   player.seenBuildings.Insert(building)
end procedure

procedure CheckBuildingSeen(building, player)
   return seenBuildings.Contains(building)
end procedure
```

Can you see, how the Attribute:
- each Building has a `bool` which is `true` or `false`

Is sort of the same as:
- each Building is in a collection `true` or not `false`

And allows us to somewhat "extend" an existing class without changing it?

# HashMap

A HashMap is very similar to a HashSet, but while a HashSet only allows unique values, the HashMap allows duplicate values. How does it do that? Let's return to our phone book example:

Index|0|1|2|3|4|5|6|7|8|9|10|>11<|12|13|14
|-|-|-|:-:|:-:|-|-|-|-|-|:-:|:-:|:-:|:-:|-|-|
Key||||Marc||||||Tom||Elise||||||||||
Value||||070847780||||||0781717122||189174748||||||||||
Next||||-1||||||11||-1|||||||||

In other words: it stores Keys AND Values for each entry. This allows you to map ONE key to ONE Object. Since multiple keys can map to the same Object, you can argue that you can map N keys to ONE Object:

Index|0|1|2|3|4|5|6|7|8|9|10|>11<|12|13|14
|-|-|-|:-:|:-:|-|-|-|-|-|:-:|:-:|:-:|:-:|-|-|
KeyCode||||KeyCode.Ctrl||||||KeyCode.Space||KeyCode.Alt||||||||||
Value||||Fire||||||Fire||Fire||||||||||
Next||||-1||||||11||-1|||||||||

This does not only mean, that we can have duplicate Values, but also, that the key and the value can be two completely different things.

## Operations:

- Insert `O(1)`
- Update `O(1)`
- Search `O(1)`
- Remove `O(1)`

## Use-Cases:

Whenever you want to look up one value (or multiple, by making the value a List) by some key:
- The player to a certain ID
- An Action to a certain input
- GameObjects to a certain Tag
- GameObjects to a certain component
- Event Listeners to a certain Event Type
- All players in a certain league

## Dictionary

In C#, the Generic version of this collection is called a `Dictionary<TKey, TValue>` and it can be used like this:

```ts
class Player
   league: id
   clan: id

playersByLeague: Dictionary<id, player>
playersByClan: Dictionary<id, player>

procedure OnPlayerLoaded(player)
   if(not playersByLeague.hasKey(player.league))
      list = new List<player>
      list.Add(player)
      playersByLeague.Add(key: player.league, value: list
   else
      list = playersByLeague.Get(key: player.league)
      list.Add(player)
   end if
end procedure
```

## Useful Example

Again, we can extend a class this way. And not only by a `bool`-Value, but by any value:

Can you see, how the Attribute:
- each Player has a field of type `T` which has any value of type `T`

Is sort of the same as:
- each Player can be the key in a collection with a value of type `T`

And allows us to somewhat "extend" an existing class without changing it?

# Exercise

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

Bonus:
- `Dictionary<TKey, TValue>`
- Coalesced Hashing
- Write your own class with a Hashing-Function