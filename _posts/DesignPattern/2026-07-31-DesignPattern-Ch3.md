---
title: 深入淺出 設計模式 Ch3 Decorator Pattern
date: 2026-07-31 20:00:00 +0800 
categories: [Design Pattern, HeadFirst]
tags: [Note, Design Pattern]
---

## 裝飾者模式 Decorator Pattern 

> 在不修改原始class的情況下，能夠動態地為object增加新的功能
{: .prompt-tip }

> Open for extension & Closed for modification 
{: .prompt-tip }

## Take an disaster example 
今天有個 Coffee class 
```cpp
class Coffee {
public: 
    virtual std::string Description() {
        return "Coffee";
    }
    virtual int Cost() {
        return 50; 
    }
}
``` 
當客人想要加上Milk/Sugar/Cream...etc 可能會無限新增類別來繼承 
(Ex: CoffeeWithMilk, CoffeeWithMilkAndSugar...etc)

## How decorator pattern do ? 
避免建立很多 sub-class, 而是將所有的物件包起來 (Wrap)

```
MilkDecorator/SugarDecorator/CreamDecorator
↓
Decorator
↓ 
Coffee
```

所以Coffee + Milk + Sugar 實際上會變成
```
Sugar(Milk(Coffee))
```

## Code Example 
```cpp
enum class Size {
    Small,
    Medium, 
    Large
};

// Component
class Coffee {
public: 
    virtual std::string Description() = 0;
    virtual int Cost() = 0;
    virtual ~Coffee() = default; 
    virtual Size GetSize() = 0;
};
// Concrete Component
class Expresso : public Coffee {
public: 
    std::string Description() override { return "Expresso"; }
    int Cost() override { return 50; }
    Size GetSize() override { return size_; }
private:
    Size size_; 
};
class DarkRoast : public Coffee {
public: 
    std::string Description() override { return "Dark Roast"; }
    int Cost() override { return 60; }
    Size GetSize() override { return size_; }
private: 
    Size size_; 
};

// Decorator base 
class CoffeeDecorator : public Coffee {
public: 
    CoffeeDecorator(std::shared_ptr<Coffee> coffee): coffee_(coffee) {}
protected: 
    std::shared_ptr<Coffee> coffee_;
};
// Milk Decorator
class MilkDecorator : public CoffeeDecorator {
public:
    using CoffeeDecorator::CoffeeDecorator;

    std::string Description() override {
        return coffee_->Description() + " + Milk";
    }
    // 甚至可以透過 Concrete Component GetSize 來 case by case compute cost. 
    int Cost() override {
        switch (coffee_->GetSize()) {
            case Size::Small:  return coffee_->Cost() + 10;
            case Size::Medium: return coffee_->Cost() + 15;
            case Size::Large:  return coffee_->Cost() + 20;
        }
        return coffee_->Cost();
    }

    Size GetSize() override {
        return coffee_->GetSize();
    }
};
// Sugar Decotrator 
class SugarDecorator : public CoffeeDecorator {
public:
    using CoffeeDecorator::CoffeeDecorator; 

    std::string Description() override {
        return coffee_->Description() + " + Sugar";
    }

    int Cost() override {
        return coffee_->Cost() + 5;
    }
};

int main () {
    auto coffee = std::make_shared<Expresso>();
    coffee = std::make_shared<MilkDecorator>(coffee);
    coffee = std::make_shared<SugarDecorator>(coffee);

    std::cout << coffee->Description(); // Coffee + Milk + Sugar
    std::cout << coffee->Cost(); //65 
}
```

在 Concrete component 上方包裝上不同的 decorator，這些 decorator 也可以自己有自己的新功能~ 

## 這跟繼承有什麼不一樣？

重點在於 Decorator & Concreate component 必須有 common super class, 利用繼承達到 **class match** 而不是利用繼承獲得 behavior。 Decorator 必須有取代 Concrete component 的能力（並成為下一個 decorator 的 concrete component）

## Known Limitation 
Decorator pattern 在 Instantiation 時會大大增加程式碼的複雜度。此外 decorator patter 不僅需要 instantiation，需要將此 object 層層包裝進 decorator 中。可用 **Factory & Builder** 來進一步解決這個問題~ 

## 後記
這個 Decorator 想法確實是我從來沒想過的做法，我原本可能只寫得出在 Coffee 裡面加一堆 member variables (ex: has_milk_, has_sugar_)，沒想到還有這個 elegant 的包裝方法~ 