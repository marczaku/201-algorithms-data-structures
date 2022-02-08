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

Indeed, a Graph! Please continue with that class now.

### 3.1.1 Grid

### 3.1.2 Graph

### 3.1.3 Mesh

## 3.2 Problem: The Path

### 3.2.1 Depth-First-Search

### 3.2.2 Iterative Depth-First-Search

### 3.2.3 Breadth-First-Search

### 3.2.4 Bidirectional Search

### 3.2.5 Dijkstra

### 3.2.6 Best-First-Search

### 3.2.7 A*

## 3.3 Problem: The Navigation
