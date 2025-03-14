# SOLID原则
[单一职责原则](#单一职责原则)  
[开放封闭原则](#开放封闭原则)  
[里氏替换原则](#里氏替换原则)  
[接口隔离原则](#接口隔离原则)  
[依赖倒置原则](#依赖倒置原则)  

## 单一职责原则
核心思想：一个类应该只有一个职责。

C++面向对象三大特性之一的封装指的就是将单一事物抽象出来组合成一个类，所以我们在设计类的时候每个类中处理的是单一事物而不是某些事物的集合。 

如果一个类承担的职责过多，会导致设计变得脆弱。

```C++
// 违反单一职责原则的类：同时处理文件操作和数据解析
class FileProcessor {
public:
    // 职责1：读取文件
    std::string readFile(const std::string& path) {
        std::ifstream file(path);
        std::string content((std::istreambuf_iterator<char>(file)), 
                          std::istreambuf_iterator<char>());
        return content;
    }

    // 职责2：解析数据
    std::vector<int> parseData(const std::string& content) {
        std::vector<int> results;
        // 假设解析逗号分隔的整数
        size_t pos = 0;
        while (pos < content.size()) {
            size_t end = content.find(',', pos);
            if (end == std::string::npos) end = content.size();
            int value = std::stoi(content.substr(pos, end-pos));
            results.push_back(value);
            pos = end + 1;
        }
        return results;
    }
};
```
上述样例当文件读取方式或数据格式改变时，都需要修改同一个类。

如果整个项目有大量类似代码，类会变得庞大且难以理解，将导致一下问题：
- 可读性差
- 修改风险高，修改一个功能可能意外破坏其他功能
- 复用困难，无法单独复用某个职责的功能
- 测试困难：需要覆盖更多不相关的代码路径
- ……

因此在实际开发中，要避免这种情况，遵循单一职责原则。
```C++
// 职责1：专门处理文件读取
class FileReader {
public:
    std::string read(const std::string& path) {
        std::ifstream file(path);
        return {std::istreambuf_iterator<char>(file), 
                std::istreambuf_iterator<char>()};
    }
};

// 职责2：专门处理数据解析
class DataParser {
public:
    std::vector<int> parse(const std::string& content) {
        std::vector<int> results;
        size_t pos = 0;
        while (pos < content.size()) {
            size_t end = content.find(',', pos);
            if (end == std::string::npos) end = content.size();
            int value = std::stoi(content.substr(pos, end-pos));
            results.push_back(value);
            pos = end + 1;
        }
        return results;
    }
};

// 组合使用
class DataProcessor {
    FileReader reader;
    DataParser parser;

public:
    std::vector<int> processFile(const std::string& path) {
        auto content = reader.read(path);
        return parser.parse(content);
    }
};
```
像这样就能较好的避免之前出现的问题,文件读取方式修改只需改FileReader，数据格式变化只需改DataParser。

## 开放封闭原则
核心思想：软件实体（类、模块、函数等）应该对扩展开放，但对修改封闭。
- 扩展开放，即能通过新增代码来添加新功能。
- 修改封闭，已有代码不需要被修改（避免破坏现有功能）。

如果不遵循该原则:
- 每次需求变更都需要修改原有代码。
- 每次修改代码都需要重新测试所有相关代码，将导致测试负担加重。
- 新功能扩展难以进行。

下面是一段违反开放封闭原则的代码示例
```C++
// 形状类型枚举
enum class ShapeType { Circle, Rectangle };

// 违反OCP的类：需要修改已有代码才能添加新形状
class Shape {
    ShapeType type;
public:
    Shape(ShapeType t) : type(t) {}

    void draw() const {
        switch(type) {
            case ShapeType::Circle:
                std::cout << "绘制圆形" << std::endl;
                break;
            case ShapeType::Rectangle:
                std::cout << "绘制矩形" << std::endl;
                break;
            // 添加新形状时需要修改此处
        }
    }
};
```
该代码每次测试新的图形都需要重新修改draw()中的代码，在了解其缺陷之后可以将之优化如下：
```C++
// 抽象基类（对扩展开放）
class Shape {
public:
    virtual ~Shape() = default;
    virtual void draw() const = 0; // 纯虚函数
};

// 具体实现：圆形（不需要修改已有代码）
class Circle : public Shape {
public:
    void draw() const override {
        std::cout << "绘制圆形" << std::endl;
    }
};

// 具体实现：矩形（不需要修改已有代码）
class Rectangle : public Shape {
public:
    void draw() const override {
        std::cout << "绘制矩形" << std::endl;
    }
};

// 新增三角形（只需添加新类）
class Triangle : public Shape {
public:
    void draw() const override {
        std::cout << "绘制三角形" << std::endl;
    }
};
```
之后如果添加新的图形便不需要每次都修改原有类和函数中的代码，相互之间互相隔离，便于测试，如果要扩展新的功能也更加方便。


## 里氏替换原则
核心思想：子类必须能够完全替换它们的基类，而不会破坏程序的正确性。

只看概率很难理解，结合代码如下：
```C++
// 基类：矩形
class Rectangle {
protected:
    int width;
    int height;

public:
    virtual void setWidth(int w) { width = w; }
    virtual void setHeight(int h) { height = h; }
    
    int getArea() const { return width * height; }
};

// 子类：正方形（数学上是矩形特例）
class Square : public Rectangle {
public:
    // 违反LSP：修改了基类行为
    void setWidth(int w) override {
        width = w;
        height = w; // 强制保持宽高相等
    }

    void setHeight(int h) override {
        width = h;  // 强制保持宽高相等
        height = h;
    }
};
```
这是一个典型反向案例，正方形继承矩形，正方形强制保持宽高相等的行为改变了矩形的根本特性，破坏了程序的正确性。

应该使矩阵和正方形相互独立，改进如下：
```C++
// 抽象基类：形状
class Shape {
public:
    virtual ~Shape() = default;
    virtual int getArea() const = 0;
};

// 独立矩形类
class Rectangle : public Shape {
    int width;
    int height;
public:
    void setDimensions(int w, int h) {
        width = w;
        height = h;
    }
    int getArea() const override { return width * height; }
};

// 独立正方形类
class Square : public Shape {
    int side;
public:
    void setSide(int s) { side = s; }
    int getArea() const override { return side * side; }
};
```
这样就不会破坏程序的正确性了。

## 接口隔离原则
定义：客户端不应被迫依赖它们不使用的接口。一个类对另一个类的依赖应建立在最小的、必要的接口上。

核心思想：将庞大的接口拆分为更小、更具体的接口，使得实现类只需关注与其相关的方法，避免不必要的依赖。

错误示例：
```C++
// 庞大的多功能接口
class IMultiFunctionDevice {
public:
    virtual ~IMultiFunctionDevice() = default;
    virtual void print() = 0;          // 打印
    virtual void scan() = 0;           // 扫描
    virtual void sendFax() = 0;        // 发送传真
    virtual void receiveFax() = 0;     // 接收传真
};

// 老旧打印机被迫实现所有接口
class OldPrinter : public IMultiFunctionDevice {
public:
    void print() override {
        std::cout << "打印文档" << std::endl;
    }

    void scan() override {
        throw std::runtime_error("不支持扫描功能");
    }

    void sendFax() override {
        throw std::runtime_error("不支持传真功能");
    }

    void receiveFax() override {
        throw std::runtime_error("不支持传真功能");
    }
};
```

这样会出现的问题就是：
- OldPrinter 必须实现不支持的扫描和传真功能。
- 在调用非支持方法时会导致运行时异常。
- 新增功能（如复印）需修改所有实现类

改进如下：
```C++
// 最小化接口定义
class IPrinter {
public:
    virtual ~IPrinter() = default;
    virtual void print() = 0;
};

class IScanner {
public:
    virtual ~IScanner() = default;
    virtual void scan() = 0;
};

class IFaxMachine {
public:
    virtual ~IFaxMachine() = default;
    virtual void sendFax() = 0;
    virtual void receiveFax() = 0;
};

// 具体实现类按需组合接口
class BasicPrinter : public IPrinter {
public:
    void print() override {
        std::cout << "基础打印功能" << std::endl;
    }
};

class OfficeMachine : public IPrinter, public IScanner, public IFaxMachine {
public:
    void print() override {
        std::cout << "高性能打印" << std::endl;
    }

    void scan() override {
        std::cout << "扫描文档" << std::endl;
    }

    void sendFax() override {
        std::cout << "发送传真" << std::endl;
    }

    void receiveFax() override {
        std::cout << "接收传真" << std::endl;
    }
};
```

接口隔离原则通过解耦依赖关系，使系统更灵活、更易维护。在C++中，合理使用纯虚接口和多重继承，可以有效实现这一原则，尤其在大型项目中，能显著降低模块间的耦合度。

## 依赖倒置原则
定义：
- 高层模块（业务逻辑）不应依赖低层模块（具体实现），两者都应依赖抽象接口
- 抽象不应依赖细节，细节应依赖抽象

核心思想：通过抽象（接口或基类）解耦模块间的直接依赖，使系统更灵活、可扩展

错误示例（日志系统场景）：
```C++
// 低层模块：直接实现文件日志
class FileLogger {
public:
    void logToFile(const std::string& message) {
        std::ofstream file("app.log", std::ios::app);
        if (file.is_open()) {
            file << "[FILE] " << message << std::endl;
        }
    }
};

// 高层模块直接依赖具体实现
class PaymentService {
    FileLogger logger; // 直接耦合具体类
public:
    void processPayment(double amount) {
        // 支付逻辑...
        logger.logToFile("Payment processed: $" + std::to_string(amount));
    }
};
```

这里就是典型的高层模块（业务逻辑）依赖低层模块（具体实现），问题严重：
- 高度耦合，以后若需改用数据库日志，必须修改PaymentService的代码。
- 无法在不修改业务逻辑的情况下扩展日志功能。
- 测试困难，单元测试时无法模拟日志行为。
- 违反开放封闭原则，每次底层变动都会导致高层修改。

改进如下：
```C++
// 抽象接口（高层与低层的共同依赖）
class ILogger {
public:
    virtual ~ILogger() = default;
    virtual void log(const std::string& message) = 0;
};

// 低层实现1：文件日志
class FileLogger : public ILogger {
public:
    void log(const std::string& message) override {
        std::ofstream file("app.log", std::ios::app);
        if (file.is_open()) {
            file << "[FILE] " << message << std::endl;
        }
    }
};

// 低层实现2：控制台日志（新增实现无需修改高层）
class ConsoleLogger : public ILogger {
public:
    void log(const std::string& message) override {
        std::cout << "[CONSOLE] " << message << std::endl;
    }
};

// 高层模块通过抽象接口依赖
class PaymentService {
    std::shared_ptr<ILogger> logger;
public:
    // 依赖注入：通过接口指针传入具体实现
    explicit PaymentService(std::shared_ptr<ILogger> logger) 
        : logger(std::move(logger)) {}

    void processPayment(double amount) {
        // 支付逻辑...
        logger->log("Payment processed: $" + std::to_string(amount));
    }
};
```
不管高层模块还是低层模块，都让其直接依赖抽象接口，使得程序更加灵活。