Клас `Future` в Dart є основою для асинхронного програмування. Він представляє значення або помилку, які будуть доступні в майбутньому після завершення певної асинхронної операції. `Future` використовується для операцій, які тривають певний час, наприклад, звернення до серверів, читання файлів або таймери.

### Що таке `Future`

`Future` в основному обіцяє, що певна операція буде завершена пізніше, і поверне значення або помилку. Це дозволяє виконувати інші завдання, поки `Future` не завершиться.

Клас `Future` має три основні стани:

1. **Незавершений (`uncompleted`)** — це початковий стан, коли асинхронна операція ще не виконалася.
2. **Завершений успішно (`completed with a value`)** — це стан, коли операція завершилася, і результат доступний.
3. **Завершений з помилкою (`completed with an error`)** — це стан, коли операція завершилася з помилкою.

### Створення об'єкта `Future`

Існує кілька способів створення `Future`:

1. **Створення з `Future` статичного метода**

Метод `Future.delayed()` дозволяє створювати `Future`, який завершується після певної затримки.

```dart
Future<void> delayedFuture() {
  return Future.delayed(Duration(seconds: 2), () {
    print("Future завершився через 2 секунди");
  });
}

void main() {
  delayedFuture();
}
```

У цьому прикладі `Future` завершиться через 2 секунди, після чого виконається вказана функція.

2. **Створення зі значенням**

Іноді потрібно негайно створити `Future`, що містить певне значення.

```dart
Future<int> immediateFuture() {
  return Future.value(42); // Створюємо Future з результатом 42
}
```

3. **Створення з помилкою**

Ви також можете створити `Future`, що одразу завершується помилкою:

```dart
Future<void> errorFuture() {
  return Future.error("Щось пішло не так");
}
```

### Методи класу `Future`

Клас `Future` надає кілька корисних методів для обробки результатів і помилок:

#### 1. **then()**

Метод `then()` використовується для отримання результату після завершення `Future`. Він викликає функцію з результатом, коли `Future` успішно завершується.

```dart
Future<int> calculateSum() {
  return Future.delayed(Duration(seconds: 2), () => 10 + 20);
}

void main() {
  calculateSum().then((result) {
    print("Результат: $result");
  });
}
```

#### 2. **catchError()**

Метод `catchError()` дозволяє обробляти помилки, що виникають під час виконання `Future`.

```dart
Future<void> fetchData() {
  return Future.delayed(Duration(seconds: 2), () => throw Exception("Помилка!"));
}

void main() {
  fetchData().catchError((error) {
    print("Помилка: $error");
  });
}
```

#### 3. **whenComplete()**

Метод `whenComplete()` викликає функцію після завершення `Future`, незалежно від того, чи завершився він успішно, чи з помилкою.

```dart
Future<void> process() {
  return Future.delayed(Duration(seconds: 2), () => throw Exception("Помилка!"));
}

void main() {
  process()
    .then((_) => print("Успішно завершено"))
    .catchError((e) => print("Завершено з помилкою"))
    .whenComplete(() => print("Операція завершена")); // Виконується у будь якому разі
}
```

#### 4. **Future.wait()**

Метод `Future.wait()` дозволяє чекати на завершення кількох `Future` одночасно. Він повертає новий `Future`, який завершиться, коли завершаться всі `Future` з переданого списку.

```dart
Future<void> fetchData1() async {
  await Future.delayed(Duration(seconds: 2));
  print("Дані 1 отримані");
}

Future<void> fetchData2() async {
  await Future.delayed(Duration(seconds: 3));
  print("Дані 2 отримані");
}

void main() async {
  await Future.wait([fetchData1(), fetchData2()]);
  print("Всі дані отримані");
}
```

### Ланцюжок `Future`

Методи `then()`, `catchError()` і `whenComplete()` можна ланцюжити для побудови послідовності операцій. Це дозволяє створювати потік асинхронних дій.

```dart
Future<void> exampleFuture() {
  return Future.delayed(Duration(seconds: 2), () => "Результат")
    .then((value) {
      print(value); // Обробляємо результат
      return Future.delayed(Duration(seconds: 1), () => "Другий результат");
    })
    .then((value) {
      print(value); // Обробляємо наступний результат
    })
    .catchError((e) {
      print("Помилка: $e"); // Обробляємо помилку, якщо вона виникне
    })
    .whenComplete(() {
      print("Операція завершена"); // Виконується після завершення всіх операцій
    });
}

void main() {
  exampleFuture();
}
```

### Асинхронні функції та `Future`

Функції, позначені як `async`, автоматично повертають `Future`. Це означає, що коли ви використовуєте ключове слово `async`, Dart обгортає результат функції в об'єкт `Future`.

```dart
Future<String> fetchData() async {
  await Future.delayed(Duration(seconds: 2));
  return "Дані отримані";
}
```

У цьому випадку функція `fetchData()` повертає `Future<String>`, навіть якщо явно в коді це не зазначено.

### Робота з множинними `Future`

Ви можете керувати кількома асинхронними операціями одночасно. Наприклад, `Future.any()` поверне перший `Future`, який завершиться, а `Future.wait()` чекає, поки всі `Future` завершаться.

- **`Future.any()`** завершується, як тільки один з `Future` успішно завершується.

```dart
Future<int> fetchFromServer1() async {
  await Future.delayed(Duration(seconds: 3));
  return 10;
}

Future<int> fetchFromServer2() async {
  await Future.delayed(Duration(seconds: 1));
  return 20;
}

void main() async {
  int result = await Future.any([fetchFromServer1(), fetchFromServer2()]);
  print("Перший результат: $result");
}
```

### Висновок

Клас `Future` — це основний механізм асинхронного програмування в Dart. Він дозволяє виконувати тривалі операції без блокування основного потоку виконання програми, обробляти успішні завершення та помилки, а також будувати складні ланцюжки асинхронних дій.