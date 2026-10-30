# Heuristic Search – A* State-Space Explorer

**AISC Project** · Artificial Intelligence and Soft Computing
**Topic:** Heuristic Search (A* algorithm)

| | |
|---|---|
| **Name** | Aditya Singh |
| **Registration No.** | 2427030212 |
| **Section** | E |
| **Institution** | Manipal University Jaipur (B.Tech, Computer Science & Engineering) |
| **Course & Branch** | B.Tech. Computer Science & Engineering |

**Live demo:** https://adit-aoe.github.io/astar-state-space-explorer/astar-explorer.html

**Code:** https://github.com/Adit-AoE/astar-state-space-explorer/blob/main/astar-explorer.html

---

## Overview

An interactive, step-by-step visualizer of the **A\* search algorithm** on a 7×9 grid. The user can place walls, move the start and goal, and watch A* expand one node at a time while the page explains *why* each node was chosen. It runs entirely in the browser: no installation, no backend.

## How to Use
 
1. Open the live demo (or `astar-explorer.html` locally).
2. Click **Step 1 Node** to let A* expand a single node, **Step Back** to undo a step, or **Auto-Play** to run the whole search. The **Interval** menu sets the speed, and **Reset Search** restarts while keeping your walls.
3. Click or drag on the grid in **Draw/Erase Walls** mode to build obstacles. Switch to **Move Start (S)** or **Move Goal (G)** and click a tile to reposition them.
4. Try the presets: *Obstacle Barrier*, *U-Trap Backtrack*, *Tie-Break Demo* and *Clear Walls*.
5. Hover any tile to see its `h`, `g` and `f` calculation. The right-hand panels show the next node A* will expand and why, and the open list in exact extraction order. The legend under the grid explains the tile colours.

## Problem Formulation

| Component | Definition |
|---|---|
| **State** | A grid cell `(row, col)` |
| **Initial state** | Start cell `S` (movable) |
| **Goal test** | Current cell equals goal cell `G` (movable) |
| **Actions** | Move up, down, left or right (4-connected) |
| **Step cost** | 1 per move |
| **Obstacles** | Wall cells are never expanded |

## Algorithm

A\* is an informed best-first search. It always expands the node in the open list with the lowest

```
f(n) = g(n) + h(n)
```

- `g(n)`: actual cost from the start to `n`
- `h(n)`: estimated cost from `n` to the goal, using the **Manhattan distance** `|r − r_goal| + |c − c_goal|`

**Why Manhattan distance works:** on a 4-connected grid with unit step costs, at least that many moves are always needed, so the heuristic never overestimates (**admissible**). Each move changes `h` by at most 1 and costs 1, so it is also **consistent**. This guarantees that the first time the goal is removed from the open list, the path found is optimal, and that no closed node ever needs to be re-expanded.

**Tie-breaking (deterministic):** lowest `f`, then lowest `h` (closest to the goal), then row-major `(row, col)` order.

### Steps

1. Put the start node in the open list.
2. Remove the node with the lowest `f` and add it to the closed set.
3. If it is the goal, trace the parent pointers back to the start and stop.
4. For each walkable, unvisited neighbour, compute `g = g(current) + 1`. If this is better than any previously known `g`, update `g`, `h`, `f` and the parent, and place the neighbour in the open list.
5. Repeat. If the open list becomes empty, no path exists.

## Data Structures

| Purpose | Structure |
|---|---|
| Open list (priority queue) | Array, re-sorted every step so the best node is at the front |
| Closed set | `Set` of `"row,col"` keys (O(1) lookup) |
| Node records (`g`, `h`, `f`, parent) | Object used as a hash map keyed by `"row,col"` |
| Path reconstruction | Parent pointers stored in each node |
| Walls | `Set` of `"row,col"` keys |
| Step history | Array of full search snapshots (enables Step Back) |

## Features

- **Step / Step Back / Auto-Play** with adjustable speed
- **Live statistics:** step count, closed nodes, open nodes, path cost
- **Selection Rationale panel:** explains in words why the next node was chosen (lowest `f`, or which tie-break decided it)
- **Cell inspector:** hover any tile to see its `h`, `g` and `f` calculation
- **Priority-queue table:** the open list in exact extraction order
- **Editable grid:** draw or erase walls by clicking or dragging, and move the start and goal
- **Preset scenarios:** Obstacle Barrier, U-Trap Backtrack, Tie-Break Demo, Clear Walls
- **Dark and light themes**

## Sample Results

| Scenario | Path cost | Nodes expanded |
|---|---|---|
| Obstacle Barrier | 10 | 11 |
| U-Trap Backtrack | 12 | 28 |
| Tie-Break Demo | 10 | 17 |
| Clear Walls | 10 | 11 |

In *U-Trap Backtrack* the straight-line estimate is only 4, but the walls force a detour, so A* explores the pocket before backtracking. This shows that `h` is an estimate, not the true cost.

## Complexity

- **Time:** worst case O(b^d), where b is the branching factor and d is the solution depth. A good heuristic reduces this substantially.
- **Space:** O(b^d), since all generated nodes are kept in memory.
- Sorting the open list each step costs O(n log n); a binary heap would reduce extraction to O(log n).

## Tech Stack

HTML, CSS and vanilla JavaScript, with Tailwind CSS (CDN), Font Awesome icons and Google Fonts. No build step.

## Run Locally

```bash
git clone https://github.com/Adit-AoE/astar-state-space-explorer.git
cd astar-state-space-explorer
# open astar-explorer.html in any modern browser (internet needed for fonts and Tailwind CDN)
```

## Project Structure

```
astar-state-space-explorer/
├── astar-explorer.html   # the complete application (markup, styles and logic)
├── index.html            # GitHub Pages entry point; redirects to this repository
└── README.md
```

## Possible Improvements

- Replace the sorted array with a binary min-heap
- Add diagonal moves with octile distance
- Add weighted terrain costs
- Compare against BFS, Dijkstra and Greedy Best-First on the same grid
