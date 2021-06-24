# Swim in Rising Water

_update Jun 21, 2021_

![image](https://user-images.githubusercontent.com/24964756/123201449-8e5b4c00-d467-11eb-8326-3a8e4b618dbb.png)

## Basic Idea

这道题就是说给定一个格子是一个水池，里面的数字代表池子中每个小方块的高度，t表示时间，水位随时间上升，只有水位超过某高度才能游过去，问需要的最低水面高度。

1. 有两种思路，比较容易想到的是二分法，即二分t的值，直到找到能游到右下角所需的最小t，每次验证需要`O(N^2)`的时间。
2. 另一种思路是Dijkstra, 每个node的score是它的高度，也就是此处grid中每个格子的value。我个人觉得这并不是典型的dijkstra，因为它在这里解决的不是最短路径问题，但是如果把它理解为BFS2就好理解一些。其实这种做法相当于在模拟真实的水流，总是在当前能接触到的格子中选择最低的先流过去，如果能流到右下角就结束，而返回值就是在这个过程中出队列过的数字的最大值。

## Java Code

### 1. Binary Search

```java
class Solution {
    int[][] grid;
    int N;

    public int swimInWater(int[][] grid) {
        this.grid = grid;
        this.N = grid.length;
        int left = 0, right = 2500;
        while (left + 1 < right) {
            int mid = left + (right - left) / 2;
            if (bfs(mid, new boolean[N][N])) {
                right = mid;
            } else {
                left = mid;
            }
        }
        if (bfs(left, new boolean[N][N])) {
            return left;
        } else {
            return right;
        }
    }

    private boolean bfs(int t, boolean[][] visited) {
        if (grid[0][0] > t) {
            return false;
        }
        int[] dr = new int[]{0, 1, 0, -1};
        int[] dc = new int[]{1, 0, -1, 0};
        visited[0][0] = true;
        Deque<int[]> queue = new ArrayDeque<>();
        queue.offerFirst(new int[]{0, 0});
        while (!queue.isEmpty()) {
            int[] curr = queue.pollLast();
            if (curr[0] == N - 1 && curr[1] == N - 1) {
                return true;
            }
            for (int i = 0; i < 4; ++i) {
                int nr = curr[0] + dr[i];
                int nc = curr[1] + dc[i];
                if (isValid(nr, nc)) {
                    if (grid[nr][nc] <= t
                       && !visited[nr][nc]
                    ) {
                        visited[nr][nc] = true;
                        queue.offerFirst(new int[]{nr, nc});
                    }
                }
            }
        }
        return false;
    }

    private boolean isValid(int r, int c) {
        return r >= 0 && r < N && c >= 0 && c < N;
    }
}
```

### 2. BFS2 （Dijkstra）

```java
class Solution {
    public int swimInWater(int[][] grid) {
        int[] dr = new int[]{0, 1, 0, -1};
        int[] dc = new int[]{1, 0, -1, 0};
        int N = grid.length;

        PriorityQueue<int[]> pq = new PriorityQueue<>(
            (a, b)->Integer.compare(grid[a[0]][a[1]], grid[b[0]][b[1]]));

        boolean[][] visited = new boolean[N][N];
        int maxHeight = -1;
        pq.offer(new int[]{0, 0});
        while (!pq.isEmpty()) {
            int[] curr = pq.poll();
            maxHeight = Math.max(maxHeight, grid[curr[0]][curr[1]]);
            if (curr[0] == N - 1 && curr[1] == N - 1) {
                return maxHeight;
            }
            for (int i = 0; i < 4; ++i) {
                int nr = curr[0] + dr[i];
                int nc = curr[1] + dc[i];
                if (isValid(nr, nc, grid) && !visited[nr][nc]) {
                    visited[nr][nc] = true;
                    pq.offer(new int[]{nr, nc});
                }
            }
        }
        return -1;
    }

    private boolean isValid(int r, int c, int[][] grid) {
        return r >= 0 && r < grid.length && c >= 0 && c < grid.length;
    }
}
```

