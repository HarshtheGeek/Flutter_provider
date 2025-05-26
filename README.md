# Flutter_Riverpod
This repo will contain the notes of flutter riverpod

---
# `copyWith` in Dart & Flutter

`copyWith` is a helpful method in Dart and Flutter used for working with **immutable objects**. Instead of creating a new object from scratch every time, `copyWith` lets you make a **copy** and change only the values you need.

---

## Why use `copyWith`?

In Dart, it's best practice to make your data **immutable** (unchangeable). So instead of modifying an object directly, you create a new one with updated values.

### Without `copyWith`
```dart
final user1 = User(name: "Harsh", age: 22);

final user2 = User(name: "Harsh", age: 23); // Tedious if many fields
````

### With `copyWith`

```dart
final user2 = user1.copyWith(age: 23); // Simple and clean
```

---

## Example: Custom Class with `copyWith`

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

### Usage:

```dart
final user1 = User(name: "Harsh", age: 22);
final user2 = user1.copyWith(age: 23);

print(user2.name); // Harsh
print(user2.age);  // 23
```

---

## Real-life Analogy

> Think of a form you filled with your details. You want to change just the age.
> Instead of writing everything again, you copy the old form and update **only the age**.
> That's what `copyWith` does!

---

## Commonly Used In:

* Flutter UI classes like `TextStyle`, `ThemeData`
* State management (`Provider`, `Bloc`, etc.)
* Any custom data model in Dart

---
## Provider
It is the most basic type of provider in Riverpod. It exposes an immutable value or computation (like a service, configuration, or computed value) that other parts of your app can read or watch.

```dart
final nameProvider = Provider<String>((ref) {
  return 'Harsh';
});
```
we can use it like this :
```dart
class MyWidget extends ConsumerWidget {
  @override
  Widget build(BuildContext context, WidgetRef ref) {
    final name = ref.watch(nameProvider);
    return Text('Hello, $name!');
  }
}
```
- In short provider is immutable jo value hold karta hai Perfect for exposing constants, configurations, or computed values.
- Automatically disposes and re-creates values when dependencies change.

# ProviderScope – Root of your provider container
ProviderScope is a widget that you place at the root of your widget tree (usually in main.dart). It is responsible for storing and managing the lifecycle of all your providers.

```dart
void main() {
  runApp(
    ProviderScope(
      child: MyApp(),
    ),
  );
}
```
**Why it's important:**
- It creates a container where all your providers live.
- Allows overriding providers (e.g., for testing or different environments).
- Needed only once, usually at the top level of the app.

Certainly, Harsh! Here's your explanation of `ConsumerWidget` written in **GitHub README-style Markdown** — perfect for documentation or learning repos:

---

# Understanding `ConsumerWidget` in Riverpod (Flutter)

## What is `ConsumerWidget`?

A `ConsumerWidget` is just like a normal Flutter widget, **but smarter**.  
It allows you to **read and watch providers** directly in the `build` method.

---

## Real-World Analogy

Think of `ConsumerWidget` as a **smart widget** that:
- Can "listen" to provider data
- Rebuilds itself **automatically** when that data changes

---

## Basic Example

```dart
// Define a provider
final nameProvider = Provider((ref) => 'Harsh');

// Use ConsumerWidget to read the provider
class MyWidget extends ConsumerWidget {
  @override
  Widget build(BuildContext context, WidgetRef ref) {
    final name = ref.watch(nameProvider); // Watch the provider
    return Text('Hello, $name!');
  }
}
````

### What’s Happening?

* `ref.watch(nameProvider)` reads the value `'Harsh'`
* The widget will rebuild **automatically** if `nameProvider` changes

---

## In Simple Terms

| Without `ConsumerWidget` | With `ConsumerWidget`        |
| ------------------------ | ---------------------------- |
| Builds static UI         | Builds dynamic, reactive UI  |
| No access to providers   | Can read/watch providers     |
| No auto updates          | Auto-rebuilds on data change |

---

# Difference Between `ref.watch` and `ref.read` in Riverpod

In Riverpod, we use the `ref` object to interact with providers. The most common methods are:

- ✅ `ref.watch(...)` – **Listens to the provider** (rebuilds UI on change)
- ✅ `ref.read(...)` – **Reads the value once** (no rebuild)
---

## `ref.watch()` – Reactive

```dart
final counterProvider = StateProvider<int>((ref) => 0);

class MyWidget extends ConsumerWidget {
  @override
  Widget build(BuildContext context, WidgetRef ref) {
    final count = ref.watch(counterProvider);
    return Text('Count: $count');
  }
}
````

### Use When:

* You want the widget to rebuild automatically when the value changes
* Good for UI display

---

## `ref.read()` – One-Time Read

```dart
ElevatedButton(
  onPressed: () {
    final counter = ref.read(counterProvider.notifier);
    counter.state++; // Write to the provider
  },
  child: Text('Increment'),
);
```

### Use When:

* You need the value **once**, like in a button or init method
* You want to **update** the provider

# `ConsumerWidget` vs `ConsumerStatefulWidget` in Riverpod

| Feature                     | `ConsumerWidget`                 | `ConsumerStatefulWidget`          |
|-----------------------------|----------------------------------|-----------------------------------|
| **Stateless or Stateful?**  | Stateless                        | Stateful                          |
| **Can use `ref.watch`?**    | ✅ Yes                           | ✅ Yes                            |
| **Can use `setState`?**     | ❌ No                            | ✅ Yes                            |
| **Has `initState`, `dispose`?** | ❌ No                        | ✅ Yes                            |
| **When to use**             | For reactive UI only             | For UI + local state management   |

---


# `Provider` vs `StateProvider` in Flutter

Understanding the difference between `Provider` and `StateProvider` in Riverpod is key to managing state effectively in Flutter apps.

---

## `Provider` – Read-Only Values

`Provider` is used for exposing **static** or **computed values** that **do not change** over time.

### Use when:
- You only need to **read a value**.
- The value is **derived**, **constant**, or **read-only**.

### Example:
```dart
final greetingProvider = Provider<String>((ref) {
  return 'Hello, Riverpod!';
});
````

### Characteristics:

* No internal state management.
* Automatically recomputes if dependencies change.
* Cannot be updated manually.

---

## `StateProvider` – Simple Mutable State

`StateProvider` is used for **managing simple, mutable state** (like counters, booleans, etc.).

### Use when:

* You need to **update state** (e.g., with buttons or inputs).
* You’re handling simple UI state.

### Example:

```dart
final counterProvider = StateProvider<int>((ref) => 0);
```

### Characteristics:

* Mutable state using `.notifier` and `.state`
* Works well with basic data types
* Ideal for toggles, text input, counters, etc.

```dart
ref.read(counterProvider.notifier).state++;
```

---

## Comparison Table

| Feature           | `Provider`                      | `StateProvider`                            |
| ----------------- | ------------------------------- | ------------------------------------------ |
| Purpose           | Read-only values                | Mutable state                              |
| Mutable           | ❌ No                            | ✅ Yes                                      |
| Access Value      | `ref.watch(provider)`           | `ref.watch(stateProvider)`                 |
| Update Value      | ❌ Not possible                  | `ref.read(stateProvider.notifier).state =` |
| Example Use Cases | Configs, API clients, constants | Counters, toggles, simple UI state         |

---

## 🔁 What if I need more control?

For more advanced use cases, consider:

* `StateNotifierProvider` – For more complex state logic
* `NotifierProvider` – For an OOP-style state management approach

---







