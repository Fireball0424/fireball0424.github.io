---
title: C++ Basic - Move
date: 2025-06-09 17:00 +0800 
categories: [C++, Basic]
tags: [Note, C++]
---

## Lvalue & Rvalue 
在介紹 std::move 是什麼之前，我們需要先釐清，什麼是Lvalue（左值）跟 Rvalue （右值）

左值 → 故名思義出現在賦值operator的**左邊**，通常是**變數或是能取得地址的表達式**

右值 → 故名思義出現在賦值operator的**右邊**，沒有名稱的臨時值

```cpp
int x = 1 + 1; // x 是左值， 1 + 1 是右值
int *p = &x; // 所以x是可以被取址的
```

```cpp
int sum(int x, int y){
    return x + y;
}

int ans = sum(1, 1); // sum(1, 1) 也是右值~
```

## Rvalue Reference

介紹完右值是什麼，來介紹右值參考~

```T&&``` ... todo 
