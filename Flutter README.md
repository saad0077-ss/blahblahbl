# Flutter — Complete Concept Guide

A reference README covering core Flutter concepts, widgets, and tooling with short explanations and code snippets.

---

## Table of Contents
1. [What is Flutter](#what-is-flutter)
2. [Advantages and Disadvantages](#advantages-and-disadvantages-of-flutter)
3. [Packages and Plugins](#packages-and-plugins)
4. [Flutter Inspector](#flutter-inspector)
5. [Flutter Doctor](#flutter-doctor)
6. [Meta Data (Annotations)](#meta-data)
7. [Run App and main()](#run-app-and-main)
8. [Scaffold](#scaffold)
9. [MaterialApp](#materialapp)
10. [State](#state)
11. [App State](#app-state)
12. [Ephemeral State](#ephemeral-state)
13. [Stateful and Stateless Widgets](#stateful-and-stateless-widgets)
14. [Container and SizedBox](#container-and-sizedbox)
15. [Horizontal List](#horizontal-list)
16. [Build Modes](#build-modes)
17. [BuildContext](#buildcontext)
18. [Choice Chips](#choice-chips)
19. [AspectRatio](#aspectratio)
20. [PreferredSize](#preferredsize)
21. [MainAxisAlignment & CrossAxisAlignment](#mainaxisalignment-and-crossaxisalignment)
22. [Modals](#modals)
23. [Stateful Widget Lifecycle](#stateful-widget-lifecycle)
24. [ConstrainedBox](#constrainedbox)
25. [TextField and TextFormField](#textfield-and-textformfield)
26. [Tree Shaking](#tree-shaking)
27. [FutureBuilder](#futurebuilder)
28. [ColoredBox](#coloredbox)
29. [Expandable Widgets](#expandable-widgets)
30. [Gestures](#gestures)
31. [Stack](#stack)
32. [initState and setState](#initstate-and-setstate)
33. [SharedPreferences](#sharedpreferences)
34. [Navigator Functions](#navigator-functions)

---

## What is Flutter

Flutter is an open-source UI toolkit created by Google for building natively compiled applications for **mobile (iOS/Android)**, **web**, **desktop (Windows/macOS/Linux)**, and **embedded devices** from a single codebase.

- Written in **Dart** (also Google's language).
- Uses its own rendering engine (Skia / Impeller) — it doesn't rely on native OS widgets, so the UI looks identical across platforms.
- Follows a **declarative, widget-based** UI model: "Everything is a widget."

```dart
void main() {
  runApp(const MyApp());
}
```

---

## Advantages and Disadvantages of Flutter

### Advantages
- **Single codebase** for multiple platforms (mobile, web, desktop).
- **Hot reload** — see code changes instantly without restarting the app.
- **Custom rendering engine** — consistent UI/UX, pixel-perfect on every device.
- Rich set of **built-in Material & Cupertino widgets**.
- Good **performance** — compiles to native ARM/x64 machine code (AOT).
- Strong **community & package ecosystem** (pub.dev).
- **Open source** and backed by Google.

### Disadvantages
- **Larger app size** compared to native apps (engine is bundled).
- **Limited third-party libraries** compared to native Android/iOS or React Native.
- Not ideal for apps that need very platform-specific native UI behavior.
- **Dart** is a smaller, less mainstream language — smaller hiring pool.
- Some advanced native features still need **platform channels** to bridge native code.

---

## Packages and Plugins

- **Package**: A reusable piece of Dart code (e.g., utility functions, UI widgets) with no platform-specific code. Example: `provider`, `http`, `intl`.
- **Plugin**: A special type of package that wraps **platform-specific native code** (Android/Kotlin/Java, iOS/Swift/Objective-C) and exposes it via Dart using **platform channels**. Example: `camera`, `shared_preferences`, `url_launcher`.

```yaml
# pubspec.yaml
dependencies:
  http: ^1.2.0
  shared_preferences: ^2.2.2
```

```bash
flutter pub get
flutter pub add http
```

---

## Flutter Inspector

A visual debugging tool built into Android Studio / VS Code (and DevTools) that lets you:

- Inspect the **widget tree** visually.
- Select widgets on screen and see their properties.
- Detect **layout overflow/issues** (red-yellow stripes).
- Toggle "Show Guidelines", "Highlight Repaints", "Slow Animations", etc.

Access it via: `Flutter DevTools` → **Inspector tab**, or directly in IDE sidebars.

---

## Flutter Doctor

A CLI command that checks your development environment and reports missing dependencies or configuration issues.

```bash
flutter doctor
flutter doctor -v   # verbose output
```

It checks:
- Flutter SDK version
- Android toolchain (SDK, licenses)
- Xcode (for iOS/macOS development)
- Connected devices
- IDE plugins (Android Studio / VS Code)

---

## Meta Data

In Dart, "metadata" refers to **annotations** — prefixed with `@` — that attach extra information to code without changing its behavior.

```dart
class Person {
  @deprecated
  void oldMethod() {}

  @override
  String toString() => 'Person';
}

@immutable
class Point {
  final int x, y;
  const Point(this.x, this.y);
}
```

Common built-in annotations: `@override`, `@deprecated`, `@required` (via `required` keyword now), `@immutable`, `@visibleForTesting`.

In native Android/iOS context, "metadata" can also refer to entries in `AndroidManifest.xml` (`<meta-data>`) or `Info.plist`, used to pass configuration (e.g., API keys) to plugins.

---

## Run App and main()

Every Flutter app starts execution from the `main()` function, which calls `runApp()` to attach the root widget to the screen.

```dart
import 'package:flutter/material.dart';

void main() {
  runApp(const MyApp());
}

class MyApp extends StatelessWidget {
  const MyApp({super.key});

  @override
  Widget build(BuildContext context) {
    return const MaterialApp(
      home: Scaffold(
        body: Center(child: Text('Hello Flutter')),
      ),
    );
  }
}
```

`runApp()` inflates the given widget and attaches it to the screen, making it the root of the widget tree.

---

## Scaffold

`Scaffold` implements the basic visual **Material Design layout structure** — app bar, body, drawer, bottom navigation, floating action button, snack bars, etc.

```dart
Scaffold(
  appBar: AppBar(title: const Text('My App')),
  body: const Center(child: Text('Content')),
  floatingActionButton: FloatingActionButton(
    onPressed: () {},
    child: const Icon(Icons.add),
  ),
  drawer: Drawer(child: ListView()),
  bottomNavigationBar: BottomNavigationBar(items: const []),
)
```

---

## MaterialApp

The top-level widget that configures the whole app — theming, routes, localization, navigation, and Material Design behavior.

```dart
MaterialApp(
  title: 'My App',
  theme: ThemeData(primarySwatch: Colors.blue),
  darkTheme: ThemeData.dark(),
  initialRoute: '/',
  routes: {
    '/': (context) => const HomeScreen(),
    '/about': (context) => const AboutScreen(),
  },
)
```

---

## State

**State** is information that can change during the lifetime of a widget and affects how it's displayed. When state changes, the widget rebuilds to reflect the new data (via `setState()` or state-management solutions like Provider/Bloc/Riverpod).

---

## App State

**App state** (a.k.a. global/shared state) is state shared across many parts of the app, and often persists across user sessions — e.g., logged-in user info, theme mode, cart items.

Typically managed using:
- `Provider`
- `Riverpod`
- `Bloc` / `Cubit`
- `GetX`
- `setState` at a high-level ancestor widget (InheritedWidget)

---

## Ephemeral State

**Ephemeral state** (a.k.a. local/UI state) is state that belongs to a single widget and doesn't need to be shared. It's lost when the widget is disposed (e.g., navigating away).

```dart
class CounterWidget extends StatefulWidget {
  const CounterWidget({super.key});
  @override
  State<CounterWidget> createState() => _CounterWidgetState();
}

class _CounterWidgetState extends State<CounterWidget> {
  int _count = 0; // ephemeral state

  void _increment() => setState(() => _count++);

  @override
  Widget build(BuildContext context) {
    return ElevatedButton(onPressed: _increment, child: Text('$_count'));
  }
}
```
Examples: current page index of a `PageView`, animation progress, text field value, checkbox checked state.

---

## Stateful and Stateless Widgets

| | StatelessWidget | StatefulWidget |
|---|---|---|
| Mutable internal state | No | Yes |
| Rebuild trigger | Parent rebuild only | `setState()` call |
| Use case | Static UI (text, icon) | Dynamic UI (forms, counters) |

```dart
// Stateless
class Greeting extends StatelessWidget {
  final String name;
  const Greeting(this.name, {super.key});
  @override
  Widget build(BuildContext context) => Text('Hello $name');
}

// Stateful
class Toggle extends StatefulWidget {
  const Toggle({super.key});
  @override
  State<Toggle> createState() => _ToggleState();
}

class _ToggleState extends State<Toggle> {
  bool isOn = false;
  @override
  Widget build(BuildContext context) {
    return Switch(value: isOn, onChanged: (v) => setState(() => isOn = v));
  }
}
```

---

## Container and SizedBox

- **Container**: A multipurpose box that can apply padding, margin, color, decoration (border/shadow/gradient), and constraints (width/height) all at once.
- **SizedBox**: A lightweight widget that simply gives a child a **fixed width/height**, or creates empty spacing. It's cheaper than `Container` when you don't need decoration.

```dart
Container(
  width: 100,
  height: 100,
  padding: const EdgeInsets.all(8),
  decoration: BoxDecoration(
    color: Colors.blue,
    borderRadius: BorderRadius.circular(12),
  ),
  child: const Text('Box'),
)

SizedBox(width: 20, height: 50, child: const Text('Sized'))
SizedBox(height: 16) // commonly used as spacing
```

---

## Horizontal List

A scrollable list arranged horizontally, typically built with `ListView` and `scrollDirection: Axis.horizontal`.

```dart
SizedBox(
  height: 100,
  child: ListView.builder(
    scrollDirection: Axis.horizontal,
    itemCount: 10,
    itemBuilder: (context, index) => Container(
      width: 80,
      margin: const EdgeInsets.all(8),
      color: Colors.amber,
      child: Center(child: Text('$index')),
    ),
  ),
)
```

---

## Build Modes

Flutter apps can be compiled in three modes:

| Mode | Purpose | Debugging | Performance | Command |
|---|---|---|---|---|
| **Debug** | Development, hot reload, assertions enabled | Yes | Slower | `flutter run` |
| **Profile** | Performance profiling, near-release speed but with some tracing | Limited | Near-release | `flutter run --profile` |
| **Release** | Production builds, fully optimized, no debug overhead | No | Fastest | `flutter run --release` / `flutter build apk` |

---

## BuildContext

`BuildContext` is a handle to the **location of a widget in the widget tree**. It's passed into every `build()` method and is used to:

- Look up ancestor widgets (`Theme.of(context)`, `MediaQuery.of(context)`)
- Navigate (`Navigator.of(context)`)
- Access `InheritedWidget` data (Provider, etc.)

```dart
@override
Widget build(BuildContext context) {
  final width = MediaQuery.of(context).size.width;
  final theme = Theme.of(context);
  return Text('Width: $width', style: theme.textTheme.bodyLarge);
}
```

⚠️ Avoid using a `BuildContext` after the widget has been disposed (common async-gap bug) — always check `context.mounted` first.

---

## Choice Chips

`ChoiceChip` represents a single selectable option from a set of options — used for filter/category selection where only one chip is typically selected.

```dart
class ChipDemo extends StatefulWidget {
  const ChipDemo({super.key});
  @override
  State<ChipDemo> createState() => _ChipDemoState();
}

class _ChipDemoState extends State<ChipDemo> {
  int selected = 0;
  final options = ['All', 'Sports', 'Music'];

  @override
  Widget build(BuildContext context) {
    return Wrap(
      spacing: 8,
      children: List.generate(options.length, (i) {
        return ChoiceChip(
          label: Text(options[i]),
          selected: selected == i,
          onSelected: (_) => setState(() => selected = i),
        );
      }),
    );
  }
}
```

---

## AspectRatio

Sizes its child to a specific **width/height ratio**, useful for images, videos, and cards that must keep proportions across screen sizes.

```dart
AspectRatio(
  aspectRatio: 16 / 9,
  child: Container(color: Colors.green),
)
```

---

## PreferredSize

A widget used to give a child a fixed/preferred size — commonly used to customize `AppBar` height inside `Scaffold.appBar`, since `AppBar` requires a `PreferredSizeWidget`.

```dart
Scaffold(
  appBar: PreferredSize(
    preferredSize: const Size.fromHeight(100),
    child: AppBar(
      title: const Text('Custom Height AppBar'),
      flexibleSpace: Container(color: Colors.deepPurple),
    ),
  ),
)
```

---

## MainAxisAlignment and CrossAxisAlignment

Used inside `Row` / `Column` to control alignment:

- **MainAxisAlignment**: alignment along the **main axis** (horizontal for Row, vertical for Column) — `start`, `end`, `center`, `spaceBetween`, `spaceAround`, `spaceEvenly`.
- **CrossAxisAlignment**: alignment along the **cross axis** (vertical for Row, horizontal for Column) — `start`, `end`, `center`, `stretch`, `baseline`.

```dart
Row(
  mainAxisAlignment: MainAxisAlignment.spaceBetween,
  crossAxisAlignment: CrossAxisAlignment.center,
  children: const [Icon(Icons.star), Icon(Icons.star), Icon(Icons.star)],
)
```

---

## Modals

Modals are pop-up surfaces shown over the current screen, often used for actions/confirmations/forms.

```dart
// Bottom sheet modal
showModalBottomSheet(
  context: context,
  builder: (context) => Container(
    height: 200,
    child: const Center(child: Text('Modal Bottom Sheet')),
  ),
);

// Dialog modal
showDialog(
  context: context,
  builder: (context) => AlertDialog(
    title: const Text('Confirm'),
    content: const Text('Are you sure?'),
    actions: [
      TextButton(onPressed: () => Navigator.pop(context), child: const Text('Cancel')),
      TextButton(onPressed: () => Navigator.pop(context), child: const Text('OK')),
    ],
  ),
);
```

---

## Stateful Widget Lifecycle

Order of lifecycle method calls for a `State` object:

1. **createState()** — called once when the StatefulWidget is created.
2. **initState()** — called once when the State object is inserted into the tree (good for one-time initialization, subscriptions).
3. **didChangeDependencies()** — called right after `initState()`, and whenever an `InheritedWidget` it depends on changes.
4. **build()** — called every time the UI needs to render.
5. **didUpdateWidget(oldWidget)** — called when the parent rebuilds and passes a new widget configuration.
6. **setState()** — triggers a rebuild (`build()` runs again).
7. **deactivate()** — called when the State is removed from the tree (may be reinserted).
8. **dispose()** — called permanently when the State object is removed; clean up controllers/listeners here.

```dart
class _MyState extends State<MyWidget> {
  @override
  void initState() {
    super.initState();
    // subscribe to streams, init controllers
  }

  @override
  void dispose() {
    // cancel subscriptions, dispose controllers
    super.dispose();
  }

  @override
  Widget build(BuildContext context) => Container();
}
```

---

## ConstrainedBox

Imposes additional **min/max width and height constraints** on its child.

```dart
ConstrainedBox(
  constraints: const BoxConstraints(
    minWidth: 100,
    maxWidth: 200,
    minHeight: 50,
    maxHeight: 100,
  ),
  child: Container(color: Colors.red),
)
```

---

## TextField and TextFormField

- **TextField**: A basic Material text input widget; manages its own value via a `TextEditingController` but has no built-in validation.
- **TextFormField**: A wrapper around `TextField` designed to work inside a `Form` widget — supports `validator`, `onSaved`, and integrates with `Form.validate()`.

```dart
// TextField
TextField(
  controller: TextEditingController(),
  decoration: const InputDecoration(labelText: 'Name'),
)

// TextFormField inside a Form
final formKey = GlobalKey<FormState>();

Form(
  key: formKey,
  child: TextFormField(
    decoration: const InputDecoration(labelText: 'Email'),
    validator: (value) {
      if (value == null || value.isEmpty) return 'Required';
      return null;
    },
  ),
)
// formKey.currentState!.validate();
```

---

## Tree Shaking

A build-time optimization where the Dart compiler **removes unused code** (functions, classes, icons, assets) from the final compiled binary, reducing app size.

- Happens automatically in **release builds**.
- Example: `flutter build apk --tree-shake-icons` removes unused icon glyphs from icon fonts like `Icons`.
- Helps keep release builds smaller than debug builds.

```bash
flutter build apk --release --tree-shake-icons
```

---

## FutureBuilder

A widget that builds itself based on the latest snapshot of interaction with a `Future` — ideal for one-time async operations like an API call.

```dart
FutureBuilder<String>(
  future: fetchData(),
  builder: (context, snapshot) {
    if (snapshot.connectionState == ConnectionState.waiting) {
      return const CircularProgressIndicator();
    } else if (snapshot.hasError) {
      return Text('Error: ${snapshot.error}');
    } else {
      return Text('Data: ${snapshot.data}');
    }
  },
)
```

---

## ColoredBox

A lightweight widget that paints a solid color behind its child — cheaper than `Container` because it skips layout/decoration overhead when you only need a flat background color.

```dart
ColoredBox(
  color: Colors.yellow,
  child: const Padding(
    padding: EdgeInsets.all(16),
    child: Text('Colored background'),
  ),
)
```

---

## Expandable Widgets

Widgets that let children **grow/shrink to fill available space** inside `Row`/`Column`/`Flex`, or expand/collapse content.

```dart
// Expanded — fills remaining space proportionally (flex)
Row(
  children: [
    Expanded(flex: 2, child: Container(color: Colors.red)),
    Expanded(flex: 1, child: Container(color: Colors.blue)),
  ],
)

// ExpansionTile — collapsible list item
ExpansionTile(
  title: const Text('Details'),
  children: const [ListTile(title: Text('More info here'))],
)

// Flexible — similar to Expanded but doesn't force fill
Flexible(child: Container(color: Colors.green))
```

---

## Gestures

Flutter detects touch input via `GestureDetector` (low-level, manual) or `InkWell` (Material ripple feedback).

```dart
GestureDetector(
  onTap: () => print('Tapped'),
  onDoubleTap: () => print('Double tapped'),
  onLongPress: () => print('Long pressed'),
  onPanUpdate: (details) => print('Dragging: ${details.delta}'),
  child: Container(width: 100, height: 100, color: Colors.blue),
)

InkWell(
  onTap: () => print('Tapped with ripple'),
  child: const Padding(padding: EdgeInsets.all(12), child: Text('Tap me')),
)
```

---

## Stack

Places widgets **on top of each other** (z-axis layering), positioned absolutely using `Positioned`.

```dart
Stack(
  alignment: Alignment.center,
  children: [
    Container(width: 200, height: 200, color: Colors.grey),
    Positioned(
      bottom: 10,
      right: 10,
      child: Icon(Icons.favorite, color: Colors.red),
    ),
    const Text('Overlay Text'),
  ],
)
```

---

## initState and setState

- **initState()**: Called once when a `State` object is created — used for one-time setup (controllers, listeners, initial async calls).
- **setState()**: Notifies the framework that internal state has changed, scheduling a rebuild of the widget.

```dart
class _CounterState extends State<Counter> {
  int count = 0;
  late AnimationController controller;

  @override
  void initState() {
    super.initState();
    controller = AnimationController(vsync: this, duration: const Duration(seconds: 1));
  }

  void increment() {
    setState(() {
      count++;
    });
  }

  @override
  void dispose() {
    controller.dispose();
    super.dispose();
  }

  @override
  Widget build(BuildContext context) => Text('$count');
}
```

---

## SharedPreferences

A plugin for storing small amounts of **persistent key-value data** locally on the device (e.g., login flags, theme preference, onboarding status). Backed by `NSUserDefaults` on iOS and `SharedPreferences` on Android.

```dart
import 'package:shared_preferences/shared_preferences.dart';

// Save
Future<void> saveName(String name) async {
  final prefs = await SharedPreferences.getInstance();
  await prefs.setString('username', name);
}

// Read
Future<String?> getName() async {
  final prefs = await SharedPreferences.getInstance();
  return prefs.getString('username');
}

// Remove
Future<void> clearName() async {
  final prefs = await SharedPreferences.getInstance();
  await prefs.remove('username');
}
```

---

## Navigator Functions

`Navigator` manages a stack of `Route`s for screen-to-screen navigation.

```dart
// Push a new screen
Navigator.push(
  context,
  MaterialPageRoute(builder: (context) => const SecondScreen()),
);

// Push and remove all previous routes (e.g., after login)
Navigator.pushAndRemoveUntil(
  context,
  MaterialPageRoute(builder: (context) => const HomeScreen()),
  (route) => false,
);

// Pop back to previous screen
Navigator.pop(context);

// Pop with a return value
Navigator.pop(context, 'result data');

// Push named route (defined in MaterialApp.routes)
Navigator.pushNamed(context, '/about');

// Replace current screen (no back navigation to it)
Navigator.pushReplacement(
  context,
  MaterialPageRoute(builder: (context) => const NewScreen()),
);

// Pop until a specific route
Navigator.popUntil(context, ModalRoute.withName('/home'));

// Check if navigator can go back
final canGoBack = Navigator.canPop(context);
```

---

*This README serves as a quick reference guide for core Flutter/Dart concepts, useful for interview prep or onboarding new developers.*
