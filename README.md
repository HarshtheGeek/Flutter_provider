# Flutter_Riverpod
This repo will contain the notes of flutter riverpod

Awesome, Harsh! Since you’ve understood `copyWith`, here’s a clean and well-formatted `README.md` content you can use for a GitHub repo to explain it.

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
## Summary

| Feature   | `copyWith`                                              |
| --------- | ------------------------------------------------------- |
| Purpose   | Create a modified copy of an object                     |
| Benefits  | Cleaner code, avoids duplication, supports immutability |
| Common In | Models, Flutter widgets, UI styling                     |

---

