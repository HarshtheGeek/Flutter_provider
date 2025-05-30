# Flutter Riverpod Notes

This repository contains structured notes and examples on using **Riverpod** for state management in Flutter, including concepts like `copyWith`, various provider types, and widgets like `ConsumerWidget`.

## Understanding `copyWith`

`copyWith` is a common method used to create modified copies of immutable objects in Dart.

### Why Use `copyWith`?

Dart encourages immutability. Instead of modifying an object directly, you create a new one with the desired changes.

#### Without `copyWith`

```dart
final user1 = User(name: "Harsh", age: 22);
final user2 = User(name: "Harsh", age: 23); // Tedious if many fields
```

#### With `copyWith`

```dart
final user2 = user1.copyWith(age: 23);
```

### Example Implementation

```dart
class User {
  final String name;
  final int age;

  User({required this.name, required this.age});

  User copyWith({String? name, int? age}) {
    return User(
      name: name ?? this.name,
      age: age ?? this.age,
    );
  }
}
```

### Usage

```dart
final user1 = User(name: "Harsh", age: 22);
final user2 = user1.copyWith(age: 23);
```

### Real-Life Analogy

> Copying a form and changing just the age instead of refilling all the data.

---

## Riverpod Basics

### Provider

A `Provider` exposes a read-only value or computation.

```dart
final nameProvider = Provider<String>((ref) {
  return 'Harsh';
});
```

Usage in UI:

```dart
class MyWidget extends ConsumerWidget {
  @override
  Widget build(BuildContext context, WidgetRef ref) {
    final name = ref.watch(nameProvider);
    return Text('Hello, $name!');
  }
}
```

**Use When:**

* You want to expose constants or computed values.
* The value does not change over time.

---

### StateProvider

A `StateProvider` allows you to manage simple, mutable state.

```dart
final counterProvider = StateProvider<int>((ref) => 0);
```

Update state:

```dart
ref.read(counterProvider.notifier).state++;
```

**Use When:**

* You need simple local UI state like counters or toggles.

---

### ProviderScope

`ProviderScope` is the root widget that initializes the provider container.

```dart
void main() {
  runApp(
    ProviderScope(
      child: MyApp(),
    ),
  );
}
```

**Why It’s Important:**

* Manages the lifecycle of providers.
* Enables overriding providers (e.g., in tests).
* Required once at the top level of your app.

---

## Widgets

### ConsumerWidget

A `ConsumerWidget` is a stateless widget that allows access to providers.

```dart
class MyWidget extends ConsumerWidget {
  @override
  Widget build(BuildContext context, WidgetRef ref) {
    final name = ref.watch(nameProvider);
    return Text('Hello, $name!');
  }
}
```

**Benefits:**

* Allows reactive UI that rebuilds when provider data changes.

---

### ConsumerStatefulWidget

Use this when you need access to `initState`, `dispose`, or `setState`.

```dart
class MyStatefulWidget extends ConsumerStatefulWidget {
  @override
  ConsumerState<MyStatefulWidget> createState() => _MyStatefulWidgetState();
}

class _MyStatefulWidgetState extends ConsumerState<MyStatefulWidget> {
  @override
  void initState() {
    super.initState();
    // Initialization logic
  }

  @override
  Widget build(BuildContext context) {
    final count = ref.watch(counterProvider);
    return Text('Count: $count');
  }
}
```

**Use When:**

* You need lifecycle methods or local mutable state.

---

## Working with `ref`

### `ref.watch`

* **Purpose**: Listens to provider changes.
* **Use In**: UI to make widgets reactive.

```dart
final count = ref.watch(counterProvider);
```

---

### `ref.read`

* **Purpose**: Reads the provider once without listening for changes.
* **Use In**: Callbacks, init logic, etc.

```dart
ref.read(counterProvider.notifier).state++;
```

---

## Comparison Tables

### `Provider` vs `StateProvider`

| Feature   | Provider                     | StateProvider                              |
| --------- | ---------------------------- | ------------------------------------------ |
| Purpose   | Read-only values             | Mutable state                              |
| Mutable   | No                           | Yes                                        |
| Access    | `ref.watch(provider)`        | `ref.watch(stateProvider)`                 |
| Update    | Not possible                 | `ref.read(stateProvider.notifier).state++` |
| Use Cases | Configs, constants, services | Counters, toggles, basic UI state          |

---

### `ConsumerWidget` vs `ConsumerStatefulWidget`

| Feature               | ConsumerWidget        | ConsumerStatefulWidget           |
| --------------------- | --------------------- | -------------------------------- |
| Widget Type           | Stateless             | Stateful                         |
| Reactive UI           | Yes                   | Yes                              |
| Uses `setState`       | No                    | Yes                              |
| Has Lifecycle Methods | No                    | Yes (`initState`, `dispose`)     |
| Use Case              | UI with provider only | UI with provider and local state |

---

## Advanced State Management

### StateNotifierProvider

Use `StateNotifierProvider` when managing complex business logic.

**StateNotifier<T>**: A class that holds some state of type T and provides logic to update that state. It's like a controller.

**StateNotifierProvider<NotifierClass, T>**: A provider that exposes a StateNotifier<T> instance to the widget tree, allowing widgets to read and listen to the state of type T.

```dart
class CounterNotifier extends StateNotifier<int> {
  CounterNotifier() : super(0);

  void increment() => state++;
}

final counterNotifierProvider =
    StateNotifierProvider<CounterNotifier, int>((ref) {
  return CounterNotifier();
});
```

Usage:

```dart
final count = ref.watch(counterNotifierProvider);
ref.read(counterNotifierProvider.notifier).increment();
```

**Use When:**

* You need structured and scalable state management.
* Ideal for separating logic from UI.

---
# `FutureProvider` in Riverpod

## What is `FutureProvider`?

`FutureProvider` is a type of provider in Riverpod used to expose asynchronous data (`Future<T>`) to the widget tree. It simplifies managing `loading`, `data`, and `error` states without writing boilerplate code.

## When to Use

Use `FutureProvider` when you:

* Need to fetch data from a remote API
* Perform asynchronous file or database operations
* Want to manage a simple async lifecycle in the UI

---

## Example

### 1. Define the Provider

```dart
// providers/user_provider.dart
import 'package:flutter_riverpod/flutter_riverpod.dart';

final userProvider = FutureProvider<String>((ref) async {
  // Simulate a delay like an API call
  await Future.delayed(Duration(seconds: 2));
  return "Harsh";
});
```

---

### 2. Use the Provider in the UI

```dart
// widgets/user_widget.dart
import 'package:flutter/material.dart';
import 'package:flutter_riverpod/flutter_riverpod.dart';
import '../providers/user_provider.dart';

class UserWidget extends ConsumerWidget {
  @override
  Widget build(BuildContext context, WidgetRef ref) {
    final userAsync = ref.watch(userProvider);

    return userAsync.when(
      data: (name) => Text('Hello, $name'),
      loading: () => CircularProgressIndicator(),
      error: (error, stack) => Text('Error: $error'),
    );
  }
}
```

---

## Notes

* `ref.watch(userProvider)` listens to the future.
* The `when` method helps to handle all possible states cleanly: loading, success (`data`), and error.

---
