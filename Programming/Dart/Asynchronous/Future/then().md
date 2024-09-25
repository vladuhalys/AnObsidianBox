Метод `then()` класу `Future` в Dart використовується для обробки результату асинхронної операції, коли `Future`успішно завершено. Він дозволяє виконати функцію після завершення асинхронної операції і отримати її результат або обробити помилку.

### Основна ідея методу `then()`

Коли `Future` завершується (успішно або з помилкою), виконання вашої програми триває завдяки методу `then()`. Це дозволяє створювати ланцюжки асинхронних операцій і реагувати на результат першого завершеного `Future`.

### Синтаксис методу `then()`

```dart
Future<T> then<T>(FutureOr<T> onValue(T value), {Function? onError});
```

- **onValue** — функція, яка викликається, коли `Future` успішно завершено. Вона отримує як аргумент результат, що повертає `Future`.
- **onError** — необов'язкова функція для обробки помилок, які можуть виникнути при виконанні `Future`. Якщо помилка виникла, а `onError` не передана, помилка буде передана далі. ^2e737b

Метод `then()` повертає новий `Future`, який містить результат функції `onValue` або, якщо виникла помилка, обробляє її через `onError`.

### Простий приклад використання `then()`

```dart
Future<int> fetchData() {
  return Future.delayed(Duration(seconds: 2), () => 42);
}

void main() {
  fetchData().then((result) {
    print("Результат: $result");
  });
}
```


У цьому прикладі функція `fetchData()` повертає `Future`, який завершиться через 2 секунди і поверне результат 42. Коли `Future` завершиться, функція всередині `then()` буде викликана і виведе результат.

### Обробка помилок через `then()`

Ви можете обробляти помилки через другий параметр методу `then()`, або використати метод `catchError()`.

#### Приклад з обробкою помилки через `then()`:

```dart
Future<int> fetchDataWithError() {
  return Future.delayed(Duration(seconds: 2), () => throw Exception("Щось пішло не так"));
}

void main() {
  fetchDataWithError().then((result) {
    print("Результат: $result");
  }, onError: (error) {
    print("Помилка: $error");
  });
}
```

У цьому випадку, якщо `Future` кидає виняток, функція `onError` викликається для обробки цієї помилки.

#### Приклад з `catchError()`:

Альтернативно, ви можете використовувати метод `catchError()` для обробки помилок:

```dart
Future<int> fetchDataWithError() {
  return Future.delayed(Duration(seconds: 2), () => throw Exception("Щось пішло не так"));
}

void main() {
  fetchDataWithError().then((result) {
    print("Результат: $result");
  }).catchError((error) {
    print("Помилка: $error");
  });
}
```
### Ланцюжки асинхронних операцій

Метод `then()` також дозволяє ланцюжити кілька асинхронних операцій. Якщо функція, яка передається в `then()`, повертає новий `Future`, цей новий `Future` буде виконано після завершення попереднього.

#### Приклад ланцюжка асинхронних операцій:

```dart
Future<int> fetchData() {
  return Future.delayed(Duration(seconds: 2), () => 42);
}

Future<String> processResult(int data) {
  return Future.delayed(Duration(seconds: 1), () => "Оброблено: $data");
}

void main() {
  fetchData()
    .then((result) {
      print("Результат: $result");
      return processResult(result); // Повертаємо новий Future
    })
    .then((processedResult) {
      print(processedResult); // Обробляємо результат другого Future
    });
}
```

У цьому прикладі:

1. Спочатку ми отримуємо дані з `fetchData()`.
2. Після завершення цієї операції ми передаємо результат в `processResult()`.
3. Обробляємо результат другої асинхронної операції і виводимо його.

### Повернення нового [[Future]] в `then()`

Якщо функція, передана в `then()`, повертає новий `Future`, метод `then()` дочекається його завершення перед тим, як перейти до наступної операції в ланцюжку.

```dart
Future<int> fetchData() {
  return Future.delayed(Duration(seconds: 2), () => 42);
}

Future<void> processResult(int result) {
  return Future.delayed(Duration(seconds: 1), () {
    print("Обробка результату: $result");
  });
}

void main() {
  fetchData().then((result) {
    return processResult(result); // Повертаємо новий Future
  }).then((_) {
    print("Всі операції завершені");
  });
}
```
### Виконання синхронного коду в `then()`

Хоча `then()` зазвичай використовується для асинхронних операцій, ви також можете виконувати синхронний код у ньому. Якщо функція не повертає `Future`, `then()` поверне завершений `Future` з результатом функції.

```dart
Future<int> fetchData() {
  return Future.delayed(Duration(seconds: 2), () => 42);
}

void main() {
  fetchData().then((result) {
    print("Результат: $result");
    return result * 2; // Повертаємо синхронний результат
  }).then((multipliedResult) {
    print("Множений результат: $multipliedResult");
  });
}
```

### Обробка помилок у ланцюжках `then()`

Якщо в ланцюжку асинхронних операцій виникне помилка, вона може бути оброблена в будь-якому з `then()`, або її можна обробити за допомогою [[catchError()]] в кінці ланцюжка.

```dart
Future<int> fetchData() {
  return Future.delayed(Duration(seconds: 2), () => throw Exception("Помилка завантаження даних"));
}

Future<void> processResult(int result) {
  return Future.delayed(Duration(seconds: 1), () {
    print("Обробка результату: $result");
  });
}

void main() {
  fetchData()
    .then((result) {
      return processResult(result);
    }).then((_) {
      print("Операції завершені");
    }).catchError((error) {
      print("Помилка в ланцюжку: $error");
    });
}
```
### Підсумки

1. **`then()`** використовується для обробки результату асинхронної операції, коли `Future` завершено успішно.
2. Метод може бути використаний для ланцюжка операцій, де один `Future` викликає інший.
3. Ви можете обробляти помилки через [[#^2e737b |onError]] у `then()` або використати [[catchError()]].
4. Якщо функція, передана в `then()`, повертає новий `Future`, метод `then()` чекає завершення цього нового `Future`.
5. Якщо функція повертає синхронне значення, Dart автоматично обгортає це значення в `Future`.

`then()` — це один із найважливіших методів для роботи з асинхронним кодом у Dart, що допомагає побудувати чітку і керовану послідовність дій для обробки результатів і помилок.