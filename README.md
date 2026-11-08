## Campus Navigation and Path Finding System

**Design and Analysis of Algorithms (DAA) – 5th Semester Mini Project**
B.Tech Information Technology

An offline, academic prototype that models a campus as a weighted graph and compares four search algorithms — **BFS, DFS, Dijkstra's Algorithm and A\* Search** — on route cost, nodes explored and execution time.

## Team

| Name | Enrolment No. |
|------|---------------|
| Parth Thumar | 12402080601102 |
| Rahul Vanani | 12402080601112 |
| Mann Patel | 12502080603008 |

---

## Table of Contents

1. [Overview](#overview)
2. [Features](#features)
3. [Problem Definition](#problem-definition)
4. [Graph Model](#graph-model)
5. [Algorithms](#algorithms)
6. [Data Structures](#data-structures)
7. [System Architecture and Flow](#system-architecture-and-flow)
8. [Requirements and Installation](#requirements-and-installation)
9. [Usage](#usage)
10. [Experimental Setup](#experimental-setup)
11. [Results](#results)
12. [Complexity Analysis](#complexity-analysis)
13. [Testing](#testing)
14. [Limitations](#limitations)
15. [Future Scope](#future-scope)
16. [References](#references)

---

## Overview

Campus navigation can be formulated as a shortest-path problem on a weighted graph. In this project:

- **Vertices** are named campus locations (e.g. Main Gate, Library, Hostel B).
- **Edges** are two-way walkways.
- **Edge weights** are Euclidean lengths computed from stored (x, y) coordinates, rounded to one decimal place.

Dijkstra and A\* are the principal weighted shortest-path algorithms. BFS and DFS serve as educational baselines to show why fewest-edges or first-found paths do not minimise weighted distance.

> **Note:** This is an offline academic simulation. It does **not** provide live GPS, traffic, GIS integration, mobile positioning or production navigation capability.

## Features

- Weighted adjacency-list graph of 12 campus locations and 19 bidirectional roads (38 adjacency entries)
- BFS, DFS, Dijkstra and A\* implemented from scratch
- Path reconstruction using predecessor maps
- Metrics for every query: path cost (m), explored-node order/count, execution time
- Deterministic benchmark on generated connected graphs of 10, 50, 100 and 500 nodes (200 repetitions per algorithm and size)
- Live Matplotlib visualisations: search exploration, route comparison, performance graphs and the final shortest-path map

## Problem Definition

**Input**
- Source and destination location names (typed at the terminal; exact name matching)
- The fixed `LOCATIONS` coordinate dictionary and `ROADS` list
- Edge lengths produced by `build_graph()`
- For benchmarking: generated connected graphs of 10, 50, 100 and 500 nodes

**Output**
- Ordered path from source to destination
- Total path cost in metres
- Explored-node order and count
- Measured execution time and Matplotlib visualisations

**Objective:** minimise the sum of edge weights along the route, and compare the algorithms on the same graph, source and destination.

**Assumptions:** static graph, offline operation, two-way roads, non-negative edge weights, synthetic benchmark coordinates.

## Graph Model

- `LOCATIONS` – 12 named vertices with (x, y) coordinates
- `ROADS` – 19 endpoint pairs
- `build_graph()` – computes each road's Euclidean length, rounds to one decimal, stores both directions
- `random_graph()` – builds connected benchmark graphs by linking each new node to its nearest earlier node and adding edges to up to three nearest neighbours

## Algorithms

### BFS
Uses a FIFO queue (`deque`). Optimal only when all edges have equal cost; used here as a baseline.

### DFS
Uses a LIFO stack (list). A traversal baseline that returns the first path found, which is often not the shortest.

### Dijkstra's Algorithm
Initialises all best distances to infinity, sets the source to 0 and uses a binary min-heap. It repeatedly extracts the smallest tentative distance, skips completed nodes, relaxes neighbours and updates parent pointers. The target is returned when extracted. Correct for non-negative weights.

```
DIJKSTRA(G, s, t)
  best[v] ← ∞ for every v; best[s] ← 0
  parent[s] ← NIL; heap ← {(0, s)}; done ← ∅
  while heap ≠ ∅:
    (d, u) ← extract-min(heap)
    if u ∈ done: continue
    add u to done and explored
    if u = t: return rebuild_path(parent, t)
    for (v, w) in G[u]:
      if d + w < best[v]: update best[v], parent[v], heap
  return no path
```

### A\* Search
Uses `f(n) = g(n) + h(n)`, where `g(n)` is the accumulated edge length and `h(n)` is the straight-line Euclidean distance to the destination. Because edge weights are straight-line distances between connected coordinates, the heuristic is a lower bound, so optimality holds **under this weight interpretation** (it should not be assumed for arbitrary weights).

```
A_STAR(G, s, t, pos)
  g[v] ← ∞ for every v; g[s] ← 0
  heap ← {(h(s), s)}; parent[s] ← NIL; done ← ∅
  while heap ≠ ∅:
    (_, u) ← extract-min(heap)
    if u ∈ done: continue
    add u to done and explored
    if u = t: return rebuild_path(parent, t)
    for (v, w) in G[u]:
      if g[u] + w < g[v]: update g[v], parent[v], heap with g[v] + h(v)
  return no path
```

### Dijkstra vs A\*

| Parameter | Dijkstra | A\* |
|-----------|----------|-----|
| Priority | g(n) | g(n) + h(n) |
| Heuristic | None | Euclidean distance to destination |
| Optimality | Non-negative weights | Under heuristic/weight assumptions |
| Direction | Expands by source distance | Directed toward goal |
| Data structure | Adjacency list + heap | Adjacency list + heap |

## Data Structures

| Structure | Purpose | Used by |
|-----------|---------|---------|
| Adjacency-list dictionary | Stores neighbours and edge weights | All algorithms |
| Parent dictionary | Predecessor for path reconstruction | All path searches |
| `deque` | FIFO frontier | BFS |
| List stack | LIFO frontier | DFS |
| `heapq` min-heap | Selects smallest priority | Dijkstra, A\* |
| `done` / visited set | Prevents repeated expansion | DFS, Dijkstra, A\* |
| Explored list | Records search order and count | All algorithms |

## System Architecture and Flow

```
User input (source, destination)
        ↓
Campus model (12 locations + 19 bidirectional roads)
        ↓
Algorithms (BFS | DFS | Dijkstra | A*)
        ↓
Metrics (path cost | explored nodes | time)
        ↓
Visual output (live search, route comparison, performance graphs, shortest-path map)
```

Program flow (from `main()`):

1. Build the weighted adjacency-list graph
2. Read and validate source and destination names
3. Run all four algorithms and measure results
4. Run the 200-repeat benchmark on 10/50/100/500-node graphs
5. Display selected live graphs
6. Display the final shortest-path graph

## Requirements and Installation

- Python 3
- `matplotlib`
- `numpy`

Standard-library modules used: `heapq`, `math`, `random`, `time`, `collections.deque`.

```bash
git clone <your-repository-url>
cd <repository-folder>
pip install matplotlib numpy
```

Any machine able to run Python and open Matplotlib windows is sufficient.

## Usage

The project is a single Python file (rename as needed; the report refers to it as `campus_navigation_live(2).py`).

```bash
python campus_navigation_live.py
```

1. Enter the **source** and **destination** exactly as named in `LOCATIONS`, or press **Enter** to use the defaults (Main Gate → Hostel B).
2. Read the printed metrics for each algorithm.
3. Choose one or more graph windows to view.
4. The program finishes by showing the shortest-path graph.

Available locations: Main Gate, Admin Block, Library, Canteen, Hostel A, Hostel B, Parking, Auditorium, CS Dept, Medical Centre, Labs, Sports Ground.

## Experimental Setup

| Item | Method |
|------|--------|
| Graph sizes | 10, 50, 100, 500 nodes |
| Graph generator | Connected coordinate graph with nearest-neighbour additions |
| Seed | 1 |
| Repetitions | 200 per algorithm and size |
| Source / destination | 0 and n−1 for generated graphs |
| Timing | `time.perf_counter()`; elapsed time divided by 200 |
| Metrics | Average time, explored nodes, path cost |

Timing is machine-dependent. Hardware/software versions were not recorded.

## Results

### Default query: Main Gate → Hostel B

| Algorithm | Path cost | Nodes explored |
|-----------|-----------|----------------|
| BFS | 591.4 m | 11 |
| DFS | 1019.5 m | 8 |
| Dijkstra | 591.4 m | 12 |
| A\* | 591.4 m | 5 |

### Benchmark summary

| Nodes | Algorithm | Avg time (ms) | Explored | Cost (m) |
|------:|-----------|--------------:|---------:|---------:|
| 10 | BFS | 0.0038 | 9 | 1127.6 |
| 10 | DFS | 0.0038 | 7 | 2307.7 |
| 10 | Dijkstra | 0.0080 | 9 | 1127.6 |
| 10 | A\* | 0.0086 | 3 | 1127.6 |
| 50 | BFS | 0.0107 | 24 | 1265.6 |
| 50 | DFS | 0.0238 | 48 | 1320.3 |
| 50 | Dijkstra | 0.0295 | 24 | 740.2 |
| 50 | A\* | 0.0229 | 6 | 740.2 |
| 100 | BFS | 0.0252 | 46 | 661.3 |
| 100 | DFS | 0.0138 | 17 | 1325.7 |
| 100 | Dijkstra | 0.0493 | 27 | 617.3 |
| 100 | A\* | 0.0364 | 15 | 617.3 |
| 500 | BFS | 0.1886 | 424 | 421.1 |
| 500 | DFS | 0.0183 | 31 | 1296.7 |
| 500 | Dijkstra | 0.0856 | 51 | 251.5 |
| 500 | A\* | 0.0518 | 9 | 251.5 |

### Interpretation

- A\* matched Dijkstra's path cost at every size and explored fewer nodes (3 vs 9, 6 vs 24, 15 vs 27, 9 vs 51).
- Timing was mixed: at 10 nodes A\* was slightly slower than Dijkstra; at 50, 100 and 500 nodes it was faster. A\* is therefore **not** claimed to be universally faster.
- BFS matched the optimal cost only at 10 nodes; DFS produced longer routes at every size. Both are baselines, not weighted shortest-path algorithms.
- Results depend on graph structure, adjacency ordering, interpreter overhead and the single seeded graph per size.

## Complexity Analysis

V = number of vertices, E = number of adjacency entries.

| Algorithm | Best / typical | Worst case | Auxiliary space | Graph storage |
|-----------|----------------|------------|-----------------|---------------|
| BFS | Immediate target O(1); traversal O(V+E) | O(V+E) | O(V) | O(V+E) |
| DFS | Immediate target O(1); traversal O(V+E) | O(V+E) | O(V) | O(V+E) |
| Dijkstra + heap | Early target may stop sooner | O((V+E) log V) | O(V+E) | O(V+E) |
| A\* + heap | Depends on heuristic direction | O((V+E) log V) | O(V+E) | O(V+E) |

A lower explored-node count does not guarantee lower wall-clock time: dictionary lookups, heap operations, heuristic calls and Python overhead all affect small-graph timing.

## Testing

No automated test suite was supplied. Verification status:

| ID | Check | Status |
|----|-------|--------|
| V-01 | Fixed graph builds with 12 locations and 19 roads | Verified |
| V-02 | Default Main Gate → Hostel B query executes | Verified |
| V-03 | Dijkstra and A\* costs match on default query | Verified (591.4 m each) |
| V-04 | Benchmark runs four graph sizes with 200 repeats | Verified |
| V-05 | Invalid-name branch exists in `main()` | Code inspected; not separately executed |
| V-06 | Unreachable destination behaviour | Not tested |
| V-07 | Source equals destination behaviour | Not tested |

## Limitations

- Static, embedded campus map
- Synthetic and small model
- No GPS, GIS, traffic or closures
- Hardware/version metadata not recorded
- Plots are not automatically exported by the app
- One seeded graph per size does not represent all possible graphs

## Future Scope

- Replace the fixed graph with validated campus GIS data
- Add automated unit and integration tests (unreachable graphs, source = destination, invalid names, path correctness, benchmark consistency)
- Export benchmark CSV/JSON and figures automatically
- Accessibility-aware routing and multiple route alternatives
- Temporary closures or dynamic weights
- Record full environment metadata with benchmark runs
- Mobile or browser deployment as future engineering work

## References

1. E. W. Dijkstra, "A Note on Two Problems in Connexion with Graphs," *Numerische Mathematik*, vol. 1, pp. 269–271, 1959.
2. P. E. Hart, N. J. Nilsson, and B. Raphael, "A Formal Basis for the Heuristic Determination of Minimum Cost Paths," *IEEE Transactions on Systems Science and Cybernetics*, vol. 4, no. 2, pp. 100–107, 1968.
3. T. H. Cormen, C. E. Leiserson, R. L. Rivest, and C. Stein, *Introduction to Algorithms*, 3rd ed., MIT Press, 2009.
4. R. Sedgewick and K. Wayne, *Algorithms*, 4th ed., Addison-Wesley, 2011.
5. Python Software Foundation, *Python 3 Documentation* (heapq, collections, math, random, time).
6. Matplotlib Development Team, *Matplotlib Documentation*.
