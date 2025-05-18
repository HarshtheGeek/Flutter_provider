# Flutter_provider
This repo will contain the notes of flutter provider


# Changenotifier
- It's a broadcaster. When your app's data changes, it informs all the listeners (widgets) that rely on it, prompting them to rebuild with updated values.
  
```dart
import 'package:flutter/foundation.dart';

// CountProvider is a ChangeNotifier, which allows widgets listening to it to rebuild when notifyListeners() is called.
class CountProvider extends ChangeNotifier {
  // Private variable to store the count value (starts at 50)
  int _count = 50;

  // Getter to expose the private _count variable to other widgets
  int get count => _count;

  // Method to increment the count and notify listeners that a change occurred
  void setCount() {
    _count++; // Increase the count by 1
    notifyListeners(); // Notify all widgets listening to this provider to rebuild
  }
}
```

---

## 🧠 What is `CountProvider`?

`CountProvider` is a simple **state management class** in Flutter using `ChangeNotifier`. It allows you to:

* Keep track of a count.
* Notify widgets when the count changes so they can rebuild automatically.

---

## 🧾 Code Explanation

### 🔒 `_count`

```dart
int _count = 50;
```

* This is a **private variable** (due to the `_`) that holds the current count.
* Starts at `50`.

---

### 👁 `get count`

```dart
int get count => _count;
```

* This is a **getter** to allow widgets to **read** the `_count` value.
* Widgets can’t change it directly—only read.

---

### 🔼 `setCount()`

```dart
void setCount() {
  _count++;
  notifyListeners();
}
```

* Increases the `_count` by 1.
* Calls `notifyListeners()`, which **tells all listening widgets to rebuild** and reflect the new count.

---

## 💡 When to Use This?

Use `CountProvider` in your Flutter app when you want to:

* Keep track of a value like a counter.
* Rebuild parts of your UI automatically when the value changes.

---

## 🧩 Example Usage

In your `main.dart`:

```dart
void main() {
  runApp(
    ChangeNotifierProvider(
      create: (context) => CountProvider(),
      child: MyApp(),
    ),
  );
}
```

In a widget:

```dart
Consumer<CountProvider>(
  builder: (context, provider, child) {
    return Column(
      children: [
        Text('Count: ${provider.count}'),
        ElevatedButton(
          onPressed: provider.setCount,
          child: Text('Increase'),
        ),
      ],
    );
  },
);
```

---

## 📌 Summary

| Part                | Purpose                                        |
| ------------------- | ---------------------------------------------- |
| `_count = 50`       | Stores the initial count value                 |
| `get count`         | Returns current count (read-only)              |
| `setCount()`        | Increases count and notifies UI                |
| `notifyListeners()` | Makes widgets that use `CountProvider` rebuild |

---

