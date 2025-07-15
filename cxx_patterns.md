# Programming Patterns
> This is respond from chat gpt for request about programming patterns.

---

## Table of content

1. 🔁 [Creational Patterns](#creational-patterns)

    - [Singleton](#singleton)
    - [Factory Method](#factory-method)
    - [Abstract Factory](#abstract-factory)
    - [Builder](#builder)
    - [Prototype](#prototype)

2. 🔄 [Structural Patterns](#structural-patterns)

    - [Adapter](#adapter)
    - [Bridge](#bridge)
    - [Composite](#composite)
    - [Decorator](#decorator)
    - [Facade](#facade)
    - [Flyweight](#flyweight)
    - [Proxy](#proxy)

3. 🔁 [Behavioral Patterns](#begavioral-patterns)

    * [Observer](#observer)
    * [Strategy](#strategy)
    * [Command](#command)
    * [State](#state)
    * [Iterator](#iterator)
    * [Mediator](#mediator)
    * [Memento](#memento)
    * [Visitor](#visitor)
    * [Chain of Responsibility](#chain-of-responsibility)
    * [Template Method](#template-method)

---

## <a id="creational-patterns">🏗️ Creational Design Patterns in C++</a>

Creational patterns deal with object creation mechanisms, trying to create objects in a manner suitable to the situation.

- [Singleton](#singleton)
- [Factory Method](#factory-method)
- [Abstract Factory](#abstract-factory)
- [Builder](#builder)
- [Prototype](#prototype)

---

### <a id="singleton">1. 🧍 Singleton Pattern</a>

**Purpose**: Ensure a class has only one instance and provide a global point of access to it.

#### ✅ Use Case:
- Logging
- Configuration settings
- Caches

#### 💡 Code Example:
```cpp
#include <iostream>
#include <mutex>

class Singleton {
private:
    static Singleton* instance;
    static std::mutex mtx;

    // Private constructor
    Singleton() {
        std::cout << "Singleton created\n";
    }

    // Prevent copy and assignment
    Singleton(const Singleton&) = delete;
    Singleton& operator=(const Singleton&) = delete;

public:
    static Singleton* getInstance() {
        std::lock_guard<std::mutex> lock(mtx);
        if (instance == nullptr) {
            instance = new Singleton();
        }
        return instance;
    }

    void doSomething() {
        std::cout << "Using Singleton\n";
    }
};

// Initialize static members
Singleton* Singleton::instance = nullptr;
std::mutex Singleton::mtx;
```
#### 🧪 Usage:
```cpp
int main() {
    Singleton* s1 = Singleton::getInstance();
    Singleton* s2 = Singleton::getInstance();

    s1->doSomething();

    if (s1 == s2) {
        std::cout << "Both are the same instance\n";
    }

    return 0;
}
```

---

### <a id="factory-method">2. 🏭 Factory Method Pattern</a>

**Purpose**: Define an interface for creating an object, but let subclasses decide which class to instantiate.

#### ✅ Use Case:
- When the client code needs to work with interfaces and not concrete classes.

#### 💡 Code Example:
```cpp
#include <iostream>
#include <memory>

// Product Interface
class Product {
public:
    virtual void use() = 0;
    virtual ~Product() = default;
};

// Concrete Product A
class ConcreteProductA : public Product {
public:
    void use() override {
        std::cout << "Using Product A\n";
    }
};

// Concrete Product B
class ConcreteProductB : public Product {
public:
    void use() override {
        std::cout << "Using Product B\n";
    }
};

// Creator (Factory)
class Creator {
public:
    virtual std::unique_ptr<Product> createProduct() = 0;
    virtual ~Creator() = default;
};

// Concrete Creator A
class ConcreteCreatorA : public Creator {
public:
    std::unique_ptr<Product> createProduct() override {
        return std::make_unique<ConcreteProductA>();
    }
};

// Concrete Creator B
class ConcreteCreatorB : public Creator {
public:
    std::unique_ptr<Product> createProduct() override {
        return std::make_unique<ConcreteProductB>();
    }
};
```
#### 🧪 Usage:
```cpp
int main() {
    std::unique_ptr<Creator> creatorA = std::make_unique<ConcreteCreatorA>();
    std::unique_ptr<Product> productA = creatorA->createProduct();
    productA->use();  // Output: Using Product A

    std::unique_ptr<Creator> creatorB = std::make_unique<ConcreteCreatorB>();
    std::unique_ptr<Product> productB = creatorB->createProduct();
    productB->use();  // Output: Using Product B

    return 0;
}
```

---

> ### ***!!! Ниже идет не проверенный матерьял !!!***  

### <a id="abstract-factory">3. 🏢 Abstract Factory Pattern</a>

**Purpose**: Provide an interface for creating families of related or dependent objects without specifying their concrete classes.

#### ✅ Use Case:
- UI toolkit that supports multiple themes (e.g., Light, Dark)

#### 💡 Code Example:
```cpp
class Button {
public:
    virtual void render() = 0;
};

class WinButton : public Button {
public:
    void render() override {
        std::cout << "Render Windows Button" << std::endl;
    }
};

class MacButton : public Button {
public:
    void render() override {
        std::cout << "Render Mac Button" << std::endl;
    }
};

class GUIFactory {
public:
    virtual Button* createButton() = 0;
};

class WinFactory : public GUIFactory {
public:
    Button* createButton() override {
        return new WinButton();
    }
};

class MacFactory : public GUIFactory {
public:
    Button* createButton() override {
        return new MacButton();
    }
};
```

---

### <a id="builder">4. 🧱 Builder Pattern</a>

**Purpose**: Separate the construction of a complex object from its representation.

#### ✅ Use Case:
- When creating objects with many optional fields or steps.

#### 💡 Code Example:
```cpp
class Product {
public:
    void setPartA(std::string part) { partA = part; }
    void setPartB(std::string part) { partB = part; }
    void show() {
        std::cout << "Product with " << partA << " and " << partB << std::endl;
    }
private:
    std::string partA, partB;
};

class Builder {
public:
    virtual void buildPartA() = 0;
    virtual void buildPartB() = 0;
    virtual Product* getResult() = 0;
};

class ConcreteBuilder : public Builder {
private:
    Product* product = new Product();
public:
    void buildPartA() override {
        product->setPartA("Part A");
    }

    void buildPartB() override {
        product->setPartB("Part B");
    }

    Product* getResult() override {
        return product;
    }
};

class Director {
public:
    void construct(Builder* builder) {
        builder->buildPartA();
        builder->buildPartB();
    }
};
```

---

### <a id="prototype">5. 🧬 Prototype Pattern</a>

**Purpose**: Create new objects by copying an existing object (a prototype).

#### ✅ Use Case:
- When object creation is expensive or complex.

#### 💡 Code Example:
```cpp
class Prototype {
public:
    virtual Prototype* clone() = 0;
    virtual void show() = 0;
};

class ConcretePrototype : public Prototype {
private:
    int id;
public:
    ConcretePrototype(int id) : id(id) {}
    Prototype* clone() override {
        return new ConcretePrototype(*this);
    }

    void show() override {
        std::cout << "Prototype ID: " << id << std::endl;
    }
};
```

---

## <a id="structural-patterns">Structural Patterns</a>
- [Adapter](#adapter)
- [Bridge](#bridge)
- [Composite](#composite)
- [Decorator](#decorator)
- [Facade](#facade)
- [Flyweight](#flyweight)
- [Proxy](#proxy)

---

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
```
#### 🧪 Usage:
```cpp
int main() {
    OldPrinter* old = new OldPrinter();
    NewPrinter* adapter = new PrinterAdapter(old);
    adapter->print("Hello, Adapter Pattern!");
    delete adapter;
    delete old;
    return 0;
}
```

---

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
```
#### 🧪 Usage:
```cpp
int main() {
    VectorRenderer vr;
    Circle c(&vr, 5.0f);
    c.draw();
    return 0;
}
```

---

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
```
#### 🧪 Usage:
```cpp
int main() {
    Composite* root = new Composite();
    root->add(new Leaf());
    root->add(new Leaf());
    root->operation();
    delete root;
    return 0;
}
```

---

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
```
#### 🧪 Usage:
```cpp
int main() {
    Coffee* coffee = new BasicCoffee();
    Coffee* milkCoffee = new MilkDecorator(coffee);
    cout << "Cost: " << milkCoffee->cost() << endl;
    delete milkCoffee;
    delete coffee;
    return 0;
}
```

---

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
```
#### 🧪 Usage:
```cpp
int main() {
    ComputerFacade computer;
    computer.start();
    return 0;
}
```

---

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
```
#### 🧪 Usage:
```cpp
int main() {
    FlyweightFactory factory;
    Flyweight* f1 = factory.getFlyweight("Circle");
    Flyweight* f2 = factory.getFlyweight("Circle");

    f1->operation("Red");
    f2->operation("Blue");
    return 0;
}
```

---

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
```
#### 🧪 Usage:
```cpp
int main() {
    Subject* proxy = new Proxy();
    proxy->request();
    delete proxy;
    return 0;
}
```

---

## <a id="begavioral-patterns">🤝 Behavioral Design Patterns in C++</a>

Behavioral patterns are concerned with algorithms and the assignment of responsibilities between objects.

- [Observer](#observer)
- [Strategy](#strategy)
- [Command](#command)
- [State](#state)
- [Iterator](#iterator)
- [Mediator](#mediator)
- [Memento](#memento)
- [Visitor](#visitor)
- [Chain of Responsibility](#chain-of-responsibility)
- [Template Method](#template-method)

---

### <a id="observer">1. 🗣️ Observer Pattern</a>

**Purpose**: Defines a one-to-many dependency between objects so that when one object changes state, all its dependents are notified.

#### ✅ Use Case:
- Event handling systems
- GUI frameworks

#### 💡 Code Example:
```cpp
#include <iostream>
#include <vector>
#include <algorithm>

class Observer {
public:
    virtual void update(int value) = 0;
};

class Subject {
private:
    std::vector<Observer*> observers;
    int state;

public:
    void attach(Observer* obs) {
        observers.push_back(obs);
    }

    void setState(int value) {
        state = value;
        notify();
    }

    void notify() {
        for (Observer* obs : observers) {
            obs->update(state);
        }
    }
};

class ConcreteObserver : public Observer {
private:
    int observerState;
public:
    void update(int value) override {
        observerState = value;
        std::cout << "Observer updated with state: " << observerState << std::endl;
    }
};
```

---

### <a id="strategy">2. 🧵 Strategy Pattern</a>

**Purpose**: Defines a family of algorithms, encapsulates each one, and makes them interchangeable.

#### ✅ Use Case:
- Dynamic behavior changes at runtime

#### 💡 Code Example:
```cpp
## 2. 🧵 Strategy Pattern

**Intent**: Define a family of algorithms, encapsulate each one, and make them interchangeable. Strategy lets the algorithm vary independently from clients that use it.

### ✅ Use When:
- You need different variants of an algorithm.
- You want to avoid conditional statements (like `if`/`switch`) for behavior selection.
- You want to allow runtime changes to the behavior of an object.

---

### 💡 Structure

- **Strategy (Interface)**: Declares an interface common to all supported algorithms.
- **Concrete Strategies**: Implement different variations of the algorithm.
- **Context**: Maintains a reference to a Strategy object and delegates the behavior to it.

---

### 💡 Example in C++

```cpp
#include <iostream>
#include <memory>

// Strategy interface
class Strategy {
public:
    virtual void execute() = 0;
    virtual ~Strategy() = default;
};

// Concrete Strategy A
class ConcreteStrategyA : public Strategy {
public:
    void execute() override {
        std::cout << "Executing Strategy A" << std::endl;
    }
};

// Concrete Strategy B
class ConcreteStrategyB : public Strategy {
public:
    void execute() override {
        std::cout << "Executing Strategy B" << std::endl;
    }
};

// Context class
class Context {
private:
    std::unique_ptr<Strategy> strategy;
public:
    Context(std::unique_ptr<Strategy> s) : strategy(std::move(s)) {}

    void setStrategy(std::unique_ptr<Strategy> s) {
        strategy = std::move(s);
    }

    void executeStrategy() {
        strategy->execute();
    }
};
```
#### 🧪 Usage:
```cpp
int main() {
    Context context(std::make_unique<ConcreteStrategyA>());
    context.executeStrategy();  // Output: Executing Strategy A

    context.setStrategy(std::make_unique<ConcreteStrategyB>());
    context.executeStrategy();  // Output: Executing Strategy B

    return 0;
}
```

---

### <a id="command">3. 🧰 Command Pattern</a>

**Purpose**: Encapsulate a request as an object, thereby allowing for parameterization of clients with queues, logs, and undo functionality.

#### ✅ Use Case:
- GUI buttons
- Task scheduling

#### 💡 Code Example:
```cpp
class Command {
public:
    virtual void execute() = 0;
};

class Receiver {
public:
    void action() {
        std::cout << "Action performed!" << std::endl;
    }
};

class ConcreteCommand : public Command {
private:
    Receiver* receiver;
public:
    ConcreteCommand(Receiver* r) : receiver(r) {}
    void execute() override {
        receiver->action();
    }
};

class Invoker {
private:
    Command* command;
public:
    void setCommand(Command* cmd) {
        command = cmd;
    }

    void run() {
        command->execute();
    }
};
```

---

### <a id="state">4. 🛂 State Pattern</a>

**Purpose**: Allows an object to alter its behavior when its internal state changes.

#### ✅ Use Case:
- Finite state machines

#### 💡 Code Example:
```cpp
class State {
public:
    virtual void handle() = 0;
};

class Context {
private:
    State* state;
public:
    Context(State* s) : state(s) {}

    void setState(State* s) {
        state = s;
    }

    void request() {
        state->handle();
    }
};

class ConcreteStateA : public State {
public:
    void handle() override {
        std::cout << "Handling in State A" << std::endl;
    }
};

class ConcreteStateB : public State {
public:
    void handle() override {
        std::cout << "Handling in State B" << std::endl;
    }
};
```

---

### <a id="iterator">5. 🔁 Iterator Pattern</a>

**Purpose**: Provides a way to access the elements of an aggregate object sequentially without exposing its underlying representation.

#### ✅ Use Case:
- Collections and containers

#### 💡 Code Example:
```cpp
#include <iostream>
#include <vector>

class Iterator {
public:
    virtual bool hasNext() = 0;
    virtual int next() = 0;
};

class Aggregate {
private:
    std::vector<int> items;
public:
    void add(int value) {
        items.push_back(value);
    }

    std::vector<int> getItems() const {
        return items;
    }
};

class ConcreteIterator : public Iterator {
private:
    const std::vector<int>& items;
    size_t index;
public:
    ConcreteIterator(const std::vector<int>& items) : items(items), index(0) {}

    bool hasNext() override {
        return index < items.size();
    }

    int next() override {
        return items[index++];
    }
};
```

---

### <a id="mediator">6. 🧭 Mediator Pattern</a>

**Intent**: Define an object that encapsulates how a set of objects interact. Mediator promotes loose coupling by keeping objects from referring to each other explicitly.

#### ✅ Use When:
- A set of objects communicate in complex ways.
- You want to centralize control and reduce direct dependencies between objects.

#### 💡 Example:
```cpp
#include <iostream>
#include <string>
#include <vector>

class Colleague;

class Mediator {
public:
    virtual void send(const std::string& message, Colleague* sender) = 0;
};

class Colleague {
protected:
    Mediator* mediator;
public:
    Colleague(Mediator* m) : mediator(m) {}
    virtual void send(const std::string& message) = 0;
    virtual void receive(const std::string& message) = 0;
};

class ConcreteColleague1 : public Colleague {
public:
    ConcreteColleague1(Mediator* m) : Colleague(m) {}
    void send(const std::string& message) override {
        std::cout << "Colleague1 sends: " << message << std::endl;
        mediator->send(message, this);
    }
    void receive(const std::string& message) override {
        std::cout << "Colleague1 receives: " << message << std::endl;
    }
};

class ConcreteColleague2 : public Colleague {
public:
    ConcreteColleague2(Mediator* m) : Colleague(m) {}
    void send(const std::string& message) override {
        std::cout << "Colleague2 sends: " << message << std::endl;
        mediator->send(message, this);
    }
    void receive(const std::string& message) override {
        std::cout << "Colleague2 receives: " << message << std::endl;
    }
};

class ConcreteMediator : public Mediator {
private:
    Colleague* colleague1;
    Colleague* colleague2;
public:
    void setColleagues(Colleague* c1, Colleague* c2) {
        colleague1 = c1;
        colleague2 = c2;
    }

    void send(const std::string& message, Colleague* sender) override {
        if (sender == colleague1)
            colleague2->receive(message);
        else
            colleague1->receive(message);
    }
};
```
#### 🧪 Usage:
```cpp
int main() {
    ConcreteMediator mediator;
    ConcreteColleague1 c1(&mediator);
    ConcreteColleague2 c2(&mediator);
    mediator.setColleagues(&c1, &c2);

    c1.send("Hello from Colleague1");
    c2.send("Hi from Colleague2");

    return 0;
}
```

---

### <a id="memento">7. 🧳 Memento Pattern</a>

**Intent**: Capture and externalize an object's internal state so that it can be restored later, without violating encapsulation.

#### ✅ Use When:
- You need to implement undo/rollback functionality.
- You want to preserve an object’s state without exposing its internals.

#### 💡 Example:
```cpp
#include <iostream>
#include <string>

class Memento {
private:
    std::string state;
public:
    Memento(const std::string& s) : state(s) {}
    std::string getState() const { return state; }
};

class Originator {
private:
    std::string state;
public:
    void setState(const std::string& s) {
        state = s;
        std::cout << "State set to: " << state << std::endl;
    }

    std::string getState() const { return state; }

    Memento save() const {
        return Memento(state);
    }

    void restore(const Memento& memento) {
        state = memento.getState();
        std::cout << "State restored to: " << state << std::endl;
    }
};

class Caretaker {
private:
    Memento memento;
public:
    Caretaker(const Memento& m) : memento(m) {}
    Memento getMemento() const { return memento; }
};
```
#### 🧪 Usage:
```cpp
int main() {
    Originator originator;
    originator.setState("State #1");

    Caretaker caretaker(originator.save());

    originator.setState("State #2");

    // Restore previous state
    originator.restore(caretaker.getMemento());

    return 0;
}
```

---

### <a id="visitor">8. 🧭 Visitor Pattern</a>

**Intent**: Represent an operation to be performed on elements of an object structure. Visitor lets you define a new operation **without changing the classes** of the elements on which it operates.

#### ✅ Use When:
- You want to perform operations across a collection of objects with different types.
- You need to add behavior to class hierarchies without modifying the classes.

#### 💡 Example:
```cpp
#include <iostream>
#include <vector>

class ConcreteElementA;
class ConcreteElementB;

class Visitor {
public:
    virtual void visit(ConcreteElementA* element) = 0;
    virtual void visit(ConcreteElementB* element) = 0;
};

class Element {
public:
    virtual void accept(Visitor* visitor) = 0;
};

class ConcreteElementA : public Element {
public:
    void accept(Visitor* visitor) override {
        visitor->visit(this);
    }
    void operationA() {
        std::cout << "Operation A in Element A" << std::endl;
    }
};

class ConcreteElementB : public Element {
public:
    void accept(Visitor* visitor) override {
        visitor->visit(this);
    }
    void operationB() {
        std::cout << "Operation B in Element B" << std::endl;
    }
};

class ConcreteVisitor : public Visitor {
public:
    void visit(ConcreteElementA* element) override {
        element->operationA();
    }

    void visit(ConcreteElementB* element) override {
        element->operationB();
    }
};
```
#### 🧪 Usage:
```cpp
int main() {
    std::vector<Element*> elements;
    elements.push_back(new ConcreteElementA());
    elements.push_back(new ConcreteElementB());

    ConcreteVisitor visitor;

    for (Element* elem : elements) {
        elem->accept(&visitor);
    }

    // Clean up
    for (Element* elem : elements) {
        delete elem;
    }

    return 0;
}
```

---

### <a id="chain-of-responsibility">9. Chain of Responsibility Pattern</a>

**Intent**: Avoid coupling the sender of a request to its receiver by giving multiple objects a chance to handle the request. Chain the receiving objects and pass the request along the chain until one handles it.

#### ✅ Use When:
- More than one object may handle a request, and you don’t want the sender to know which.
- You want to decouple senders and receivers.

#### 💡 Example:
```cpp
#include <iostream>
#include <string>

class Handler {
protected:
    Handler* next;
public:
    Handler() : next(nullptr) {}

    void setNext(Handler* handler) {
        next = handler;
    }

    virtual void handleRequest(int level) {
        if (next)
            next->handleRequest(level);
    }

    virtual ~Handler() = default;
};

class ConcreteHandler1 : public Handler {
public:
    void handleRequest(int level) override {
        if (level == 1) {
            std::cout << "Handled by Handler 1\n";
        } else if (next) {
            next->handleRequest(level);
        }
    }
};

class ConcreteHandler2 : public Handler {
public:
    void handleRequest(int level) override {
        if (level == 2) {
            std::cout << "Handled by Handler 2\n";
        } else if (next) {
            next->handleRequest(level);
        }
    }
};

class ConcreteHandler3 : public Handler {
public:
    void handleRequest(int level) override {
        if (level == 3) {
            std::cout << "Handled by Handler 3\n";
        } else {
            std::cout << "Request not handled\n";
        }
    }
};
```
#### 🧪 Usage:
```cpp
int main() {
    Handler* h1 = new ConcreteHandler1();
    Handler* h2 = new ConcreteHandler2();
    Handler* h3 = new ConcreteHandler3();

    h1->setNext(h2);
    h2->setNext(h3);

    h1->handleRequest(2); // Output: Handled by Handler 2
    h1->handleRequest(3); // Output: Handled by Handler 3
    h1->handleRequest(4); // Output: Request not handled

    delete h1;
    delete h2;
    delete h3;

    return 0;
}
```

---

### <a id="template-method">10. 🧩 Template Method Pattern</a>

**Intent**: Define the skeleton of an algorithm in a base class, but let subclasses override specific steps of the algorithm without changing its overall structure.

#### ✅ Use When:
- You want to define the outline of an algorithm, letting subclasses implement specific steps.
- You want to enforce a consistent process structure across classes.

#### 💡 Example:
```cpp
#include <iostream>

class AbstractClass {
public:
    void templateMethod() {
        baseOperation1();
        requiredOperation1();
        baseOperation2();
        requiredOperation2();
        hook();
    }

protected:
    void baseOperation1() {
        std::cout << "Base operation 1\n";
    }

    void baseOperation2() {
        std::cout << "Base operation 2\n";
    }

    virtual void requiredOperation1() = 0;
    virtual void requiredOperation2() = 0;

    virtual void hook() {
        // Optional override
    }
};

class ConcreteClassA : public AbstractClass {
protected:
    void requiredOperation1() override {
        std::cout << "ConcreteClassA: Operation 1\n";
    }

    void requiredOperation2() override {
        std::cout << "ConcreteClassA: Operation 2\n";
    }
};

class ConcreteClassB : public AbstractClass {
protected:
    void requiredOperation1() override {
        std::cout << "ConcreteClassB: Operation 1\n";
    }

    void requiredOperation2() override {
        std::cout << "ConcreteClassB: Operation 2\n";
    }

    void hook() override {
        std::cout << "ConcreteClassB: Optional hook\n";
    }
};
```
#### 🧪 Usage:
```cpp
int main() {
    AbstractClass* a = new ConcreteClassA();
    AbstractClass* b = new ConcreteClassB();

    std::cout << "Executing A:\n";
    a->templateMethod();

    std::cout << "\nExecuting B:\n";
    b->templateMethod();

    delete a;
    delete b;

    return 0;
}
```

---