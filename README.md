# Flutter Riverpod Notes

This repository contains structured notes and examples on using **Riverpod** for state management in Flutter, including concepts like `copyWith`, various provider types, and widgets like `ConsumerWidget`.

# FLUTTER CLEAN ARCHITECTURE

### 1. **domain/** — *What your app should do*

Think of this as the *idea* or *plan* of your travel app.

* **entities/** — These are the main things you care about, like a **Trip**, **User**, or **Hotel**.
  *Example:* A Trip might be "Visit Paris" with dates and places to see.
* **repositories/** — These are like a *rulebook* telling what actions your app can do, without saying how to do them.
  *Example:* “GetTrips()” means your app can get a list of trips, but it doesn’t say from where yet.
* **usecases/** — These are *tasks* your app does, one at a time.
  *Example:* “GetTrips” fetches trips, “LoginUser” lets someone log in.

---

### 2. **data/** — *How data is handled*

This is where your app actually works with data—like getting trip info from the internet or saving it on your phone.

* **models/** — These are versions of your entities that can easily be saved or sent over the internet (like packing your trip info into a suitcase to send it).
* **datasources/** — These get data from somewhere real, like the internet or your phone’s storage.
  *Example:* Fetching trips from a travel website API.
* **repositories/** — These are the *real workers* that follow the rules from domain/repositories and use datasources to get or save data.

---

### 3. **presentation/** — *What the user sees*

This is the *front desk* of your travel app — what people see and interact with.

* **providers/** — These help manage the data for the UI and update it when things change.
  *Example:* When you add a new trip, the screen updates automatically.
* **widgets/** — Small parts of the screen, like buttons or cards.
  *Example:* A button that says “Add Trip” or a card showing trip details.
* **pages/** — Full screens like the home page, profile page, or trip details page.

---

### **lib/core/** — *Shared stuff across the app*

This is like the common tools and settings everyone uses.

* **network/** — Settings for connecting to the internet (APIs).
* **utils/** — Small helpers or constants used everywhere.
  *Example:* A function to format dates or a color code used throughout the app.

---

### Summary with real-world example:

Imagine you’re building a travel planner:

* **domain/** is the travel plan itself (what trips you want).
* **data/** is how you get trip info from the internet or save it on your phone.
* **presentation/** is the travel brochure and booking desk where users see and interact with trips.
* **core/** is the office tools everyone uses, like phones or computers that help the whole operation.

---

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

# `StreamProvider` in Riverpod (Flutter)

## Overview

`StreamProvider` is part of the state management package for Flutter. It is used to expose a `Stream<T>` to the widget tree and reactively listen to asynchronous data changes over time.

Typical use cases include:

* Realtime updates (e.g., Firebase Firestore `snapshots()`)
* WebSocket communication
* Timers and clocks
* Sensor or hardware feeds

---
# Stream :
**What is a Stream in Flutter?**
- A Stream is like a pipe through which data flows over time. It can send multiple pieces of data, one after the other, as they become available.
- 
Think of it like a water tap:
You turn it on, and water (data) keeps coming drop by drop.

You can listen to this tap and do something every time a new drop (new data) comes in.

In Flutter, Streams are often used to handle things that change over time, such as:

User location updates

Network responses that take time

Real-time messages

You listen to a stream using .listen() or widgets like StreamBuilder.

## What is `StreamProvider`?

`StreamProvider<T>` allows you to bind a stream to the UI and automatically manage its lifecycle.

### Key Benefits

* **Reactive**: Emits new UI states based on stream output
* **State-aware**: Manages loading, data, and error states using `AsyncValue<T>`
* **Lifecycle-aware**: Automatically disposes the stream when not used
* **Testable**: Easy to mock in unit or widget tests

---

## Syntax

```dart
final myStreamProvider = StreamProvider<int>((ref) {
  return Stream.periodic(Duration(seconds: 1), (count) => count);
});
```

This provider emits an incrementing integer every second.

---

## Understanding `AsyncValue<T>`

When you consume a `StreamProvider`, you get an `AsyncValue<T>` which has three states:

* `AsyncLoading` – while waiting for the stream to emit the first value
* `AsyncData<T>` – when a new value is received
* `AsyncError` – if an error occurs

You typically handle these with `.when`:

```dart
final value = ref.watch(myStreamProvider);

value.when(
  data: (val) => Text('Value: $val'),
  loading: () => CircularProgressIndicator(),
  error: (e, st) => Text('Error: $e'),
);
```

---

## Full Example

### Step 1: Define the Provider

```dart
import 'package:flutter_riverpod/flutter_riverpod.dart';

final timeStreamProvider = StreamProvider<DateTime>((ref) {
  return Stream.periodic(
    Duration(seconds: 1),
    (_) => DateTime.now(),
  );
});
```

### Step 2: Consume in the UI

```dart
import 'package:flutter/material.dart';
import 'package:flutter_riverpod/flutter_riverpod.dart';

class TimeWidget extends ConsumerWidget {
  @override
  Widget build(BuildContext context, WidgetRef ref) {
    final time = ref.watch(timeStreamProvider);

    return time.when(
      data: (value) => Text('Current time: ${value.toLocal()}'),
      loading: () => CircularProgressIndicator(),
      error: (err, _) => Text('Error: $err'),
    );
  }
}
```

---

## AutoDispose Version

You can make the stream auto-cancel when the widget is removed:

```dart
final autoDisposeStreamProvider = StreamProvider.autoDispose<int>((ref) {
  return Stream.periodic(Duration(seconds: 1), (count) => count);
});
```

---

## Use Cases

* Firebase `snapshots()`
* Live chat messages
* IoT sensor feeds
* Realtime game scores
* Timer and stopwatch

---
## `ref.invalidate()` in Riverpod

### What is `ref.invalidate()`?

`ref.invalidate(provider)` tells Riverpod to:

* **Dispose** the current instance of the provider.
* **Recreate** it the next time it is accessed.

In simple terms, it's like telling Riverpod:

> "Forget everything about this provider and start fresh."

---

### When to Use It

You might use `ref.invalidate()` when:

* You want to **force a refresh** (like re-fetching from an API).
* You need to **reset the internal state** of a `StateNotifier`, `AsyncNotifier`, or `StreamNotifier`.
* You want to **restart a stream** or force a rebuild of dependent logic.

---

### Example 1: Invalidating a StreamNotifier

```dart
ref.invalidate(stockPriceNotifier);
```

This cancels the current stream and restarts the `StockPriceNotifier` the next time it is accessed.

---

### Example 2: Using in a Button

```dart
ElevatedButton(
  onPressed: () {
    ref.invalidate(stockPriceNotifier);
  },
  child: Text('Refresh Price Stream'),
)
```

This button, when pressed, will reset the `stockPriceNotifier`, causing it to restart and emit values from scratch.

---

### Behavior Summary

| Feature                 | Description                                          |
| ----------------------- | ---------------------------------------------------- |
| Disposes Provider       | Yes                                                  |
| Rebuilds on Next Access | Yes                                                  |
| Resets Internal State   | Yes                                                  |
| Common Use Cases        | Refreshing data, restarting streams, resetting state |

---

### Notes

* `ref.invalidate()` is a **manual way to reset a provider**, which can be useful in user-triggered actions or error recovery.
* It does **not** immediately rebuild UI. Instead, the next `ref.watch()` triggers the fresh build.
* If you want to refresh and get the new value right away, consider using:

```dart
ref.refresh(provider);
```

This is shorthand for:
```dart
ref.invalidate(provider);
ref.watch(provider);
```
---
