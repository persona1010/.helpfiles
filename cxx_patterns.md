# Programming Patterns
1. 🔁 [Creational Patterns](#creational-patterns)

    - [Singleton](#singleton)
    - Factory Method
    - Abstract Factory
    - Builder
    - Prototype

2. 🔄 [Structural Patterns](#structural-patterns)

    - [Adapter](#adapter)
    - [Bridge](#bridge)
    - [Composite](#composite)
    - [Decorator](#decorator)
    - [Facade](#facade)
    - [Flyweight](#flyweight)
    - [Proxy](#proxy)

3. 🔁 Behavioral Patterns

    * Observer
    * Strategy
    * Command
    * State
    * Iterator
    * Mediator
    * Memento
    * Visitor
    * Chain of Responsibility
    * Template Method

---
## <a id="creational-patterns">Creational Patterns</a>

## <a id="structural-patterns">Structural Patterns</a>
### <a id="adapter">🧱 1. Adapter Pattern</a>
Allows incompatible interfaces to work together.
```cpp
#include <iostream>
using namespace std;

// Adaptee
class OldPrinter {
public:
    void oldPrint(const string& text) {
        cout << "Old print: " << text << endl;
    }
};

// Target interface
class NewPrinter {
public:
    virtual void print(const string& text) = 0;
};

// Adapter
class PrinterAdapter : public NewPrinter {
private:
    OldPrinter* oldPrinter;
public:
    PrinterAdapter(OldPrinter* printer) : oldPrinter(printer) {}
    void print(const string& text) override {
        oldPrinter->oldPrint(text);
    }
};

int main() {
    OldPrinter* old = new OldPrinter();
    NewPrinter* adapter = new PrinterAdapter(old);
    adapter->print("Hello, Adapter Pattern!");
    delete adapter;
    delete old;
    return 0;
}
```

### <a id="bridge">🌉 2. Bridge Pattern</a>

Separates abstraction from implementation.

```cpp
#include <iostream>
using namespace std;

// Implementation
class Renderer {
public:
    virtual void renderCircle(float radius) = 0;
};

class VectorRenderer : public Renderer {
public:
    void renderCircle(float radius) override {
        cout << "Drawing circle as vector, radius = " << radius << endl;
    }
};

class RasterRenderer : public Renderer {
public:
    void renderCircle(float radius) override {
        cout << "Drawing circle as raster, radius = " << radius << endl;
    }
};

// Abstraction
class Shape {
protected:
    Renderer* renderer;
public:
    Shape(Renderer* r) : renderer(r) {}
    virtual void draw() = 0;
};

class Circle : public Shape {
private:
    float radius;
public:
    Circle(Renderer* r, float radius) : Shape(r), radius(radius) {}
    void draw() override {
        renderer->renderCircle(radius);
    }
};

int main() {
    VectorRenderer vr;
    Circle c(&vr, 5.0f);
    c.draw();
    return 0;
}
```
### <a id="composite">🌲 3. Composite Pattern</a>

Treats individual objects and composites uniformly.
```cpp
#include <iostream>
#include <vector>
using namespace std;

class Component {
public:
    virtual void operation() = 0;
    virtual ~Component() {}
};

class Leaf : public Component {
public:
    void operation() override {
        cout << "Leaf operation\n";
    }
};

class Composite : public Component {
private:
    vector<Component*> children;
public:
    void add(Component* comp) {
        children.push_back(comp);
    }
    void operation() override {
        for (auto child : children) {
            child->operation();
        }
    }
    ~Composite() {
        for (auto c : children) delete c;
    }
};

int main() {
    Composite* root = new Composite();
    root->add(new Leaf());
    root->add(new Leaf());
    root->operation();
    delete root;
    return 0;
}
```
### <a id="decorator">🎨 4. Decorator Pattern</a>

Adds behavior to objects dynamically.
```cpp
#include <iostream>
using namespace std;

class Coffee {
public:
    virtual int cost() = 0;
    virtual ~Coffee() {}
};

class BasicCoffee : public Coffee {
public:
    int cost() override {
        return 5;
    }
};

class MilkDecorator : public Coffee {
private:
    Coffee* coffee;
public:
    MilkDecorator(Coffee* c) : coffee(c) {}
    int cost() override {
        return coffee->cost() + 2;
    }
};

int main() {
    Coffee* coffee = new BasicCoffee();
    Coffee* milkCoffee = new MilkDecorator(coffee);
    cout << "Cost: " << milkCoffee->cost() << endl;
    delete milkCoffee;
    delete coffee;
    return 0;
}
```
### <a id="facade">🏠 5. Facade Pattern</a>

Provides a simplified interface to a complex system.
```cpp
#include <iostream>
using namespace std;

class CPU {
public:
    void freeze() { cout << "CPU freeze\n"; }
    void execute() { cout << "CPU execute\n"; }
};

class Memory {
public:
    void load() { cout << "Memory load\n"; }
};

class ComputerFacade {
private:
    CPU cpu;
    Memory memory;
public:
    void start() {
        cpu.freeze();
        memory.load();
        cpu.execute();
    }
};

int main() {
    ComputerFacade computer;
    computer.start();
    return 0;
}
```
### <a id="flyweight">🪶 6. Flyweight Pattern</a>

Reduces memory usage by sharing data.
```cpp
#include <iostream>
#include <unordered_map>
using namespace std;

class Flyweight {
private:
    string sharedState;
public:
    Flyweight(const string& state) : sharedState(state) {}
    void operation(const string& uniqueState) {
        cout << "Shared: " << sharedState << ", Unique: " << uniqueState << endl;
    }
};

class FlyweightFactory {
private:
    unordered_map<string, Flyweight*> flyweights;
public:
    Flyweight* getFlyweight(const string& key) {
        if (flyweights.find(key) == flyweights.end()) {
            flyweights[key] = new Flyweight(key);
        }
        return flyweights[key];
    }

    ~FlyweightFactory() {
        for (auto& p : flyweights) {
            delete p.second;
        }
    }
};

int main() {
    FlyweightFactory factory;
    Flyweight* f1 = factory.getFlyweight("Circle");
    Flyweight* f2 = factory.getFlyweight("Circle");

    f1->operation("Red");
    f2->operation("Blue");
    return 0;
}
```
### <a id="proxy">🛡 7. Proxy Pattern</a>

Acts as a placeholder or access controller.
```cpp
#include <iostream>
using namespace std;

class Subject {
public:
    virtual void request() = 0;
    virtual ~Subject() {}
};

class RealSubject : public Subject {
public:
    void request() override {
        cout << "RealSubject: Handling request.\n";
    }
};

class Proxy : public Subject {
private:
    RealSubject* realSubject;
public:
    Proxy() {
        realSubject = new RealSubject();
    }

    void request() override {
        cout << "Proxy: Checking access...\n";
        realSubject->request();
    }

    ~Proxy() {
        delete realSubject;
    }
};

int main() {
    Subject* proxy = new Proxy();
    proxy->request();
    delete proxy;
    return 0;
}
```