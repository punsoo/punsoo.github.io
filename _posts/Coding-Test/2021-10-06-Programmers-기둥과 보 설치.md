---
defaults:
- scope:
  path: ""
  type: posts
  values:
  layout: single
  author_profile: true
  comments: true
  related: true

title: "[Programmers] 기둥과 보 설치"
excerpt: "[Programmers] 기둥과 보 설치"
toc: true
toc_sticky: true
toc_label: "목차"
categories:
  - Coding Test 
tags:
  - [Coding Test, Programmers, 구현, 비트마스크]
date: 2021-10-06
last_modified_at: 2021-10-06
---
# [Programmers] 기둥과 보 설치

Problem URL : [기둥과 보 설치](https://programmers.co.kr/learn/courses/30/lessons/60061)

```cpp
#include <string>
#include <vector>
#include <iostream>
using namespace std;

vector<vector<int>> solution(int n, vector<vector<int>> build_frame) {
    vector<vector<int>> answer;
    int visit[102][102];
    
    for(int i = 0; i < build_frame.size(); i++) {
        int x = build_frame[i][0];
        int y = build_frame[i][1];
        int a = build_frame[i][2];
        int b = build_frame[i][3];
        if(b == 1) {
            if(a == 0) {
                if(y == 0 || visit[x][y] & 14) {
                    visit[x][y] |= 1;
                    visit[x][y+1] |= 2;
                }
            }else {
                if(visit[x][y] & 2 || (visit[x][y] & 8 && visit[x+1][y] & 4) || visit[x+1][y] & 2) {
                    visit[x][y] |= 4;
                    visit[x+1][y] |= 8;
                }
            }
        }else {
            if(a == 0){
                if(visit[x][y+1] == 1) continue;
                if(x > 0 && visit[x-1][y+1] & 4) {
                    if(!(visit[x-1][y+1] & 2) && ((visit[x][y+1] & 12) != 12)) continue;
                }
                if(x < n && visit[x][y+1] & 4) {
                    if(!(visit[x+1][y+1] & 2) && ((visit[x][y+1] & 12) != 12)) continue;
                }
                visit[x][y] &= 14;
                visit[x][y+1] &= 13;
            }else {
                if(x > 0 && !(visit[x-1][y] & 2) && !(visit[x][y] & 2)) continue;
                if(x < n - 1 && !(visit[x+1][y] & 2) && !(visit[x+2][y] & 2)) continue;
                if(visit[x][y] == 5 || visit[x+1][y] == 9) continue;
                visit[x][y] &= 11;
                visit[x+1][y] &= 7;
            }
        }
    }
    for(int x = 0; x <= n; x++) {
        for(int y = 0; y <= n; y++) {
            if(visit[x][y] & 1){
                answer.push_back({x,y,0});
            }else if(visit[x][y] & 4){
                answer.push_back({x,y,1});
            }
        }    
    }
    return answer;
}
```
## Comments
테스트 케이스가 너무 적게 주어졌다...  
오류를 찾는 중...