% Order Statistics

**Goal**: Select the $i^{th}$ smallest of $n$ elements, which is the element with rank $i$.

* Minimum: $i = 1$
* Maximum: $i = n$
* Median: $i = floor(\frac{n+1}{2})$ or $i = ceil(\frac{n+1}{2})$

A naive algorithm: sort the array, then select the $i^{th}$ element. If we use merge sort or heapsort, this algorithm takes $\Theta(n \log{n}) + \Theta(1) = \Theta(n \log{n})$ time.

## Finding the Minimum and Maximum ##

We can find the minimum and maximum individually in $O(n)$ comparisons, so, by extension, we can find both in $O(2n)$ comparisons (specifically, $2n - 2$ comparisons).

However, we can improve on this algorithm with the following:

1. Make $ceil(\frac{n}{2})$ pairwise comparisons (compare A[1] with A[2], A[3] with A[4], etc.).
2. Make $ceil(\frac{n}{2})$ comparisons to find both the minimum and maximum.

Example: [2, 1, 0, 5, ..., 7, 9]

+-----+-----------+-----------+-----+-----+---------+
|     | A[1] A[2] | A[3] A[4] | ... | A[n - 1] A[n] |
+=====+===========+===========+=====+===============+
| min | A[2]      | A[3]      | ... | A[n - 1]      |
+-----+-----------+-----------+-----+-----+---------+
| max | A[1]      | A[4]      | ... | A[n]          |
+-----+-----------+-----------+-----+-----+---------+

We can then compare all of the elements in the first row to determine the minimum (A[3] = 0) and all of the elements in the second row to determine the maximum (A[n] = 9).

## Finding the 2nd Smallest Element ##

We can divide the array into two parts and find the two smallest elements in each, at a cost of $T(n) = 2T(\frac{n}{2}) + 2$.

## A General Algorithm ##

    order-stat(array, i) {
        pick a pivot value v
        partition array into three parts: // n comparisons
            A = {x | x < v}
            B = {y | y = v}
            C = {z | z > v}
        
        if i <= |A|, return order-stat(A, i)
        if i > |A| and i <= |A| + |B|, return v
        it i > |A| + |B|, return order-stat(C, i - |A| - |B|)
    }

For this algorithm, $T(n) = n + max(T(|A|, |C|))$ in the general case. In the worst case, this recurrence becomes $T(n) = n + T(n - 1) = O(n^2)$, but it will be a much better $T(n) = n + T(\frac{3n}{4}) = O(4n)$ if $|A| \text{ or } |C| < \frac{3n}{4}$.

Therefore, we want to repeatedly pick a pivot value randomly until both $|A| \text{ and } |C| < \frac{3n}{4}$. If we do that, $T(n) = 2n + T(\frac{3n}{4}) = O(8n)$, since we will partition the array twice in expection before achieving the desired balance.

**Proof**:

$T(n) = T(\alpha n) + c n$

$0 < \alpha < 1$

$c \text{ constant}$

Assume $T(n) \leq d n$.

Then,

$d \alpha n + cn = (d \alpha + c) n \leq d n$

$d \alpha + c \leq d$

$c \leq d(1 - \alpha)$

$d \geq \frac{c}{1 - \alpha}$.

## A Deterministic Algorithm ##

    select(i, n) {
        // running time: 10 * ceil(n/5)
        divide the n elements into groups of 5, and find the median of each group by rote

        // running time: T(floor(n / 5))
        recursively select the median x of the floor(n/5) group medians as the pivot

        // running time: n
        partition the array around the pivot x
        let k = rank(x)

        if i = k, return x
        if i < k, return select(i, lower-part)
        if i > k, return select(i - k, upper-part)
    }

For this algorithm, $T(n) = 2n + T(\frac{n}{5}) + max(T(|A|), T(|C|))$. Since $|A| \text{ and } |C| \geq \frac{3n}{10}$, $max(T(|A|), T(|C|)) \leq \frac{7n}{10}$.

$T(n) = T(\alpha n) + T(\beta n) + c n$, where $\alpha + \beta < 1, 0 < \alpha, \beta < 1, \alpha, \beta, c \text{ constant} \implies T(n) O(n)$

## Example: Matching Nuts and Bolts ##

**input**: $n$ bolts, $n$ nuts of different sizes.

**goal**: pair them, with minimal matching effort, such that bolts and nuts fit each other.

    alg(nuts, bolts) {
        pick a nut
        for i = 1 to n, find a bolt that fits the nut and partition the nuts into A < nut and C > nut
        for i = 1 to n, use the bolt to partition the nuts into A' < bolt and C' > bolt

        alg(A, A')
        alg(C, C')
    }

$T(n) = n + n + T(|A|) + T(|C|) = O(n \log{n})$ with randomization to bound $|A|$ and $|C|$ at $\frac{3n}{4}$.

## Example: Databases ##

**input**: $k$ databases $D_1, D_2, ..., D_k$, with all records in $D_i$ sorted.

**goal**: find the median of $\bigcup\limits_{i=1}^{k} D_i$ with the minimal number of calls.

$\text{let} find(D_i, j) = \text{find } j^{th} \text{ item in } D_i$

Algorithm: drop elements smaller than the smallest median, adjust the index accordingly, and recurse.