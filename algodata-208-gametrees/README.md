# 1 What's A.I. in Games?

Challenging NPC Opponents

<img src="https://cdn.vox-cdn.com/thumbor/WwDkpf0Qp72AdHu9JOXFFhnemnQ=/0x0:3360x2100/1820x1213/filters:focal(1412x782:1948x1318):format(webp)/cdn.vox-cdn.com/uploads/chorus_image/image/65584607/AS_Bnet_Protoss.0.jpg">

- The most classic use case is to provide Non-Player-Characters to allow the player to play a game without matchmaking, toxic behavior, disconnects, etc, like here in StarCraft 2.

Matchmaking

<img src="https://i.imgur.com/TqV4XAl.png">

- While we're at it: as soon as it's not Single-Player, an A.I. is needed to match players for engaging matches. Trying to avoid win- or lose-streaks, unbalanced matches, conflicts in play-styles etc like here in League of Legends.

Finding Cover

<img src="https://eu-images.contentstack.com/v3/assets/blt95b381df7c12c15d/bltb00e7daef8d816a3/611e99f975830930bf6e434e/the-division-cover.jpg?width=828&quality=80&format=webply&disable=upscale">

- In The Division, not only encountered enemies, but also the player can find cover. An AI analyzes what counts as cover and calculates a short path there when the player presses the "Take Cover"-Button.

Driving Human-Like

<img src="https://eu-images.contentstack.com/v3/assets/blt95b381df7c12c15d/blt18536004a8c74b87/611e99fc8ab5544acc6456b8/ForzaDrivatar.jpg?width=828&quality=80&format=webply&disable=upscale">

- Forza adapts its driving style to real player-data to feel more human-like.

Hunting (Stealth-Game)

<img src="https://eu-images.contentstack.com/v3/assets/blt95b381df7c12c15d/blt650396c4ffceb138/611e99fe88a9a63b61fca713/AlienIsolationXenomorph.jpg?width=828&quality=80&format=webply&disable=upscale">

- Alien's AI has smart roaming behavior and can react to signals like sound and movement to adjust its hunting pattern.

Playtesting

<img src="https://eu-images.contentstack.com/v3/assets/blt95b381df7c12c15d/blte1e3d0f819b3592d/611e9a029a154b63bef7fa19/CityConquest.jpg?width=828&quality=80&format=webply&disable=upscale">

- Hiring staff to test your games can be extremely expensiv. City Conquest utilized Genetic Algorithms to Playtest and play each session differently in order to find bugs and help balance the levels.

AI Directors

<img src="https://eu-images.contentstack.com/v3/assets/blt95b381df7c12c15d/bltd9bbddd5ebe796a0/611e9a0675830930bf6e435c/l4d04.jpg?width=828&quality=80&format=webply&disable=upscale">

Left 4 Dead used an AI that "directed" the Game's Pacing, by keeping an eye on the group's health and progression speed and adapting to it with smart climaxes of tension, but also moments to recover and punishing players that start camping to get them out of their cover.


# 2 Constraints

## 2.1 Time

One of our biggest issues is time. Especially, if decisions need to happen in real-time, time and therefore processing power is extremely limited.

## 2.2 Fun

Game A.I. is often considered to not be "real' artificial intelligence, due to our goal often not being to write the optimal Algorithms, but the most fun ones.

## 2.3 Humanity

Very often great algorithms fulfil all other requirements, but still not satisfy our demand for AIs to behave human-like. This might mean making mistakes, having delays or other irrational behavior.

## 2.4 Game Rules

Does your A.I. utilize means that players can't?

There's multiple ways of cheating:

- Breaking game mechanics, e.g. getting extra resources in an RTS, extra speed when behind in a Racing Game or picking, which card will be drawn next. Maybe even swapping out cards from its deck mid-game.
- complete information, e.g. reacting to your strategy in an RTS without scouting your base, or knowing, what card will be drawn next.
-  Super-human behavior, e.g. directly aiming at your head, without the need of reaction time and time for moving the cursor

## 2.5 Configurability

Is your A.I. configurable, so it allows for different difficulties or experiences
- easy, medium, hard
- defensive, neutral, aggressive

## 2.6 Adaptability

Does your A.I. improve by learning from past matches?


# 3 Classifications

## 3.1 Perfect / Imperfect Information

- Perfect: All moves can be seen, all information available (chess)
- Imperfect: Limited knowledge (card games)

## 3.2 Symmetric / Asymmetric

- Symmetric: Same goals, only different strategies to achieve them (rts)
- Asymmetric: Different goals, conflicting or not (werewolves)

## 3.3 Deterministic / Non-Deterministic

- Deterministic: All moves have predictable outcomes (chess)
- Non-Deterministic: Random chance play a roll (poker)

## 3.4 Simultaneous / Sequential

- Simultaneous: Actions can be taken concurrently (real-time strategy)
- Sequential: moves are done in order (round-based strategy)

## 3.5 Zero-Sum / Non-Zero-Sum
- Zero-Sum: An action that benefits one player equally damages the other player
- Non-Zero-Sum: Multiple players can benefit of the gains of another player

# 4 Decision Trees

In this course, we're not planning to do a deep-dive into AIs, though, but instead we want to look at one kind of problem and the solution to it.

## 4.1 Restrictions
- Zero-Sum
- Perfect Knowledge
- Deterministic

e.g. Chess, Tic-Tac-Toe, NIM, Checkers

## 4.2 Approach

Let's imagine the following state of a Tic-Tac-Toe Game:

|O|O|X|
|-|-|-|
| |X| |
|O|X| |

What's the next bext move?

We could start by looking at all three possible moves and then look at the state:

Option 1:
|O|O|X|
|-|-|-|
|X|X| |
|O|X| |

Option 2:
|O|O|X|
|-|-|-|
| |X|X|
|O|X| |

Option 3:
|O|O|X|
|-|-|-|
| |X| |
|O|X|X|

- What's the best move?
- How did you come up with that?
- But what about this state?

|-|-|-|
|-|-|-|
|-|X|O|
|-|-|-|

We need to start looking further down the possible moves:

<img src="https://www.ocf.berkeley.edu/~yosenl/extras/alphabeta/alphabeta.jpg">


The possible moves in a Game form a Tree. The leaves of the tree are terminal Game States in which either player has won.

We can use Pathfinding in this Tree! Start-Node: Current State. Target-Node: Any Node in which the player has won.

- This will yield us the shortest route to Victory
- Works perfectly in Single-Player Games

# 5 Minimax Algorithm

Problem: What will the A.I. consider the best play / shortest path to victory in the case of Tic-Tac-Toe?

|X|O| |
|-|-|-|
|X|O| |
|X| | |

Isn't it much more likely, that these Plays will turn out like this?

|X|X|O|
|-|-|-|
|X|O| |
|X|O|X|

The Problem:
- A move that's good for us is bad for the opponent.
- And a move that's good for the opponent is bad for us.
- We need to assume that the opponent makes the best move possible

> The **maximin** value is the highest value that the player can be sure to get without knowing the actions of the other players

- Maximizing the own play, while assuming the Worst-Case Scenario

> The **minimax** value of a player is the smallest value that the other players can force the player to receive, without knowing the player's actions

- Minimizing the opponent's options while assuming their Best-Case Scenario

```
function  minimax(node, depth, maximizingPlayer) is
    if depth = 0 or node is a terminal node then
        return the heuristic value of node
    if maximizingPlayer then
        value := −∞
        for each child of node do
            value := max(value, minimax(child, depth − 1, FALSE))
        return value
    else (* minimizing player *)
        value := +∞
        for each child of node do
            value := min(value, minimax(child, depth − 1, TRUE))
        return value
```

```
(* Initial call *)
minimax(origin, depth, TRUE)
```

Here's a picture of the algorithm's result assuming that each decision results in a final score for both players:

<img src="https://upload.wikimedia.org/wikipedia/commons/thumb/6/6f/Minimax.svg/800px-Minimax.svg.png">

# 6 Iterative Deepening MiniMax

Problem: Many games are insanely complex:

- Tic-Tac-Toe: 19,683
- Poker Hands: 2,598,960
- Rubik's Cube: 10^18
- Checkers: 10^21
- Chess: 10^45

This results in us not being able to calculate all possible moves. Actually, if you were able to turn the whole earth (all 10^50 atoms) into a computer, you might be able to store all possible chess combinations.

=> We need to be able to work with a limited set of possible moves being calculated.

This is called **Iterative Deepening MiniMax** Algorithm.

- Choose a depth to begin your calculations with.
- Run algorithm only until chosen depth.
- (does this remind you of another algorithm?)
- Increase the depth and re-run, if the result is not good enough, yet and there is more time available

# 7 Heuristic Functions

Problem:

If we calculate a game like chess with a depth of a few nodes only, we don't know yet who's going to win. We need to evaluate non-terminal leaves by some heuristic. Let's observe this issue by the example of chess:

<img src="https://www.houseofstaunton.com/media/catalog/product/cache/35ece35ec071d185e0970020aa695e66/g/r/grand-eb-6_7.jpg">

## 7.1 Counting Figures

First of all, you could just count the number of pieces on the board. Having more pieces on the board than the opponent is better, right?

## 7.2 Ranking Figures

Then, you can give values to different pieces. This will make sure that we don't think that sacrificing a Queen for two Pawns is a good idea:

|piece|score|
|-----|-----|
|pawn|1|
|knight|3|
|bishop|3|
|rook|5|
|queen|9|
|king|+Infinity|

## 7.3 Other heuristics

Modern algorithms go many steps further and evaluate positions, e.g. by:
- what pieces are *bound* (needed to defend other pieces)
- what pieces are *threatened* (could be taken by an opposing piece)
- how freely movable are pieces (are they locked in or can they roam the whole board)

## 7.4 Conclusion

You will need to find a smart heuristic evaluation of states and the better your heuristic, the better your A.I. will do.

# 8 Transposition Table

Problem: When using Iterative Deepening, we will encounter the same states very often. Not only because we start searching from the root again, but also because many combinations of moves can bring the board to the same state.

Solution: Caching

## 8.1 What do we cache?
- A Hash-Key that represents the current State of the Game
- The depth with which the information has been analyzed
- The score of the node

## 8.2 What do we use the information for?

The Hash-Key is used to identify and recognize the State. It is needed in order to recognize the state when it is encountered and see that it has been analyzed already.

The depth is used to recognize, how accurately the state has been analyzed before. It is used to determine, whether this is a node that's worth to analyze in more detail now.

The score is the important value that's being cached.

## 8.3 Cache-Size

Caching every possible state will very quickly fill the RAM, so it needs to be determined, what states are more worth caching that others.

States with a very high or very low score and large depth ar ideal:
- they save a lot of computing power (depth)
- they provide a very meaningful value

# 9 Alpha-Beta-Pruning

Idea: If we find out that for two choices A and B, the Opponent has each two choices AA, AB and BA, BB.

Then, if we find out that AA and AB give the opponent -3 and +3 points respectively.

Then, if BA gives the opponent +4 points already, then there is no point in calculating what score BB would yield in.

The opponent is expected to maximize his play and therefore end up with +3 points if we chose A or **at least** +4 points if we choose B.

We can therefore skip the calculation of BB.

=> This is called Alpha-Beta-Pruning.

Technique:
- fully evaluate first branch
- keep track of the minimax and the maximin.
- skip whatever second choices not worth evaluating

```
function alphabeta(node, depth, α, β, maximizingPlayer) is
    if depth = 0 or node is a terminal node then
        return the heuristic value of node
    if maximizingPlayer then
        value := −∞
        for each child of node do
            value := max(value, alphabeta(child, depth − 1, α, β, FALSE))
            if value ≥ β then
                break (* β cutoff *)
            α := max(α, value)
        return value
    else
        value := +∞
        for each child of node do
            value := min(value, alphabeta(child, depth − 1, α, β, TRUE))
            if value ≤ α then
                break (* α cutoff *)
            β := min(β, value)
        return value
```

```
(* Initial call *)
alphabeta(origin, depth, −∞, +∞, TRUE)
```

<img src="https://media.geeksforgeeks.org/wp-content/uploads/MIN_MAX1.jpg">

# 10 Expectiminimax

Variation of Minimax that can handle chances.

```
function expectiminimax(node, depth)
    if node is a terminal node or depth = 0
        return the heuristic value of node
    if the adversary is to play at node
        // Return value of minimum-valued child node
        let α := +∞
        foreach child of node
            α := min(α, expectiminimax(child, depth-1))
    else if we are to play at node
        // Return value of maximum-valued child node
        let α := -∞
        foreach child of node
            α := max(α, expectiminimax(child, depth-1))
    else if random event at node
        // Return weighted average of all child nodes' values
        let α := 0
        foreach child of node
            α := α + (Probability[child] × expectiminimax(child, depth-1))
    return α
```

# 11 Monte Carlo Tree Search

This algorithm has been used by AlphaGo to beat World Class Go Players!

It works with a exploitation-exploration-tradeoff, which means that it balances playing through random moves until the very end of a game and then signaling the win/loss back to its parent nodes, which then update its UCB (Upper Confidence Bounds, how confident are we in the value of this move?) as well as its Score (do we usually win or lose from here?)

The stages are:
- selection (picking the most promising move)
- expansion (if no more nodes can be selected after selection criteria, it expands all possible states from the leaf node)
- simulation (after expansion, we pick a random or a heuristically chosen child node to evaluate the result from here)
- backpropagaion (also known as update phase)

# 12 Topics that we haven't covered:

## 12.1 Trained A.I.

This is a field of AIs that need to be trained first (like neural networks)

This includes:
- evolutionary / genetical algorithms

## 12.2 A.I. Behavior States

This is a field of study that assesses how to have scripted A.I.

This includes:
- Finite State Machines (FSM, A Decision Tree that can react to events and switch State / Behavior)
- Hierarchical State Machines (Nested FSMs that reduce duplication and allow clean solutions for complex scenarios)
- Behavior Trees (allow change of state without transitions, instead uses Success, Failed and Running callbacks)
- Utility-Based System (Give scores to Actions, pick the most promising one)
- Stanford Research Institute Problem Solver (STRIPS, using Pre-Conditions and Post-Conditions per Action)
- Goal-Oriented Action Planning (GOAP, add costs to actions, go from goal backwards to current state on shortest path)