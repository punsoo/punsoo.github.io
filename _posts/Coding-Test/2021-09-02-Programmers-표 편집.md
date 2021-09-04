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

title: "[BOJ] 표 편집"
excerpt: "[BOJ] 표 편집"
toc: true
toc_sticky: true
toc_label: "목차"
categories:
  - Coding Test 
tags:
  - [Coding Test, BOJ, 구현, STACK, 연결 리스트]
date: 2021-09-04
last_modified_at: 2021-09-04
---
# [BOJ] 표 편집

Problem URL : [표 편집](https://programmers.co.kr/learn/courses/30/lessons/81303)

## 연결 리스트를 이용한 풀이

```cpp
#include <string>
#include <vector>
#include <stack>
#include <cstdlib>
#include <list>
#include <iostream>

using namespace std;

string solution(int n, int k, vector<string> cmd) {
    string answer = "";
    list<int> table;
    for(int i = 0; i < n; i++) {
        table.push_back(i);
        answer += "X";
    }
    stack<int> s;
    for(int i = 0; i < cmd.size(); i++) {
        char command = cmd[i][0];
        if(command == 'U') {
            k -= stoi(cmd[i].substr(2));
        }else if(command == 'D') {
            k += stoi(cmd[i].substr(2));
        }else if(command == 'C'){
            auto iter = table.begin();
            for(int j = 0; j < k; j++) {
                iter++;
            }
            s.push(*iter);
            table.erase(iter);
            if(table.size() == k) {
                k -= 1;
            }
        }else{
            int top = s.top();
            s.pop();
            auto iter = table.begin();
            int idx = 0;
            while(top < *iter) {
                iter++;
                idx++;
            }
            if(idx <= k) {
                k += 1;
            }
            table.insert(iter, top);
        }
    }
    for(auto iter = table.begin(); iter != table.end(); iter++) {
        answer[*iter] = 'O';
    }
    return answer;
}
```

## 효율성 통과하지 못한 풀이
```cpp
#include <string>
#include <vector>
#include <stack>
#include <cstdlib>
#include <iostream>

using namespace std;

string solution(int n, int k, vector<string> cmd) {
    string answer = "";
    vector<int> table;
    table.resize(n);
    for(int i = 0; i < n; i++) {
        table[i] = i;
        answer += "X";
    }
    stack<pair<int,int>> s;
    for(int i = 0; i < cmd.size(); i++) {
        char command = cmd[i][0];
        if(command == 'U') {
            k -= stoi(cmd[i].substr(2));
        }else if(command == 'D') {
            k += stoi(cmd[i].substr(2));
        }else if(command == 'C'){
            s.push({k, table[k]});
            table.erase(table.begin() + k);
            if(table.size() == k) {
                k -= 1;
            }
        }else{
            pair<int,int> top = s.top();
            s.pop();
            if(top.first <= k) {
                k += 1;
            }
            table.insert(table.begin() + top.first, top.second);
        }
    }
    for(int i = 0; i < table.size(); i++) {
        answer[table[i]] = 'O';
    }
    return answer;
}
```