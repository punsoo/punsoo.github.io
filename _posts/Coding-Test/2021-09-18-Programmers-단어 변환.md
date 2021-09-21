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

title: "[BOJ] 단어 변환"
excerpt: "[BOJ] 단어 변환"
toc: true
toc_sticky: true
toc_label: "목차"
categories:
  - Coding Test 
tags:
  - [Coding Test, BOJ, DFS]
date: 2021-09-18
last_modified_at: 2021-09-18
---
# [BOJ] 단어 변환

Problem URL : [단어 변환](https://programmers.co.kr/learn/courses/30/lessons/43163)

```cpp
#include <string>
#include <vector>
using namespace std;
int answer;
int size;
int len;
 
void dfs(string &begin, string &target, vector<string> &words, int visit, int cnt = 0) {
    if(begin == target && answer > cnt) {
        answer = cnt;
        return;
    }
    for (int i = 0; i < size; i++) {
        int diff = 0;
        for (int j = 0; j < len; j++)
            if (visit & ( 1 << (j - 1) ) == 0 && begin[j] != words[i][j]) diff++;
        if (diff == 1) {
            int tmp = visit;
            visit = visit | 1 << (j - 1);
            dfs(words[i], target, words, visit, cnt + 1);
            visit = tmp;
        }
    }
}
 
int solution(string begin, string target, vector<string> words) {
    int visit = 0;
    int ans = 11;
    int size = words.size();
    int len = words[0].size();
    dfs(begin, target, words, visit);
    if (answer == 100)   return 0;
    return answer;
}
```
