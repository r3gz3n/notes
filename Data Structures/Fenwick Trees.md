# Fenwick Tree / Binary Indexed Tree
## Overview
- Fenwick tree is an array $T$ of size $N$
- Stores the sum of elements of $A$ in range $[g(i), i]$
 $$T_i = \sum_{j=g(i)}^{i} A_j$$
 where $g$ is some function that satisfies $0 \le g(i) \le i$
 
 - if $g(i) = i$, $T = A$ and summation queries are slow
 - if $g(i) = 0$, $T$ corresponds to prefix sum array and updates are slow

- To find the sum till index $x$:
	- find sum of range $[g(x), x] = T[x]$
	- find sum of range $[g(g(x)-1), g(x)-1] = T[g(x)-1]$
	- and so on
- To increase value at index $x$:
	- iterate over all $j$ such that $g(j) \le x \le j$ by $val$ i.e. $T[j] += val$

## Definition of $g(i)$
- $g(i)$ = replace all trailing $1$ bits in the binary representation of $i$ with $0$ bits
$$g(i) = i \And (i+1)$$
- to iterate over all $j$ such that $g(j) \le x \le j$ (call it $h(j)$):
	- flip last unset bit of $x$ and repeat

$$ h(j) = j | (j+1)$$
![Interpretation of Fenwick Tree](D:\Notes\Images\binary_indexed_tree.png)
## Implementation
### Finding sum in 1-D array
```
template <typename T=int>
struct FenwickTree {
    vector<T> bit;
    int n;
  
    FenwickTree(int n) {
        this->n = n;
        bit.assign(n, 0);
    }
  
    FenwickTree(vector<int> a) : FenwickTree(a.size()) {
        for (int i = 0; i < a.size(); i++) {
            update(i, a[i]);
        }
    }
  
    T sum(int idx) {
        T ret = 0;
        for (; idx >= 0; idx = (idx & (idx + 1)) - 1) {
            ret += bit[idx];
        }
        return ret;
    }
  
    T rangeSum(int l, int r) {
        return sum(r) - sum(l-1);
    }
  
    void update(int idx, T val) {
        for (; idx < n; idx = idx | (idx + 1)) {
            bit[idx] += val;
        }
    }
}
```
- `sum(idx)` will return sum from $[0, idx]$
- `sum(r) - sum(l-1)` will return sum from $[l, r]$. This is handled in `rangeSum(l, r)`

### Finding sum in 2-D array
```
template <typename T=int>
struct FenwickTree {
    vector<vector<T>> bit;
    int n, m;
  
    FenwickTree(int n, int m) {
        this->n = n;
        this->m = m;
        bit.assign(n, vector<T>(m, 0));
    }
  
    FenwickTree(vector<vector<int>> a) : FenwickTree(a.size(), a[0].size()) {
        for (int i = 0; i < a.size(); i++) {
            for (int j = 0; j < n; j++)
                update(i, j, a[i][j]);
        }
    }
  
    T sum(int x, int y) {
        T ret = 0;
        for (int i = x; i >= 0; i = (i & (i+1)) - 1) {
            for (int j = y; j >= 0; j = (j & (j+1)) - 1) {
                ret += bit[i][j];
            }
        }
        return ret;
    }
  
    T rangeSum(int l, int r) {
        return sum(r) - sum(l-1);
    }
  
    void update(int x, int y, T val) {
        for (int i = x; i < n; i = i | (i+1)) {
            for (int j = y; j < m; j = j | (j+1)) {
                bit[i][j] += val;
            }
        }
    }
}
```

### One-based indexing approach
- $g(i)$ = toggling of the last set $1$ bit in the binary representation of $i$

$$g(i) = i - (i \And -i)$$
$$h(i) = i + (i \And -i)$$
- Main benefit is binary operations complement each other

## Range Operations
### Point Update and Range Query
- ordinary Fenwick tree

### Range Update and Point Query
- to increment interval $[l, r]$ by $val$, make two point updates:
	- `update(l, val)` and `update(r+1, -val)`
