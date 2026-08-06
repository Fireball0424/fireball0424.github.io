---
title: 深入淺出 設計模式 Ch4 Factory Pattern
date: 2026-08-01 01:00:00 +0800 
categories: [Design Pattern, HeadFirst]
tags: [Note, Design Pattern]
---

## 工廠模式 Factory Pattern 
> 把「建立物件」交給專門的工廠而非直接 `new`
{: .prompt-tip }

## Take an disaster example 
```cpp
Coffee* coffee;

if (type == "Latte") {
    coffee = new Latte();
} else if (type == "Americano") {
    coffee = new Americano();
} else if (type == "Mocha") {
    coffee = new Mocha();
}

coffee->brew();
```

相當於每增加一個種類的咖啡，就要多一個 `if-else`，而這樣的 `if-else` 可能會散落在整個 codebase 當中，非常不便維護

## Implement with factory pattern 
```cpp
class CoffeeFactory {
public:
    static Coffee* CreateCoffee(const std::string& type) {
        if (type == "Latte") return new Latte();
        if (type == "Americano") return new Americano();
        if (type == "Mocha") return new Mocha();
        return nullptr;
    }
};
```
> Static 修飾 就可以直接使用 CreateCoffee function 而不需要先建立 factory object
{: .prompt-tip}

## Fancy Example - PizzaStore 
現在來看一下 HeadFirst 裡面提供的例子 - PizzaStore 

```cpp
// Abstract pizza class
class Pizza {
public: 
    virtual ~Pizza() = default;
    virtual void Prepare() = 0;
};
// Concrete pizza class 
class CheessPizza: public Pizza {
public:
    void Prepare() override {std::cout << "Prepare cheese pizza\n";}
};
class MargheritaPizza: public Pizza {
public:
    void Prepare() override {std::cout << "Prepare margherita pizza\n";}
};

// Abstract pizza store creator 
class PizzaStore {
public: 
    std::unique_ptr<Pizza> OrderPizza(const std::string& type) {
        auto pizza = CreatePizza(type); // Factory Method
        pizza->Prepare();
        return pizza; 
    }
protected:
    virtual std::unique_ptr<Pizza> CreatePizza(const std::string& type) = 0;
}
// Concrete pizza store creator 
class NYPizzaStore: public PizzaStore {
protected: 
    std::unique_ptr<Pizza> CreatePizza(const std::string &type) override {
        if (type == "cheese") return std::make_unique<CheesePizza>();
        if (type == "margherita") return std::make_unique<MargheritaPizza>();
        return nullptr;
    }
}

int main() {
    std::unique_ptr<PizzaStore> store = std::make_unique<NYPizzaStore>();
    auto cheese = store->OrderPizza("cheese");
    auto margherita = store->OrderPizza("margherita");
}
```

可以注意到 abstract pizza store creator 的 `OrderPizza` 並不是 virtual function, 因為不論是哪一間 pizza store，對於 order 這個動作都是一樣的流程~ 

## Features of Factory Method Pattern 
1. Abstract class 定義流程 
2. Subclass 決定建立 object 的細節
3. Open/Closed Principle  

    當今天想再新增 `ChicagoPizzaStore` 只需要新增 `class ChicagoPizzaStore: public PizzaStore` 並實作自己的 `CreatePizza()` 且不需要修改任何 PizzaStore & Client

如此一來，Client side 永遠只需要對著 abstract class API

## 小記
Factory method 在 Chromium 中就比起前面幾種 pattern 感覺常見很多，並且分工以及設計非常的明確！ 
而讀完這章後，我也更了解了Chromium設計的概念、目的、以及未來要寫相關的 code 要注意的種種細節~