# Programming Patterns
> This is respond from chat gpt for request about programming patterns.

---

## Table of content

1. 🔁 [Creational Patterns](#creational-patterns)

    - [1.1 Singleton](#singleton)
    - [1.2 Factory Method](#factory-method)
    - [1.3 Abstract Factory](#abstract-factory)
    - [1.4 Builder](#builder)
    - [1.5 Prototype](#prototype)

2. 🔄 [Structural Patterns](#structural-patterns)

    - [2.1 Adapter](#adapter)
    - [2.2 Bridge](#bridge)
    - [2.3 Composite](#composite)
    - [2.4 Decorator](#decorator)
    - [2.5 Facade](#facade)
    - [2.6 Flyweight](#flyweight)
    - [2.7 Proxy](#proxy)

3. 🔁 [Behavioral Patterns](#begavioral-patterns)

    * [3.1 Observer](#observer)
    * [3.2 Strategy](#strategy)
    * [3.3 Command](#command)
    * [3.4 State](#state)
    * [3.5 Iterator](#iterator)
    * [3.6 Mediator](#mediator)
    * [3.7 Memento](#memento)
    * [3.8 Visitor](#visitor)
    * [3.9 Chain of Responsibility](#chain-of-responsibility)
    * [3.10 Template Method](#template-method)

---

## <a id="creational-patterns">🏗️ Creational Design Patterns in C++</a>

Creational patterns deal with object creation mechanisms, trying to create objects in a manner suitable to the situation.

- [1. Singleton](#singleton)
- [2. Factory Method](#factory-method)
- [3. Abstract Factory](#abstract-factory)
- [4. Builder](#builder)
- [5. Prototype](#prototype)

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
* You want to ensure products are used together (e.g., matching buttons and checkboxes for a UI).
* You want to switch between different product families easily at runtime (e.g., Windows vs Mac).

#### ✅ Use Case:
- UI toolkit that supports multiple themes (e.g., Light, Dark)
- Database access libraries (e.g., switching between SQL engines)
- Dependency injection frameworks (to switch services at runtime)

#### Key Components:
- **Abstract Factory** - Declares methods to create abstract products.
- **Concrete Factory** - Implements creation methods for specific product variants.
- **Abstract Product** - Declares interfaces for product types.
- **Concrete Product** - Implements the abstract product interface.
- **Client** - Uses only interfaces declared by abstract factory and products.
#### 💡 Code Example:
```cpp
#include <iostream>
#include <memory>
using namespace std;

// Abstract Product A
class Button {
public:
    virtual void render() = 0;
    virtual ~Button() = default;
};

// Abstract Product B
class Checkbox {
public:
    virtual void render() = 0;
    virtual ~Checkbox() = default;
};

// Concrete Product A1
class WindowsButton : public Button {
public:
    void render() override {
        cout << "Rendering Windows Button\n";
    }
};

// Concrete Product B1
class WindowsCheckbox : public Checkbox {
public:
    void render() override {
        cout << "Rendering Windows Checkbox\n";
    }
};

// Concrete Product A2
class MacButton : public Button {
public:
    void render() override {
        cout << "Rendering Mac Button\n";
    }
};

// Concrete Product B2
class MacCheckbox : public Checkbox {
public:
    void render() override {
        cout << "Rendering Mac Checkbox\n";
    }
};

// Abstract Factory
class GUIFactory {
public:
    virtual Button* createButton() = 0;
    virtual Checkbox* createCheckbox() = 0;
    virtual ~GUIFactory() = default;
};

// Concrete Factory 1
class WindowsFactory : public GUIFactory {
public:
    Button* createButton() override {
        return new WindowsButton();
    }

    Checkbox* createCheckbox() override {
        return new WindowsCheckbox();
    }
};

// Concrete Factory 2
class MacFactory : public GUIFactory {
public:
    Button* createButton() override {
        return new MacButton();
    }

    Checkbox* createCheckbox() override {
        return new MacCheckbox();
    }
};

// Client Code
class Application {
private:
    unique_ptr<Button> button;
    unique_ptr<Checkbox> checkbox;

public:
    Application(GUIFactory* factory) {
        button.reset(factory->createButton());
        checkbox.reset(factory->createCheckbox());
    }

    void renderUI() {
        button->render();
        checkbox->render();
    }
};
```
#### 🧪 Usage:
```cpp
int main() {
    // Choose factory (e.g., based on OS or user setting)
    GUIFactory* factory = new WindowsFactory();
    Application app(factory);
    app.renderUI();
```

---

### <a id="builder">4. 🧱 Builder Pattern</a>

**Purpose**: Separate the construction of a complex object from its representation.

#### ✅ Use Case:
- When creating objects with many optional fields or steps.

#### 💡 Code Example:
```cpp
#include <iostream>
#include <string>
using namespace std;

// Product
class Car {
public:
    string engine;
    string wheels;
    string color;

    void show() const {
        cout << "Car with " << engine << " engine, " << wheels << " wheels, color " << color << ".\n";
    }
};

// Abstract Builder
class CarBuilder {
public:
    virtual void buildEngine() = 0;
    virtual void buildWheels() = 0;
    virtual void paintCar() = 0;
    virtual Car* getCar() = 0;
    virtual ~CarBuilder() = default;
};

// Concrete Builder
class SportsCarBuilder : public CarBuilder {
private:
    Car* car;

public:
    SportsCarBuilder() {
        car = new Car();
    }

    void buildEngine() override {
        car->engine = "V8";
    }

    void buildWheels() override {
        car->wheels = "Alloy";
    }

    void paintCar() override {
        car->color = "Red";
    }

    Car* getCar() override {
        return car;
    }
};

// Director (optional)
class Director {
public:
    void construct(CarBuilder* builder) {
        builder->buildEngine();
        builder->buildWheels();
        builder->paintCar();
    }
};
```
#### 🧪 Usage:
```cpp
int main() {
    Director director;
    SportsCarBuilder builder;

    director.construct(&builder);
    Car* car = builder.getCar();
    car->show();

    delete car;
    return 0;
}
```

---

### <a id="prototype">5. 🧬 Prototype Pattern</a>

**Purpose**: Create new objects by copying an existing object (a prototype).

#### ✅ Use Case:
- When object creation is expensive or complex.
- When you want to keep a registry of prototype objects.
- In graphical applications for duplicating objects.

#### ✅ Benefits
- Cloning can be more efficient than creating objects from scratch.
- Allows dynamic configuration of new objects at runtime.
- Avoids subclasses proliferation.


#### 💡 Code Example:
```cpp
#include <iostream>
#include <string>
using namespace std;

class Prototype {
public:
    virtual Prototype* clone() const = 0;
    virtual void info() const = 0;
    virtual ~Prototype() = default;
};

class ConcretePrototype : public Prototype {
private:
    string data;
public:
    ConcretePrototype(const string& d) : data(d) {}

    Prototype* clone() const override {
        return new ConcretePrototype(*this);  // copy constructor
    }

    void info() const override {
        cout << "ConcretePrototype with data: " << data << endl;
    }
};
```
#### 🧪 Usage:
```cpp
int main() {
    ConcretePrototype* original = new ConcretePrototype("Original");
    ConcretePrototype* copy = static_cast<ConcretePrototype*>(original->clone());

    original->info();
    copy->info();

    delete original;
    delete copy;
    return 0;
}
```

---

## <a id="structural-patterns">Structural Patterns</a>
- [1. Adapter](#adapter)
- [2. Bridge](#bridge)
- [3. Composite](#composite)
- [4. Decorator](#decorator)
- [5. Facade](#facade)
- [6. Flyweight](#flyweight)
- [7. Proxy](#proxy)

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

- [1. Observer](#observer)
- [2. Strategy](#strategy)
- [3. Command](#command)
- [4. State](#state)
- [5. Iterator](#iterator)
- [6. Mediator](#mediator)
- [7. Memento](#memento)
- [8. Visitor](#visitor)
- [9. Chain of Responsibility](#chain-of-responsibility)
- [10. Template Method](#template-method)

---

### <a id="observer">1. 🗣️ Observer Pattern</a>

**Purpose**: Defines a one-to-many dependency between objects so that when one object changes state, all its dependents are notified.

#### ✅ Use Case:
- Event handling systems
- GUI frameworks


#### 💡 Intent

> Define a **one-to-many dependency** between objects so that when one object changes state, all its dependents are notified and updated **automatically**.

#### 🔧 Structure

- **Subject (Publisher)**:  
  Maintains a list of observers and notifies them of state changes.
  
- **Observer (Subscriber)**:  
  Defines an interface for receiving updates from the subject.

- **ConcreteSubject**:  
  Stores the actual state of interest and sends notifications.

- **ConcreteObserver**:  
  Implements the update behavior in response to changes in the subject.

#### 💡 Code Example:

```cpp
#include <iostream>
#include <vector>
#include <string>
using namespace std;

// Forward declaration
class Observer;

// Subject Interface
class Subject {
public:
    virtual void attach(Observer* o) = 0;
    virtual void detach(Observer* o) = 0;
    virtual void notify() = 0;
    virtual ~Subject() = default;
};

// Observer Interface
class Observer {
public:
    virtual void update(float temperature) = 0;
    virtual ~Observer() = default;
};

// Concrete Subject
class WeatherStation : public Subject {
private:
    vector<Observer*> observers;
    float temperature = 0.0;

public:
    void attach(Observer* o) override {
        observers.push_back(o);
    }

    void detach(Observer* o) override {
        observers.erase(remove(observers.begin(), observers.end(), o), observers.end());
    }

    void notify() override {
        for (auto o : observers) {
            o->update(temperature);
        }
    }

    void setTemperature(float temp) {
        temperature = temp;
        notify();
    }
};

// Concrete Observer
class Display : public Observer {
private:
    string name;

public:
    Display(const string& n) : name(n) {}

    void update(float temperature) override {
        cout << name << " display: Temperature updated to " << temperature << "°C\n";
    }
};
```
#### 🧪 Usage:
```cpp
int main() {
    WeatherStation station;

    Display screen1("Main");
    Display screen2("Outdoor");

    station.attach(&screen1);
    station.attach(&screen2);

    station.setTemperature(25.0);
    station.setTemperature(30.5);

    station.detach(&screen1);

    station.setTemperature(28.0);

    return 0;
}
```

---

### <a id="strategy">2. 🧵 Strategy Pattern</a>

**Purpose**: Defines a family of algorithms, encapsulates each one, and makes them interchangeable.

**Intent**: Define a family of algorithms, encapsulate each one, and make them interchangeable. Strategy lets the algorithm vary independently from clients that use it.

#### ✅ Use Case:
- Dynamic behavior changes at runtime

#### ✅ Use When:
- You need different variants of an algorithm.
- You want to avoid conditional statements (like `if`/`switch`) for behavior selection.
- You want to allow runtime changes to the behavior of an object.

#### 💡 Structure

- **Strategy (Interface)**: Declares an interface common to all supported algorithms.
- **Concrete Strategies**: Implement different variations of the algorithm.
- **Context**: Maintains a reference to a Strategy object and delegates the behavior to it.

#### 💡 Code Example:
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
#include <iostream>
using namespace std;

// Receiver
class Light {
public:
    void on() {
        cout << "Light is ON\n";
    }

    void off() {
        cout << "Light is OFF\n";
    }
};

// Command Interface
class Command {
public:
    virtual void execute() = 0;
    virtual ~Command() = default;
};

// Concrete Commands
class LightOnCommand : public Command {
private:
    Light* light;

public:
    LightOnCommand(Light* l) : light(l) {}
    void execute() override {
        light->on();
    }
};

class LightOffCommand : public Command {
private:
    Light* light;

public:
    LightOffCommand(Light* l) : light(l) {}
    void execute() override {
        light->off();
    }
};

// Invoker
class RemoteControl {
private:
    Command* command;

public:
    void setCommand(Command* cmd) {
        command = cmd;
    }

    void pressButton() {
        if (command)
            command->execute();
    }
};
```
#### 🧪 Usage:
```cpp
int main() {
    Light livingRoomLight;

    LightOnCommand onCmd(&livingRoomLight);
    LightOffCommand offCmd(&livingRoomLight);

    RemoteControl remote;

    remote.setCommand(&onCmd);
    remote.pressButton();  // Light is ON

    remote.setCommand(&offCmd);
    remote.pressButton();  // Light is OFF

    return 0;
}
```

---

### <a id="state">4. 🛂 State Pattern</a>

**Purpose**: Allows an object to alter its behavior when its internal state changes.

#### ✅ Use Case:
- Finite state machines

#### 💡 Code Example:
```cpp
#include <iostream>
#include <string>
using namespace std;

// Forward declaration
class Document;

// State interface
class State {
public:
    virtual void handle(Document* doc) = 0;
    virtual string getName() = 0;
    virtual ~State() = default;
};

// Context
class Document {
private:
    State* state;

public:
    Document(State* initialState) : state(initialState) {}

    void setState(State* newState) {
        state = newState;
    }

    void request() {
        state->handle(this);
    }

    void printState() {
        cout << "Current state: " << state->getName() << endl;
    }
};

// Concrete States
class Draft : public State {
public:
    void handle(Document* doc) override;
    string getName() override {
        return "Draft";
    }
};

class Moderation : public State {
public:
    void handle(Document* doc) override;
    string getName() override {
        return "Moderation";
    }
};

class Published : public State {
public:
    void handle(Document* doc) override {
        cout << "Document is already published.\n";
    }
    string getName() override {
        return "Published";
    }
};

// State transitions
void Draft::handle(Document* doc) {
    cout << "Submitting draft for moderation...\n";
    doc->setState(new Moderation());
    delete this;  // Clean up
}

void Moderation::handle(Document* doc) {
    cout << "Approving document...\n";
    doc->setState(new Published());
    delete this;  // Clean up
}
```
#### 🧪 Usage:
```cpp
int main() {
    Document doc(new Draft());
    doc.printState();

    doc.request();  // Moves to Moderation
    doc.printState();

    doc.request();  // Moves to Published
    doc.printState();

    doc.request();  // Already published
    return 0;
}
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
#include <string>
using namespace std;

// Iterator Interface
class Iterator {
public:
    virtual bool hasNext() = 0;
    virtual string next() = 0;
    virtual ~Iterator() = default;
};

// Aggregate Interface
class Aggregate {
public:
    virtual Iterator* createIterator() = 0;
    virtual ~Aggregate() = default;
};

// Concrete Aggregate
class MyCollection : public Aggregate {
private:
    vector<string> items;

public:
    void add(const string& item) {
        items.push_back(item);
    }

    string get(int index) const {
        return items[index];
    }

    int size() const {
        return items.size();
    }

    class MyIterator : public Iterator {
    private:
        const MyCollection& collection;
        int index = 0;

    public:
        MyIterator(const MyCollection& coll) : collection(coll) {}

        bool hasNext() override {
            return index < collection.size();
        }

        string next() override {
            return collection.get(index++);
        }
    };

    Iterator* createIterator() override {
        return new MyIterator(*this);
    }
};
```
#### 🧪 Usage:
```cpp
int main() {
    MyCollection collection;
    collection.add("A");
    collection.add("B");
    collection.add("C");

    Iterator* it = collection.createIterator();

    while (it->hasNext()) {
        cout << it->next() << endl;
    }

    delete it; // Cleanup

    return 0;
}
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