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

#### 3.1.3.1 Where's my Grid?

<img width="544" alt="image" src="https://user-images.githubusercontent.com/7360266/153051587-418982e4-5ece-4e4f-bb4d-ff6338ef0295.png">

The Grid can be found by connecting each Polygon with its adjacent neighbors.

#### 3.1.3.2 But how do I create a Navigation Mesh?

Press the Correct Button in Unity or Unreal or get a Vertex-Editor and start modeling :D

Seriously, though, it's [not easy](https://gamedev.stackexchange.com/questions/38721/how-can-i-generate-a-navigation-mesh-for-a-tile-grid/43826)

## 3.2 Problem: The Path

Okay, we got a Grid. We have some Data Format, where we can find the Start-Node and the End-Node and we can find Neighbors of each Nodes. How do we find the Path from Start to End now?

### 3.2.1 Depth-First-Search



### 3.2.2 Iterative Depth-First-Search

### 3.2.3 Breadth-First-Search

### 3.2.4 Bidirectional Search

### 3.2.5 Dijkstra

### 3.2.6 Best-First-Search

### 3.2.7 A*

## 3.3 Problem: The Navigation
