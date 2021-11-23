---
title: "LeetCode: Number of Islands"
date: 2021-11-22T19:24:35-08:00
draft: flase
tags: ["software", "development", "algorithms", "leetcode", "c/c++"]
---

# LeetCode: Number of Islands
For the second entry in the LeetCode series, I will be discussing another medium-difficulty problem. However, unlike the in-depth analysis covered in the [last LeetCode problem](/2021/11/leetcode-set-matrix-zeroes-part-1/), this post will be a succinct overview of an efficient solution. This problem is great because it's easily to visualize the input, and it does not have a lot of constraints. It also never hurts to have more practice with multidimensional arrays + graph searching algorithms.

### The Problem
The problem, [Number of Islands - LeetCode](https://leetcode.com/problems/number-of-islands/) is as follows:

>Given an `m x n` 2D binary grid `grid` which represents a map of `'1'`s (land) and `'0'`s (water), return _the number of islands_.
>
>An **island** is surrounded by water and is formed by connecting adjacent lands horizontally or vertically. You may assume all four edges of the grid are all surrounded by water.
>
>**Constraints:**
>-   `m == grid.length`
>-   `n == grid[i].length`
>-   `1 <= m, n <= 300`
>-   `grid[i][j]` is `'0'` or `'1'`.

##### Example:
```
Input: 
grid = [['1', '1', '1', '1'],
        ['1', '1', '0', '0'],
        ['0', '0', '1', '1'],
        ['1', '0', '0', '0']]

Output: 3
```

### Approach:
The approach leverages recursion and uses [depth-first search (DFS)](https://www.hackerearth.com/practice/algorithms/graphs/depth-first-search/tutorial/) to discover ***all*** elements composing a single island. Then, we must simply count each island that is fully discovered.
- First, find an island. If no island is found, the expected output is `0`.
- Upon the first piece of land found (i.e. an element is `'1'`), discover the rest of the island. Using recursion & DFS, we can explore all adjacent nodes and their neighbors, overwriting all elements indicating land with `'0'`, marking it as explored.
- After the DFS has been completed, marking the entirety of one island as explored, increment a counter.
- Repeat this process for all elements in the input, *grid*.
- Return the value of the counter.

It's worth noting that since the input is passed by reference, the modifications made to the input will modify the data in the caller's scope. However, since there is no restriction against this, we can save time & space by not creating a copy of the grid and instead directly modify the input.

```c++
class Solution {
public:
    int dx[4] = {0, 0, -1, 1}; // adjacencies (x-axis)
    int dy[4] = {-1, 1, 0, 0}; // adjacencies (y-axis)
    int m, n;

    int numIslands(vector<vector<char>>& grid) {
        m = grid.size();
        n = grid[0].size();
        int numIslands = 0;

        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (grid[i][j] == '1') {
                    exploreIsland(grid, i, j);
                    numIslands++;
                }
            }
        }

        return numIslands;
    }

    // DFS search for immediately neighboring land.
    void exploreIsland(vector<vector<char>>& grid, int i, int j) {        
        if (i < 0 || i >= m || j < 0 || j >= n) {
            return;
        }

        if (grid[i][j] == '0') {
            return;
        }

        grid[i][j] = '0';

        for (int k = 0; k < 4; k++){
            exploreIsland(grid, i + dy[k], j + dx[k]);
        }
    }
};
```

### Run-time: 
Note that through the nested loops in the `numIslands` function, we will visit each element once (input size is $m \times n$) to find the land of an island. However, during the DFS search in `destroyIslands`, we may visit elements that have not yet been visited by the outer loops. That means each node may be visited twice in the worst case, so we see a run-time of `O(m * n)`.