# Python Object-Oriented Programming (OOP)

OOP is a programming approach that organizes code around objects containing data and behavior.

## 1. Classes and Objects

A class defines a structure and behavior. An object is an instance of a class.

```python
class Student:
    def introduce(self):
        print("I am a student.")

student1 = Student()
student1.introduce()
```

## 2. The __init__ Method and self

`__init__` initializes an object when it is created. `self` refers to the current instance and is written explicitly as the first parameter of normal instance methods.

```python
class Student:
    def __init__(self, name, age):
        self.name = name
        self.age = age

    def introduce(self):
        print(f"My name is {self.name} and I am {self.age}.")

student = Student("Harshitha", 22)
student.introduce()
```

`self.name` and `self.age` are instance attributes.

## 3. Instance and Class Attributes

Instance attributes belong to individual objects. Class attributes are shared through the class unless an instance shadows them.

```python
class Student:
    school = "ABC School"  # Class attribute

    def __init__(self, name):
        self.name = name  # Instance attribute

s1 = Student("Asha")
s2 = Student("Ravi")
print(s1.school)
print(s2.school)
print(s1.name)
```

Use class attributes for data that genuinely belongs to the class as a whole.

## 4. Instance, Class, and Static Methods

```python
class Calculator:
    category = "Math"

    def describe(self):
        return self.category

    @classmethod
    def get_category(cls):
        return cls.category

    @staticmethod
    def add(a, b):
        return a + b

calc = Calculator()
print(calc.describe())
print(Calculator.get_category())
print(Calculator.add(2, 3))
```

- Instance methods receive `self`.
- Class methods receive `cls` and use `@classmethod`.
- Static methods receive neither automatically and use `@staticmethod`.

## 5. Encapsulation

Encapsulation groups data and methods together and provides a controlled interface for working with an object's state.

```python
class BankAccount:
    def __init__(self, balance=0):
        if balance < 0:
            raise ValueError("Balance cannot be negative")
        self._balance = balance

    def deposit(self, amount):
        if amount <= 0:
            raise ValueError("Deposit must be positive")
        self._balance += amount

    def get_balance(self):
        return self._balance

account = BankAccount(100)
account.deposit(50)
print(account.get_balance())  # 150
```

A leading underscore, as in `_balance`, signals that an attribute is intended for internal use. It does not make it strictly private.

## 6. Inheritance

Inheritance allows a class to extend or specialize another class.

```python
class Animal:
    def speak(self):
        print("Animal sound")

class Dog(Animal):
    def speak(self):
        print("Woof!")

dog = Dog()
dog.speak()  # Woof!
```

`Dog` inherits from `Animal` and overrides its `speak()` method.

## 7. Using super()

`super()` lets a subclass call methods from its parent class, commonly to initialize inherited state.

```python
class Person:
    def __init__(self, name):
        self.name = name

class Employee(Person):
    def __init__(self, name, role):
        super().__init__(name)
        self.role = role

employee = Employee("Harshitha", "Developer")
print(employee.name, employee.role)
```

## 8. Polymorphism

Polymorphism means that different objects can respond to the same operation in their own way.

```python
class Cat:
    def speak(self):
        return "Meow"

class Dog:
    def speak(self):
        return "Woof"

for animal in [Cat(), Dog()]:
    print(animal.speak())
```

Both objects support `speak()`, but the behavior differs.

## 9. Abstraction

Abstraction exposes essential behavior while hiding implementation details. Python's `abc` module supports abstract base classes.

```python
from abc import ABC, abstractmethod

class Shape(ABC):
    @abstractmethod
    def area(self):
        pass

class Rectangle(Shape):
    def __init__(self, width, height):
        self.width = width
        self.height = height

    def area(self):
        return self.width * self.height

rectangle = Rectangle(4, 5)
print(rectangle.area())  # 20
```

A concrete subclass must implement inherited abstract methods before it can be instantiated.

## 10. Properties

Properties allow attribute-style access while running getter or setter logic.

```python
class Product:
    def __init__(self, price):
        self.price = price

    @property
    def price(self):
        return self._price

    @price.setter
    def price(self, value):
        if value < 0:
            raise ValueError("Price cannot be negative")
        self._price = value

product = Product(100)
product.price = 150
print(product.price)
```

## 11. Composition

Composition builds a class using objects of other classes. It is often a flexible alternative to inheritance.

```python
class Engine:
    def start(self):
        print("Engine started")

class Car:
    def __init__(self):
        self.engine = Engine()

    def start(self):
        self.engine.start()
        print("Car is ready")

car = Car()
car.start()
```

## 12. Common Mistakes

- Forgetting `self` in an instance method definition.
- Confusing a class with an instance.
- Accidentally sharing mutable class attributes between instances.
- Overusing inheritance where composition would be simpler.
- Assuming a leading underscore makes an attribute strictly private.
- Forgetting to call `super().__init__()` when parent initialization is needed.

## Practice Problems

1. Create a `Student` class with name, marks, and a method to display details.
2. Create a `BankAccount` class with deposit and withdrawal methods.
3. Create a parent `Animal` class and subclasses `Dog` and `Cat`.
4. Demonstrate method overriding and polymorphism.
5. Create an abstract `Shape` class with `Circle` and `Rectangle` subclasses.
6. Use a property to validate an object's attribute.
7. Build a `Car` class that contains an `Engine` object.

## Key Takeaways

- Classes define objects; objects are instances of classes.
- `__init__` initializes instance state and `self` refers to the instance.
- Encapsulation, inheritance, polymorphism, and abstraction are core OOP concepts.
- Class methods and static methods serve different purposes.
- Composition can help model relationships between objects.
