# Learning Architecture

## 1. Learning principles

- Teach one concept at a time, then combine concepts through patterns.
- Require active recall through quizzes before coding-heavy practice.
- Use visual simulations for stateful algorithms.
- Move from implementation fluency to pattern recognition to optimization.
- Track mastery per topic using lesson completion, quiz score, accepted submissions, and recency.

## 2. Topic dependency graph

| Topic | Direct prerequisites | Unlocks |
| --- | --- | --- |
| Complexity Analysis | None | All topics |
| Arrays | Complexity Analysis | Strings, Searching, Sorting, Hashing, DP |
| Strings | Arrays | Hashing, Tries, DP |
| Linked Lists | Complexity Analysis | Stacks, Queues, Trees fundamentals |
| Stacks | Arrays, Linked Lists | Recursion, Trees, Backtracking |
| Queues | Arrays, Linked Lists | BFS, Graphs, Heaps |
| Recursion | Complexity Analysis, Stacks | Trees, Backtracking, Divide and Conquer, DP |
| Sorting | Arrays, Complexity Analysis | Searching, Greedy, Divide and Conquer |
| Searching | Arrays, Sorting | Binary search patterns, Trees, Graph traversal |
| Hashing | Arrays, Strings | Graphs, DP memoization, interview patterns |
| Trees | Recursion, Queues | Heaps, Tries, Graphs, DP on trees |
| Heaps | Trees, Arrays | Greedy, graph shortest paths |
| Tries | Strings, Trees | Autocomplete, prefix matching, word search |
| Graphs | Trees, Queues, Hashing | Greedy graph algorithms, DP on graphs |
| Dynamic Programming | Recursion, Arrays, Hashing | Advanced optimization problems |
| Greedy Algorithms | Sorting, Heaps | Scheduling, interval, graph MST patterns |
| Backtracking | Recursion, Stacks | Constraint search, combinatorics |
| Divide and Conquer | Recursion, Sorting, Searching | Advanced sorting/searching and DP optimization |

## 3. Learning roadmap

### Phase A: Foundations

1. Complexity Analysis
2. Arrays
3. Strings
4. Linked Lists
5. Stacks
6. Queues

Outcomes:

- Explain Big O, Big Omega, and Big Theta.
- Choose between array, string, linked list, stack, and queue representations.
- Implement traversal, insertion, deletion, and simple transformations.
- Solve beginner problems without relying on brute force alone.

### Phase B: Core problem-solving tools

1. Recursion
2. Sorting
3. Searching
4. Hashing

Outcomes:

- Trace recursive calls and base cases.
- Implement standard sorting/searching algorithms.
- Use hash maps/sets for frequency, lookup, grouping, and deduplication.
- Analyze time-space tradeoffs.

### Phase C: Hierarchical structures

1. Trees
2. Heaps
3. Tries

Outcomes:

- Traverse trees with DFS and BFS.
- Use heaps for priority scheduling and top-k problems.
- Use tries for prefix and dictionary problems.
- Recognize recursive structure in hierarchical data.

### Phase D: Networked structures and patterns

1. Graphs
2. Greedy Algorithms
3. Backtracking
4. Divide and Conquer

Outcomes:

- Model relationships as graphs.
- Apply BFS, DFS, topological sorting, shortest path, and connected components.
- Use greedy choices when exchange arguments or local optimality apply.
- Explore search spaces with pruning.
- Split problems into independent subproblems.

### Phase E: Advanced optimization

1. Dynamic Programming
2. Mixed-topic interview sets
3. Timed assessments

Outcomes:

- Identify overlapping subproblems and optimal substructure.
- Convert recursion to memoization and tabulation.
- Optimize space in DP tables.
- Combine multiple patterns in interview-style problems.

## 4. Beginner to advanced progression

| Level | Learner behavior | Platform support | Assessment style |
| --- | --- | --- | --- |
| Beginner | Needs definitions, examples, and guided practice | Linear roadmap, visualizations, starter code | Concept checks and easy coding tasks |
| Intermediate | Can implement known structures but needs pattern recognition | Tagged practice sets, hints, complexity prompts | Mixed easy/medium problems |
| Advanced | Optimizes and combines patterns | Timed sets, hidden tests, code review | Medium/hard problems and design explanations |
| Interview-ready | Explains tradeoffs clearly under constraints | Mock interviews, weak-area analytics | Timed multi-topic assessments |

## 5. Mastery model

Each topic receives a mastery score from 0 to 100.

| Signal | Weight |
| --- | --- |
| Lesson completion | 15% |
| Quiz accuracy | 20% |
| Accepted coding problems | 35% |
| Recent successful submissions | 10% |
| Assessment score | 15% |
| Explanation quality or AI-reviewed reflection | 5% |

Suggested topic states:

- `LOCKED`: prerequisites incomplete.
- `AVAILABLE`: learner can start.
- `IN_PROGRESS`: at least one lesson or problem attempted.
- `PRACTICE_READY`: core lessons complete.
- `MASTERED`: mastery score above threshold and assessment passed.
- `NEEDS_REVIEW`: score decayed or recent attempts failed.

## 6. Recommendation rules

- Recommend the next unlocked lesson with the lowest mastery score.
- If quiz score is low, recommend conceptual review before more coding.
- If compile/runtime errors dominate, recommend language-specific debugging help.
- If wrong answers dominate, recommend examples, trace tables, and hints.
- If accepted solutions are consistently inefficient, recommend complexity optimization lessons.
