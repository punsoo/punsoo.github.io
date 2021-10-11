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

title: "[Programmers] 길 찾기 게임"
excerpt: "[Programmers] 길 찾기 게임"
toc: true
toc_sticky: true
toc_label: "목차"
categories:
  - Coding Test 
tags:
  - [Coding Test, Programmers, 구현]
date: 2021-10-11
last_modified_at: 2021-10-11
---
# [Programmers] 길 찾기 게임

Problem URL : [길 찾기 게임](https://programmers.co.kr/learn/courses/30/lessons/64062)

```cpp
#include <string>
#include <vector>
#include <set>
#include <unordered_map>

using namespace std;

int solution(vector<int> stones, int k) {
    int answer = 0;
    set<int> s;
    unordered_map<int,vector<int>> um;
    for(int i = 0; i < stones.size(); i++) {
        s.insert(stones[i]);
        um[stones[i]].push_back(i);
    }
    
    set<int> zero;
    zero.clear();
    
    for(auto it = s.begin(); it != s.end(); it++){
		answer += *it;
        for(int i = 0; i < um[*it].size(); i++) {
            zero.insert(um[*it][i]);
        }
        int pre = -1;
        int cnt = 0;
        for(auto iter = zero.begin(); iter != zero.end(); iter++) {
            if(*iter == pre + 1) {
                cnt++;
            }else {
                cnt = 1;
                pre = *iter;
            }
            if(cnt == k) return answer;
        }
	}
    return answer;
}
```