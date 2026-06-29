# Flutter Database & Storage Guide

A reference README covering local & remote data storage options in Flutter — SharedPreferences, Hive, SQLite (sqflite), ObjectBox, and Firebase — with explanations, comparisons, and code snippets.

---

## Table of Contents
1. [Overview of Storage Options](#overview-of-storage-options)
2. [SharedPreferences](#sharedpreferences)
3. [Hive](#hive)
4. [Hive Type Adapters](#hive-type-adapters)
5. [sqflite (SQLite)](#sqflite-sqlite)
6. [ObjectBox](#objectbox)
7. [Firebase Firestore](#firebase-firestore)
8. [Firebase Realtime Database](#firebase-realtime-database)
9. [Comparison Table](#comparison-table)
10. [Which One Should You Use?](#which-one-should-you-use)
11. [CRUD Cheat Sheet (Side by Side)](#crud-cheat-sheet-side-by-side)
12. [Data Migration & Versioning](#data-migration--versioning)
13. [Encryption & Security](#encryption--security)

---

## Overview of Storage Options

Flutter apps generally need to persist data **locally** (on-device) or **remotely** (cloud). Common choices:

| Type | Examples |
|---|---|
| Key-value storage | `shared_preferences`, `Hive` (also supports objects) |
| NoSQL local object DB | `Hive`, `ObjectBox`, `Isar` |
| Relational local DB | `sqflite` (SQLite) |
| Cloud NoSQL DB | `Firebase Firestore`, `Realtime Database` |
| Cloud relational DB | `Supabase`, custom REST/GraphQL API + Postgres/MySQL |

---

## SharedPreferences

A simple **key-value store** for small amounts of primitive data (`String`, `int`, `double`, `bool`, `List<String>`). Backed by `NSUserDefaults` on iOS and `SharedPreferences` (XML file) on Android. **Not encrypted** and **not suited for large/structured data**.

### Setup
```yaml
dependencies:
  shared_preferences: ^2.2.2
```

### Usage
```dart
import 'package:shared_preferences/shared_preferences.dart';

class PrefsService {
  // CREATE / UPDATE
  Future<void> saveUser(String name, int age) async {
    final prefs = await SharedPreferences.getInstance();
    await prefs.setString('name', name);
    await prefs.setInt('age', age);
  }

  // READ
  Future<Map<String, dynamic>> getUser() async {
    final prefs = await SharedPreferences.getInstance();
    return {
      'name': prefs.getString('name') ?? 'Guest',
      'age': prefs.getInt('age') ?? 0,
    };
  }

  // DELETE one key
  Future<void> clearName() async {
    final prefs = await SharedPreferences.getInstance();
    await prefs.remove('name');
  }

  // DELETE all
  Future<void> clearAll() async {
    final prefs = await SharedPreferences.getInstance();
    await prefs.clear();
  }

  // CHECK existence
  Future<bool> hasUser() async {
    final prefs = await SharedPreferences.getInstance();
    return prefs.containsKey('name');
  }
}
```

**Best for:** onboarding flags, theme mode, login tokens (non-sensitive), feature toggles, simple app settings.

**Avoid for:** large lists, complex objects, sensitive data (tokens/passwords — use `flutter_secure_storage` instead), data that needs querying.

---

## Hive

A lightweight, **fast NoSQL key-value database** written in pure Dart — no native dependencies, works on mobile/web/desktop, and can store **custom Dart objects** (not just primitives) using generated type adapters.

### Setup
```yaml
dependencies:
  hive: ^2.2.3
  hive_flutter: ^1.1.0

dev_dependencies:
  hive_generator: ^2.0.1
  build_runner: ^2.4.9
```

### Initialization
```dart
import 'package:hive_flutter/hive_flutter.dart';

void main() async {
  await Hive.initFlutter();
  await Hive.openBox('myBox'); // a "box" = a table/collection
  runApp(const MyApp());
}
```

### Basic CRUD (primitive values)
```dart
var box = Hive.box('myBox');

// CREATE / UPDATE
box.put('username', 'Tom');
box.put('age', 25);

// READ
String name = box.get('username', defaultValue: 'Guest');

// DELETE
box.delete('username');

// CLEAR all entries in the box
box.clear();

// Listen to changes (reactive)
box.watch(key: 'username').listen((event) {
  print('Changed to: ${event.value}');
});
```

### Storing Custom Objects
```dart
// 1. Define the model with Hive annotations
import 'package:hive/hive.dart';
part 'person.g.dart'; // generated file

@HiveType(typeId: 0)
class Person extends HiveObject {
  @HiveField(0)
  String name;

  @HiveField(1)
  int age;

  Person(this.name, this.age);
}
```

```bash
# Generate the adapter
flutter pub run build_runner build
```

```dart
// 2. Register adapter & open a typed box
Hive.registerAdapter(PersonAdapter());
var personBox = await Hive.openBox<Person>('personBox');

// CREATE
await personBox.add(Person('Tom', 25));        // auto key (int index)
await personBox.put('p1', Person('Sam', 30));  // custom key

// READ
Person? p = personBox.get('p1');
List<Person> all = personBox.values.toList();

// UPDATE
p?.age = 31;
await p?.save(); // since Person extends HiveObject

// DELETE
await personBox.delete('p1');
await p?.delete();
```

**Best for:** offline-first apps, caching API responses, storing structured objects fast, apps needing reactive local storage without native SQL.

---

## Hive Type Adapters

A **TypeAdapter** is what Hive uses to serialize/deserialize custom Dart objects to/from binary. It's auto-generated via `hive_generator` + `build_runner`, but can also be written manually.

```dart
class PersonAdapter extends TypeAdapter<Person> {
  @override
  final int typeId = 0;

  @override
  Person read(BinaryReader reader) {
    return Person(reader.readString(), reader.readInt());
  }

  @override
  void write(BinaryWriter writer, Person obj) {
    writer.writeString(obj.name);
    writer.writeInt(obj.age);
  }
}
```

Each custom type needs a **unique `typeId`** (0–223) registered once via `Hive.registerAdapter()` before opening any box that uses it.

---

## sqflite (SQLite)

A plugin providing access to **SQLite**, a full relational database, supporting raw SQL queries, joins, transactions, and complex relationships between tables.

### Setup
```yaml
dependencies:
  sqflite: ^2.3.3
  path: ^1.9.0
```

### Database Setup & CRUD
```dart
import 'package:sqflite/sqflite.dart';
import 'package:path/path.dart';

class DBHelper {
  static Database? _db;

  Future<Database> get database async {
    if (_db != null) return _db!;
    _db = await _initDB();
    return _db!;
  }

  Future<Database> _initDB() async {
    String path = join(await getDatabasesPath(), 'app.db');
    return openDatabase(
      path,
      version: 1,
      onCreate: (db, version) async {
        await db.execute('''
          CREATE TABLE users(
            id INTEGER PRIMARY KEY AUTOINCREMENT,
            name TEXT,
            age INTEGER
          )
        ''');
      },
    );
  }

  // CREATE
  Future<int> insertUser(String name, int age) async {
    final db = await database;
    return db.insert('users', {'name': name, 'age': age});
  }

  // READ
  Future<List<Map<String, dynamic>>> getUsers() async {
    final db = await database;
    return db.query('users');
  }

  // UPDATE
  Future<int> updateUser(int id, String name) async {
    final db = await database;
    return db.update('users', {'name': name}, where: 'id = ?', whereArgs: [id]);
  }

  // DELETE
  Future<int> deleteUser(int id) async {
    final db = await database;
    return db.delete('users', where: 'id = ?', whereArgs: [id]);
  }
}
```

**Best for:** apps needing complex relational queries (joins, filtering, sorting), large structured datasets, existing SQL schema migration.

---

## ObjectBox

A high-performance NoSQL object database for Flutter, often **faster than Hive and sqflite**, with built-in support for relations, queries, and reactive streams. Uses code generation similar to Hive.

```yaml
dependencies:
  objectbox: ^4.0.1
  objectbox_flutter_libs: any

dev_dependencies:
  build_runner: ^2.4.9
  objectbox_generator: ^4.0.1
```

```dart
@Entity()
class Person {
  @Id()
  int id = 0;
  String name;
  int age;

  Person({this.id = 0, required this.name, required this.age});
}

// CRUD
final box = store.box<Person>();
box.put(Person(name: 'Tom', age: 25));        // CREATE
final all = box.getAll();                      // READ
box.put(Person(id: 1, name: 'Tom', age: 26));  // UPDATE (same id)
box.remove(1);                                 // DELETE
```

**Best for:** apps needing top performance with relations and complex queries, without writing raw SQL.

---

## Firebase Firestore

A **cloud-hosted NoSQL document database** from Firebase — data is organized into **collections** of **documents**, supports real-time sync across devices, and offline caching.

```yaml
dependencies:
  cloud_firestore: ^5.4.0
```

```dart
import 'package:cloud_firestore/cloud_firestore.dart';

final usersRef = FirebaseFirestore.instance.collection('users');

// CREATE
await usersRef.add({'name': 'Tom', 'age': 25});

// READ (one-time)
final snapshot = await usersRef.get();
for (var doc in snapshot.docs) {
  print(doc.data());
}

// READ (real-time stream)
usersRef.snapshots().listen((snapshot) {
  for (var doc in snapshot.docs) {
    print(doc['name']);
  }
});

// UPDATE
await usersRef.doc('userId123').update({'age': 26});

// DELETE
await usersRef.doc('userId123').delete();
```

**Best for:** apps needing real-time sync across multiple devices/users, cloud backup, scalable backend without writing your own server.

---

## Firebase Realtime Database

An older Firebase cloud database storing data as **one large JSON tree**, with real-time syncing. Generally replaced by Firestore for new projects but still used for simple, latency-sensitive real-time apps (e.g., chat, presence systems).

```dart
import 'package:firebase_database/firebase_database.dart';

final dbRef = FirebaseDatabase.instance.ref('users');

// CREATE / UPDATE
await dbRef.child('user1').set({'name': 'Tom', 'age': 25});

// READ (one-time)
final snapshot = await dbRef.child('user1').get();
print(snapshot.value);

// READ (real-time)
dbRef.onValue.listen((event) {
  print(event.snapshot.value);
});

// DELETE
await dbRef.child('user1').remove();
```

---

## Comparison Table

| Feature | SharedPreferences | Hive | sqflite | ObjectBox | Firestore |
|---|---|---|---|---|---|
| Type | Key-value | NoSQL object DB | Relational (SQL) | NoSQL object DB | Cloud NoSQL |
| Speed | Fast (small data) | Very fast | Moderate | Fastest (local) | Network-dependent |
| Structured/complex data | ❌ | ✅ | ✅ | ✅ | ✅ |
| Relations/Joins | ❌ | Manual | ✅ (SQL joins) | ✅ (built-in) | Manual/denormalized |
| Offline support | ✅ | ✅ | ✅ | ✅ | ✅ (cached) |
| Real-time sync | ❌ | ❌ (local only) | ❌ | ❌ (local only) | ✅ |
| Works without internet | ✅ | ✅ | ✅ | ✅ | ✅ (with cache) |
| Best use case | App settings/flags | Caching, offline-first | Complex local queries | High-perf local DB | Multi-device sync |

---

## Which One Should You Use?

- **Tiny flags/settings (theme, onboarding, login state)** → `SharedPreferences`
- **Offline-first app caching structured objects, fast & simple** → `Hive`
- **Need SQL joins, complex relational queries, migrations from existing SQL schema** → `sqflite`
- **Need the fastest local DB with relations/queries, no SQL** → `ObjectBox`
- **Need real-time sync across devices/users, no custom backend** → `Firestore` / `Realtime Database`
- **Sensitive data (tokens, passwords)** → `flutter_secure_storage` (encrypted key-value, uses Keychain/Keystore)

---

## CRUD Cheat Sheet (Side by Side)

```dart
// ---------- SharedPreferences ----------
final prefs = await SharedPreferences.getInstance();
await prefs.setString('key', 'value'); // Create/Update
prefs.getString('key');                // Read
await prefs.remove('key');             // Delete

// ---------- Hive ----------
var box = await Hive.openBox('box');
box.put('key', 'value'); // Create/Update
box.get('key');          // Read
box.delete('key');       // Delete

// ---------- sqflite ----------
await db.insert('table', {'col': 'value'});             // Create
await db.query('table');                                  // Read
await db.update('table', {'col': 'new'}, where: 'id=?', whereArgs: [1]); // Update
await db.delete('table', where: 'id=?', whereArgs: [1]); // Delete

// ---------- Firestore ----------
await collection.add({'field': 'value'});      // Create
await collection.get();                         // Read
await collection.doc(id).update({'field': 'x'}); // Update
await collection.doc(id).delete();              // Delete
```

---

## Data Migration & Versioning

- **sqflite**: Use the `version` parameter in `openDatabase()` and the `onUpgrade` callback to run `ALTER TABLE` migrations when the schema changes.
- **Hive**: Since it's schema-less (per object), add new `@HiveField` indexes for new fields rather than reusing/removing old index numbers (this breaks backward compatibility).
- **Firestore**: Schema-less by nature — handle missing/extra fields defensively in your model's `fromJson()`.

```dart
// sqflite migration example
onUpgrade: (db, oldVersion, newVersion) async {
  if (oldVersion < 2) {
    await db.execute('ALTER TABLE users ADD COLUMN email TEXT');
  }
}
```

---

## Encryption & Security

- `SharedPreferences` and `Hive` boxes are **not encrypted by default** — avoid storing sensitive data (tokens, passwords) in them.
- **Hive supports AES-256 encryption** for boxes:
```dart
final key = Hive.generateSecureKey();
var secureBox = await Hive.openBox(
  'secureBox',
  encryptionCipher: HiveAesCipher(key),
);
```
- For highly sensitive data (auth tokens, biometrics keys), use **`flutter_secure_storage`**, which wraps the Android Keystore / iOS Keychain:
```dart
import 'package:flutter_secure_storage/flutter_secure_storage.dart';

final storage = FlutterSecureStorage();
await storage.write(key: 'token', value: 'abc123');
String? token = await storage.read(key: 'token');
await storage.delete(key: 'token');
```

---

*This README serves as a quick reference guide for local & cloud database/storage options in Flutter, useful for interview prep or onboarding new developers.*
