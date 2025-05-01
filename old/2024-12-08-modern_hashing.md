---
layout: posts
title: Modern hashing made simple
usemathjax: true
published: true
---

<!-- Introduction -->
This is a short personal note on ["Modern Hashing Made Simple" by Bender et al. (2024)](https://epubs.siam.org/doi/10.1137/1.9781611977936.33).
I'm going to present this paper for the [Advanced Graph Algorithms and Optimization Seminar (Fall 2024)](https://kyng.inf.ethz.ch/courses/AGAO24seminar/) at ETH Zürich.
My personal goal is to understand the paper well enough to present it in a clear and engaging way.

**The set-up.**
We want to store $$n$$ items in a hash table that supports three operations: querying, insertion, and deletion.
We assume that the [word size](https://en.wikipedia.org/wiki/Word_(computer_architecture)) $$w=\Theta(\log n)$$.
Our goal is to design such a hash table that is _time-efficient_ and _space-efficient_.
By time-efficient, I mean that the execution time of each operation should be sublinear in $$n$$, ideally $$\mathcal{O}(1)$$ (on average or with high probability).
And by space-efficient, I mean that the total number of bits used by the hash table should be of order $$(1+o(1))n w$$, because otherwise we can achieve constant time execution by storing all items in an long array with zero hash collisions.

Here’s a small anecdote to start with:
Most hashing algorithms with space $$(1+o(1))n w$$, such as [std::unordered_map in C++](https://en.cppreference.com/w/cpp/container/unordered_map) or [linear probing](https://en.wikipedia.org/wiki/Linear_probing), support querying, insertion, and deletion operations in **$$\mathcal{O}(1)$$ time on average**.
However, they can degrade to **$$\mathcal{O}(n)$$ time in the worst case**.
Many modern work has led to hashing algorithms that guarantee **$$\mathcal{O}(1)$$ time with high probability** for all operations.
This is a much stronger guarantee than the average-case constant time of traditional hashing algorithms, since the probability of a bad event is exponentially small in the input size (usually being bounded by $$1/\text{poly}(n)$$).

However, these modern hashing algorithms are often more complex and thus harder to explain than traditional hashing algorithms.
The paper by Bender et al. presents a simple and practical hashing algorithm that achieves the said strong guarantee.

## The roadmap

Throughout this work, we assume to have access to a fully random hash function $$f$$, meaning that the output of $$f$$ is _uniformly distributed_ over the range of possible hash values.
The paper is structured as follows:

1. **Slow partition hash table.**
We start with a simple hash table that is partitioned into blocks of size $$\mathcal{O}(\log^3(n))$$.
The time complexity for insertion is constant w.h.p., but the time complexity for querying and deletion is linear in block size.
The space complexity is $$nw + \mathcal{O}(n)$$ bits.

2. **Indexed partition hash table.**
Building on the slow partition hash table, we introduce an index that allows us to query and delete in worst-case constant time (an improvement!) but insertion time is constant on average (a degradation).
We will make use of a data structure called [B-tree](https://en.wikipedia.org/wiki/B-tree) to maintain the index.
The space complexity is $$nw + \mathcal{O}(n\log \log n)$$ bits, which has an extra overhead of $$\log \log n$$ bits compared to the slow partition hash table.

3. **Partition hash table.**
We will further introduce an extra layer of processing to achieve constant time w.h.p. for insertion and deletion operations.
The core idea is to [amortize](https://en.wikipedia.org/wiki/Amortized_analysis) the cost of insertion, which is occasionally expensive, over multiple insertions and deletions.
We will use a data structure called [k-tries](https://en.wikipedia.org/wiki/Trie) to maintain a growing buffer that store the upcoming insertions and deletions, and a shrinking buffer that executes these operations in parallel.
The space complexity is $$nw + \mathcal{O}(n\log \log n)$$ bits.

## Slow partition hash table

As a starting point, we will use an array of size $$n$$ to represent our hash table and partition it into $$n/\log^3(n)$$ blocks of size $$\log^3(n)$$.

To insert a new item $$x$$, we first hash it to a random block $$f(x)$$ and insert it to the first empty slot $$k$$ from the left of the block.
We can maintain this left-aligned invariant by maintaining a $$\mathcal{O}(\log \log n)$$-bit counter for each block that keeps track of the number of items in the block.
So each time we insert an item, we insert it to the slot $$k+1$$ and increment the counter by one.
However, if the block is full, we rebuild the entire hash table by choosing a new random hash function and rehashing all items.
For deletion, we linearly scan the block to find the item and remove it, and move the right-most item to the empty slot and decrement the counter by one.
For querying, we linearly scan the block to find the item.

The time complexity for deletion and querying is linear in the block size, which is $$\log^3(n)$$.
The time complexity for insertion is constant unless we need to rebuild the hash table.
If we introduce a small slack factor $$\color{darkorange}{c\log^2(n)}$$ to the block size, the probability of having a block with more than $$\log^3(n) + \color{darkorange}{c\log^2(n)}$$ items is very small, e.g. at most $$1/\text{poly}(n)$$.
This fact is a consequence of the [Chernoff's bound](https://en.wikipedia.org/wiki/Chernoff_bound).
So on average, the runtime of insertion is constant with high probability.
Overall, the space complexity is $$nw + \mathcal{O}(n)$$ bits.

## Indexed partition hash table

In the previous step, we simply neglected deletions/queries in order to achieve constant time insertion w.h.p.
To make deletions/queries efficient, we will introduce an index for each block that allows us to find the location of an item in constant time.

One naive way to index a block is to use a hash function $$g:B\rightarrow [\log^3(n)]$$ that maps each item in block $$B$$ to a unique index in the block.
The probability of having a collision for any pair $$x, y\in B$$ is:

$$
\begin{align}
    \Pr(g(x) = g(y)) = \frac{1}{\log^3(n)}
\end{align}
$$

By union bound, the probability of any collisions occuring is at most $$\text{card}(B)^2 \frac{1}{\log^3(n)} = \log^3(n)$$, which is a vacuous bound.

Instead, we assign all items $$x$$ within a block to distinct $$\Theta(\log\log n)$$-bit fingerprints $$g(x)$$.
Here, we specifically choose $$g: B \rightarrow [\log^9(n)]$$, which will allow us to show that the probability of having a fingerprint collision is at most $$1/\log^3(n)$$ (the proof is similar to the previous one).
If the fingerprints are not distinct, we redo the assignment of fingerprints using a new random hash function.

Next, we will maintain a data structure for each block called _query-mapper_ that maps each fingerprint $$g(x)$$ to the corresponding position $$i\in \mathcal{O}(\log^3 n)$$ where the item $$x$$ is stored in that block.
In itself, the query-mapper is nothing but a hash table that maps fingerprints to positions (isn't this the problem we are trying to solve?).
However, notice that the fingerprints and positions are $$\Theta(\log\log n)$$ bits long, which means that the key-value pairs are small enough to be stored in a special data structure called B-tree.
The exact details of how B-tree works are not important, but the key idea is that it allows us to query/insert/delete in worst-case constant time with a small memory overhead of $$m\log\log n$$ bits, where $$m=\text{polylog}(n)$$ is the number of key-value pairs in the B-tree.

The query-mapper will interact with our hash table in the following way:
- To insert an item $$x$$, we first check if the fingerprint $$g(x)$$ is already in the query-mapper.
If it is, there is a fingerprint collision and we need to rebuild the index from scratch (for this block only).
Otherwise, we insert the item to the first empty position $$k$$ and add the key-value pair $$(g(x), k)$$ to the query-mapper.

- To query an item $$x$$, we simply look up the fingerprint $$g(x)$$ in the query-mapper to find the position $$k$$ where the item is stored.

- To delete an item $$x$$, we look up the fingerprint $$g(x)$$ in the query-mapper to find the position $$k$$ where the item is stored, remove the item from the hash table and potentially move the right-most item $$x'$$ to the empty slot to keep the hash table left-aligned, and delete the key-value pair $$(g(x), k)$$ and update the position for $$x'$$ in the query-mapper.

Overall, the time complexity for querying and deletion is constant in the worst case.
The time complexity for insertion is constant on average however.
We need to rebuild the index only when there is a fingerprint collision (which happens with probability of at most $$1/\log^3(n)$$).
The time complexity for rebuilding the index is $$\mathcal{O}(\log^3(n))$$, which is the same as the block size.
There is also a chance that we need to rebuild the index several times in a row, but the expected time spent on rebuilding is still constant:

$$
\sum_{i=1}^{\infty} \mathcal{O}(\log^3 n) \cdot \mathcal{O}(1/\log^3 n)^i = \mathcal{O}(1)
$$

The space complexity is $$nw + \mathcal{O}(n\log\log n)$$ bits, which has an extra overhead of $$\log\log n$$ bits compared to the slow partition hash table.

## Partition hash table

The last step is to make insertion and deletion operations constant time w.h.p while maintaining the constant time worst-case guarantee for querying.
We will use a trick called _deamortization_ to achieve this.
The core idea is to deamortize the cost of insertion, so if they formerly required $$\mathcal{O}(1)$$ time on average, they now require $$\mathcal{O}(1)$$ time w.h.p.
One interesting remark is that this trick can be extended to apply to any hash tables.

We maintain two type of buffers: a growing buffer and a shrinking buffer.
We will use a data structure called k-tries to implement these buffers, with $$k=\sqrt{n}$$, and they store up to $$n^{1/4}$$ items of $$w$$ bits each.
Again, the exact details of how k-tries work are not important, but the key idea is that they allow us to insert/delete/query in worst-case constant time with a small memory overhead of $$\mathcal{O}(n^{0.75}\log n)$$ bits.

The growing buffer receives the upcoming insertions/deletions while the shrinking buffer has their items removed as they are executed.
For each insertion/deletion being added to the growing buffer, we spend a constant $$c>1$$ amount of time executing the operations in the shrinking buffer in the actual hash table.
We will guarantee that whenever the growing buffer reaches size $$n^{1/4}$$, the shrinking buffer is empty, and they switch roles.

We will prove the following claim: _Any batch $$Q$$ of $$n^{1/4}$$ insertions/deletions can be executed in total $$\mathcal{O}(n^{1/4})$$ time w.h.p_.

Why is this so crucial?
The shrinking trie is slowly being cleared out as new operations occur.
If every batch of $$n^{1/4}$$ operations can be done in $$O(n^{1/4})$$ total time w.h.p, that means each operation’s amortized cost is constant (since $$O(n^{1/4}) / n^{1/4} = O(1)$$).
As a result, by proving this claim, we ensure the shrinking trie will be emptied at a rate that keeps the entire hash table's performance near constant time per update. 
In other words, if we can show that even a fairly large batch of operations doesn’t cause a time blow-up, then doing a little work per operation (just constant time on average) suffices to keep things running smoothly.

_Proof sketch of the claim:_
Let $$A_1, A_2, \ldots, A_{n/\log^3(n)}$$ be the number of insertion/deletion operations from $$Q$$ that get mapped to the $$i$$-th block.
We've shown that, with high probability, $$A_i \le \mathcal{O}(\log^3(n))$$ for all $$i$$.
Let $$X_1, X_2, \ldots, X_{n/\log^3(n)}$$ be the amount of time needed to execute the operations from $$A_i$$ on the $$i$$-th block.
In the worst case, we can show that $$X_i \le \mathcal{O}(\log^7(n))$$ with high probability (this is simply because there are at most $$\log^3(n)$$ operations to execute in $$A_i$$, the cost of rebuilding is $$\mathcal{O}(\log^3(n))$$, and w.h.p there will be $$\mathcal{O}(\log n)$$ rebuild attempts).
Furthermore, we have $$\mathbb{E}[X_i] \le \mathcal{O}(|A_i|)$$ by linearity of expectation and the fact that each operation in $$A_i$$ takes constant time on average (shown in the previous section).
By Chernoff's bound, we can show that $$X:=\sum_{i=1}^{n/\log^3(n)} X_i \le \mathcal{O}(n^{1/4})$$ with high probability.

The space complexity is $$nw + \mathcal{O}(n\log\log n)$$ bits, which is the same as the indexed partition hash table.