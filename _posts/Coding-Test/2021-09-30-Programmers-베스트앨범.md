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

title: "[BOJ] 경주로 건설"
excerpt: "[BOJ] 경주로 건설"
toc: true
toc_sticky: true
toc_label: "목차"
categories:
  - Coding Test 
tags:
  - [Coding Test, Programmers, 해시]
date: 2021-09-30
last_modified_at: 2021-09-30
---
# [BOJ] 경주로 건설

Problem URL : [경주로 건설](https://programmers.co.kr/learn/courses/30/lessons/42579)

```cpp
#include <string>
#include <vector>
#include <map>
using namespace std;

vector<int> solution(vector<string> genres, vector<int> plays) {
    vector<int> answer;
    map<string, int> music;
    map<string, vector<int>> musicList;
    for (int i = 0; i < genres.size(); i++) {
        music[genres[i]] += plays[i];
        musicList[genres[i]].push_back(plays[i]);
    }
    
    int first = -1;
    int second = -1;
    string firstGenre = "";
    string secondGenre = "";
    for(auto i = music.begin(); i != music.end(); i++) {
        if(first < i->second) {
            second = first;
            secondGenre = firstGenre;
            first = i->second;
            firstGenre = i->first;
        }else {
            if(second < i->second) {
                second = i->second;
                secondGenre = i->first;
            }
        }
    }
    bool twoGenre = true;
    if(second == -1) {
        twoGenre = false;
    }
    first = -1;
    second = -1;
    vector<int> tmp = musicList[firstGenre];
    for(int i = 0; i < tmp.size(); i++) {
        if(first < tmp[i]) {
            second = first;
            first = tmp[i];
        }else {
            if(second < tmp[i]) {
                second = tmp[i];
                secondGenre = tmp[i];
            }
        }
    }
    answer.push_back(first);
    if(second != -1) {
        answer.push_back(second);
    }
    if(twoGenre) {
        first = -1;
        second = -1;
        tmp = musicList[secondGenre];
        for(int i = 0; i < tmp.size(); i++) {
            if(first < tmp[i]) {
                second = first;
                first = tmp[i];
            }else {
                if(second < tmp[i]) {
                    second = tmp[i];
                    secondGenre = tmp[i];
                }
            }
        }
        answer.push_back(first);
        if(second != -1) {
            answer.push_back(second);
        }
    }
    return answer;
}
```
