---
defaults:
  - scope:
      path: ""
      type: posts
    values:
      layout: single
      author_profile: true
      comments: true
      share: true
      related: true

title: "[Unknown] Unknown"
excerpt: "[Unknown] Unknown"
toc: true
toc_sticky: true
toc_label: "목차"
categories:
  - Coding Test
tags:
  - [Coding Test, DFS, BFS]
date: 2021-08-28
last_modified_at: 2021-08-28
---
# [Unknown] Unknown

```cpp
#include <iostream>
#include <queue>
#include <vector>

using namespace std;
int ans = 0;
int n;
int dx[4] = {1, -1, 0, 0};
int dy[4] = {0, 0, 1, -1};

void dfs(vector<vector<int>> map, int sum, int cnt) {
    if (cnt == 3) {
        if (ans < sum) {
            ans = sum;
        }
        return;
    }
    for (int i = 0; i < n; i++) {
        for (int j = n - 1; j >= 0; j--) {
            if (map[i][j] == 0) {
                map[i].erase(map[i].begin() + j);
            }
        }
    }
    vector<vector<bool>> visit(n, vector<bool>(n));
    for (int i = 0; i < n; i++) {
        for (int j = 0; j < n; j++) {
            if (!visit[i][j]) {
                vector<vector<int>> tmp = map;
                cout << cnt << tmp[0][0] << tmp[0][1] << endl;
                queue<pair<int, int>> q;
                int point = 0;
                int value = tmp[i][j];
                tmp[i][j] = 0;
                q.push({i, j});
                int minX = i;
                int maxX = i;
                int minY = j;
                int maxY = j;
                while (!q.empty()) {
                    int x = q.front().first;
                    int y = q.front().second;
                    if (visit[x][y]) continue;
                    visit[x][y] = true;
                    tmp[x][y] = 0;
                    point++;
                    if (minX > x) minX = x;
                    if (maxX < x) maxX = x;
                    if (minY > y) minY = y;
                    if (maxY < y) maxY = y;
                    q.pop();
                    for (int dir = 0; dir < 4; dir++) {
                        int nx = x + dx[dir];
                        int ny = y + dy[dir];
                        if (nx < 0 || ny < 0 || nx >= n || ny >= n || visit[nx][ny] || tmp[nx][ny] != value) continue;
                        q.push({nx, ny});
                    }
                }
                point += (maxX - minX + 1) * (maxY - minY + 1);
                dfs(tmp, sum + point, cnt + 1);
            }
        }
    }
}

int main(int argc, char** argv) {
    ios_base::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);
    cin >> n;
    vector<vector<int>> map(n, vector<int>(n * 3));
    for (int i = 3 * n - 1; i >= 0; i--) {
        for (int j = 0; j < n; j++) {
            cin >> map[j][i];
        }
    }
    dfs(map, 0, 0);
    cout << ans << endl;
    return 0;
}
```
## Comments
출처 불분명 문제의 DFS + BFS 조합한 풀이이다.