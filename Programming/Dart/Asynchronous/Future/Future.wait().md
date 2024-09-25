
Метод `Future.wait()` в Dart дозволяє одночасно чекати завершення кількох асинхронних операцій (кількох `Future`) і повертає новий `Future`, який завершується після того, як усі передані `Future` будуть завершені.

### Основна ідея `Future.wait()`

Іноді у вашій програмі потрібно виконати кілька асинхронних операцій одночасно і дочекатися завершення кожної з них. У такому випадку метод `Future.wait()` стає дуже корисним, оскільки він дозволяє паралельно виконувати декілька операцій і чекати їх завершення. Як тільки всі операції завершені, `Future.wait()` повертає новий `Future`, який містить список результатів усіх переданих операцій.

### Синтаксис `Future.wait()`

```dart
Future<List<T>> wait<T>(Iterable<Future<T>> futures, {bool eagerError = false, void Function(T successValue)? cleanUp})
```

- **futures** — список або ітератор з об'єктами `Future`, які потрібно дочекатися.
- **eagerError** — (необов'язковий параметр) визначає, чи завершити новий `Future` одразу після виникнення помилки в одному з переданих `Future` (якщо встановлено в `true`), або дочекатися завершення всіх `Future`, навіть якщо деякі з них завершилися з помилкою.
- **cleanUp** — (необов'язковий параметр) функція, яку можна викликати для очищення ресурсів, коли операція була успішно завершена.

### Основні моменти

1. **Звичайне використання**: Метод чекає, поки всі передані `Future` завершаться, і повертає список результатів кожного `Future`.
2. **Робота з помилками**: Якщо один із `Future` завершиться з помилкою, весь `Future.wait()` завершиться з цією помилкою, якщо параметр `eagerError` встановлено в `true` (за замовчуванням). Однак можна дочекатися завершення всіх `Future`, навіть якщо деякі завершаться з помилкою, встановивши цей параметр в `false`.

### Простий приклад використання `Future.wait()`

У цьому прикладі ми створимо кілька `Future`, які завершуються через різний час, і використаємо `Future.wait()`, щоб дочекатися завершення всіх цих операцій.

```dart
Future<void> fetchData1() async {
  await Future.delayed(Duration(seconds: 2));
  print("Дані 1 отримані");
  return "Результат 1";
}

Future<void> fetchData2() async {
  await Future.delayed(Duration(seconds: 3));
  print("Дані 2 отримані");
  return "Результат 2";
}

void main() async {
  await Future.wait([fetchData1(), fetchData2()]);
  print("Всі дані отримані");
}
```

У цьому прикладі:

- Дві асинхронні операції (`fetchData1()` і `fetchData2()`) виконуються одночасно.
- `Future.wait()` чекає завершення обох операцій і лише тоді виводить повідомлення "Всі дані отримані".

### Повернення результатів усіх `Future`

Метод `Future.wait()` повертає `Future`, який містить список результатів кожного переданого `Future`. У наступному прикладі ми продемонструємо, як отримати ці результати.

```dart
Future<int> fetchData1() async {
  await Future.delayed(Duration(seconds: 2));
  return 10;
}

Future<int> fetchData2() async {
  await Future.delayed(Duration(seconds: 3));
  return 20;
}

void main() async {
  List<int> results = await Future.wait([fetchData1(), fetchData2()]);
  print("Результати: $results"); // Виведе: Результати: [10, 20]
}
```

У цьому прикладі:

- Ми отримуємо список результатів з двох `Future`: `[10, 20]`.
- Після завершення всіх операцій ми виводимо цей список.

### Обробка помилок у `Future.wait()`

Якщо один із `Future` завершується з помилкою, весь `Future.wait()` завершиться з цією помилкою. Однак є можливість налаштувати поведінку через параметр `eagerError`.

#### Приклад з помилкою:

```dart
Future<int> fetchData1() async {
  await Future.delayed(Duration(seconds: 2));
  return 10;
}

Future<int> fetchDataWithError() async {
  await Future.delayed(Duration(seconds: 1));
  throw Exception("Помилка під час отримання даних");
}

void main() async {
  try {
    List<int> results = await Future.wait([fetchData1(), fetchDataWithError()]);
    print("Результати: $results");
  } catch (error) {
    print("Помилка: $error"); // Виведе: Помилка: Exception: Помилка під час отримання даних
  }
}
```

У цьому випадку:

- Одна з асинхронних операцій завершується з помилкою.
- `Future.wait()` завершується з тією ж помилкою, і вона обробляється в `catch` блоці.

### Параметр `eagerError`

Якщо встановити параметр `eagerError` в `false`, `Future.wait()` дочекається завершення всіх `Future`, навіть якщо деякі з них завершаться з помилкою.

```dart
Future<int> fetchData1() async {
  await Future.delayed(Duration(seconds: 2));
  return 10;
}

Future<int> fetchDataWithError() async {
  await Future.delayed(Duration(seconds: 1));
  throw Exception("Помилка під час отримання даних");
}

void main() async {
  try {
    List<int> results = await Future.wait([fetchData1(), fetchDataWithError()], eagerError: false);
    print("Результати: $results");
  } catch (error) {
    print("Помилка: $error");
  }
}
```

У цьому прикладі:

- Навіть якщо один з `Future` завершився з помилкою, інший `Future` продовжує виконання.
- `Future.wait()` чекає завершення всіх операцій, але все ще може завершитися з помилкою після завершення всіх `Future`.

### Використання `cleanUp` для очищення

Метод `Future.wait()` також підтримує параметр `cleanUp`, який дозволяє очистити ресурси після успішного завершення асинхронної операції.

```dart
Future<String> fetchData(int index) async {
  await Future.delayed(Duration(seconds: index));
  if (index == 2) throw Exception("Помилка $index");
  return "Дані $index";
}

void main() async {
  try {
    List<String> results = await Future.wait(
      [
        fetchData(1),
        fetchData(2),
        fetchData(3),
      ],
      eagerError: true,
      cleanUp: (successValue) => print("Очищення після: $successValue"),
    );
    print("Результати: $results");
  } catch (error) {
    print("Помилка: $error");
  }
}
```

У цьому прикладі параметр `cleanUp` викликається для кожного успішного `Future`, навіть якщо один з них завершився помилкою.

### Підсумки

- **`Future.wait()`** дозволяє одночасно чекати завершення кількох асинхронних операцій.
- Він повертає новий `Future`, який завершується, коли всі передані `Future` завершені, і результатом є список результатів цих `Future`.
- Якщо один з `Future` завершується з помилкою, весь `Future.wait()` може завершитися з тією ж помилкою, але це можна змінити за допомогою параметра `eagerError`.
- Параметр `cleanUp` дозволяє очищати ресурси після завершення успішних `Future`.

Цей метод є корисним для керування кількома паралельними асинхронними операціями, забезпечуючи просту й ефективну обробку результатів і помилок.