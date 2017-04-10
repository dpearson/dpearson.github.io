% Greedy Algorithms

There are two general steps for constructing greedy algorithms:

1. Define priorities, which _must_ yield an absolute order.
2. Follow those priorities to generate the solution set.

We will often use an exchange argument to prove the optimality of solutions produced by a greedy algorithm.

## Interval Scheduling ##

Input: A set of jobs, where job $j$ starts at $s_j$ and finishes at $f_j$. Two jobs are compatible if they do not overlap.

Goal: Find the maximum subset of mutually compatible jobs -- $max_s \sum_{i \in s} w_i$.

### General Approach ###

1. Sort all jobs in increasing order of deadlines.
2. $opt(S_i) \leftarrow \text{best value we can get out of } S_i$.
3. $opt(S_n) = \text{optimal solution}$.

So, $opt(S_{i+1}) = max(w_{i+1} + opt(S_j) \mid d_j \le s_{i+1}, opt(S_i))$.

### A Greedy Solution ###

We have a couple of different possible priorities:

1. Earliest start time $s_j$.
2. Earliest finish time $f_j$.
3. Shortest interval $p_j = f_j - s_j$.
4. Minimum number of conflicts $n_j$.

Of these possible priorities, only using the finish time $f_j$ will yield a correct solution.

    alg(n, s1...sn, f1...fn) {
        sort jobs by finish time so f1 <= f2 <= ... <= fn

        let A = {}
        for j = 1 to n {
            a = A U {j} if j is compatible with A
        }

        return A
    }

This algorithm can be implemented in $O(n log(n))$ time:

* Keep track of job $j^*$ last added to $A$.
* A job $j$ is compatible with $A$ iff $s_j \ge f_{j^*}$.
* Sorting the list of jobs takes $O(n log(n))$ time.

### Proof of Correctness ###

First, we assume that the greedy algorithm is not optimal.

let $i_1, i_2, ..., i_n$ denote the list of jobs selected by the greedy algorithm.
let $j_1, j_2, ..., j_m$ denote the list of jobs selected in an optimal solution, with $i_1 = j_1, i_2 = j_2, ..., i_r = j_r$ for the largest possible value of $r$.

If job $i_{r+1}$ exists and finishes before $j_{r+1}$, we can replace job $j_{r+1}$ with job i_{r+1}. This solution will still be optimal, but this exchange creates a contradiction: it implies that $r$ was not the maximum such index.

## Huffman Coding ##

Huffman coding gives prefix-free codes needed when using variable length codewords; we want to select these codes such that the total message length is minimized: $min \sum p(i) f_i$, where $p(i) = \text{size of codeword for } i$ and $f_i = \text{frequency of word } i$. So, a higher $f_i$ implies a lower $p(i).

To construct these codes, we can map codewords to a binary tree. Initially, each codeword is an individual leaf node; then, we greedily combine the least frequent trees until all codewords are included in the tree.

### Proof of Optimality ###

A proof of the optimality of the Huffman coding algorithm can be found [here](http://www.cs.utoronto.ca/~brudno/csc373w09/huffman.pdf).

## Minimum Spanning Tree ##

The minimum spanning tree for a graph $G(V, E)$ is the tree $T$ containing all vertices in $V$ with the minimum weight $w = \sum_{e \in E'} w(e)$.

### General Rules ###

There are two properties that the following algorithms rely on:

1. The cycle property -- if there is a cycle, then the largest edge of that cycle should not be included in the minimum spanning tree.
2. The cut property -- if there is a cut, then the smallest edge in that cut should be in the tree.

Both of these properties can be proved by contradiction.

### Prim's Algorithm ###

Start with a single vertex $v \in V$, then repeatedly add outgoing edges $e \in E$ with the minimum weight until all vertices are included in the tree.

### Kruskal's Algorithm ###

Repeatedly add edges $e \in E$ with the minimum weight (that do not introduce a cycle) until all vertices are included in the tree.

### Reverse-Delete Algorithm ###

Repeatedly remove edges $e \in E$ with the maximum weight (that do not make the graph disconnected) until no such edges can be removed.

### An Example ###

Input: A list of $n$ people to invite to a party.

Goal: Maximize the party size, where a candidate $i$ may be invited only if $i$ knows at least 6 other people attending the party and $i$ does not know at least other 6 people attending the party.

Solution: Repeatedly remove people who do not satisfy the inclusion criteria until no such person can be removed.

## Slides ##

* [Introduction to Algorithms](http://www.cs.utoronto.ca/~brudno/csc373w09/huffman.pdf)
* [Algorithm Design](http://www.cs.princeton.edu/~wayne/kleinberg-tardos/pdf/04GreedyAlgorithmsI.pdf)