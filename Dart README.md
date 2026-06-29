# Dart — Complete Concept Guide

A reference README covering core Dart language concepts with short explanations and code snippets.

---

## Table of Contents
1. [What is Dart](#what-is-dart)
2. [Data Types](#data-types)
3. [num](#num)
4. [var vs dynamic](#var-vs-dynamic)
5. [final vs const](#final-vs-const)
6. [Runtime vs Compile Time](#runtime-vs-compile-time)
7. [Ternary Operator](#ternary-operator)
8. [Spread Operator](#spread-operator)
9. [Arrow Function](#arrow-function)
10. [Null-aware Operators](#null-aware-operators)
11. [while vs do-while](#while-vs-do-while)
12. [for-in vs forEach](#for-in-vs-foreach)
13. [Parameter Types](#parameter-types)
14. [async-await](#async-await)
15. [Isolates](#isolates)
16. [Mixins](#mixins)
17. [break and continue](#break-and-continue)
18. [Parse](#parse)
19. [Core Libraries in Dart](#core-libraries-in-dart)
20. [Asynchronous Programming](#asynchronous-programming)
21. [Lambda Functions (Anonymous Functions)](#lambda-functions-anonymous-functions)
22. [Future](#future)
23. [Streams](#streams)
24. [Different Types of Streams](#different-types-of-streams)
25. [Constructors](#constructors)
26. [Different Types of Constructors](#different-types-of-constructors)
27. [Exception Handling](#exception-handling)
28. [async*](#async)
29. [Enumerator (enum)](#enumerator-enum)
30. [is and as Operators](#is-and-as-operators)
31. [late Keyword](#late-keyword)
32. [Mutable and Immutable](#mutable-and-immutable)
33. [Cascade Operator](#cascade-operator)
34. [trim](#trim)
35. [CRUD Operation](#crud-operation)

---

## What is Dart

Dart is an open-source, **object-oriented**, **client-optimized** programming language developed by Google. It's the language behind Flutter.

- **Statically typed** with type inference (`var`).
- Compiles to **native machine code** (AOT) for fast performance, or **JavaScript** for web.
- Supports both **JIT** (Just-In-Time, used in development for hot reload) and **AOT** (Ahead-Of-Time, used in release builds).
- Garbage collected, supports null safety (sound null safety since Dart 2.12).

```dart
void main() {
  print('Hello, Dart!');
}
```

---

## Data Types

Dart's built-in data types:

| Type | Example | Description |
|---|---|---|
| `int` | `int age = 25;` | Whole numbers |
| `double` | `double price = 9.99;` | Decimal numbers |
| `num` | `num value = 10;` | Superclass of int & double |
| `String` | `String name = 'Tom';` | Text |
| `bool` | `bool isActive = true;` | True/false |
| `List` | `List<int> nums = [1,2,3];` | Ordered collection |
| `Set` | `Set<int> ids = {1,2,3};` | Unordered, unique collection |
| `Map` | `Map<String,int> ages = {'a':1};` | Key-value pairs |
| `Runes` | `Runes r = Runes('\u2665');` | Unicode code points |
| `Symbol` | `Symbol s = #mySymbol;` | Reference to identifier |
| `dynamic` | `dynamic x = 5;` | Type checked at runtime |

---

## num

`num` is the abstract superclass for both `int` and `double`. A variable typed `num` can hold either type and switch between them.

```dart
num value = 10;     // int
value = 10.5;        // now double — both allowed since type is num

num a = 5;
num b = 2.5;
print(a + b); // 7.5
```

---

## var vs dynamic

| | `var` | `dynamic` |
|---|---|---|
| Type | Inferred once at declaration, then **fixed** | Can change type at runtime |
| Type-checking | Compile-time (static) | Runtime |
| Safety | Safer | Less safe, more flexible |

```dart
var name = 'Tom';
// name = 5; // ERROR — type is locked to String

dynamic value = 'Tom';
value = 5;       // OK — dynamic allows type change
value = true;    // Still OK
```

---

## final vs const

| | `final` | `const` |
|---|---|---|
| When value is set | Runtime (once) | Compile time |
| Reassignable | No | No |
| Use case | Value known only at runtime (e.g. `DateTime.now()`) | Value known at compile time (e.g. `3.14`) |

```dart
final currentTime = DateTime.now(); // OK, set at runtime
const pi = 3.14;                    // must be a compile-time constant

// const currentTime = DateTime.now(); // ERROR — not a compile-time constant
```

---

## Runtime vs Compile Time

- **Compile time**: Happens when source code is translated/checked **before execution** — type checking, syntax errors, `const` evaluation.
- **Runtime**: Happens **while the program is executing** — variable assignment, network calls, exceptions during execution, `final` evaluation.

```dart
const a = 10;           // compile-time constant
final b = DateTime.now(); // evaluated at runtime
print(1 / 0);           // runtime behavior (Infinity for double division)
```

---

## Ternary Operator

A shorthand conditional expression: `condition ? expr1 : expr2`.

```dart
int age = 20;
String result = age >= 18 ? 'Adult' : 'Minor';
print(result); // Adult
```

---

## Spread Operator

`...` inserts all elements of one collection into another. `...?` does the same but safely handles `null`.

```dart
List<int> list1 = [1, 2, 3];
List<int> list2 = [0, ...list1, 4]; // [0, 1, 2, 3, 4]

List<int>? nullableList;
List<int> combined = [...?nullableList, 5]; // [5] — no error even if null
```

---

## Arrow Function

A shorthand syntax `=>` for functions that contain a **single expression** — implicitly returns that expression.

```dart
int square(int x) => x * x;

// Equivalent to:
int squareLong(int x) {
  return x * x;
}

// Common in widgets:
@override
Widget build(BuildContext context) => Text('Hello');
```

---

## Null-aware Operators

Operators that help work safely with nullable values:

| Operator | Meaning |
|---|---|
| `??` | Returns right value if left is null |
| `??=` | Assigns right value only if variable is currently null |
| `?.` | Safe access — calls method/property only if not null |
| `?[]` | Safe index access |
| `!` | Asserts a value is non-null (throws if null) |

```dart
String? name;
print(name ?? 'Guest');     // Guest
name ??= 'Default';         // assigns if null
print(name?.length);        // safe access
String confirmed = name!;   // asserts non-null
```

---

## while vs do-while

- **while**: Checks condition **before** executing the loop body. May never run.
- **do-while**: Executes the body **once first**, then checks the condition. Always runs at least once.

```dart
int i = 0;
while (i < 3) {
  print('while: $i');
  i++;
}

int j = 0;
do {
  print('do-while: $j');
  j++;
} while (j < 3);
```

---

## for-in vs forEach

- **for-in**: A loop construct that iterates over elements of an `Iterable`; supports `break`/`continue`.
- **forEach**: A higher-order method that takes a callback function; **cannot** use `break`/`continue` inside it.

```dart
List<int> nums = [1, 2, 3];

// for-in
for (var n in nums) {
  if (n == 2) continue;
  print(n);
}

// forEach
nums.forEach((n) => print(n)); // no break/continue allowed
```

---

## Parameter Types

Dart functions support several parameter styles:

```dart
// 1. Required positional
void greet(String name) => print('Hello $name');

// 2. Optional positional (square brackets)
void greetOptional(String name, [String? title]) =>
    print('Hello ${title ?? ''} $name');

// 3. Named parameters (curly braces)
void greetNamed({required String name, String title = 'Mr.'}) =>
    print('Hello $title $name');

// 4. Default values
void greetDefault(String name, [String title = 'Friend']) =>
    print('Hello $title $name');

// Usage:
greet('Tom');
greetOptional('Tom', 'Dr.');
greetNamed(name: 'Tom', title: 'Mrs.');
```

---

## async-await

`async` marks a function as asynchronous (it returns a `Future`); `await` pauses execution until the `Future` completes, **without blocking the main thread**.

```dart
Future<String> fetchData() async {
  await Future.delayed(const Duration(seconds: 2));
  return 'Data loaded';
}

void main() async {
  print('Loading...');
  String data = await fetchData();
  print(data); // Data loaded (after 2 sec)
}
```

---

## Isolates

Dart is **single-threaded** by default — all code runs in one isolate (memory + event loop). **Isolates** let you run code on separate threads with **no shared memory** (they communicate via message passing), avoiding race conditions and enabling true parallelism for heavy computation.

```dart
import 'dart:isolate';

void heavyTask(SendPort sendPort) {
  int sum = 0;
  for (int i = 0; i < 1000000000; i++) sum += i;
  sendPort.send(sum);
}

void main() async {
  final receivePort = ReceivePort();
  await Isolate.spawn(heavyTask, receivePort.sendPort);
  receivePort.listen((result) {
    print('Result: $result');
  });
}
```

In Flutter, `compute()` is a convenient wrapper around isolates for offloading heavy work.

---

## Mixins

A mixin lets a class **reuse code from multiple classes** without using traditional inheritance (Dart only supports single inheritance via `extends`). Declared with `mixin` and applied via `with`.

```dart
mixin Swimmer {
  void swim() => print('Swimming');
}

mixin Flyer {
  void fly() => print('Flying');
}

class Duck with Swimmer, Flyer {}

void main() {
  final duck = Duck();
  duck.swim(); // Swimming
  duck.fly();  // Flying
}
```

---

## break and continue

- **break**: Exits the loop entirely.
- **continue**: Skips the current iteration and moves to the next one.

```dart
for (int i = 0; i < 5; i++) {
  if (i == 3) break;     // stops loop at i=3
  print(i);              // prints 0, 1, 2
}

for (int i = 0; i < 5; i++) {
  if (i == 2) continue;  // skips i=2
  print(i);              // prints 0, 1, 3, 4
}
```

---

## Parse

Converting a `String` to a number (or other type) using `parse`/`tryParse` methods.

```dart
int n = int.parse('42');           // 42 — throws if invalid
double d = double.parse('3.14');   // 3.14

int? safe = int.tryParse('abc');   // null instead of throwing
print(safe); // null
```

---

## Core Libraries in Dart

Built-in libraries that come with the Dart SDK (imported via `dart:`):

| Library | Purpose |
|---|---|
| `dart:core` | Built-in types, collections (`String`, `List`, `Map`) — auto-imported |
| `dart:async` | `Future`, `Stream`, async programming |
| `dart:math` | Math functions/constants (`sqrt`, `pi`, `Random`) |
| `dart:convert` | JSON/UTF-8 encoding & decoding |
| `dart:io` | File, socket, HTTP I/O (not available on web) |
| `dart:isolate` | Concurrent programming with isolates |
| `dart:ui` (Flutter) | Low-level UI primitives |

```dart
import 'dart:math';
import 'dart:convert';

print(sqrt(16));               // 4.0
print(jsonEncode({'a': 1}));   // {"a":1}
```

---

## Asynchronous Programming

A programming model that allows operations (file I/O, network calls, timers) to run **without blocking** the main thread. Dart handles this through an **event loop**, `Future`, `Stream`, and `async`/`await`.

```dart
void main() {
  print('1. Start');
  Future.delayed(const Duration(seconds: 1), () => print('3. Async done'));
  print('2. End (runs before async completes)');
}
// Output: 1. Start -> 2. End -> 3. Async done
```

---

## Lambda Functions (Anonymous Functions)

Functions **without a name**, often passed as arguments to other functions (also called anonymous functions/closures).

```dart
// Anonymous function assigned to a variable
var multiply = (int a, int b) {
  return a * b;
};
print(multiply(2, 3)); // 6

// Passed directly as a callback (lambda style)
List<int> nums = [1, 2, 3];
nums.forEach((n) => print(n * 2));

var squared = nums.map((n) => n * n).toList(); // [1, 4, 9]
```

---

## Future

A `Future` represents a value (or error) that will be available **at some point in the future** — the result of an asynchronous operation.

```dart
Future<String> fetchUser() {
  return Future.delayed(const Duration(seconds: 2), () => 'Tom');
}

void main() {
  fetchUser().then((name) {
    print('User: $name');
  }).catchError((e) {
    print('Error: $e');
  });
}
```

States of a Future: **uncompleted** → **completed with value** or **completed with error**.

---

## Streams

A `Stream` is a sequence of **asynchronous events/data over time** — think of it as a Future that can emit multiple values instead of just one.

```dart
Stream<int> countStream() async* {
  for (int i = 1; i <= 5; i++) {
    await Future.delayed(const Duration(seconds: 1));
    yield i;
  }
}

void main() async {
  await for (var value in countStream()) {
    print(value); // prints 1,2,3,4,5 one per second
  }
}
```

---

## Different Types of Streams

1. **Single-subscription Stream** (default): Can be listened to **only once**; events are typically produced sequentially (e.g., reading a file).
2. **Broadcast Stream**: Allows **multiple listeners**; doesn't buffer events for listeners that join late.

```dart
// Single-subscription
Stream<int> singleStream = Stream.fromIterable([1, 2, 3]);
singleStream.listen((data) => print('Listener A: $data'));
// singleStream.listen(...); // ERROR if called again

// Broadcast
StreamController<int> controller = StreamController<int>.broadcast();
controller.stream.listen((data) => print('Listener 1: $data'));
controller.stream.listen((data) => print('Listener 2: $data'));
controller.add(10); // both listeners receive it
```

---

## Constructors

A constructor is a special method used to **create and initialize an object** of a class. It has the same name as the class.

```dart
class Person {
  String name;
  int age;

  Person(this.name, this.age); // constructor
}

void main() {
  var p = Person('Tom', 25);
  print('${p.name}, ${p.age}');
}
```

---

## Different Types of Constructors

```dart
class Point {
  double x, y;

  // 1. Default/standard constructor
  Point(this.x, this.y);

  // 2. Named constructor
  Point.origin() : x = 0, y = 0;

  // 3. Constant constructor (for immutable objects)
  const Point.constant(this.x, this.y);

  // 4. Factory constructor (can return existing/cached instance or subclass)
  factory Point.fromMap(Map<String, double> map) {
    return Point(map['x']!, map['y']!);
  }

  // 5. Redirecting constructor
  Point.redirect(double x) : this(x, 0);
}

class Shape {
  // 6. Private constructor (restricts external instantiation)
  Shape._internal();
  static final Shape instance = Shape._internal(); // Singleton pattern
}
```

---

## Exception Handling

Dart uses `try`, `catch`, `on`, `finally`, and `throw` for handling runtime errors gracefully.

```dart
void divide(int a, int b) {
  try {
    if (b == 0) throw Exception('Cannot divide by zero');
    print(a / b);
  } on Exception catch (e) {
    print('Caught: $e');
  } catch (e, stackTrace) {
    print('Unknown error: $e');
  } finally {
    print('Done dividing');
  }
}

void main() => divide(10, 0);
```

Custom exceptions:
```dart
class InvalidAgeException implements Exception {
  final String message;
  InvalidAgeException(this.message);
}

throw InvalidAgeException('Age cannot be negative');
```

---

## async*

A generator function marker that creates a `Stream` lazily, using `yield` to emit values one at a time (as opposed to `async` which returns a single `Future`).

```dart
Stream<int> numberStream() async* {
  for (int i = 1; i <= 3; i++) {
    await Future.delayed(const Duration(milliseconds: 500));
    yield i;          // emits a value into the stream
  }
}

void main() async {
  numberStream().listen((value) => print(value)); // 1, 2, 3
}
```

---

## Enumerator (enum)

A special class used to represent a **fixed set of constant values**.

```dart
enum Status { pending, approved, rejected }

void checkStatus(Status status) {
  switch (status) {
    case Status.pending:
      print('Pending');
      break;
    case Status.approved:
      print('Approved');
      break;
    case Status.rejected:
      print('Rejected');
      break;
  }
}

// Enhanced enums (Dart 2.17+) — can have fields, methods, constructors
enum Planet {
  earth(5.97),
  mars(0.64);

  final double mass;
  const Planet(this.mass);
}

void main() {
  checkStatus(Status.approved);
  print(Planet.earth.mass); // 5.97
  print(Status.values);     // [Status.pending, Status.approved, Status.rejected]
}
```

---

## is and as Operators

- **is**: Checks the **runtime type** of an object (type test) — returns `bool`. `is!` is the negation.
- **as**: Performs an explicit **type cast**; throws if the cast is invalid.

```dart
Object value = 'Hello';

if (value is String) {
  print(value.length); // type-promoted to String automatically inside this block
}

if (value is! int) {
  print('Not an int');
}

String str = value as String; // explicit cast
```

---

## late Keyword

Declares a **non-nullable variable** that will be initialized **later**, but before it's used. Useful when you can't initialize at declaration time but guarantee it'll be set before access.

```dart
class Profile {
  late String bio; // initialized later, but is non-nullable

  void loadBio() {
    bio = 'Loaded from server';
  }
}

// Lazy initialization — computed only on first access
late String expensiveValue = computeExpensiveValue();

String computeExpensiveValue() {
  print('Computing...');
  return 'Result';
}
```

⚠️ Accessing a `late` variable before assignment throws a `LateInitializationError`.

---

## Mutable and Immutable

- **Mutable**: Object's state/value **can be changed** after creation.
- **Immutable**: Object's state **cannot be changed** once created (e.g., `const` objects, `final` collections marked unmodifiable).

```dart
// Mutable
List<int> mutableList = [1, 2, 3];
mutableList.add(4); // OK

// Immutable
final List<int> immutableList = List.unmodifiable([1, 2, 3]);
// immutableList.add(4); // ERROR — Unsupported operation

const point = Point(1, 2); // const object — fully immutable
```

---

## Cascade Operator

`..` allows calling **multiple methods/setting multiple properties on the same object** in a single chained expression, without repeating the object reference.

```dart
class Paint {
  Color? color;
  double? strokeWidth;
  bool? isAntiAlias;
}

var paint = Paint()
  ..color = Colors.red
  ..strokeWidth = 5.0
  ..isAntiAlias = true;

// Equivalent to:
// var paint = Paint();
// paint.color = Colors.red;
// paint.strokeWidth = 5.0;
// paint.isAntiAlias = true;
```

---

## trim

A `String` method that removes leading and trailing **whitespace**.

```dart
String text = '   Hello World   ';
print(text.trim());       // 'Hello World'
print(text.trimLeft());   // 'Hello World   '
print(text.trimRight());  // '   Hello World'
```

---

## CRUD Operation

CRUD = **Create, Read, Update, Delete** — the four basic operations for managing data, commonly implemented with a `List`/`Map`, local DB (sqflite, Hive), or remote API.

```dart
class Task {
  int id;
  String title;
  Task(this.id, this.title);
}

List<Task> tasks = [];

// CREATE
void addTask(Task task) {
  tasks.add(task);
}

// READ
Task? getTask(int id) {
  return tasks.firstWhere((t) => t.id == id, orElse: () => Task(-1, 'Not found'));
}

// UPDATE
void updateTask(int id, String newTitle) {
  final index = tasks.indexWhere((t) => t.id == id);
  if (index != -1) tasks[index].title = newTitle;
}

// DELETE
void deleteTask(int id) {
  tasks.removeWhere((t) => t.id == id);
}

void main() {
  addTask(Task(1, 'Buy groceries'));
  updateTask(1, 'Buy groceries and cook');
  print(getTask(1)?.title);
  deleteTask(1);
}
```

---

*This README serves as a quick reference guide for core Dart language concepts, useful for interview prep or onboarding new developers.*
