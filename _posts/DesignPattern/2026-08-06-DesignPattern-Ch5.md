---
title: 深入淺出 設計模式 Ch5 Singleton Pattern
date: 2026-08-06 01:00:00 +0800 
categories: [Design Pattern, HeadFirst]
tags: [Note, Design Pattern]
---

## 單例模式 Singleton Pattern 
Singleton 基本上就是希望這個 class 從頭到尾都是同一個 object，並且會提供一個 global access point。
常用在 thread pool、logger、config manager ... 等如果有兩個以上的 object 會很麻煩的 class 

## Simple Implementation Example
```cpp
class MyClass {
private:
    MyClass() {}
    // Private constructor 就讓外面的 client 無法自行 initialize MyClass object 
public: 
    static MyClass& GetInstance() {
        if(instance_ == nullptr) {
            instance_ = new MyClass();
        }
        return instance_; 
    }
    
    // Avoid from copy 
    MyClass(const MyClass&) = delete;
    MyClass& operator=(const MyClass&) = delete;

private: 
    static instance_; 
};
```

## Deal with multi-threads
上面的寫法有個很大的問題，如果同時兩個 threads 呼叫了 GetInstance 並且在此之前沒有呼叫過 GetInstance，那麼就有可能實際上創建了兩個 objects ！

不過 C++11 以後大部分就是用 function-local static 的寫法，有特別規定當同時多個 thread 同時呼叫，只能有一個 thread 負責 initialize，其他的 thread 則必須等待 

```cpp
class MyClass {
private:
    MyClass() {}
public: 
    static MyClass& GetInstance() {
        static MyClass instance;
        return instance; 
    }
    // ...... 
};
```

## Known Limitations 
1. Global state: 會隱藏 class 之間的 dependencies，使程式的 coupling 提高 [Link: low coupling]({% post_url 2025-05-23-DesignPattern-Ch2 %})
2. Hard to Test: 難以在 unittest 中替換成 Mock object 
3. Multi-threading: 如果是舊版 C++ 或是其他語言，在寫的過程中還需要額外注意 thread-safe issue. 

因此在大型專案（ex: Chromium）中，其實會使用 dependency injection 的技巧~ 

### Dependency Injection 
假設 MyClass 需要一個 Logger (需要有 singleton 的特性)
```cpp
class MyClass {
public: 
    void PrintLog() {
        Logger::GetInstance().Log("MyClass!");
    }
};
```
MyClass 就跟 Logger coupling，沒有從其他地方替換 logger 的機會，測試時也只能使用真正的 logger。


```cpp
class MyClass {
public: 
    MyClass(Logger* logger): logger_(logger) {}
    void PrintLog() {
        logger_->Log("MyClass!");
    }
private:
    Logger* logger_; 
}

int main() {
    Logger logger;
    MyClass example(&logger);
}
```
如此一來，MyClass & Logger 之間就沒有 coupling issue, logger 可以是任何subclass，甚至測試時還能直接換成 `MockLogger`！

## 小記
Singleton pattern 這一單元其實滿簡單暴力的，不過known limits好像也很多，感覺在使用上要非常小心 > < 

