# 類別 6 大關係

分為兩兩一組，以比較方式理解
- [Realization(實現) ＆ Generalization(泛化)](#realization實現--generalization泛化)
- [Association(關聯) ＆ Dependency(依賴)](#association關聯--dependency依賴)
- [Aggregation(聚合) ＆ Composition(組合)](#aggregation聚合--composition組合)

## 一個類別的結構

對我而言，設計樣式的白話文，代表的是類別與類別之間的關係。因此在討論關係之前，需要先了解類別有多少方式，與其他的類別產生關係。

```python
class A(類別甲):
    def __init__(self, 初始化類別的屬性, 類別乙, 類別丙):
        self.A_屬性 = 初始化類別的屬性
        
        self.A_類別 = 類別乙
        
        self.A_類別_list = [類別丙]
        
        self.A_類別2 = 類別戊
    
    def A功能(self, 功能需要的參數, 類別丁):
        類別丁.功能
        ...
    
    def 類別甲的功能(self)
        ...

```

## Realization(實現) ＆ Generalization(泛化)

### Realization (- - -▷)

1. 定義接口(Interface)和實現類之間的關係
2. 實現類必須實作接口中定義的所有方法
3. Python中使用抽象基類(ABC)來實現接口概念
4. 虛線空心三角箭頭 (- - -▷)

```python
from abc import ABC, abstractmethod

class PaymentInterface(ABC):
    @abstractmethod
    def process_payment(self, amount):
        pass
    
    @abstractmethod
    def refund(self, amount):
        pass

class CreditCardPayment(PaymentInterface):  # 實現關係：實現了PaymentInterface
    def process_payment(self, amount):
        return f"信用卡支付 ${amount}"
    
    def refund(self, amount):
        return f"信用卡退款 ${amount}"
        
class LinePay(PaymentInterface):  # 實現關係：實現了PaymentInterface
    def process_payment(self, amount):
        return f"Line Pay支付 ${amount}"
    
    def refund(self, amount):
        return f"Line Pay退款 ${amount}"

# 使用示例
cc_payment = CreditCardPayment()
line_payment = LinePay()
print(cc_payment.process_payment(100))   # 輸出：信用卡支付 $100
print(line_payment.process_payment(50))  # 輸出：Line Pay支付 $50
```

```mermaid
classDiagram
    PaymentInterface <|.. CreditCardPayment
    PaymentInterface <|.. LinePay
    
    class PaymentInterface {
        <<interface>>
        +process_payment(amount)
        +refund(amount)
    }
    
    class CreditCardPayment {
        +process_payment(amount)
        +refund(amount)
    }
    
    class LinePay {
        +process_payment(amount)
        +refund(amount)
    }
```

### Generalization (——▷)

1. 就是我們常說的繼承(Inheritance)關係
2. 代表"is-a"關係
3. 子類繼承父類的屬性和方法
4. 實線空心三角箭頭 (——▷)

```python
class Animal:
    def __init__(self, name):
        self.name = name
    
    def make_sound(self):
        pass
        
class Dog(Animal):  # 泛化關係：Dog是一種Animal
    def make_sound(self):
        return f"{self.name}說：汪汪！"
        
class Cat(Animal):  # 泛化關係：Cat是一種Animal
    def make_sound(self):
        return f"{self.name}說：喵喵！"
        
# 使用示例
dog = Dog("小黑")
cat = Cat("小花")
print(dog.make_sound())  # 輸出：小黑說：汪汪！
print(cat.make_sound())  # 輸出：小花說：喵喵！
```

```mermaid
classDiagram
    Animal <|-- Dog
    Animal <|-- Cat

    class Animal {
        +name: str
        +make_sound()
    }
    
    class Dog {
        +make_sound()
    }
    
    class Cat {
        +make_sound()
    }
```

[回目錄](#類別-6-大關係)

## Association(關聯) ＆ Dependency(依賴)

### Association (——>)
1. 表示兩個類之間有較長期的連接關係
1. 一個類會保持對另一個類的引用
1. 通常通過類的屬性來實現
1. 實線箭頭 (——>)

```python
class Student:
    def __init__(self, name):
        self.name = name
        
class Course:
    def __init__(self, name, student):
        self.name = name
        self.student = student  # 關聯關係：Course類保持了對Student的引用
        
# 使用示例
student = Student("小明")
course = Course("Python程式設計", student)
```

```mermaid
classDiagram
    Student "1" --> "1..*" Course : enrolls in
    Course "1..*" --> "1" Student : has

    class Student {
        +name: str
        +courses: List
        +enroll(course)
    }
    
    class Course {
        +name: str
        +students: List
        +add_student(student)
    }
```

### Dependency (- - ->)
1. 表示一個類暫時性地使用另一個類
1. 通常出現在方法參數、局部變數或返回值中
1. 不會長期保持對另一個類的引用
1. 虛線箭頭 (- - ->)

```python
class Printer:
    def print_document(self, document):  # 依賴關係：Printer類僅在方法中暫時使用Document
        print(f"打印文件: {document.content}")
        
class Document:
    def __init__(self, content):
        self.content = content
        
# 使用示例
doc = Document("這是一份報告")
printer = Printer()
printer.print_document(doc)  # Printer只是暫時性地使用Document
```

```mermaid
classDiagram
    Printer ..> Document
    
    class Printer {
        +print_document(document)
    }
    
    class Document {
        +content: str
    }
```

[回目錄](#類別-6-大關係)

## Aggregation(聚合) ＆ Composition(組合)

### Aggregation (◇——)

1. 表示"整體"與"部分"的關係，但"部分"可以獨立存在
1. "弱"擁有關係，生命週期可以不同步
1. 通常用"has-a"來描述
1. 空心菱形 (◇——)

```python
class Department:
    def __init__(self, name):
        self.name = name
        self.teachers = []  # 聚合關係：教師可以存在於不同部門
        
    def add_teacher(self, teacher):
        self.teachers.append(teacher)
        
    def remove_teacher(self, teacher):
        self.teachers.remove(teacher)
        
class Teacher:
    def __init__(self, name):
        self.name = name
        
# 使用示例
teacher1 = Teacher("張老師")
teacher2 = Teacher("李老師")

cs_dept = Department("計算機系")
math_dept = Department("數學系")

cs_dept.add_teacher(teacher1)  # 張老師加入計算機系
math_dept.add_teacher(teacher1)  # 同一位老師也可以屬於數學系
```

```mermaid
classDiagram
    Department o-- Teacher
    
    class Department {
        +name: str
        +teachers: List
        +add_teacher(teacher)
        +remove_teacher(teacher)
    }
    
    class Teacher {
        +name: str
    }
```

### Composition (♦——)

1. 表示"整體"與"部分"的關係，但"部分"不能獨立存在
1. "強"擁有關係，生命週期同步
1. 通常用"part-of"來描述
1. 實心菱形 (♦——)

```python
class Engine:
    def __init__(self, type):
        self.type = type
        
    def start(self):
        return f"{self.type}引擎啟動"

class Car:
    def __init__(self, brand):
        self.brand = brand
        self.engine = Engine("V8")  # 組合關係：引擎是汽車的一部分，不能獨立存在
        
    def start_car(self):
        return f"{self.brand}車輛: {self.engine.start()}"
        
# 使用示例
car = Car("Toyota")
print(car.start_car())  # 輸出: Toyota車輛: V8引擎啟動
```

```mermaid
classDiagram
    Car *-- Engine
    
    class Car {
        +brand: str
        -engine: Engine
        +start_car()
    }
    
    class Engine {
        +type: str
        +start()
    }
```

[回目錄](#類別-6-大關係)