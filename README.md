# CI2024_lab3 
##  Purpose of the Lab

The goal of this lab is to solve the \(n^2 - 1\) sliding puzzle efficiently using the **A\* algorithm**. The puzzle is a classic problem where the objective is to rearrange the tiles from a given initial configuration to a defined goal configuration by sliding one tile at a time. The challenge lies in determining an optimal sequence of moves that minimizes the total cost, while ensuring that only valid states are explored.

The key aspects of solving this problem include:
1. Ensuring that the initial configuration is solvable.
2. Designing an effective heuristic to guide the search.
3. Managing explored and unexplored states efficiently using a priority queue.
4. Utilizing compact representations like `tobytes()` to track states.

---

## Conditions for Solvability

Not every possible configuration of the \(n^2 - 1\) puzzle solved.  A configuration's solvability is determined by the number of **inversions** in the linearized array of the puzzle (ignoring the empty space). An inversion occurs when a higher-numbered tile precedes a lower-numbered tile in this 1D representation.

The rules for determining solvability vary based on the puzzle's dimension \(n\):

- **Case 1: \(n\) is odd**  
  A configuration is solvable if the number of inversions is **even**.

- **Case 2: \(n\) is even**  
  A configuration is solvable if:
  1. The number of inversions is even and the blank tile (`0`) is on an **odd row from the bottom**.
  2. The number of inversions is odd and the blank tile (`0`) is on an **even row from the bottom**.

To ensure the algorithm begins with a solvable configuration, the function `generate_random_solvable_state()` executes the following steps:

1. **Start from the goal state**: Since the goal state is inherently solvable, it serves as the starting point.  
2. **Apply random moves**: A series of valid random moves is applied to the goal state, following the puzzle's movement rules. Each move creates a new configuration while preserving solvability because the process starts from a known solvable state.

By design, this method eliminates the need for post-verification of the generated configuration’s solvability. It ensures that all initial states are valid while maintaining computational efficiency.

--- 

## Implementing the Heuristic Function

The **heuristic function** is a vital component of the A* algorithm, used to estimate the cost (\(h(n)\)) of reaching the goal state from the current state. A well-designed heuristic should:

- **Guide the search efficiently** by prioritizing states closer to the goal.
- Be **admissible**, meaning it never overestimates the actual cost to the goal.
- Be **consistent** to ensure optimality, maintaining the property that \(h(n) \leq g(n) + h(n')\) for any neighboring states \(n\) and \(n'\).

For the \(n^2-1\) puzzle, two commonly used heuristic functions are **Manhattan Distance** and **Misplaced Tiles**.

### Manhattan Distance

The **Manhattan Distance** heuristic computes the sum of the vertical and horizontal distances of each tile from its target position. It assumes that tiles can only move up, down, left, or right, mimicking the actual movements allowed in the puzzle.

#### Advantages:
- It is **accurate** because it reflects the minimum number of moves required to place each tile in its correct position.
- It is **admissible**, ensuring that the algorithm finds the shortest solution path.

#### Formula:
h_manhattan = Σ |x_current - x_goal| + |y_current - y_goal|

Where:
- `x_current` and `y_current`: The current row and column of tile `i`.
- `x_goal` and `y_goal`: The goal row and column of tile `i`.
### Why Manhattan Distance was Chosen

The Manhattan Distance was chosen as the heuristic for this implementation because:
1. **Accuracy**: It closely estimates the actual cost required to solve the puzzle.
2. **Admissibility**: It guarantees that the algorithm will always find the optimal solution.
3. **Efficiency**: It performs better in guiding the search compared to simpler heuristics like Misplaced Tiles, reducing unnecessary exploration.

By using Manhattan Distance, the algorithm can efficiently navigate through the puzzle space, prioritizing states that are genuinely closer to the solution. This makes it a robust choice for solving the \(n^2-1\) puzzle. 

---


## The Priority Queue (`heapq`)

The A\* algorithm relies on a **priority queue** to manage states. The priority queue ensures that states with the lowest estimated total cost \(f(n) = g(n) + h(n)\) are explored first, optimizing the search process.

### Why Use `heapq`
Python’s `heapq` module provides an efficient implementation of a min-heap, allowing:
1. **Fast insertion and removal**: Operations like `heappush` and `heappop` run in \(O(\log n)\), making the priority queue efficient for large problem spaces.
2. **Priority management**: The state with the lowest \(f(n)\) is always processed first, aligning with the A\* algorithm's strategy.

In this implementation, the priority queue stores:
- \(f(n)\): The total estimated cost for the state.
- `state_bytes`: A compact binary representation of the puzzle state.
- `state`: The actual NumPy array representing the puzzle configuration.

This structure ensures minimal memory overhead while preserving necessary information for the algorithm.

---

## Using `tobytes()` for State Representation

A critical component of the A\* implementation is tracking visited states to avoid redundant processing. This is accomplished by representing states as compact, hashable binary strings using NumPy's `tobytes()` method.

### Why `tobytes()`?
1. **Compactness**: Instead of storing large NumPy arrays in sets or dictionaries, `tobytes()` converts arrays into binary strings, reducing memory usage.
2. **Hashability**: The binary representation can be used as keys in dictionaries or elements in sets, enabling fast lookups and ensuring efficient state tracking.
3. **Efficiency**: Converting an array to bytes and back is computationally inexpensive:
   - Converting back: `np.frombuffer(state_bytes, dtype=int).reshape(shape)`

### Example
```python
state = np.array([[7, 2, 4], [5, 0, 6], [8, 3, 1]])
state_bytes = state.tobytes()

# Store in a dictionary
visited = {state_bytes: "visited"}

# Convert back to NumPy array
reconstructed_state = np.frombuffer(state_bytes, dtype=int).reshape(state.shape)
```

By using `tobytes()`, the implementation achieves both memory efficiency and computational speed, making it ideal for solving large-scale problems.

