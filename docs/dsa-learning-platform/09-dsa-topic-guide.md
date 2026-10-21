# DSA Topic Guide

Code examples use TypeScript-style JavaScript for readability.

## 1. Arrays

**Definition:** Contiguous indexed collection with O(1) random access.

**Real-world use cases:** Lists, buffers, matrices, time-series samples, UI rows.

**Complexity:** Access O(1), search O(n), append amortized O(1), insert/delete middle O(n), space O(n).

**Common interview questions:** Two Sum, Best Time to Buy and Sell Stock, Rotate Array, Maximum Subarray.

```ts
function maxSubArray(nums: number[]): number {
  let best = nums[0], current = nums[0];
  for (let i = 1; i < nums.length; i++) {
    current = Math.max(nums[i], current + nums[i]);
    best = Math.max(best, current);
  }
  return best;
}
```

## 2. Strings

**Definition:** Sequence of characters, often treated as an array with encoding constraints.

**Real-world use cases:** Search, validation, parsing, logs, text editors, URLs.

**Complexity:** Index O(1) in many languages, search O(n*m) naive, concatenation varies, space O(n).

**Common interview questions:** Valid Anagram, Longest Substring Without Repeating Characters, Palindrome Check, String Compression.

```ts
function isPalindrome(s: string): boolean {
  let l = 0, r = s.length - 1;
  while (l < r) {
    while (l < r && !/[a-z0-9]/i.test(s[l])) l++;
    while (l < r && !/[a-z0-9]/i.test(s[r])) r--;
    if (s[l].toLowerCase() !== s[r].toLowerCase()) return false;
    l++; r--;
  }
  return true;
}
```

## 3. Linked Lists

**Definition:** Nodes connected by references; each node stores data and next pointer.

**Real-world use cases:** Undo stacks, queues, memory allocators, LRU internals.

**Complexity:** Access O(n), search O(n), insert/delete known node O(1), space O(n).

**Common interview questions:** Reverse Linked List, Detect Cycle, Merge Two Sorted Lists, Remove Nth Node.

```ts
type ListNode = { val: number; next: ListNode | null };

function reverseList(head: ListNode | null): ListNode | null {
  let prev: ListNode | null = null;
  let cur = head;
  while (cur) {
    const next = cur.next;
    cur.next = prev;
    prev = cur;
    cur = next;
  }
  return prev;
}
```

## 4. Stacks

**Definition:** Last-in-first-out collection.

**Real-world use cases:** Call stack, undo, browser history, parsing, expression evaluation.

**Complexity:** Push O(1), pop O(1), peek O(1), search O(n), space O(n).

**Common interview questions:** Valid Parentheses, Min Stack, Daily Temperatures, Evaluate Reverse Polish Notation.

```ts
function isValidParentheses(s: string): boolean {
  const stack: string[] = [];
  const closeToOpen: Record<string, string> = { ")": "(", "]": "[", "}": "{" };
  for (const ch of s) {
    if (ch in closeToOpen) {
      if (stack.pop() !== closeToOpen[ch]) return false;
    } else {
      stack.push(ch);
    }
  }
  return stack.length === 0;
}
```

## 5. Queues

**Definition:** First-in-first-out collection.

**Real-world use cases:** Job queues, BFS, event loops, request buffering.

**Complexity:** Enqueue O(1), dequeue O(1) with proper implementation, peek O(1), space O(n).

**Common interview questions:** Implement Queue with Stacks, Number of Islands, Rotting Oranges, Level Order Traversal.

```ts
class Queue<T> {
  private data: T[] = [];
  private head = 0;

  enqueue(item: T) { this.data.push(item); }
  dequeue(): T | undefined {
    if (this.head >= this.data.length) return undefined;
    return this.data[this.head++];
  }
  get length() { return this.data.length - this.head; }
}
```

## 6. Recursion

**Definition:** A technique where a function solves a problem by calling itself on smaller inputs.

**Real-world use cases:** File trees, parsers, tree traversal, divide and conquer, backtracking.

**Complexity:** Depends on recurrence; stack space equals recursion depth.

**Common interview questions:** Fibonacci, Generate Parentheses, Permutations, Tree DFS.

```ts
function factorial(n: number): number {
  if (n <= 1) return 1;
  return n * factorial(n - 1);
}
```

## 7. Sorting

**Definition:** Reordering data according to a comparator.

**Real-world use cases:** Search optimization, reports, rankings, deduplication, scheduling.

**Complexity:** Merge sort O(n log n) time and O(n) space; quicksort average O(n log n), worst O(n^2); insertion sort O(n^2), useful for small/nearly sorted inputs.

**Common interview questions:** Merge Intervals, Sort Colors, K Closest Points, Meeting Rooms.

```ts
function mergeSort(nums: number[]): number[] {
  if (nums.length <= 1) return nums;
  const mid = Math.floor(nums.length / 2);
  const left = mergeSort(nums.slice(0, mid));
  const right = mergeSort(nums.slice(mid));
  const out: number[] = [];
  let i = 0, j = 0;
  while (i < left.length || j < right.length) {
    if (j === right.length || (i < left.length && left[i] <= right[j])) out.push(left[i++]);
    else out.push(right[j++]);
  }
  return out;
}
```

## 8. Searching

**Definition:** Finding an element or boundary in a search space.

**Real-world use cases:** Database lookups, autocomplete, inventory search, threshold detection.

**Complexity:** Linear search O(n); binary search O(log n) on sorted/monotonic data; space O(1) iterative.

**Common interview questions:** Binary Search, Search Insert Position, Find Minimum in Rotated Sorted Array, First Bad Version.

```ts
function binarySearch(nums: number[], target: number): number {
  let l = 0, r = nums.length - 1;
  while (l <= r) {
    const m = l + Math.floor((r - l) / 2);
    if (nums[m] === target) return m;
    if (nums[m] < target) l = m + 1;
    else r = m - 1;
  }
  return -1;
}
```

## 9. Hashing

**Definition:** Mapping keys to values through a hash function for fast lookup.

**Real-world use cases:** Caches, indexes, deduplication, frequency counts, session stores.

**Complexity:** Average insert/search/delete O(1), worst O(n) with collisions, space O(n).

**Common interview questions:** Two Sum, Group Anagrams, Longest Consecutive Sequence, Subarray Sum Equals K.

```ts
function twoSum(nums: number[], target: number): number[] {
  const seen = new Map<number, number>();
  for (let i = 0; i < nums.length; i++) {
    const need = target - nums[i];
    if (seen.has(need)) return [seen.get(need)!, i];
    seen.set(nums[i], i);
  }
  return [];
}
```

## 10. Trees

**Definition:** Hierarchical nodes with parent-child relationships and no cycles.

**Real-world use cases:** DOM, filesystems, organization charts, indexes, expression parsing.

**Complexity:** Traversal O(n); balanced BST search/insert/delete O(log n); unbalanced BST can be O(n); space O(h) recursion.

**Common interview questions:** Maximum Depth, Validate BST, Lowest Common Ancestor, Binary Tree Level Order Traversal.

```ts
type TreeNode = { val: number; left: TreeNode | null; right: TreeNode | null };

function maxDepth(root: TreeNode | null): number {
  if (!root) return 0;
  return 1 + Math.max(maxDepth(root.left), maxDepth(root.right));
}
```

## 11. Heaps

**Definition:** Complete tree usually stored in an array where parent priority dominates children.

**Real-world use cases:** Priority queues, schedulers, top-k, streaming medians, Dijkstra.

**Complexity:** Peek O(1), insert O(log n), extract O(log n), heapify O(n), space O(n).

**Common interview questions:** Kth Largest Element, Merge K Sorted Lists, Top K Frequent Elements, Find Median from Data Stream.

```ts
class MinHeap {
  private a: number[] = [];
  push(x: number) {
    this.a.push(x);
    for (let i = this.a.length - 1; i > 0;) {
      const p = Math.floor((i - 1) / 2);
      if (this.a[p] <= this.a[i]) break;
      [this.a[p], this.a[i]] = [this.a[i], this.a[p]];
      i = p;
    }
  }
}
```

## 12. Tries

**Definition:** Prefix tree where edges represent characters or tokens.

**Real-world use cases:** Autocomplete, spellcheck, IP routing, dictionary lookup, prefix search.

**Complexity:** Insert/search O(k) where k is key length; space O(total characters).

**Common interview questions:** Implement Trie, Word Search II, Design Add and Search Words, Replace Words.

```ts
class TrieNode {
  children = new Map<string, TrieNode>();
  isWord = false;
}

class Trie {
  root = new TrieNode();
  insert(word: string) {
    let node = this.root;
    for (const ch of word) {
      if (!node.children.has(ch)) node.children.set(ch, new TrieNode());
      node = node.children.get(ch)!;
    }
    node.isWord = true;
  }
}
```

## 13. Graphs

**Definition:** Vertices connected by edges; edges may be directed, undirected, weighted, or unweighted.

**Real-world use cases:** Maps, social networks, dependency graphs, recommendation systems, network routing.

**Complexity:** BFS/DFS O(V + E), adjacency list space O(V + E), adjacency matrix space O(V^2).

**Common interview questions:** Number of Islands, Course Schedule, Clone Graph, Network Delay Time.

```ts
function bfs(graph: Map<string, string[]>, start: string): string[] {
  const seen = new Set([start]);
  const q = [start];
  const order: string[] = [];
  for (let head = 0; head < q.length; head++) {
    const node = q[head];
    order.push(node);
    for (const next of graph.get(node) ?? []) {
      if (!seen.has(next)) {
        seen.add(next);
        q.push(next);
      }
    }
  }
  return order;
}
```

## 14. Dynamic Programming

**Definition:** Solving problems with overlapping subproblems and optimal substructure using memoization or tabulation.

**Real-world use cases:** Route planning, resource allocation, sequence alignment, recommendation scoring, pricing.

**Complexity:** Usually number of states times transition cost; space can often be optimized.

**Common interview questions:** Climbing Stairs, Coin Change, Longest Increasing Subsequence, 0/1 Knapsack.

```ts
function climbStairs(n: number): number {
  let a = 1, b = 1;
  for (let i = 2; i <= n; i++) {
    [a, b] = [b, a + b];
  }
  return b;
}
```

## 15. Greedy Algorithms

**Definition:** Builds a solution through locally optimal choices that can be proven globally optimal.

**Real-world use cases:** Scheduling, compression, routing approximations, interval selection, caching.

**Complexity:** Often sorting O(n log n) plus scan O(n); space often O(1) or O(n).

**Common interview questions:** Jump Game, Gas Station, Non-overlapping Intervals, Task Scheduler.

```ts
function eraseOverlapIntervals(intervals: number[][]): number {
  intervals.sort((a, b) => a[1] - b[1]);
  let removed = 0, end = -Infinity;
  for (const [s, e] of intervals) {
    if (s < end) removed++;
    else end = e;
  }
  return removed;
}
```

## 16. Backtracking

**Definition:** Depth-first search over choices, undoing choices when they cannot lead to valid solutions.

**Real-world use cases:** Constraint solving, scheduling, puzzles, combinations, configuration generation.

**Complexity:** Often exponential; pruning reduces explored branches; recursion space O(depth).

**Common interview questions:** Subsets, Permutations, Combination Sum, N-Queens, Sudoku Solver.

```ts
function subsets(nums: number[]): number[][] {
  const result: number[][] = [];
  const path: number[] = [];
  function dfs(i: number) {
    if (i === nums.length) {
      result.push([...path]);
      return;
    }
    dfs(i + 1);
    path.push(nums[i]);
    dfs(i + 1);
    path.pop();
  }
  dfs(0);
  return result;
}
```

## 17. Divide and Conquer

**Definition:** Splits a problem into smaller independent subproblems, solves them, and combines results.

**Real-world use cases:** Sorting, parallel computation, image processing, search, large-scale aggregation.

**Complexity:** Described by recurrences such as T(n) = 2T(n/2) + O(n), commonly O(n log n); recursion space often O(log n).

**Common interview questions:** Merge Sort, Quick Sort, Search a 2D Matrix, Maximum Subarray Divide and Conquer.

```ts
function pow(x: number, n: number): number {
  if (n === 0) return 1;
  if (n < 0) return 1 / pow(x, -n);
  const half = pow(x, Math.floor(n / 2));
  return n % 2 === 0 ? half * half : half * half * x;
}
```

## 18. Complexity Analysis

**Definition:** Mathematical analysis of how runtime and memory grow with input size.

**Real-world use cases:** Capacity planning, algorithm selection, performance reviews, scalability decisions.

**Complexity:** Common orders are O(1), O(log n), O(n), O(n log n), O(n^2), O(2^n), and O(n!).

**Common interview questions:** Analyze nested loops, compare hash map vs array scan, optimize brute force, derive recursion complexity.

```ts
function hasDuplicate(nums: number[]): boolean {
  const seen = new Set<number>();
  for (const n of nums) {
    if (seen.has(n)) return true;
    seen.add(n);
  }
  return false; // O(n) time, O(n) space
}
```
