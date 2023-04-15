<h3>Java Virtual Machine. Виртуальная машина Java</h3>
<h7>Рассмотрим, как она работает на примере загрузки и выполнения класса:</h7>

```Java
public class JvmComprehension {

    public static void main(String[] args) {
        int i = 1;                      // 1
        Object o = new Object();        // 2
        Integer ii = 2;                 // 3
        printAll(o, i, ii);             // 4
        System.out.println("finished"); // 7
    }

    private static void printAll(Object o, int i, Integer ii) {
        Integer uselessVar = 700;                   // 5
        System.out.println(o.toString() + i + ii);  // 6
    }
}
```

##### Что бы загрузить класс ```class JvmComprehension``` в JVM его необходимо... загрузить. 
Для этого существуют специальные классы-загрузчики ```ClassLoader```:
  * ```Bootstrap```, как базовый загрузчик
  * ```Platform``` - загрузчик расширений и потомок ```Bootstrap```
  * ```Application```, как системный загрузчик классов

Главный класс программы ```class JvmComprehension``` всегда загружается 
  ```Application``` класс-загрузчиком, который делегирует свои права классу-предку
```Platform``` и проверяет, не был ли загружен главный класс программы ранее. ```Platform```,
в свою очередь делегирует права ```Bootstrap``` и проверка повторяется. Если же класс повторно
не был загружен ClassLoader-родителями, то управление возвращается ```Application``` и после загрузки
```class JvmComprehension``` начинается этап линковки, который делится на 3 части:
1. ```Verify``` - Проверка байт-кода. Java проверяет, нет ли синтаксический ошибок, совместимость типов, корректность инструкций
2. ```Prepare``` - Выделение памяти под статические ```static``` поля и их инициализация
3. ```Resolve``` - Разрешение символьных ссылок

##### Класс ```class JvmComprehension``` инициализируется.
##### Если же, загружаемый класс, не был найден ни одним из классов-загрузчиков, то генерируется ошибка
```java.lang.ClassNotFoundException```

##### После загрузки класса класс-загрузчиком, класс попадает в область памяти ```Metaspace```, где хранятся:
  * данные о классе (имя, методы, поля и др.)
  * константы

| Стек JVM                                                                                                                                                                                          |
|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 7. Создается метод `println()` и печать текста `finished`. Класс `JvmComprehension` завершает свою работу.                                                                                        |
| 6. Создается метод `println()` и переменные `o`, `i`, `ii`, которым передаются ссылки на ранее созданные переменные (см. п.п. 1-3). Выполняется печать результата и метод `printAll` завершается. |
| 5. Ссылочная переменная `uselessVar` типа Integer. В куче `Heap` выделяется область памяти `700` и<br/> в переменную `uselessVar` передается ссылка на объект в `Heap`                            |
| 4. Создается новый фрейм и метод `printAll()` с переменными `o`, `i`, `ii`, которым передаются ссылки на ранее созданные переменные (см. п.п. 1-3)                                                |
| 3. Ссылочная переменная `ii` типа Integer. В куче `Heap` выделяется область памяти `2` и<br/> в переменную `ii` передается ссылка на объект в `Heap`                                              |
| 2. Ссылочная переменная `o` типа `Object`. В куче `Heap` выделяется область памяти `new Object` и<br/> в переменную `o` передается ссылка на объект в `Heap`                                      |
| 1. Загружается примитивный тип данных ``int i = 1``                                                                                                                                               |
| 0. В момент вызова метода в стеке создается фрейм (кадр) ```main()```                                                                                                                             |

##### В момент перехода на позицию стека 7, ссылки на объекты в области памяти `o`, `i`, `ii` больше не нужны и чистильщик `Garbage Collection` очищает от них область `Heap`

| Область памяти Heap (куча)                                                                                                |
|---------------------------------------------------------------------------------------------------------------------------|
| 5. Выделяется область памяти со значением 700 типа Integer, ссылка на область передается в стек в переменную `uselessVar` |
| 3. Выделяется область памяти со значением 2 типа Integer, ссылка на область передается в стек в переменную `ii`           |
| 2. Выделяется область памяти `new Object()`, ссылка на область передается в стек в переменную `o`                         |