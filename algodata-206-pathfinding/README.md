# 1. What is Pathfinding?

## 1.1 Traditional, 2d Grid

Pathfinding can solve this very traditional 2d grid maze problem:

<img src="https://en.scratch-wiki.info/w/images/Pathfinding.jpg">

You want to move from A to B on the shortest Path in a known grid of walkable and unwalkable cells. This path needs to be found (hence, Pathfinder), so we can walk it.

## 1.2 2d NavMesh

Your world could also be more complex than a simple grid, though. Objects of all shapes and sizes:

<img src="https://forum.unity.com/proxy.php?image=https%3A%2F%2Fi.gyazo.com%2F917507b346b0dcd98ce4cf20dd78b8a6.png&hash=affdb5a61a35ea8d1f164a73a4e61389">

In this case, you need to scan the area and find out, where you can walk and where we can't before finding a path from A to B.

## 1.3 3d NavMesh

Your game could also be 3-Dimensional:

<img src="https://docs.unity3d.com/550/Documentation/uploads/Main/NavMeshCover.png">

This will make a big difference in how you can actually scan the area. In a 2d-game, you can quite easily tell from above, what area is walkable, but in 3d, many parameters like walkable slope angles and walkable step height make the scan process more complicated.

## 1.4 Advanced Paths

Often, characters can do more than just walking to navigate environment:

<img src="https://doy2mn9upadnk.cloudfront.net/uploads/default/optimized/4X/6/c/5/6c55394420dec94acc9143d96b713b5bb57dd623_2_689x349.png">

They could maybe Jump, Fall, Open Doors, Climb Ladders, Crouch, Swim, Dive, Destroy environment, use a Vehicle, a Portal, a PowerUp or much more to navigate the Area. The Pathfinding algorithm needs to be able to consider these paths.

## 1.5 Dangerous Paths

Some paths could be dangerous, or limited, or slow you down:

<img src="https://developer.roblox.com/assets/blte5791b887ee3412c/Pathfinding-Paths-Shortest-Best.jpg">

The algorithm should make sure not to drown, consider whether swimming a shorter path is faster, or walking a longer path. It might have to weigh the options and consider walking into Open Fire to get to a goal or prefer to walk a long route in cover.

## 1.6 Limited Range of Motion

What if your character sits in a car?

<img src="https://3.bp.blogspot.com/-9mSvNWTvCIM/VwIQVT8lQMI/AAAAAAAAT1U/G3bPT-N1HfUumimyp_pbrK88L5kF_nw0g/s1600/self-driving-tesla-5.png">

Cars have a limited range of motion, they cannot rotate on the spot. They need to find paths that allow them to get around corners.

## 1.7 Extended Range of Motion

What if your character can fly?

<img src="https://preview.redd.it/6wodo82eydg41.png?width=960&crop=smart&auto=webp&s=805725d0a96aeba8ae27eb09580ff1c3a6dcb461">

Without being gravity-bound, the navigation is not as simple anymore. Surfaces have a local 2-dimensional space. And as long as we only walk on surfaces, 3-dimensional problems can be reduced to 2-dimensional ones. But what if we can truly navigate through a room of options?

## 1.8 Limited Knowledge

There might be Unexplored Areas or Fog of War

<img src="https://blog.gemserk.com/wp-content/uploads/2018/08/Stage1_fadeout_test5_with_objective_scaled.png">

This requires your Algorithm to "work with what it knows", possibly adapt its strategy as it "gains more knowledge".

## 1.9 Huge Amount of Data

Your world might be really, really huge

<img src="https://p.turbosquid.com/ts-thumb/1Z/JqcTPF/pf/hznew_0001/jpg/1606624876/1920x1080/fit_q99/81b598caca7b4d603c22a3a4884b2efe83f7843c/hznew_0001.jpg">

So you'll have to optimize your algorithms to make sure that you don't try to find a path one centimeter at a time.

## 1.10 Not quite a path, or...?

What, if your game looks like this?

<img src="https://image.winudf.com/v2/image1/Y29tLmdzci5ucHV6emxlX3NjcmVlbl8yXzE1OTk3MjM0NjVfMDI3/screen-2.jpg?fakeurl=1&h=360&type=webp">

Or this?

<img width="200" src="https://upload.wikimedia.org/wikipedia/commons/thumb/a/a6/Rubik%27s_cube.svg/960px-Rubik%27s_cube.svg.png">

Or this?

<img src="https://static.wikia.nocookie.net/essentialsdocs/images/7/70/Battle.png/revision/latest/scale-to-width-down/256?cb=20190219202514">

These are in fact all Pathfinding Problems, as we will see. You want to get from A (the game as it is right now) to B (you have solved the puzzle / won the battle) and ideally want to follow the shortest or safest path.

## 1.11 Other Variations
- you don't want the shortest, but the longest path (avoid losing)
- Yen's k-shortest Paths: you want the n shortest paths to compare them (alternative routes)
- All Pairs Shortest Path: you want the shortest path that walks the whole area (cleaning robot, patrolling AI)
- Minimum Spanning Tree: you want the shortest path that connects multiple points (travel planner, multiple win requirements)

Okay, that's enough. I hope that you can see that Pathfinding is equally complex and important.

# 2 Bug Algorithms: Pathfinding on the Move

The first set of Pathfinding Algorithms all start walking first and then react to their obstacles as they hit them. This might sound stupid, but it is very low in initial resource costs (the AI does not need to plan first), can be an interesting mechanic (an AI that's supposed to explore) and sometimes it's the only possible approach (e.g. cleaning robots don't get a map of your apartment).

It can also be used in combination with smarter Pathfinding Algorithms to allow your units to begin walking while waiting for the path calculation to be finished as a background process.

For all the upcoming samples, we will assume:
- a static 2d terrain
- a square grid
- every grid cell is either clear or blocked
- terrain is completely known
- `start` and `target` cells ar known
- can receive a cell's `neighbors`

## 2.1 First off, walk towards target

```
current_cell = start
while not at target
   neighbors = current_cell.neighbors
   best_neighbor = closest_to_target(neighbors)
   if best_neighbor is walkable
      current_cell = best_neighbor
   else
      error: "I'm stuck! Help!"
   end if
end while
```

This algorithm can nicely navigate to its target in an Open Field. But the slightest obstacle... and it will fail. 

## 2.2 If blocked, find a way around

```
current_cell <= start
while not at target
   neighbors <= current_cell.neighbors
   best_neighbor <= closest_to_target(neighbors)
   if best_neighbor is walkable
      current_cell <= best_neighbor
   else
      current_cell <= run_evasion_strategy(current_cell, best_neighbor)
   end if
end while
```

Okay, obviously, we need to find a way around the obstacle. But what could this strategy look like?

### 2.2.1 Evasion Strategy 1: Random Bounce

<img width="329" alt="image" src="https://user-images.githubusercontent.com/7360266/152982906-7ac859aa-38c9-480b-b825-144035c49224.png">



Our first strategy will be very naive: we just walk a random direction and hope that it brings us around the obstacle at some point.

```
candidates <= neighbors - best_neighbor
current_cell <= random from candidates
```

- not very smart
- might bounce back and forth a lot
- but eventually, it will often find its target

<img width="315" alt="image" src="https://user-images.githubusercontent.com/7360266/152982991-5f2812f8-5f78-43d1-8d43-08110fec914e.png">


- unfortunately, it gets stuck in concave obstacles
  - because it needs to walk "backwards"
  - but afterwards, it will always the previous cell as a "good candidate" again
- it will not notice, if there's no way

### 2.2.2 Evasion Strategy 2: Simple Tracing

<img width="315" alt="image" src="https://user-images.githubusercontent.com/7360266/152983015-9e8e318f-23cc-465d-abcf-412d14dcd3e3.png">


This time, we'll apply a classic strategy used in Mazes: we circumvent the obstacle by following the wall until we can freely walk in the original direction that we were blocked in. In this case, we'll always walk the left way and keep our "right hand" on the wall:

```
blocked_direction <= direction from current_cell to best_neighbor
while(current_cell + blocked_direction is not walkable)
   current_cell = follow_the_wall()
```

- Works nicely, even for concave obstacles

<img width="315" alt="image" src="https://user-images.githubusercontent.com/7360266/152983057-8197d090-63ba-403a-99f0-3e26578dd242.png">


- But fails in some cases

### 2.2.3 Evasion Strategy 3: Robust Tracing

<img width="315" alt="image" src="https://user-images.githubusercontent.com/7360266/153022117-a938ed33-69e5-44e8-8ceb-bf9b98a86d70.png">



This solution is a standard solution in robotics. As soon as we collide, we:
- calculate a line between current position and target
- we again choose left/right and trace the wall
- we stop tracing when both:
  - line was crossed at least twice
  - the original blocking point is no longer between position and target
- tracing fails, if it reaches the starting point of tracing again

```
original_obstacle <= best_neighbor
blocked_direction <= direction from current_cell to best_neighbor
blocked_line <= line through current_cell and target_cell
while(current_cell + blocked_direction is not walkable)
   current_cell = follow_the_wall()
```

An easier implementation of this would be:
- Trace the wall for as long as the total rotation is not 0 Degrees and the original direction is free.
- Turning Left = -90 Degrees.
- Turning Right = 90 Degrees.
- Walk until back at 0 Degrees. Not 360 or - 360.

## 2.3 SLAM: Simultaneous Localization and Mapping

We'll skip on this chapter, but this is a series of algorithms that explore their environment while building a Map of it at the same time. You could say it's learns while moving, or it has memory.

## 2.4 Ant Colony Optimization Algorithms

Also another interesting field: Ants walk randomly / explore and leave a decaying trace when they find something interesting, which makes other ants more likely to follow this path, too. Those ants will again increase the trace, if their search was successful, attracting more ants. This is also being applied in Computer Science.

# 3 Pathfinding: This time, with a Map and a Plan!

<img width="300" src="https://camo.githubusercontent.com/3ea84edd32d72fee15daeaf2bb1a2cef79fdf05fbb78471d09fa40ba6f6f2007/687474703a2f2f692e696d6775722e636f6d2f396330524d756a2e706e67">

Why were our previous Algorithms so inefficient and "stupid"? Because they had to find a way on their own without any help.

Solution: Use a Map of the environment and plan the path that we want to take first.

## 3.1 Problem: The Map

We need to create a Map for our Pathfinding algorithm! 

A digital Map uses the following vocabulary:

- State: the current situation, e.g. current position in the maze, current pieces on chess board
- Transition: an action that leads from one state to another, e.g. making a step, moving the king
- Successor state: a state that can be reached from the current state using one transition, e.g. a position right next to the current one, or a state where one piece has moved in chess
- Target state: the state that we want to find, e.g. exit of a maze, solved rubik's cube
- State space: collection of all accessible states, e.g. all walkable cells of a maze, all states of a rubik's cube (10^19)

What does this sound like?

Indeed, a Graph! Please continue [here](../algodata-205-grid).

### 3.1.1 Occupancy Grid

<img width="315" alt="image" src="https://user-images.githubusercontent.com/7360266/153027145-7715819d-ac75-427f-a615-a85cea797c6f.png">

The idea is, that we have a map in form of a Grid. Why a Grid? Because it is very efficient to find the correct cell from where we're standing. e.g.

If each Grid Cell is 1x1 in Size and we're at position (5.7, 3.6), then we're standing on Cell (5, 3)

If each Grid Cell was 2x2 in Size, then for the same position (5.7, 3.6), we'd be standing on Cell (2, 1)

What's the Math?

```
index_x = floor((position_x-cell_offset_x)/cell_width)
```

#### 3.1.1.1 Where's my Graph?

Okay, cool, but didn't you just say we need a Graph?

Well, that's all a matter of perspective:

<img width="315" alt="image" src="https://user-images.githubusercontent.com/7360266/153036206-f0d5fc40-5f84-4597-9a3e-3d15b50bfe8e.png">

You can see the centers of the cells (red dots) as Nodes and the neighbouring, walkable Cells as Adjacent Cells, shaping Connections between them. You don't even need to create a Graph ahead of time, instead, you can implement a method like this:

```
procedure get_adjacent_nodes(x, y)
   adjacent_nodes = []
   if(nodes[x-1,y].walkable)
      adjacent_nodes.push([x-1,y])
   endif
   if(nodes[x+1,y].walkable)
   ...
   endif
   return adjacent_nodes
end procedure
```

For Graph Traversal, all you need is a Start Node and each nodes' neighbors, right?

#### 3.1.1.2 But where's my Grid?

<img width="413" alt="image" src="https://user-images.githubusercontent.com/7360266/153038130-fa28fc83-6051-436a-8084-5872a4051d84.png">

In some cases you might be lucky and have your Game Data stored in a Grid anyways (old-school 2d RPGs with tiled maps). Here, you could just store the information what's walkable directly on the Tile Information.

<img width="413" alt="image" src="https://user-images.githubusercontent.com/7360266/153038260-1ff7ec17-667a-4b21-8141-5fbde82a1796.png">

But you might have a world that's not so "simple". How do you end up with a grid in these cases?

##### 3.1.1.2.1 Bounding Boxes

This is a very simple approach:

<img width="315" alt="image" src="https://user-images.githubusercontent.com/7360266/153041410-d2082b0f-f238-4d8a-bd31-27e728227586.png">

If each of your Cells has a `walkable`-Property:
- iterate over all objects
- foreach object calculate the Bounds
- all Cells that fall within the Bounds
  - floot the min coordinate of the Bounds
  - ceil the max coordinate of the Bounds
  - will be flagged as `!walkable`

If your Environment is destructible, you can also reference the object in that cell.
- This can allow your AI to decide to destroy the building in that cell instead of walking around.

- Pro: Very simple to implement, low performance impact
- Con: Some shapes have a lot of "invisible blocked" Area

##### 3.1.1.2.2 Rasterization

For a more accurate representation, you could use Rasterization. A process that the GPU does when rendering your Vertex Data to the Screen: Converting Vertex-Data to Pixels:

<img width="413" alt="image" src="https://user-images.githubusercontent.com/7360266/153039852-012a3f6a-4511-48ba-9246-88b010fc70ac.png">

For Rasterization, there is multiple approaches. Either:
- you raycast the center of each Cell. If it hits something, then it's blocked (Sampling)
- you raycast multiple points of each Cell to better cover partially coverted cells (Multisampling)
- you first check the bounds of your shape (all cells outside the bounds don't matter)
  - and then run edge-tests (it's an algorithm) on your shape's edges
  - to find out, whether each cell is inside or not (imagine your shape is hollow, like a donut)

#### 3.1.1.3 Grid in 3D??

<img width="315" alt="image" src="https://user-images.githubusercontent.com/7360266/153045953-a338cf2d-cb15-4843-b3f2-2ac6615b9626.png">

<img width="315" alt="image" src="https://user-images.githubusercontent.com/7360266/153046160-7bea9893-07b0-4db8-b3aa-59c65dec0e67.png">

Your 3D World can also use a Grid for Navigation! It's just more Neighbor Cells for each cell. Keyword: Voxel [More](https://medium.com/ironequal/pathfinding-like-a-king-part-2-4b74588262af)

#### 3.1.1.4 It's not all Tiles

<img width="315" alt="image" src="https://user-images.githubusercontent.com/7360266/153048398-82480017-c016-41ed-9506-9b9320dfaccb.png">

- We talk about Tiles, if your Game has Rects and you can walk in four directions (left, right, up, down).
- It's Octiles, if you can also walk diagonally, resulting in a total of eight directions
- Hexagon-Tiles have a much nicer and organic shape, though, are not worse in performance and very often result in smoother paths. If your objects are more organically shaped, try Hexagons. If everything consists of Boxes, don't bother.

#### 3.1.1.5 Summary

So, Grids can be extremely useful because of how efficiently Cells can be accessed. The disadvantage is that they consume a lot of Memory (even 1000x1000 Cells are already 1 Million Cells = 1MB ber byte of Data in each Cell).

You can create Grids from your Game Data. Either during Runtime, or you could bake it during Build-Time. If your Game Data is simple enough and your World is small enough, this might be your best option. [More](https://svn.sable.mcgill.ca/sable/courses/COMP763/oldpapers/yap-02-grid-based.pdf)

Also, Grids are very efficient in case you need to Add or Remove Objects during Runtime. Updating Grids is very easy due to how the structure of Grids won't change and accessing Grids is so efficient. => e.g. RTS or Tower-Defence Game.

### 3.1.2 Graph

In many cases, your Data just happens to be a Graph Already. e.g. Public Transport Stations, Game States, maybe the character can only drive on Rails?

In this case, there is not much to add. We will see later, why we need a Graph of some sort.

### 3.1.3 Mesh

<img width="788" alt="image" src="https://user-images.githubusercontent.com/7360266/153051052-501dfc7a-c9b4-4fd1-b34a-9a50534d88b3.png">

Navigation Meshes are a great extension of Grids. Instead of rasterising the whole Map into same-sized blocks, the Map is smartly converted by generating a Mesh that covers all Corners of Obstacles nicely.

The advantage is that a really big empty area only needs a few vertices instead of thousands of empty Grid Cells.

#### 3.1.3.1 Where's my Graph?

<img width="544" alt="image" src="https://user-images.githubusercontent.com/7360266/153051587-418982e4-5ece-4e4f-bb4d-ff6338ef0295.png">

The Graph can be found by connecting each Polygon with its adjacent neighbors.

#### 3.1.3.2 But how do I create a Navigation Mesh?

Press the Correct Button in Unity or Unreal or get a Vertex-Editor and start modeling :D

Seriously, though, it's [not easy](https://gamedev.stackexchange.com/questions/38721/how-can-i-generate-a-navigation-mesh-for-a-tile-grid/43826)

## 3.2 Problem: The Path

Okay, we got a Graph. We have some Data Format, where we can find the Start-Node and the End-Node and we can find Neighbors of each Nodes. How do we find the Path from Start to End now?

There is a few ways of classifying Pathfinding Algorithms:
- Efficiency - how many nodes need to be visited in order to find the path?
- Completeness - is it guaranteed to find a solution, if it exists?
- Optimality - does it find the best solution?

### 3.2.1 Depth-First-Search

![image](https://user-images.githubusercontent.com/7360266/153076821-d55923c8-6290-4869-aebb-e8aacebcda89.png)

In blue, you can see the trace of the algorithm scanning the Grid.
In pink, you can see the final path that has been found.

Depth First, as already mentioned in Graphs, will always follow one path through until it is blocked and then backtrack to the last unblocked state and continue finding a path from there. As you can see in the example, it is quite inefficient at finding a path. But if a Path exists, it will always find it.


```
procedure find_path(start_node, end_node)
   visited_nodes = [start_node]
   path = [start_node]

   while path not empty
      found_next_node = false
      for each neighbor in path.peek().getNeighbors()
         if(neighbor in visited_nodes)
            continue
         else
            visited_nodes.add(neighbor)
            path.push(neighbor)
            if(neighbor == end_node)   // NEW
               return path             // NEW
            end if                     // NEW
            found_next_node = true
            break
         end if
      end for
      if(not found_next_node)
         path.pop()
      end if
   end while
   return null // NEW
end procedure
```

There is a problem with this algorithm, though:

![image](https://user-images.githubusercontent.com/7360266/153077795-5a5dcd1b-45fe-4104-a75b-9b02a62e7612.png)

It's not very good at finding the shortest path :|

- Pro: Complete. Low Memory Usage.
- Con: Not CPU Efficient. Not Optimal.

### 3.2.2 Iterative Deepening Depth-First-Search

You could change the Depth-First-Search by limiting the depth and then iteratively going deeper and deeper. Meaning that in previous example:

Iteration 1: Depth-First-Search with Depth-Limit: 1
![image](https://user-images.githubusercontent.com/7360266/153079592-0297d962-23fa-4fc2-9fa5-b7a60995464e.png)

Iteration 2: Depth-First-Search with Depth-Limit: 2
![image](https://user-images.githubusercontent.com/7360266/153079668-aa53275e-5d6c-49bf-95ee-9ae8dfd10bfc.png)

Iteration 3: Depth-First-Search with Depth-Limit: 3
![image](https://user-images.githubusercontent.com/7360266/153079764-34cf00b0-3006-4170-95b9-e571ea7be00d.png)

Iteration 4: Depth-First-Search with Depth-Limit: 4
![image](https://user-images.githubusercontent.com/7360266/153079871-d1d5a36f-27d8-4b2a-8af3-a4e1fd728d16.png)

Careful: the following Pseudo-Code does not handle the case well, if no Path exists to the Target. You'd have to do an extra Error handling when you notice, that `depth` is larger than the maximum reachable depth.

```
procedure find_path(start_node, end_node)
   depth = 1
   while(true)
      result = find_path(start_node, end_node, depth++)
      if(result != null)
         return result
      end if
   end while
end procedure
```

```
procedure find_path(start_node, end_node, allowed_depth) // NEW
   visited_nodes = [start_node]
   path = [start_node]

   while path not empty
      found_next_node = false
      for each neighbor in path.peek().getNeighbors()
         if(neighbor in visited_nodes)
            continue
         else if depth > 0                // NEW
            visited_nodes.add(neighbor)
            path.push(neighbor)
            allowed_depth--               // NEW
            if(neighbor == end_node)
               return path
            end if
            found_next_node = true
            break
         end if
      end for
      if(not found_next_node)
         path.pop()
         allowed_depth++                  // NEW
      end if
   end while
   return null
end procedure
```

Those are a lot of steps. But! The algorithm is very Memory Efficient. While the number of visited cells might grow largely (which can be prevented by adding a visited-Flag on the Graph-Nodes instead - which then again needs to be reset before finding a path again), the path nodes will never be larger than the length of the final path. And the path is directly available as soon as the target has been found!

- Pro: Complete. Optimal. Low Memory Usage.
- Con: Not CPU Efficient.

### 3.2.3 Breadth-First-Search

![image](https://user-images.githubusercontent.com/7360266/153085691-05278aa0-b494-409f-8099-9a1ec4b38264.png)

In above image, you can see:
- blue: visited nodes
- purple: queued nodes
- pink: found path

Breadth First, as already mentioned in Graphs, will check all nodes in a queue. Making sure, that we first visit all nodes with a distance of 1, then all nodes with a distance of 2, then ... etc. until the target is found.

The special thing in Pathfinding is, that for each node that is queued, we additionally need to save the whole path that lead us to that node in the queue, too. After all:
- one path could be: ABCDF
- one path could be: ABCEG
- one path could be: ABCEH

In case the next neighbor of G (let's call him I) is our Target Node, we need to know, how we got there:

```
procedure find_path(start_node, end_node)
   visited_nodes = [start_node]
   todo_paths = [[start_node]]    // CHANGE

   while path not empty
      path = todo_paths.dequeue()
      current_node = path.peek()        // NEW
      for each neighbor in current_node.getNeighbors()
         if(neighbor == end_node)         // NEW
            return [path, neighbor]       // NEW
         else if(neighbor in visited_nodes)
            continue
         else
            visited_nodes.add(neighbor)
            todo_paths.enqueue([path, neighbor])    // CHANGE
         end if
      end for
   end while
end procedure
```

This escalates pretty quickly:

<img width="608" alt="image" src="https://user-images.githubusercontent.com/7360266/153086287-e8e33c27-77f1-440c-85f8-4c208b3d4480.png">

Here:
- the blue nodes are visited
- the green ones queued 
- yellow is the path to the Target Node

What you can't see:

![image](https://user-images.githubusercontent.com/7360266/153086789-d39ea5ed-60ae-4178-816f-b5aa3cd3473c.png)

In Pink: All of the different paths that need to be stored as well.

And that's actually a lot of used Memory. The direct neighbor of the start node is already part of 10 paths. The next one of 9 paths etce. We're talking about 40 Paths with a length of 10 until a path of length 10 is found (400 Nodes stored in Memory).

This can be reduced to 200, if you store, for each Cell, it's Previous Cell (Predecessor) in a Dictionary, or directly in the Cell. Instead of the neighbor of the start node being referenced in 10 paths, it will only know that it's predecessor was the Start Node.

Then, when we found a path, we walk from the current Node through all predecessors backwards to build the Path (and then reverse it).

```
procedure find_path(start_node, end_node)
   todo_nodes = [start_node]        // REVERT
   predecessors = []=>[]            // NEW

   while path not empty
      current_node = todo_nodes.dequeue()
      for each neighbor in current_node.getNeighbors()
         if(neighbor == end_node)
            return build_path(predecessors, neighbor) // NEW
         else if(neighbor in predecessors)
            continue
         else
            predecessors[neighbor] = current_node  // NEW
            todo_nodes.enqueue(neighbor)           // REVERT
         end if
      end for
   end while
end procedure
```

```
procedure build_path(predecessors, neighbor)
   path = []
   while neighbor is not null
      path.push(neighbor)
      neighbor = predecessors[neighbor]
   end while
   path.reverse()
   return path
end procedure
```

Pro: Complete. Optimal.
Con: Complexity is polynomial (n^2). Memory usage is rather high.

### 3.2.4 Bidirectional Search

A really great optimization and one that works with most similar algorithms:

We can see, that the complexity gets more and more extreme, the longer the path is. It's 4*(n^2), actually. What, if we can make n half the size? Instead of 4 * (10 * 10) = 400 Nodes, it could be 4 * (5 * 5) = 100 Nodes! Nice! How do we do that?

<img width="581" alt="image" src="https://user-images.githubusercontent.com/7360266/153088243-d9bf5ef1-bbb7-455b-9b04-5c62d7be49d7.png">

We just start searching from Both Ends and complete the algorithm, as soon as a Node is in both ends' visited stacks! Then, you just build the path from there in both directions and combine it.

I won't specify the Pseudo-Code here. In general, it is a great optimization, but it does not change any characteristics of the Breadth-First-Search.

### 3.2.5 Dijkstra

Yay, finally an Algorithm named after a person. This algorithm must be a great improvement! Let's look at its results right away:

<img width="605" alt="image" src="https://user-images.githubusercontent.com/7360266/153089384-31e27a7b-9a27-4a17-a470-0b331c38c277.png">

Okay, that was underwhelming. Why is there no difference?

Becase the Dijkstra Algorithm does not actually change the kind of Search that we do on the Graph. All it changes, is, it allows us to specify costs on each transition
- e.g. road = 1, swamp = 3
- or Slussen to TCentralen = 7, Slussen to Sickla = 13
- or walk = 1, swim = 3, ladder = 10, teleport = 0

And then, the Algorithm helps us find the shortest path, respecting those costs. Cool after all, right?

```
procedure find_path(start_node, end_node)
   todo_nodes = []
   predecessors = []=>[]
   costs = []=>[]          // NEW
   
   todo_nodes.enqueue(0, start_node)
   costs[start_node] = 0   // NEW

   while path not empty
      current_node = todo_nodes.dequeue()
      if(current_node == end_node)
         return build_path(predecessors, current_node)
      end if
      for each connection in path.getNeighbors()
         neighbor = connection.next
         new_costs = costs[current_node] + connection.costs      // NEW
         if(neighbor in costs and costs[neighbor] <= new_costs)  // CHANGE
            continue
         else
            if(neighbor in todo_nodes)
               remove neighbor from todo_nodes
            end if
            predecessors[neighbor] = current_node
            costs[neighbor] = new_costs                // NEW
            todo_nodes.enqueue(new_costs, neighbor)    // CHANGE!
         end if
      end for
      visited_nodes.add(current_node)
   end while
end procedure
```

Wait, what happened here? Why does `enqueue` suddenly take two arguments?

The first argument is the Priority. Lowest Value is returned first. Instead of always returning the one that was enqueued first, here the nodes receive a Priority and are returned in that order.

If you wish to implement this class yourself, have a go at implementing a Min-Heap. (It's a special kind of Binary Tree, not a Binary Search Tree)

Pro: Complete. Optimal. Can Deal with Connection-Costs.
Con: Complexity is polynomial (n^2). Memory usage is rather high.

### 3.2.6 Best-First-Search

After meeting two Families of Algorithms: Depth-First and Breadth-First, we'll now look at a third Family of Algorithms: Heuristic Algorithms.

Heuristic Algorithms are Algorithms that use a Heuristic (an approximation, an estimate, an assumption) into consideration when evaluating what's the best path.

For Dijkstra, all Nodes with a Distance of 5 are equally valuable. Even, if they head in the exact opposit direction.

With a Heuristic Algorithm, we pass in a Heuristic function which says, how promising a certain state looks.

This allows us, to then visit the most promising states first.

The first adaption of such an algorithm would be Best-First:

We use Dijkstra
- but sort todo_nodes by estimation instead of actual costs
- what could the estimation be? Let's start with Distance(node, end_node):

<img width="448" alt="image" src="https://user-images.githubusercontent.com/7360266/153093212-4bed33cc-2e60-4f46-9f8e-3dda74170456.png">

Hey, that's an algorithm that I can get behind of!

<img width="599" alt="image" src="https://user-images.githubusercontent.com/7360266/153093510-68956f35-09a8-4e64-a8e3-a457f8387318.png">

Problem: It can be fooled. Here, the upper path seems to always be closer to the goal than the lower path, so it doesn't really consider the lower path anymore. Looks like we still got some work ahead of us!

PRO: Complete. Fast.
CON: Non-Optimal. Ignores Costs.

### 3.2.7 A*

And now Kiss!
> A* = Dijkstra + Best-First

x = State
g(x) = actual costs
h(x) = estimated remaining costs
f(x) = rating, priority

f(x) = g(x) + h(x)

```
procedure find_path(start_node, end_node)
   todo_nodes = []
   todo_nodes.enqueue(0, start_node)
   predecessors = []=>[]
   costs = []=>[]
   costs[start_node] = 0

   while path not empty
      current_node = todo_nodes.dequeue()
      if(current_node == end_node)
         return build_path(predecessors, current_node)
      end if
      for each connection in path.getNeighbors()
         neighbor = connection.next
         new_costs = costs[current_node] + connection.costs
         if(neighbor in costs and costs[neighbor] <= new_costs)
            continue
         else
            if(neighbor in todo_nodes)
               remove neighbor from todo_nodes
            end if
            predecessors[neighbor] = current_node
            costs[neighbor] = new_costs
            heuristic = estimate_remaining(neighbor)            // NEW
            todo_nodes.enqueue(new_costs+heuristic, neighbor)   // CHANGE
         end if
      end for
      visited_nodes.add(current_node)
   end while
end procedure
```

<img width="568" alt="image" src="https://user-images.githubusercontent.com/7360266/153094147-7589f356-575c-458e-aa5d-735fc2400e5e.png">

That looks wonderful!

Fact: As long as your Heuristic Function never overestimates the actual remaining costs, A* is guaranteed to find the shortest path! => Stay Optimistic. But not too optimistic (like 0, this would be Dijkstra again), or your algorithm is useless (This is a huge problem, if you have Teleporters)

PRO: Complete. Fast. Optimal.*
CON: Heuristic must be correct.

### 3.2.8 More Algorithms
D* and IDA* appear to be worth reading up on. But I got no idea at this point, what these algorithms are. Apparently, D* is like A* but easier to update in case your environment changes.

FlowFields are also an interesting concept, especially, if there is only a few Targets, but hundreds or thousands of Actors. Here, you create a Grid for each Target, telling for each Cell, how to get there. Then each actor can know without any calculations, how to get there.

<img width="814" alt="image" src="https://user-images.githubusercontent.com/7360266/153094784-17684959-4787-4ec4-817d-c3bbe2722187.png">

### 3.2.9 More Graphs

It may make sense to generate multiple Graphs for your Game. 
- e.g. one for big enemies and one for small ones.
- one for characters that can climb and one for ones that can't.
- one for highways and one for small streets

They will allow you to map more precise paths for each use-case.

### 3.2.10 Less Actors

If you want to move multiple units from one place to another, maybe not each of them needs to calculate the path, but only one "leader" and then they can share the path?

### 3.2.11 Storing Pathfinding Information on the Nodes

Very often, you'll find that information like the Total Costs and the Predecessor are stored on the Nodes directly instead of in Dictionaries. That makes a lot of sense! But I did not want to show different Node-Class implementations but instead focus on the Algorithm.

But as mentioned before (on the Hashing-Slides):

```
Dictionary<Cell, int> costs;
costs[cell] = 5;
```

Is the same as:
```
cell.costs = 5;
```


## 3.3 Problem: The Navigation

Well, now, all you got is a list of points. But your character needs to nicely follow these points, still.

## 3.3.1 Steering

<img src="https://arongranberg.com/astar/docs/images/aipath_variables.png">

Steering has many important features
- determine current target node
- evaluate target direction
- calculate desired velocity
- correct current velocity
- determine whether close enough to pick next target node

Steering can be exceptionally complex, if your nodes are special nodes (which require actions like interactions, jumping, ...) or if the steering correction is more complex (car, helicopter, ...)

## 3.3.2 Smoothing
Make the path smoother by using Bezier curves, or just use a smart steering algorithm to do this for you.

## 3.3.3 Funneling
Optimize the paths by applying the Funnel Algorithm to find shortest paths on NavMesh (e.g. Simple Stupid Funnel Algorithm), again, a smart steering behavior might be able to do these steps, too.

## 3.3.4 Local Avoidance
Avoid other actors on the way.

## 3.3.5 Reevaluate
Recalculate the path in regular intervals or when the actor notices that it is stuck (maybe, he got pushed down or missed a jump)