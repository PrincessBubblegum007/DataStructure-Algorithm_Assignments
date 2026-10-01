# 1-analysis — Time Complexity Exercises

These exercises build up from simple loops to recursion and algorithm
comparison. For each one, work out the time complexity yourself before
opening the answer.

## The exercises

| Exercise | Topic | Points |
|---|---|---|
| 1 | Simple Loop | 1 |
| 2 | Sequential Loops | 1 |
| 3 | Nested Loops | 1 |
| 4 | Function Call in Loop | 1 |
| 5 | Expensive Function in Loop | 1 |
| 6 | Doubling Loop | 1 |
| 7 | Best & Worst Case | 1 |
| 8 | Recursion | 1 |
| 9 | Three Nested Loops | 1 |
| 10 | Compare Algorithms | 1 |

---

## Exercise 1 — Simple Loop

## What is the time complexity?

```text
function sumSquares(a, n):
    total = 0                      // 1 step
    for i = 0 to n-1:             // n iterations
        total = total + a[i]*a[i] // 2 steps per iteration
    return total                   // 1 step
```

**Question:** Write t(n) by counting every step, then express the result as Big O.

<details>
<summary>Show Answer</summary>

`t(n) = 1 + n×2 + 1 = 2n + 2`

Dominant term: `2n`. Drop the coefficient and constant → **O(n)**.

The loop visits every element exactly once, so growth is linear regardless of the values in `a`.

</details>

---

## Exercise 2 — Sequential Loops

## What is the time complexity?

```text
function stats(a, n):
    sum = 0
    for i = 0 to n-1:       // loop 1
        sum = sum + a[i]
    max = a[0]
    for i = 1 to n-1:       // loop 2
        if a[i] > max:
            max = a[i]
    return sum, max
```

**Question:** Is this O(n) or O(n²)? The function contains two loops — does that make it quadratic?

<details>
<summary>Show Answer</summary>

Loop 1 runs `n` iterations. Loop 2 runs `n-1` iterations. They run *one after the other*, so costs are **added**:

`t(n) = n + (n-1) = 2n - 1 → O(n)`

It is **not O(n²)**. Quadratic requires one loop *inside* another. Two sequential loops each of size n gives O(n), not O(n²).

</details>

---

## Exercise 3 — Nested Loops

## What is the time complexity?

```text
function countDuplicates(a, n):
    count = 0
    for i = 0 to n-1:          // outer loop
        for j = i+1 to n-1:    // inner loop starts at i+1
            if a[i] == a[j]:
                count = count + 1
    return count
```

**Question:** The inner loop does not always run n times — does that change the Big O class?

<details>
<summary>Show Answer</summary>

Inner iterations per outer step: `(n-1) + (n-2) + … + 1 = n(n-1)/2`

`t(n) = n(n-1)/2 ≈ n²/2 → O(n²)`

The halved constant ½ is dropped. Starting the inner loop at `i+1` saves exactly half the work compared to starting at `0`, but Big O does not distinguish between `n²` and `n²/2`.

</details>

---

## Exercise 4 — Function Call in Loop

## What is the time complexity of `applyAll`?

```text
function clamp(x, lo, hi):  // keeps x in range [lo, hi]
    if x < lo: return lo     // O(1)
    if x > hi: return hi     // O(1)
    return x                  // O(1)

function applyAll(a, n, lo, hi):
    for i = 0 to n-1:
        a[i] = clamp(a[i], lo, hi)
```

**Question:** First determine the complexity of `clamp`, then use that to find the complexity of `applyAll`.

<details>
<summary>Show Answer</summary>

`clamp` executes at most 3 comparisons/returns — no loops, no recursion → `O(1)`.

`applyAll` calls `clamp` once per iteration over `n` elements:

`t(n) = n × O(1) = O(n)`

Rule: **loop iterations × cost per iteration**. An O(1) function in a linear loop stays O(n).

</details>

---

## Exercise 5 — Expensive Function in Loop

## What is the time complexity of `printRows`?

```text
function printLine(a, n):   // prints every element
    for i = 0 to n-1:
        print(a[i])          // n steps total → O(n)

function printRows(a, n):   // calls printLine n times
    for i = 0 to n-1:
        printLine(a, n)
```

**Question:** `printLine` is O(n). It is called inside a loop that runs n times. What is the overall complexity?

<details>
<summary>Show Answer</summary>

`printLine` costs `O(n)`. The outer loop calls it `n` times:

`t(n) = n × O(n) = O(n²)`

This is the key trap: calling an O(n) function inside a loop of n iterations multiplies the costs and produces **O(n²)** — the same class as nested loops written explicitly.

</details>

---

## Exercise 6 — Doubling Loop

## What is the time complexity?

```text
function countDoubles(n):
    count = 0
    i = 1
    while i <= n:
        count = count + 1
        i = i * 2            // i doubles each step
    return count
```

**Question:** List the values `i` takes for n = 16, then generalise: how many iterations does the loop run?

<details>
<summary>Show Answer</summary>

For n = 16: i takes values `1, 2, 4, 8, 16` → 5 iterations = log₂(16) + 1.

In general, the loop runs while `2^k ≤ n`, so the number of iterations is `⌊log₂ n⌋ + 1`.

`t(n) = O(log n)`

The function actually *computes* log₂ n by counting how many times it can double before exceeding n.

</details>

---

## Exercise 7 — Best & Worst Case

## What are the best and worst case complexities?

```text
function findNegative(a, n):
    for i = 0 to n-1:
        if a[i] < 0:
            return i      // stop as soon as one is found
    return -1             // none found
```

**Question:** Describe the input that produces the best case and the input that produces the worst case. Give the Big O for each.

<details>
<summary>Show Answer</summary>

**Best case:** `a[0]` is negative — the function returns after 1 iteration.
`t(n) = O(1)` — independent of n.

**Worst case:** No element is negative (or only the last one is) — the loop runs all n iterations before returning.
`t(n) = O(n)`

We report **O(n)** as the complexity of this function because Big O describes the worst case.

</details>

---

## Exercise 8 — Recursion

## What is the time complexity?

```text
function sumDown(n):
    if n <= 0:
        return 0          // base case: O(1)
    return n + sumDown(n - 1)  // 1 addition + recursive call
```

**Question:** Write the recurrence relation T(n), then expand it to find the closed form and Big O.

<details>
<summary>Show Answer</summary>

Recurrence: `T(0) = 1`, &nbsp;`T(n) = T(n-1) + 2` (1 comparison + 1 addition).

Expanding:

`T(n) = T(n-1) + 2 = T(n-2) + 4 = … = T(0) + 2n = 2n + 1`

`t(n) = O(n)`

Each of the n recursive calls does O(1) work, so total work is proportional to n.

</details>

---

## Exercise 9 — Three Nested Loops

## What is the time complexity?

```text
function countTriples(a, n, target):
    count = 0
    for i = 0 to n-1:
        for j = i+1 to n-1:
            for k = j+1 to n-1:
                if a[i]+a[j]+a[k] == target:
                    count = count + 1
    return count
```

**Question:** Each loop starts one step ahead of the previous. Does that reduce the Big O class below O(n³)?

<details>
<summary>Show Answer</summary>

The number of (i, j, k) triples with i < j < k is `n(n-1)(n-2)/6 ≈ n³/6`.

`t(n) = O(n³)`

The constant factor 1/6 is dropped. Three levels of nesting, each running to n, always produces O(n³) regardless of whether the inner loops start at 0 or at a higher index — the leading term is still cubic.

</details>

---

## Exercise 10 — Compare Algorithms

## Which algorithm is faster for large n?

**Algorithm A**

```text
function checkAll(a, n):
  for i = 0 to n-1:
    for j = 0 to n-1:
      if a[i] == a[j]+1:
        return true
  return false
```

**Algorithm B**

```text
function checkSorted(a, n):
  // assumes a is sorted
  for i = 0 to n-2:
    if a[i+1] - a[i] == 1:
      return true
  return false
```

**Question:** Find the Big O of each algorithm. For n = 1 000, approximately how many operations does each perform?

<details>
<summary>Show Answer</summary>

**Algorithm A:** two nested loops each of size n → `O(n²)`. For n = 1 000: ~1 000 000 operations.

**Algorithm B:** one loop of size n-1 → `O(n)`. For n = 1 000: ~1 000 operations.

B is **1 000× faster** at n = 1 000, and the gap grows with n. The trade-off: B requires the array to be sorted first (O(n log n) preprocessing). If the array changes frequently, A may be simpler despite being slower.

</details>
