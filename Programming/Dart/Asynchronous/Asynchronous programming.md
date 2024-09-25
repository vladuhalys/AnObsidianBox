Асинхронне програмування в Dart є важливою частиною, оскільки дозволяє писати код, який не блокує виконання під час очікування операцій, таких як звернення до мережі, читання/запис файлів або таймери.

Основні концепції асинхронного програмування в Dart:

Асинхронне програмування в Dart є важливою частиною, оскільки дозволяє писати код, який не блокує виконання під час очікування операцій, таких як звернення до мережі, читання/запис файлів або таймери.

Основні концепції асинхронного програмування в Dart:

### 1. **[[Future]]**

`Future` представляє результат операції, яка виконується асинхронно і буде завершена в майбутньому. Це зручний спосіб виконувати код, який потребує часу, не блокуючи основний потік виконання програми.

Приклад:

```dart
Future<String> fetchData() async {
  return await Future.delayed(Duration(seconds: 2), () => "Data received");
}

void main() async {
  print("Fetching data...");
  String data = await fetchData();
  print(data);
}
```

У цьому прикладі `fetchData` симулює асинхронну операцію (очікування 2 секунди), після чого повертає рядок.

### 2. **[[async & await]]**

Ключові слова `async` та `await` використовуються для полегшення роботи з асинхронними функціями. `async`позначає функцію як асинхронну, а `await` використовується для очікування результату `Future`.

Приклад:

```dart
Future<void> process() async {
  print("Start processing...");
  await Future.delayed(Duration(seconds: 3));
  print("Processing completed.");
}

void main() async {
  await process();
  print("Done.");
}
```

### 3. **[[Stream]]**

`Stream` використовується для обробки послідовних подій або даних, які приходять із затримкою. Це корисно, коли потрібно обробляти множинні значення, що надходять з часом (наприклад, події користувача, оновлення датчиків або дані з інтернету).

Приклад:

```dart
Stream<int> generateNumbers() async* {
  for (int i = 1; i <= 5; i++) {
    await Future.delayed(Duration(seconds: 1));
    yield i;
  }
}

void main() async {
  await for (int value in generateNumbers()) {
    print(value);
  }
}
```

У цьому прикладі `Stream` генерує числа з інтервалом у 1 секунду.

### 4. **Handling errors**

Асинхронний код може генерувати помилки, і Dart надає механізми для обробки цих помилок за допомогою `try-catch`.

Приклад:

```dart
Future<void> fetchData() async {
  try {
    String data = await Future.delayed(Duration(seconds: 2), () => throw Exception("Error!"));
    print(data);
  } catch (e) {
    print("Caught error: $e");
  }
}

void main() async {
  await fetchData();
}
```

Асинхронне програмування в Dart спрощує роботу з тривалими операціями та дозволяє створювати більш ефективні програми без блокування головного потоку.