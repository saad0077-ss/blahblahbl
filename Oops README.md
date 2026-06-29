# OOP Concepts in Dart — Complete Guide

A reference README covering Object-Oriented Programming concepts as implemented in Dart, with explanations and code snippets.

---

## Table of Contents
1. [What is OOP](#what-is-oop)
2. [Class and Object](#class-and-object)
3. [Encapsulation](#encapsulation)
4. [Access Modifiers (Public/Private)](#access-modifiers-publicprivate)
5. [Inheritance](#inheritance)
6. [Polymorphism](#polymorphism)
7. [Method Overriding](#method-overriding)
8. [Method Overloading](#method-overloading)
9. [Operator Overloading](#operator-overloading)
10. [Abstraction](#abstraction)
11. [Abstract Class](#abstract-class)
12. [Interface](#interface)
13. [extends vs implements vs with](#extends-vs-implements-vs-with)
14. [this Keyword](#this-keyword)
15. [super Keyword](#super-keyword)
16. [Static Members](#static-members)
17. [Getters and Setters](#getters-and-setters)
18. [Constructors Recap](#constructors-recap)
19. [Generics](#generics)
20. [Composition vs Inheritance](#composition-vs-inheritance)
21. [Singleton Pattern](#singleton-pattern)
22. [Factory Design Pattern](#factory-design-pattern)
23. [toString, ==, and hashCode Overrides](#tostring--and-hashcode-overrides)
24. [Object Class (Root of All Classes)](#object-class-root-of-all-classes)

---

## What is OOP

**Object-Oriented Programming (OOP)** is a programming paradigm based on the concept of **objects** — bundles of **data (fields/properties)** and **behavior (methods)** — rather than just functions and logic.

The four pillars of OOP:
1. **Encapsulation** — bundling data and restricting direct access.
2. **Inheritance** — reusing code from a parent class.
3. **Polymorphism** — one interface, many implementations.
4. **Abstraction** — hiding implementation details, exposing only essentials.

Dart is a **fully object-oriented language** — even `int`, `bool`, and functions are objects.

```dart
class Car {
  String brand = 'Toyota';
  void drive() => print('$brand is driving');
}

void main() {
  Car myCar = Car(); // object/instance
  myCar.drive();
}
```

---

## Class and Object

- **Class**: A blueprint/template that defines properties (fields) and behaviors (methods) for objects.
- **Object**: An **instance** of a class — created using a constructor; holds actual data.

```dart
class Animal {
  String name;
  int age;

  Animal(this.name, this.age);

  void makeSound() => print('$name makes a sound');
}

void main() {
  Animal dog = Animal('Rex', 3); // dog is an object of class Animal
  dog.makeSound(); // Rex makes a sound
}
```

---

## Encapsulation

The practice of **bundling data and methods together** inside a class, and **restricting direct access** to internal state — usually exposing controlled access via methods or getters/setters.

In Dart, encapsulation is achieved using the underscore (`_`) prefix to mark members **private to the library/file**.

```dart
class BankAccount {
  double _balance = 0; // private field

  void deposit(double amount) {
    if (amount > 0) _balance += amount;
  }

  double get balance => _balance; // controlled read access
}

void main() {
  var account = BankAccount();
  account.deposit(500);
  print(account.balance); // 500
  // account._balance = 1000; // ERROR if accessed outside the library
}
```

---

## Access Modifiers (Public/Private)

Dart has a simpler access model than languages like Java:

| Modifier | Syntax | Scope |
|---|---|---|
| Public | normal name (`name`) | Accessible anywhere |
| Private | prefix with `_` (`_name`) | Accessible only **within the same library/file** |

> Dart has no `protected` keyword — privacy is enforced at the **library level**, not the class level.

```dart
class Person {
  String name;       // public
  int _age;           // private (to this file)

  Person(this.name, this._age);
}
```

---

## Inheritance

A mechanism where a class (**subclass/child**) acquires properties and methods of another class (**superclass/parent**) using the `extends` keyword — promoting code reuse.

```dart
class Animal {
  void eat() => print('Animal eats');
}

class Dog extends Animal {
  void bark() => print('Dog barks');
}

void main() {
  Dog dog = Dog();
  dog.eat();  // inherited from Animal
  dog.bark(); // own method
}
```

> Dart supports **single inheritance only** (`extends` one class) — use `mixin`/`with` for multiple behavior reuse, and `implements` for multiple interfaces.

---

## Polymorphism

"Many forms" — the ability for **different classes to be treated through a common interface**, with each providing its own specific behavior.

Two main types:
1. **Compile-time (static) polymorphism** — method overloading (limited support in Dart via named/optional params).
2. **Runtime (dynamic) polymorphism** — method overriding via inheritance.

```dart
class Shape {
  double area() => 0;
}

class Circle extends Shape {
  double radius;
  Circle(this.radius);
  @override
  double area() => 3.14 * radius * radius;
}

class Square extends Shape {
  double side;
  Square(this.side);
  @override
  double area() => side * side;
}

void main() {
  List<Shape> shapes = [Circle(3), Square(4)];
  for (var shape in shapes) {
    print(shape.area()); // calls the correct overridden method at runtime
  }
}
```

---

## Method Overriding

Redefining a method that's already defined in a parent class, in a child class — with the **same name and signature** but different implementation. Marked with `@override`.

```dart
class Vehicle {
  void start() => print('Vehicle starting');
}

class Car extends Vehicle {
  @override
  void start() => print('Car starting with key');
}

void main() {
  Vehicle v = Car();
  v.start(); // Car starting with key (runtime polymorphism)
}
```

---

## Method Overloading

Dart **does not support traditional method overloading** (same method name, different parameter lists) like Java/C++. Instead, Dart achieves similar flexibility using:

- **Optional positional parameters** (`[]`)
- **Named parameters** (`{}`)

```dart
// Simulated "overloading" using optional parameters
class Calculator {
  int add(int a, int b, [int c = 0]) => a + b + c;
}

void main() {
  var calc = Calculator();
  print(calc.add(2, 3));    // 5
  print(calc.add(2, 3, 4)); // 9
}
```

---

## Operator Overloading

Dart allows redefining the behavior of built-in operators (`+`, `-`, `==`, `[]`, etc.) for custom classes using the `operator` keyword.

```dart
class Point {
  final double x, y;
  Point(this.x, this.y);

  Point operator +(Point other) => Point(x + other.x, y + other.y);

  @override
  String toString() => 'Point($x, $y)';
}

void main() {
  Point p1 = Point(1, 2);
  Point p2 = Point(3, 4);
  Point p3 = p1 + p2; // uses overloaded + operator
  print(p3); // Point(4.0, 6.0)
}
```

---

## Abstraction

Hiding internal implementation details and exposing only the **essential features** of an object. Achieved in Dart using **abstract classes** and **interfaces**.

```dart
abstract class PaymentMethod {
  void pay(double amount); // no implementation — abstraction
}

class CreditCard extends PaymentMethod {
  @override
  void pay(double amount) => print('Paid $amount via Credit Card');
}

void main() {
  PaymentMethod payment = CreditCard();
  payment.pay(100); // user doesn't need to know HOW it works internally
}
```

---

## Abstract Class

A class declared with `abstract` that **cannot be instantiated directly**. It can contain both implemented methods and unimplemented (abstract) methods, meant to be extended by subclasses.

```dart
abstract class Shape {
  double area(); // abstract method — must be implemented by subclasses

  void display() {
    print('Area: ${area()}'); // concrete method — can be inherited as-is
  }
}

class Rectangle extends Shape {
  double width, height;
  Rectangle(this.width, this.height);

  @override
  double area() => width * height;
}

void main() {
  Rectangle rect = Rectangle(4, 5);
  rect.display(); // Area: 20.0

  // Shape s = Shape(); // ERROR — cannot instantiate abstract class
}
```

---

## Interface

Dart doesn't have a separate `interface` keyword — **any class can act as an interface** and be implemented using the `implements` keyword. A class implementing an interface must provide its **own implementation for all methods**.

```dart
class Flyer {
  void fly() => print('Flying...'); // treated as interface contract
}

class Swimmer {
  void swim() => print('Swimming...');
}

class Duck implements Flyer, Swimmer {
  @override
  void fly() => print('Duck flying');

  @override
  void swim() => print('Duck swimming');
}

void main() {
  Duck duck = Duck();
  duck.fly();
  duck.swim();
}
```

> Unlike `extends` (single inheritance), a class can `implements` **multiple** interfaces.

---

## extends vs implements vs with

| Keyword | Purpose | Multiple allowed? | Inherits implementation? |
|---|---|---|---|
| `extends` | Inherit from one superclass | No (single only) | Yes |
| `implements` | Adopt a class as an interface contract | Yes | No (must re-implement all methods) |
| `with` (mixin) | Reuse behavior from one or more mixins | Yes | Yes |

```dart
class Animal {
  void eat() => print('Eating');
}

mixin Walkable {
  void walk() => print('Walking');
}

class Robot {
  void move() => print('Robot moving');
}

class Human extends Animal with Walkable implements Robot {
  @override
  void move() => print('Human moving like a robot');
}
```

---

## this Keyword

Refers to the **current instance** of the class — used to distinguish instance fields from parameters with the same name, or to call other constructors.

```dart
class Person {
  String name;
  int age;

  Person(this.name, this.age); // shorthand using `this`

  Person.copy(Person other) : this(other.name, other.age); // redirecting constructor
}
```

---

## super Keyword

Refers to the **parent class** — used to call the parent's constructor, methods, or properties from within a subclass.

```dart
class Animal {
  String name;
  Animal(this.name);
  void speak() => print('$name makes a sound');
}

class Dog extends Animal {
  Dog(String name) : super(name); // call parent constructor

  @override
  void speak() {
    super.speak();        // call parent method
    print('$name barks'); // add child-specific behavior
  }
}

void main() {
  Dog dog = Dog('Rex');
  dog.speak();
  // Rex makes a sound
  // Rex barks
}
```

---

## Static Members

Members (fields/methods) that belong to the **class itself**, not to individual instances — accessed via the class name, shared across all objects.

```dart
class Counter {
  static int count = 0; // shared across all instances

  Counter() {
    count++;
  }

  static void showCount() => print('Count: $count');
}

void main() {
  Counter();
  Counter();
  Counter();
  Counter.showCount(); // Count: 3
}
```

---

## Getters and Setters

Special methods that provide **controlled access** to a class's private fields — `get` for reading, `set` for writing — often used for validation or computed properties.

```dart
class Rectangle {
  double _width, _height;

  Rectangle(this._width, this._height);

  double get area => _width * _height; // computed getter

  set width(double value) {
    if (value > 0) _width = value; // validation in setter
  }
}

void main() {
  var rect = Rectangle(4, 5);
  print(rect.area); // 20.0 — called like a property, no parentheses
  rect.width = 10;
  print(rect.area); // 50.0
}
```

---

## Constructors Recap

Constructors initialize objects. Key variants in OOP context:

```dart
class Employee {
  String name;
  double salary;

  // Default constructor
  Employee(this.name, this.salary);

  // Named constructor
  Employee.intern(this.name) : salary = 0;

  // Constant constructor (for immutable instances)
  const Employee.template() : name = 'Unknown', salary = 0;

  // Factory constructor — can control instance creation
  factory Employee.fromJson(Map<String, dynamic> json) {
    return Employee(json['name'], json['salary']);
  }
}
```

---

## Generics

Allow classes, methods, and collections to work with **any data type** while still preserving type safety, using type parameters (`<T>`).

```dart
class Box<T> {
  T value;
  Box(this.value);

  T getValue() => value;
}

void main() {
  Box<int> intBox = Box<int>(10);
  Box<String> strBox = Box<String>('Hello');

  print(intBox.getValue());  // 10
  print(strBox.getValue());  // Hello
}

// Generic method
T firstElement<T>(List<T> list) => list.first;
```

---

## Composition vs Inheritance

- **Inheritance ("is-a" relationship)**: A class extends another class, inheriting its behavior — tightly coupled.
- **Composition ("has-a" relationship)**: A class **contains an instance of another class** to reuse its functionality — more flexible, often preferred ("favor composition over inheritance").

```dart
// Inheritance — Car IS-A Vehicle
class Vehicle {
  void move() => print('Moving');
}
class Car extends Vehicle {}

// Composition — Car HAS-A Engine
class Engine {
  void start() => print('Engine starting');
}
class Car2 {
  final Engine engine = Engine(); // composed, not inherited
  void start() => engine.start();
}
```

---

## Singleton Pattern

A design pattern that ensures a class has **only one instance** throughout the app, with a global access point to it. Commonly used for things like database connections, shared services.

```dart
class ApiService {
  ApiService._internal(); // private named constructor
  static final ApiService _instance = ApiService._internal();

  factory ApiService() => _instance; // always returns the same instance

  void fetchData() => print('Fetching data...');
}

void main() {
  var a = ApiService();
  var b = ApiService();
  print(a == b); // true — same instance
}
```

---

## Factory Design Pattern

A pattern where a **factory constructor decides** what kind of object to return — useful for caching, returning subclasses, or parsing data (e.g., JSON deserialization).

```dart
abstract class Shape {
  factory Shape(String type) {
    switch (type) {
      case 'circle':
        return Circle();
      case 'square':
        return Square();
      default:
        throw Exception('Unknown shape');
    }
  }
  void draw();
}

class Circle implements Shape {
  @override
  void draw() => print('Drawing Circle');
}

class Square implements Shape {
  @override
  void draw() => print('Drawing Square');
}

void main() {
  Shape shape = Shape('circle'); // factory decides actual implementation
  shape.draw(); // Drawing Circle
}
```

---

## toString, ==, and hashCode Overrides

Every Dart object inherits `toString()`, `==`, and `hashCode` from `Object` — these are commonly overridden for meaningful comparisons/printing of custom classes.

```dart
class Point {
  final int x, y;
  Point(this.x, this.y);

  @override
  String toString() => 'Point($x, $y)';

  @override
  bool operator ==(Object other) =>
      other is Point && other.x == x && other.y == y;

  @override
  int get hashCode => Object.hash(x, y);
}

void main() {
  var p1 = Point(1, 2);
  var p2 = Point(1, 2);
  print(p1);          // Point(1, 2)
  print(p1 == p2);     // true — custom equality
  print(p1.hashCode == p2.hashCode); // true
}
```

---

## Object Class (Root of All Classes)

In Dart, **every class implicitly extends `Object`** (except `Object` itself), making it the root of the class hierarchy. This means every object automatically has access to `toString()`, `hashCode`, `==`, `runtimeType`, and `noSuchMethod()`.

```dart
class Anything {}

void main() {
  var obj = Anything();
  print(obj.runtimeType); // Anything
  print(obj.toString());  // Instance of 'Anything'
  print(obj.hashCode);    // some integer hash
}
```

---

*This README serves as a quick reference guide for core OOP concepts in Dart, useful for interview prep or onboarding new developers.*
