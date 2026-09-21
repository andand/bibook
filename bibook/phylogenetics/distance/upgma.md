---
authors:
  - name: Anders Andersson
---

# UPGMA

**UPGMA** (Unweighted Pair Group Method with Arithmetic Mean) constructs a rooted tree by repeatedly joining the closest clusters. It is simple and useful for understanding distance-based inference, but its biological assumptions are restrictive.

## Agglomerative clustering

Initially, each sequence forms a cluster. UPGMA repeats four steps:

1. Find the two clusters with the smallest distance.
2. Join them at a new internal node.
3. Place that node at half their distance.
4. Replace the two clusters by their union and update its distances to all other clusters.

For clusters $A$ and $B$, UPGMA defines their distance as the arithmetic mean of all pairwise distances between their members:

$$
d(A,B)=\frac{1}{|A||B|}\sum_{i\in A}\sum_{j\in B}d(i,j).
$$

The word *unweighted* means that every original sequence contributes equally.

## Worked example

Consider this distance matrix:

| | S1 | S2 | S3 | S4 |
|---|---:|---:|---:|---:|
| S1 | 0.0 | 0.2 | 0.3 | 0.4 |
| S2 | 0.2 | 0.0 | 0.3 | 0.4 |
| S3 | 0.3 | 0.3 | 0.0 | 0.1 |
| S4 | 0.4 | 0.4 | 0.1 | 0.0 |

### Step 1: Join S3 and S4

The smallest non-zero distance in the matrix is

$$
d(S3,S4)=0.1,
$$

so S3 and S4 are joined to form the cluster $(S3,S4)$. The new internal node is placed at half the distance between them:

$$
h_{(S3,S4)}=\frac{0.1}{2}=0.05.
$$

Thus, the branches from S3 and S4 to their common ancestor both have length $0.05$.

We now replace S3 and S4 in the matrix by the new cluster $(S3,S4)$. Its distance to a remaining sequence is the mean of that sequence's distances to S3 and S4:

$$
d\bigl(S1,(S3,S4)\bigr)
=\frac{d(S1,S3)+d(S1,S4)}{2}
=\frac{0.3+0.4}{2}
=0.35,
$$

and

$$
d\bigl(S2,(S3,S4)\bigr)
=\frac{d(S2,S3)+d(S2,S4)}{2}
=\frac{0.3+0.4}{2}
=0.35.
$$

The updated distance matrix is therefore:

| | S1 | S2 | (S3,S4) |
|---|---:|---:|---:|
| S1 | 0.0 | 0.2 | 0.35 |
| S2 | 0.2 | 0.0 | 0.35 |
| (S3,S4) | 0.35 | 0.35 | 0.0 |

### Step 2: Join S1 and S2

The smallest non-zero distance in the updated matrix is now

$$
d(S1,S2)=0.2.
$$

S1 and S2 are therefore joined to form the cluster $(S1,S2)$. Its internal node is placed at height

$$
h_{(S1,S2)}=\frac{0.2}{2}=0.10.
$$

The branches from S1 and S2 to this node both have length $0.10$.

We next calculate the distance between the two clusters. Because each cluster contains two sequences, this is the mean of the four distances connecting their members:

$$
d\bigl((S1,S2),(S3,S4)\bigr)
=\frac{d(S1,S3)+d(S1,S4)+d(S2,S3)+d(S2,S4)}{4}
=\frac{0.3+0.4+0.3+0.4}{4}
=0.35.
$$

After replacing S1 and S2 by their new cluster, only two clusters remain:

| | (S1,S2) | (S3,S4) |
|---|---:|---:|
| (S1,S2) | 0.0 | 0.35 |
| (S3,S4) | 0.35 | 0.0 |

### Step 3: Join the two remaining clusters

The two clusters are separated by a distance of $0.35$, so they are joined at the root. The root height is

$$
h_{\mathrm{root}}=\frac{0.35}{2}=0.175.
$$

Branch lengths are differences between the heights of connected nodes. The branch from the $(S1,S2)$ node to the root therefore has length

$$
0.175-0.10=0.075,
$$

whereas the branch from the $(S3,S4)$ node to the root has length

$$
0.175-0.05=0.125.
$$

The final rooted tree, including all branch lengths, is

```text
((S1:0.10,S2:0.10):0.075,(S3:0.05,S4:0.05):0.125);
```

As required for an ultrametric tree, the distance from the root to every sequence is $0.175$. For example, the path to S1 is $0.075+0.10=0.175$, and the path to S3 is $0.125+0.05=0.175$.

## Ultrametric assumption

UPGMA produces an **ultrametric** tree: every leaf is equally distant from the root. Biologically, this corresponds to a strict molecular clock in which all sampled lineages have accumulated substitutions at the same rate.

If one lineage evolves much faster, UPGMA can cluster by similar observed distance rather than true ancestry and infer the wrong topology. **Neighbor Joining (NJ)** does not require a strict molecular clock and can therefore accommodate differences in evolutionary rates among lineages.

## Strengths and limitations

UPGMA is fast, deterministic and easy to inspect. It is appropriate when distances are approximately ultrametric, and it is widely useful as a clustering method. It was popular in the early days of phylogenetics. However, because of its equal-rate assumption, it is rarely used for phylogenetic inference today.

```{exercise}
Repeat the first UPGMA step after changing $d(S3,S4)$ from 0.1 to 0.5. Which pair joins first, and at what height? Do not implement the algorithm in code; show the matrix reasoning.
```
