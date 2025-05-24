---
title: 深入淺出 設計模式 Ch2 Observer Pattern 
date: 2025-05-23 20:00:00 +0800 
categories: [Design Pattern, HeadFirst]
tags: [Note, Design Pattern]
---

## 觀察者模式 Observer

就像是 **出版者** (subject) 和 **訂閱者** (observer) 的關係

當observer註冊subject後，每當subject的數據改變，observer就會收到更新

> 定義了objects之間的 one-to-many dependencies
>
> 當一個object改變狀態時，所有dependent objects 都會收到狀態並自動更新

<!------>

> 努力設計interact objects 之間的low coupling
{: .prompt-tip }

## Low Coupling

- 提高可維護性：修改某個模組時，不會影響其他部分。
- 增強可重用性：模組可以獨立使用，不需要依賴特定的環境。
- 降低錯誤影響範圍：當某個部分出現問題時，不會導致整個系統崩潰。

Subject只知道observers實做了一些API，不知道具體class為何以及實作細節。

Subject隨時都能新增、刪除observers，且不會影響Subject本身的運作

## Push and Pull

有時Observer並不想隨時隨地的處理變化，或者只需要一部分的資料

所以我們可以設定通知後，現在要由Subject主動push data or等待observer自己來pull data

## Code example

Virtual class as interface

```cpp
class Subject{
public:
    virtual void registerObserver(Observer o) = 0;
    virtual void removeObserver(Observer o) = 0;
    virtual void notifyObservers(Data* arg) = 0;
    virtual void setChanged() = 0;
}

class Observer{
public: 
    virtual void update (float temp, float humidity) = 0;
}

class DisplayElement{
public: 
    virtual void display() = 0;
}

class Data{
    float temp;
    float humid; 
}
```

Implement Subject

```cpp
class WeatherData: public Subject{
private: 
    vector<Observer> observers; 
    Data data;  
    bool changed = false; 
public: 
    void registerObserver override(Observer o){observers.push_back(o);}
    void removeObserver override(Observer o){observers.delete(o);}
    void notifyObservers override(Data* arg){
        if(changed){
            for (auto o : observers){
                o.update(this, arg);
            }
            changed = false;
        }
    }
    
    void measureChanged(){
        setChanged();
        nodifyObservers(NULL); // pull (or we can modify to push)
    }

    void setMeasurements(Data data){
        if(abs(this.data.temp - data.temp) >= 0.1){
            this.data = data; 
            measuredChanged();
        }
        // ... 
    }
    void setChanged(){changed = true;}

    // observers 也可以主動利用getter function來pull data 
    void getData(){} 
    // ... 
}
```

一個dashboard可以多重繼承不同的class

```cpp
class CurrentCondition: public Observer, public DisplayElement{
private:
    WeatherData weatherData; 
    Data data; 
public: 
    CurrentCondition(Subject weatherData){
        this weatherData = weatherData; // 記錄Subject，方便未來cancel register
        weatherData.registerObserver(this); 
    }

    void update override(Subject weatherData, Data* arg){
        if(dynamic_cast<Data*>(arg)){// ... push
            
        }else{ // pull 
            this.data = weatherData.getData();
        }
        display(); // 在Model-View-Controller pattern時，會提到更好call display()的方法
    }
    void display(){/*show ... */}
}
```

## Connect to Ch1

1. 將會變化的部分獨立出來 → Observer就是會變化的部分，和Subject分開來

2. Code by Interface → Observer 和 Subject 之間都用API來溝通（low coupling）

3. 多用組合，少用繼承→Observer和Subject之間的關係不是用繼承來連接的

## 小小的後記

這本書原本的code都是用java來實作的

在寫筆記的過程中改寫成c++也滿有趣的，一方面可以重新複習一下c++ oo 的許多概念，也間接學會了java oo 的語法！

（像是書中有提到java 有內建的package for observer pattern，等我哪天需要java，要再回頭看一下這段~）

不過我對dynamic_cast那部分好像還不是很熟，要找時間好好讀&筆記一下了！
