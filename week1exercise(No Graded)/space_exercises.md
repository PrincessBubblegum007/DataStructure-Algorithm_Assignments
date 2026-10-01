# 1-analysis — Space Complexity Exercises

These exercises build up from counting variables to recursion stacks and
memory-constrained trade-offs. For each one, work out the space
complexity yourself before opening the answer.

## The exercises

| Exercise | Topic | Points |
|---|---|---|
| 1 | Counting Variables | 1 |
| 2 | New Array | 1 |
| 3 | Two Growing Structures | 1 |
| 4 | Matrix Allocation | 1 |
| 5 | Linear Recursion | 1 |
| 6 | Logarithmic Recursion | 1 |
| 7 | Common Trap | 1 |
| 8 | Compare Two Algorithms | 1 |
| 9 | Shrinking a DP Table | 1 |
| 10 | Memory-Constrained Sorting | 1 |

---

## Exercise 1 — Counting Variables

## What is the space complexity?

```text
function computeStats(a, n):
    sum = 0          // 1 cell
    count = 0        // 1 cell
    avg = 0.0        // 1 cell
    for i = 0 to n-1:      // 1 cell (i)
        sum = sum + a[i]
        count = count + 1
    avg = sum / count
    return avg
```

**Question:** Count the auxiliary memory cells this function allocates, then express the result as Big O. Does the array `a` count?

<details>
<summary>Show Answer</summary>

Extra variables: `sum, count, avg, i` → 4 cells, none of which grow with `n`.

**O(1)** auxiliary space.

The array `a` is **input space**, not auxiliary space — we don't count memory the caller already provided, only what the algorithm allocates *on top of it*.

</details>

---

## Exercise 2 — New Array

## What is the space complexity?

```text
function squareAll(a, n):
    b = new array[n]     // n cells
    for i = 0 to n-1:     // 1 cell (i)
        b[i] = a[i] * a[i]
    return b
```

**Question:** Identify every allocation this function makes beyond the input `a`, then give the auxiliary space in Big O.

<details>
<summary>Show Answer</summary>

Allocations: array `b` of size `n`, plus loop variable `i` (1 cell).

`space(n) = n + 1` → dominant term `n` → **O(n)**.

Unlike Exercise 1, this function returns a brand-new structure the same size as the input, so the auxiliary space grows linearly with `n` even though the algorithm's logic is a single simple loop.

</details>

---

## Exercise 3 — Two Growing Structures

## What is the space complexity?

```text
function firstDuplicates(a, n):
    seen = new Set()      // up to n entries
    result = []            // up to n entries
    for i = 0 to n-1:
        if seen.contains(a[i]):
            result.add(a[i])
        else:
            seen.add(a[i])
    return result
```

**Question:** Two structures grow during the loop. In the worst case, how many cells does each hold, and what is the overall Big O?

<details>
<summary>Show Answer</summary>

Worst case: every element is unique until the end, or all duplicates — either way `seen` can hold up to `n` entries and `result` up to `n` entries.

`space(n) = n + n = 2n` → drop the coefficient → **O(n)**.

Adding a second linear structure does not change the complexity *class* — two O(n) allocations are still O(n) overall, just as two sequential O(n) loops are still O(n) in time.

</details>

---

## Exercise 4 — Matrix Allocation

## What is the space complexity?

```text
function transpose(a, n):
    // a is an n x n matrix
    b = new array[n][n]   // n² cells
    for i = 0 to n-1:
        for j = 0 to n-1:
            b[j][i] = a[i][j]
    return b
```

**Question:** Give the auxiliary space in Big O. If `n = 100` and each cell is one memory unit, how many units does `b` use? What about `n = 1000`?

<details>
<summary>Show Answer</summary>

`b` is an `n × n` matrix → `n²` cells → **O(n²)** auxiliary space. The input matrix `a` is not counted — only the new matrix `b` that the algorithm allocates.

`n = 100`: `100² = 10 000` units. &nbsp; `n = 1000`: `1000² = 1 000 000` units.

A 10× increase in `n` produced a **100×** increase in memory — the signature of quadratic growth. Note that here time and space happen to share the same O(n²) class, but that is a coincidence of this particular algorithm, not a rule.

</details>

---

## Exercise 5 — Linear Recursion

## What is the space complexity?

```text
function sumRange(a, lo, hi):
    if lo > hi:
        return 0
    return a[lo] + sumRange(a, lo+1, hi)

// called as sumRange(a, 0, n-1)
```

```text
// Call stack for n=4 (lo=0..3):
// sumRange(0,3)
//   sumRange(1,3)
//     sumRange(2,3)
//       sumRange(3,3)
//         sumRange(4,3) ← base case, deepest
```

**Question:** Each recursive call adds one stack frame. In terms of `n = hi - lo + 1`, what is the maximum call depth, and what does that make the auxiliary space?

<details>
<summary>Show Answer</summary>

The function calls itself once per element until `lo > hi`, so the recursion reaches depth `n + 1` before unwinding.

Each frame stores a fixed number of local values (`lo`, `hi`, a return address), so total stack space is proportional to depth:

`space(n) = O(n)`.

Note this is *separate* from the array `a` — no new array is created here. The cost comes entirely from the call stack, not from data structures.

</details>

---

## Exercise 6 — Logarithmic Recursion

## What is the space complexity?

```text
function binarySearch(a, lo, hi, target):
    if lo > hi: return -1
    mid = (lo + hi) / 2
    if a[mid] == target: return mid
    if a[mid] < target:
        return binarySearch(a, mid+1, hi, target)
    return binarySearch(a, lo, mid-1, target)
```

```text
// Call stack for n=8:
// binarySearch(0,7)
//   binarySearch(4,7) or (0,2)
//     binarySearch(...)  ← range halves each call
//       binarySearch(...) ← deepest, 1 element left
```

**Question:** Each call halves the search range. In terms of `n`, what is the maximum recursion depth, and what is the auxiliary space? How would an **iterative** version compare?

<details>
<summary>Show Answer</summary>

The range shrinks by half every call, so after `k` calls the range size is `n / 2^k`. The recursion bottoms out when `n / 2^k ≈ 1`, i.e. `k ≈ log₂n`.

Depth = `log₂n` stack frames → **O(log n)** auxiliary space.

An **iterative** binary search (a `while` loop updating `lo`/`hi`) needs no call stack at all — just a few variables — giving **O(1)** space for the same O(log n) time. This is a common pattern: converting tail recursion to a loop trades away stack space for free.

</details>

---

## Exercise 7 — Common Trap

## Does exponential time mean exponential space?

```text
function fib(n):
    if n <= 1: return n
    return fib(n-1) + fib(n-2)
```

```text
// Call tree for fib(4) — many branches, but
// only ONE root-to-leaf path is on the stack
// at any instant:
// fib(4)
//   fib(3)
//     fib(2)
//       fib(1) ← deepest point reached
```

**Question:** This function has `O(2ⁿ)` *time* complexity because it makes exponentially many calls overall. Does it also use `O(2ⁿ)` *space*? Justify your answer using the call stack.

<details>
<summary>Show Answer</summary>

No — the space complexity is **O(n)**, not O(2ⁿ).

The recursion tree has exponentially many *nodes*, but a call stack only ever holds the frames on the *current path from root to the active call* — one branch at a time. Once `fib(n-1)` returns, its frames are popped before `fib(n-2)` is even called.

The deepest such path has length `n` (the chain `fib(n) → fib(n-1) → … → fib(1)`), so the stack never holds more than `n` frames.

**Lesson:** time complexity counts *total work done*; space complexity counts *memory alive at any one moment*. They must be analyzed separately — a huge number of operations does not imply a huge amount of simultaneous memory.

</details>

---

## Exercise 8 — Compare Two Algorithms

## Which one uses less space — and when would you use the other anyway?

**Algorithm A**

```text
function reverseInPlace(a, n):
  lo = 0
  hi = n - 1
  while lo < hi:
    swap(a, lo, hi)
    lo++; hi--
  // a is reversed directly
```

**Algorithm B**

```text
function reverseCopy(a, n):
  b = new array[n]
  for i = 0 to n-1:
    b[i] = a[n-1-i]
  return b
  // a is untouched
```

**Question:** Give the space complexity of each. Algorithm A is strictly more memory-efficient — so why might a real program still choose Algorithm B?

<details>
<summary>Show Answer</summary>

**Algorithm A:** only `lo` and `hi` — **O(1)** auxiliary space.

**Algorithm B:** allocates array `b` of size `n` — **O(n)** auxiliary space.

Despite using more memory, B is preferable whenever the original array must stay **unmodified** — e.g. other parts of the program still hold a reference to `a`, the data is shared across threads, or the function must be side-effect-free (a common requirement for pure/functional code). Space efficiency is one goal among several; correctness and safety requirements can outweigh it.

</details>

---

## Exercise 9 — Shrinking a DP Table

## What is the space complexity — and can it be reduced?

```text
function climbStairs(n):
    if n <= 2: return n
    memo = new array[n+1]   // n+1 cells
    memo[1] = 1
    memo[2] = 2
    for i = 3 to n:
        memo[i] = memo[i-1] + memo[i-2]
    return memo[n]
```

**Question:** Give the space complexity of `climbStairs` as written. Then look closely at what `memo[i]` actually depends on — can the array be avoided entirely?

<details>
<summary>Show Answer</summary>

As written: the array `memo` has `n+1` cells → **O(n)** auxiliary space.

But `memo[i]` only ever depends on `memo[i-1]` and `memo[i-2]` — the two most recent values. The whole table is never needed at once, only a two-value "window":

```text
function climbStairs(n):
    if n <= 2: return n
    prev2 = 1; prev1 = 2
    for i = 3 to n:
        cur = prev1 + prev2
        prev2 = prev1; prev1 = cur
    return prev1
```

Replacing the array with a few rolling variables reduces space to **O(1)** while keeping the same O(n) time. This "rolling variable" trick applies whenever a DP recurrence only looks back a fixed, constant number of steps.

</details>

---

## Exercise 10 — Memory-Constrained Sorting

## Which algorithm can run on 2 KB of extra RAM?

**Algorithm A — Selection sort**

```text
function selectionSort(a, n):
  for i = 0 to n-2:
    minIdx = i
    for j = i+1 to n-1:
      if a[j] < a[minIdx]:
        minIdx = j
    swap(a, i, minIdx)
  // sorts a[] directly, in place
```

**Algorithm B — Merge sort**

```text
function mergeSort(a, n):
  if n <= 1: return a
  mid = n / 2
  left  = mergeSort(a[0..mid])
  right = mergeSort(a[mid..n])
  return merge(left, right)
  // merge() allocates a helper array
```

**Question:** Give the auxiliary space complexity of each algorithm. An embedded device has only **2 KB** of extra RAM and must sort an array of **100 000** 4-byte integers (400 KB of input data). Which algorithm can it run, and why?

<details>
<summary>Show Answer</summary>

**Selection sort:** only a handful of index variables (`i, j, minIdx`) → `O(1)` auxiliary space, regardless of `n`.

**Merge sort:** the `merge` step allocates a helper array proportional to the slice being merged, plus `O(log n)` recursion stack → `O(n)` auxiliary space overall.

For `n = 100 000`: merge sort needs roughly `100 000 × 4 bytes ≈ 400 KB` of extra memory for its helper arrays — far more than the 2 KB budget. Selection sort needs only a few bytes, comfortably fitting the constraint.

**Selection sort is the only option here**, even though it is O(n²) in time versus merge sort's O(n log n) — on memory-constrained hardware, the space bound can rule out an otherwise faster algorithm entirely.

</details>
