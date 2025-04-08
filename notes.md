# Personal notes for Design of Algorithms Class

## Divide et Impera (Divide and Conquer)

- Split problems into sub-problems
- Solve sub-problems recursively
- Combine sub-solutions to form the final solution

**Steps**:
- **Divide**: Break the problem into smaller sub-problems
- **Conquer**: Solve sub-problems recursively
- **Combine**: Merge sub-solutions into the overall solution

| Pros | Cons |
|------|------|
| Efficient algorithms | Limited applicability (compared to DP) |
| Independent sub-problems | Recursion overhead (stack frames) |
| Cache-efficient | |
| Enables hybrid algorithms | |

### Merge Sort

Classic example of Divide & Conquer.

**Steps**:
- **Divide**: Split the `n` elements into two halves
- **Conquer**: Recursively sort each `n/2` sub-array
- **Combine**: Merge the sorted halves

Recursion stops when `n == 1` (single element is always sorted). Key operation is merging.

```python
def MergeSort(A, p, r):
    if p < r:
        q = (p + r) // 2  # Divide
        MergeSort(A, p, q)  # Conquer
        MergeSort(A, q + 1, r)
        Merge(A, p, q, r)  # Combine
```

```py
def Merge(A, p, q, r):  # p,r: first/last indices; q: midpoint
    n1 = q - p + 1  # Left sub-array size
    n2 = r - q      # Right sub-array size

    L = [0] * (n1 + 1)
    R = [0] * (n2 + 1)

    for i in range(n1):
        L[i] = A[p + i]
    for j in range(n2):
        R[j] = A[q + 1 + j]

    L[n1] = float('inf')  # Sentinel
    R[n2] = float('inf')

    i = j = 0
    for k in range(p, r + 1):
        if L[i] <= R[j]:
            A[k] = L[i]
            i += 1
        else:
            A[k] = R[j]
            j += 1
```

#### Time Complexity

$T(n) = 2T(\dfrac{n}{2}) + \theta(n)$

#### Optimizations

- Skip merge if right half's smallest > left half's largest
- Use InsertionSort for small sub-arrays

## Greedy

## Dynamic Programming

## Backtracking

## Graphs


## Reminder

### Master Theorem

The Master Theorem provides a way to solve recurrence relations of the form `T(n) = aT(n/b) + f(n)`, which is common in divide-and-conquer algorithms.

Let `T(n)` be a recurrence relation of the form:  
`T(n) = aT(n/b) + f(n)`  
where:
- `a ≥ 1` (number of subproblems)
- `b > 1` (size division factor)
- `f(n)` (cost of divide/combine steps)

#### Case 1
If `f(n) = O(n^log_b a - ε)` for some `ε > 0`, then:  
`T(n) = Θ(n^log_b a)`

#### Case 2
If `f(n) = Θ(n^log_b a * log^k n)`, then:  
`T(n) = Θ(n^log_b a * log^{k+1} n)`

**Edge cases:**
- When `k = 0` (standard form): `T(n) = Θ(n^log_b a * log n)`
- When `k = -1`: `T(n) = Θ(n^log_b a * log log n)`
- When `k ≤ -2`: `T(n) = Θ(n^log_b a)`

#### Case 3
If `f(n) = Ω(n^log_b a + ε)` for some `ε > 0`, and the regularity condition `a f(n/b) ≤ c f(n)` holds for some `c < 1`, then:  
`T(n) = Θ(f(n))`

#### Intuition
The theorem compares `f(n)` to `n^log_b a`:
- **Case 1**: `f(n)` is polynomially *smaller* than `n^log_b a`: *Leaves dominate*
- **Case 2**: `f(n)` is polynomially *equal* to `n^log_b a`: *Balanced work*
- **Case 3**: `f(n)` is polynomially *larger* than `n^log_b a`: *Root dominates*
